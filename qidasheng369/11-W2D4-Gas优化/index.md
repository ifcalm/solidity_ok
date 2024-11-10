# 11-W2D4-Gas优化

**目标：理解并应用gas优化技巧**

- Gas使用的原理与优化的基本策略
- 减少存储读写操作
- 避免使用动态数组的某些操作
- 内联汇编（Yul）的简单使用

笔记

- Gas使用的原理与优化的基本策略
    - [**存储模式与 gas 优化**](https://decert.me/tutorial/solidity/solidity-adv/storage_gas)
        
        在 Solidity 编程中，优化合约的 gas 消耗不只是一个性能问题，更是一个经济问题，因为 gas 费用直接关联到合约的使用成本。
        
        Solidity 中的存储模式对 gas 消耗有直接影响，因此理解不同的存储方式以及如何优化它们对于开发高效和成本效益的智能合约至关重要。
        
        ## **存储概览[​](https://decert.me/tutorial/solidity/solidity-adv/storage_gas#%E5%AD%98%E5%82%A8%E6%A6%82%E8%A7%88)**
        
        在Solidity中，有三种类型的存储位置：storage、memory 和 calldata，它们各有不同的用途和成本影响：
        
        1. Storage：是合约在区块链上的永久存储。写入或修改存储的操作是最昂贵的，读取相对较便宜。
        2. Memory：是临时存储，仅在外部函数调用期间存在。其数据在函数调用结束后消失。读写 memory 比 storage 便宜得多。
        3. Calldata：是非持久性的存储位置，只存在于链上的函数调用期间，主要用于存储函数参数。calldata 类型的访问成本通常比memory低。
    - Chat-GPT
        
        在 Solidity 中，**Gas** 是执行智能合约过程中消耗的计算资源单位。了解 Gas 的使用原理和优化策略对于智能合约开发非常重要，因为以太坊的执行成本是基于 Gas 的消耗，Gas 消耗越多，执行的成本就越高。以下是 Gas 使用的基本原理和优化的常见策略。
        
        ### 1. **Gas 的基本原理**
        
        - **Gas 概念**：Gas 是执行以太坊网络中每一步计算操作时所需的单位。每个操作（例如存储、计算、传输）都有固定的 Gas 成本。
        - **Gas Limit**：每个区块都有一个 Gas 限额，这决定了单个区块中能包含的交易或计算的复杂程度。每笔交易也可以指定一个 `gasLimit`，这是用户愿意为该交易支付的最大 Gas 量。
        - **Gas Price**：交易发起者为每单位 Gas 愿意支付的以太币（ETH）价格。`Gas Price` 乘以消耗的 `Gas` 数量决定了最终支付的费用。
        - **Gas 消耗的分布**：
            - **计算操作**：基础的算术操作消耗较少的 Gas，如加法、乘法、逻辑运算等。
            - **存储操作**：写入和修改以太坊的存储数据（即合约的持久化存储）是最昂贵的操作之一，远高于计算操作。存储是每次调用合约时 Gas 费用的主要部分。
            - **合约创建**：部署新的合约需要大量 Gas，因为创建和初始化合约包括写入到区块链存储。
            - **合约调用**：调用一个合约中的函数，尤其是涉及其他合约交互时，可能消耗大量 Gas。
        
        ### 2. **常见的 Gas 优化策略**
        
        ### 2.1 **减少存储操作**
        
        存储在以太坊中是最昂贵的操作之一，因此减少不必要的存储写入可以极大地降低 Gas 成本。
        
        - **优化存储结构**：合约中每个 `storage` 变量都直接映射到区块链上的永久存储，这意味着每次读写都会消耗大量的 Gas。可以通过使用 `memory`（临时存储） 或 `calldata` 来优化 Gas 消耗。存储一次数据需要 20,000 Gas，而更新存储需要 5,000 Gas，相比之下，`memory` 和 `calldata` 的成本更低。
        - **批量更新存储**：与其在多个操作中反复写入存储，尽可能将数据在 `memory` 中处理后一次性写入 `storage`。例如，在循环中先累积计算，循环结束后再将结果写入 `storage`。
        - **删除存储数据以回收 Gas**：如果不再需要某些数据，可以通过将它们删除来节省 Gas（删除存储会返还 15,000 Gas）。例如，如果合约中有数组，使用 `delete` 操作可以回收一部分 Gas。
        
        ### 2.2 **使用高效的数据类型**
        
        - **合理选择数据类型**：Solidity 支持多种数据类型，如 `uint8`, `uint16`, `uint256` 等。尽量使用适当大小的数据类型，尤其是 `storage` 中的变量，因为使用过大的数据类型（如 `uint256`）可能会导致不必要的 Gas 消耗。如果数据量较小，选择较小的整数类型有助于节省存储空间。
        - **数据紧密打包**：多个小数据类型（如 `uint8` 或 `bool`）可以被紧密打包到同一个存储槽中。这可以显著减少每个变量占用的存储空间，降低存储相关的 Gas 消耗。
        
        ### 2.3 **减少外部合约调用**
        
        调用外部合约是一个昂贵的操作，因为它需要在不同的合约之间传递数据和执行逻辑。可以考虑以下策略：
        
        - **尽量减少合约间的调用**：如果逻辑可以在当前合约内完成，避免调用外部合约。尤其是循环中不宜多次调用外部合约。
        - **最小化调用的输入和输出数据**：通过减少传递给外部合约的参数和返回值的数量与大小来减少 Gas 的消耗。
        
        ### 2.4 **优化循环和复杂操作**
        
        - **避免大量循环操作**：Solidity 中的循环如果处理大量元素，会迅速消耗 Gas。尤其是当涉及 `storage` 变量的读写时，循环中的每一步都可能变得昂贵。可以考虑将数据处理分布在多个交易中执行，或采用链下计算（off-chain）等方式。
        - **提前退出循环**：如果可以确定某些条件满足时可以提前结束循环（如 `break`），可以减少不必要的循环次数，节省 Gas。
        
        ### 2.5 **合理设置 `gasLimit`**
        
        为每个交易设置合适的 `gasLimit`，可以避免交易失败（因为 Gas 不够）或者浪费不必要的 Gas。特别是对于复杂合约调用，确保 `gasLimit` 足够，但也不要设置过高。
        
        ### 2.6 **合约的逻辑优化**
        
        - **减少不必要的函数调用**：每次函数调用都有额外的开销，因此合并可以合并的逻辑，减少递归或嵌套函数调用。
        - **使用事件代替返回值**：在某些情况下，可以通过发出事件来代替函数返回值，从而减少计算复杂度和 Gas 消耗。事件的存储和读取成本较低，适合用于记录合约的操作结果。
        
        ### 3. **Gas 估算工具**
        
        - **Solidity Optimizer**：在编译合约时，启用 Solidity 内置的优化器可以自动进行一些优化，如移除不必要的中间变量和简化操作。
        - **Gas Reporter 和调试工具**：可以使用工具如 `eth-gas-reporter` 和 `Remix` 中的 Gas Profiler 来评估合约的 Gas 消耗，找到瓶颈并优化它们。
        - **EIP-2929（Gas 优化提案）**：EIP-2929 是以太坊的一项升级，它对存储的访问成本进行了调整。如果你的合约依赖较早版本，需要确保对最新的存储 Gas 成本优化进行了解。
        
        ### 4. **总结**
        
        Gas 优化的核心在于减少昂贵的存储操作，避免过多的外部合约调用，并通过合理的数据结构和逻辑优化来减少复杂度。通过使用这些策略，可以显著降低合约的执行成本，提高合约的经济性和可用性。
        
        你在开发合约时，有没有具体的部分遇到了性能瓶颈或者 Gas 消耗过高的问题呢？我可以帮你一起分析。
        
- 减少存储读写操作
    - **Storage 优化**
        1. 避免在循环中使用存储变量：
            
            尽量避免在循环中读取或写入存储变量，因为这将多次执行昂贵的存储操作。
            
            如果可能，将变量缓存到内存中（声明为 memory 类型），在循环外部进行一次写入。 示例：
            
            ```solidity
            uint256[] public values;
            
            // 较低效的方式：在循环中频繁读/写存储
            function bad(uint256[] memory newValues) public {
                for (uint i = 0; i < newValues.length; i++) {
                    values[i] = newValues[i];
                }
            }
            ```
            
            ```solidity
            // 更高效的方式：使用内存数据进行操作，之后写回存储
            function good(uint256[] memory newValues) public {
                uint256[] memory tempValues = values;
                for (uint i = 0; i < newValues.length; i++) {
                    tempValues[i] = newValues[i];
                }
                values = tempValues;
            }
            ```
            
    - **Memory 优化[​](https://decert.me/tutorial/solidity/solidity-adv/storage_gas#memory-%E4%BC%98%E5%8C%96)**
        
        尽管内存的使用比 storage 便宜，了解何时使用内存也能帮助进一步优化 gas 消耗：
        
        - 避免在内存中复制大的数据结构，尽可能使用指针。
        - 准确计算函数的内存使用，尽量减少不必要的内存占用。
        
        ```solidity
        contract MemoryExample {
            function processLargeArray(uint[] calldata data) external pure returns (uint[] memory) {
                uint[] memory temp = new uint[](data.length);
                for (uint i = 0; i < data.length; ++i) {
                    temp[i] = data[i] * 2;  // 对数据进行处理
                }
                return temp;
            }
        }
        ```
        
    - **Calldata 优化[​](https://decert.me/tutorial/solidity/solidity-adv/storage_gas#calldata-%E4%BC%98%E5%8C%96)**
        
        calldata 类型的数据访问成本通常比 memory 低，因为它直接操作输入数据，避免了额外的内存分配。
        
        以下是一个简单的例子，说明如何在 Solidity 中使用 calldata 优化 gas 消耗：
        
        ```solidity
        pragma solidity ^0.8.0;
        
        contract CalldataExample {
        // 函数输入参数使用 calldata 而非 memory
            function sum(uint256[] calldata numbers) external pure returns (uint256) {
                uint256 sum = 0;
                for (uint256 i = 0; i < numbers.length; ++i) {
                    sum += numbers[i];
                }
                return sum;
            }
        }
        ```
        
        在这个例子中，函数 `sum` 将一个 `uint256` 数组作为输入，该数组被指定为 `calldata` 类型。
        
        这意味着在数组处理时不会进行内存复制，从而节省 gas。
        
    
    在 Solidity 中有效地管理和优化不同的存储方式，不仅能显著降低合约执行的 gas 成本，还能大幅提高合约的执行效率。通过运用以上的策略并结合实际的开发经验，开发者可以设计出既经济高效又性能优异的智能合约。
    
    > 关于更多提升 gas 效率的技巧，可以查看 Decert.me 翻译的《[Gas 优化手册](https://decert.me/tutorial/rareskills-gas-optimization/)》。
    > 
- 避免使用动态数组的某些操作
    - Chat-GPT
        
        在 Solidity 中，**动态数组** 是一种灵活的数据结构，但由于其对存储的频繁读写操作，使用不当时可能会消耗大量的 Gas。因此，优化动态数组的使用对于降低智能合约的 Gas 成本至关重要。下面我将介绍一些针对动态数组的优化策略，帮助你减少 Gas 消耗。
        
        ### 1. **存储操作的成本理解**
        
        在 Solidity 中，存储（`storage`）是消耗 Gas 的主要来源之一。每次对存储中的动态数组进行写操作（添加、删除、修改元素），都会产生较高的 Gas 成本。
        
        - **写入新数据到存储**：写入一个新元素到动态数组需要消耗 20,000 Gas。
        - **修改现有存储数据**：修改现有元素需要消耗 5,000 Gas。
        - **读取存储数据**：读取存储中的数据相对便宜，消耗 800 Gas。
        
        因此，减少对存储的写操作、减少不必要的存储修改是优化动态数组的关键。
        
        ### 2. **优化动态数组的策略**
        
        ### 2.1 **批量操作，减少重复存储写入**
        
        在合约中使用动态数组时，避免在循环中频繁地写入和修改 `storage` 中的数组。尽量在 `memory` 中进行操作，然后一次性将结果写入 `storage`。这是因为 `memory` 中的读写操作相比 `storage` 便宜得多。
        
        **示例：存储批量更新**
        
        ```solidity
        // bad
        
        pragma solidity ^0.8.0;
        
        contract ArrayOptimization {
            uint256[] public numbers;
        
            // 添加多个元素，但批量更新存储
            function addNumbers(uint256[] memory newNumbers) public {
                uint256 length = newNumbers.length;
                for (uint256 i = 0; i < length; i++) {
                    // 在 memory 中操作，避免每次写入 storage
                    numbers.push(newNumbers[i]);
                }
            }
        }
        
        // good
        uint256[] public values;
        
        function good(uint256[] memory newValues) public {
            uint256[] memory tempValues = values;
            for (uint i = 0; i < newValues.length; i++) {
                tempValues[i] = newValues[i];
            }
            values = tempValues;
        }
        ```
        
        **注意**：虽然最终仍然需要写入 `storage`，但批量操作可以避免在循环内多次写入 `storage`，从而减少每次写入时的高 Gas 消耗。
        
        ### 2.2 **合理利用 `memory` 和 `calldata`**
        
        - **`calldata`**：对于函数输入参数，尤其是涉及大量数据的动态数组，使用 `calldata` 可以显著减少 Gas 消耗。`calldata` 是只读的，存储在更便宜的外部调用数据中。
        - **`memory`**：如果数组数据只在函数内部使用（即不需要存储在区块链上），可以将数组保存在 `memory` 中，因为 `memory` 的操作比 `storage` 更便宜。
        
        **示例：使用 `calldata` 优化动态数组**
        
        ```solidity
        pragma solidity ^0.8.0;
        
        contract ArrayOptimization {
            // 使用 calldata 传入数组，避免拷贝到内存
            function processArray(uint256[] calldata numbers) external {
                for (uint256 i = 0; i < numbers.length; i++) {
                    // 对 numbers[i] 进行处理，只在 calldata 中读取
                }
            }
        }
        
        ```
        
        使用 `calldata` 作为参数传递数组时，不需要将整个数组复制到 `memory` 中，从而节省了 Gas。
        
        ### 2.3 **删除元素时减少 Gas 消耗**
        
        动态数组中的元素删除在 Solidity 中可能会带来高昂的 Gas 消耗，尤其是当数组元素较多时。如果不考虑元素顺序，删除数组元素的一个常见优化是**将要删除的元素与数组末尾的元素交换**，然后移除最后一个元素，这样可以避免移动所有后续元素。
        
        **示例：优化的数组删除操作**
        
        ```solidity
        pragma solidity ^0.8.0;
        
        contract ArrayOptimization {
            uint256[] public numbers;
        
            // 删除数组中的某个元素，保持无序
            function removeElement(uint256 index) public {
                require(index < numbers.length, "Index out of bounds");
                numbers[index] = numbers[numbers.length - 1]; // 将最后一个元素移到删除位置
                numbers.pop(); // 删除最后一个元素
            }
        }
        
        ```
        
        这种方式避免了在删除元素时重新排列数组内的所有元素，从而减少 Gas 消耗。
        
        ### 2.4 **初始化和扩展数组时预留空间**
        
        动态数组在扩展时会不断消耗 Gas，特别是在 `push` 操作中。因此，如果你知道数组将来会有大量的元素插入，可以考虑通过初始化时预留足够的空间来减少多次扩展的开销。
        
        **示例：预先分配数组大小**
        
        ```solidity
        pragma solidity ^0.8.0;
        
        contract ArrayOptimization {
            uint256[] public numbers;
        
            function initializeArray(uint256 size) public {
                numbers = new uint256[](size); // 预先分配指定大小的数组
            }
        }
        
        ```
        
        这种方法可以避免在后续 `push` 操作时动态扩展数组的额外成本。
        
        ### 2.5 **使用固定大小数组替代动态数组**
        
        在某些场景下，如果数组的大小是固定的，考虑使用固定大小的数组。固定大小的数组避免了动态数组的扩展开销，同时在操作上更加高效。
        
        **示例：固定大小数组**
        
        ```solidity
        pragma solidity ^0.8.0;
        
        contract ArrayOptimization {
            uint256[10] public numbers; // 固定大小的数组
        }
        
        ```
        
        固定大小的数组在 `storage` 中节省了 Gas，因为它们不需要不断调整大小。
        
        ### 2.6 **分段处理大量数组操作**
        
        如果你需要处理一个很大的动态数组，尽量避免在一次交易中完成所有操作，因为一次处理过多的数组元素可能会导致 Gas 消耗超过区块的 `gasLimit`。可以将大量操作分段处理，每次处理一部分数据。
        
        **示例：分段处理数组**
        
        ```solidity
        pragma solidity ^0.8.0;
        
        contract ArrayOptimization {
            uint256[] public numbers;
        
            // 分批添加数据
            function addNumbersBatch(uint256[] memory newNumbers) public {
                uint256 length = newNumbers.length;
                for (uint256 i = 0; i < length; i++) {
                    numbers.push(newNumbers[i]);
                }
            }
        
            // 分批处理数据
            function processBatch(uint256 batchSize) public {
                uint256 length = numbers.length;
                for (uint256 i = 0; i < batchSize && i < length; i++) {
                    // 处理 numbers[i] 的逻辑
                }
            }
        }
        
        ```
        
        这样可以避免在一次交易中消耗过多 Gas，降低交易失败的风险。
        
        ### 2.7 **考虑链下处理**
        
        在某些情况下，数组的处理可以在链下完成，将计算复杂度从链上移到链下，然后只将最终结果提交到链上。这种方式可以显著减少链上的 Gas 消耗。
        
        ### 3. **总结**
        
        在 Solidity 中使用动态数组时，优化的核心策略是尽量减少对 `storage` 的频繁写操作，并通过批量处理、使用 `memory` 和 `calldata` 以及避免多余的存储访问来降低 Gas 消耗。通过这些优化，可以显著减少智能合约中动态数组的操作成本，提升合约的经济性和可扩展性。
        
        如果你有具体的合约代码或问题，欢迎分享，我可以进一步帮助你进行优化分析。
        
- 内联汇编（Yul）的简单使用
    - Chat-GPT
        
        在 Solidity 中，使用内联汇编（**Yul**）可以进行更细粒度的 Gas 优化。内联汇编允许开发者直接与以太坊虚拟机（EVM）进行交互，跳过 Solidity 编译器的高级抽象，从而能够手动优化某些操作，提高效率并减少 Gas 消耗。
        
        ### 1. **Yul 的基本概念**
        
        **Yul** 是一种中间语言，它不仅用于 EVM 代码优化，还可以通过编写低级的操作代码直接与 EVM 交互。由于 Solidity 编译器会将大部分 Solidity 代码编译为 Yul，因此，手动使用 Yul 可以绕过 Solidity 自带的某些优化限制，控制更精确的操作。
        
        ### 2. **为什么使用内联汇编进行优化？**
        
        - **更高效的存储和内存操作**：Solidity 中的高层次语法可能引入额外的检查或步骤，而这些在低级汇编中可以省略。
        - **精细控制**：可以通过直接使用 EVM 的操作码（Opcodes）来进行手动优化，避免冗余操作。
        - **减少函数调用开销**：直接在内联汇编中执行一些原本由多条 Solidity 语句处理的逻辑，减少了函数调用的额外开销。
        
        ### 3. **Yul 的基本语法**
        
        Yul 的语法相对简单。以下是 Yul 的一些关键概念和语法规则：
        
        - **`let` 变量声明**：使用 `let` 来声明局部变量。`let` 声明的变量只在汇编块中可用。
        - **`mstore`、`mload`**：用于内存的读取和写入。`mstore` 将值存储到内存中，`mload` 从内存中读取。
        - **`sstore`、`sload`**：用于存储的读取和写入。`sstore` 将值存储到存储中，`sload` 从存储中读取。
        - **直接调用 EVM 操作码**：可以直接使用 EVM 操作码，例如 `add`、`mul`、`div` 等。
        
        ### 4. **内联汇编（Yul）进行 Gas 优化的场景和技巧**
        
        ### 4.1 **优化算术操作**
        
        对于简单的算术运算，Solidity 有时会插入一些边界检查（如溢出检查）。在确定不需要这些检查的情况下，使用 Yul 可以直接进行运算操作，节省 Gas。
        
        **示例：优化加法操作**
        
        ```solidity
        pragma solidity ^0.8.0;
        
        contract AssemblyOptimization {
            function add(uint256 a, uint256 b) public pure returns (uint256 result) {
                assembly {
                    result := add(a, b)  // 直接使用 EVM 的 add 操作码
                }
            }
        }
        
        ```
        
        这个例子通过使用 `assembly` 块直接调用 `add` 操作码，避免了 Solidity 中的溢出检查。
        
        ### 4.2 **优化存储读写**
        
        Solidity 中的存储操作（如 `storage` 变量的读写）是非常昂贵的。在内联汇编中，可以通过更直接的方式进行存储的读取和写入操作，减少开销。
        
        **示例：优化存储操作**
        
        ```solidity
        pragma solidity ^0.8.0;
        
        contract StorageOptimization {
            uint256 public storedData;
        
            function setStoredData(uint256 x) public {
                assembly {
                    sstore(storedData.slot, x)  // 直接使用 sstore 存储数据
                }
            }
        
            function getStoredData() public view returns (uint256) {
                assembly {
                    let result := sload(storedData.slot)  // 直接使用 sload 读取存储数据
                    mstore(0x80, result)  // 将结果存入内存
                    return(0x80, 32)  // 返回内存中的值
                }
            }
        }
        
        ```
        
        在这个例子中，使用 `sstore` 和 `sload` 直接与存储进行交互，避免了 Solidity 生成的额外开销。
        
        ### 4.3 **批量操作优化**
        
        当你需要对数组或多个存储位置执行批量操作时，使用 Yul 可以一次性进行多个存储或内存操作，而不是逐一调用 Solidity 的存储函数。
        
        **示例：批量数组操作优化**
        
        ```solidity
        pragma solidity ^0.8.0;
        
        contract BatchOptimization {
            uint256[] public numbers;
        
            function batchUpdate(uint256[] memory newNumbers) public {
                uint256 length = newNumbers.length;
                assembly {
                    let ptr := add(newNumbers, 0x20) // 获取 newNumbers 数组的指针，跳过前 32 字节的长度信息
                    let storagePtr := numbers.slot
        
                    for { let i := 0 } lt(i, length) { i := add(i, 1) } {
                        sstore(add(storagePtr, i), mload(ptr)) // 批量存储数组元素到 storage
                        ptr := add(ptr, 0x20)  // 移动到下一个元素
                    }
                }
            }
        }
        
        ```
        
        这个例子展示了如何通过内联汇编批量写入数组，减少每次单独调用存储操作所带来的开销。
        
        ### 4.4 **减少内存分配的开销**
        
        在 Solidity 中，内存的分配和释放可能带来额外的开销。在 Yul 中，开发者可以通过手动管理内存分配来优化这些操作。
        
        **示例：优化内存分配**
        
        ```solidity
        pragma solidity ^0.8.0;
        
        contract MemoryOptimization {
            function getMemoryValue() public pure returns (uint256) {
                assembly {
                    let freePtr := mload(0x40) // 获取空闲内存指针
                    mstore(freePtr, 42)        // 在该位置存储 42
                    mstore(0x40, add(freePtr, 0x20)) // 更新空闲内存指针位置
                    return(freePtr, 32) // 返回存储的值
                }
            }
        }
        
        ```
        
        通过手动管理内存指针，避免了 Solidity 自动处理内存分配和释放的额外开销。
        
        ### 4.5 **优化函数调用**
        
        Solidity 函数调用引入了额外的开销，特别是在多次调用时。内联汇编可以将某些逻辑直接在汇编块内执行，避免函数调用的开销。
        
        **示例：优化递归调用**
        
        ```solidity
        pragma solidity ^0.8.0;
        
        contract RecursiveSum {
            // 使用内联汇编优化递归函数
            function sum(uint256 n) public pure returns (uint256) {
                assembly {
                    function recursiveSum(x) -> result {
                        switch x
                        case 0 { result := 0 }
                        default { result := add(x, recursiveSum(sub(x, 1))) }
                    }
        
                    let result := recursiveSum(n)
                    mstore(0x80, result)
                    return(0x80, 32)
                }
            }
        }
        
        ```
        
        在这个例子中，通过在汇编块内定义递归函数，避免了 Solidity 中函数调用带来的额外成本。
        
        ### 5. **Yul 使用中的注意事项**
        
        - **安全性**：使用内联汇编时，需要特别注意安全问题。因为 Yul 允许直接与 EVM 进行交互，编写不当的代码可能引发安全漏洞，比如整数溢出、存储篡改等。因此，在编写和测试 Yul 代码时要格外谨慎。
        - **可读性和维护性**：虽然内联汇编可以提供优化，但它的代码可读性较差。建议仅在性能至关重要的地方使用，并在代码中进行充分注释，以便未来的维护和审查。
        - **仅在必要时使用**：Solidity 编译器已经具备许多内置的优化功能，通常情况下，编译器会生成足够高效的代码。因此，内联汇编通常只在特定场景（如批量操作、大量循环或存储访问优化）中使用。
        
        ### 6. **总结**
        
        使用内联汇编（Yul）可以在特定场景下对 Solidity 合约进行显著的 Gas 优化，尤其是在需要直接访问 EVM 操作码、批量处理存储或内存数据时。通过手动控制存储、内存和函数调用，可以绕过 Solidity 编译器生成的冗余代码，从而降低合约的执行成本。
        
        你可以根据具体需求，使用内联汇编来优化合约中的关键逻辑，但需要注意代码的可读性、安全性和可维护性。如果你有具体的优化需求或代码示例，我可以帮助你进一步优化。
        
    - [**Assembly:内联汇编**](https://professional-solidity.readthedocs.io/zh-cn/latest/source/16.assembly.html)
        
        
    - [**Yul**](https://docs.soliditylang.org/zh/latest/yul.html#)