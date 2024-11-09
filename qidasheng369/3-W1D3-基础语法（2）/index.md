# 3-W1D3-基础语法（2）

**目标：掌握Solidity的基本结构与控制流**

- 函数定义与修饰符
- 条件语句（if-else）和循环（for, while）
- 函数的可见性（public, private, internal, external）
- 函数的返回值

**笔记**

- 函数定义与修饰符
    - **定义函数[​](https://decert.me/tutorial/solidity/solidity-basic/solidity_layout#%E5%AE%9A%E4%B9%89%E5%87%BD%E6%95%B0)**
        
        合约由**状态变量（合约数据）**和**合约函数**组成，刚才介绍了定义变量，现在来看看定义函数：
        
        ```solidity
          function count() public {
              counter = counter + 1;
          }
        ```
        
        使用 `function` 关键字定义函数，这行代码声明了一个名为 `count()` 函数，`public` 表示这个函数可以被公开访问。
        
        `count()` 函数的作用是对`counter`状态变量加 1 ，因此调用这个函数会修改区块链状态，这时我们就需要[通过一个交易来调用该函数](https://decert.me/tutorial/solidity/tools/remix#%E8%B0%83%E7%94%A8%E5%90%88%E7%BA%A6%E5%87%BD%E6%95%B0)，调用者为交易提供 Gas，验证者（矿工）收取 Gas 打包交易，经过区块链共识后，`counter`变量才真正算完成加1 。
        
        这里的 `count()` 函数非常简单，我们还可以根据需要定义函数的参数与返回值以及指定该函数是否要修改状态，一个函数定义形式可以这样表示：
        
        ```solidity
        function 函数名(<参数类型> <参数名>) <可见性> <状态可变性> [returns(<返回类型>)]{
        
        }
        ```
        
        - 函数名
            
            在Solidity中，函数命名遵循以下规范：
            
            1. **小写字母开头**：函数名通常以小写字母开头。
            2. **使用驼峰命名法**：函数名应该使用驼峰命名法（CamelCase），即第一个单词的首字母小写，后续单词的首字母大写。
            例如：`getBalance`, `updateValue`, `transferFunds`。
            3. **简洁明了**：函数名应该简洁并且能够清楚地描述函数的功能。
            4. **避免使用下划线**：在Solidity中，通常不推荐使用下划线作为函数名的第一个字符，因为这通常用于特殊的内部变量或函数。
            5. **避免使用保留字**：不要使用Solidity中的保留字或关键字作为函数名。
            以下是一些遵循上述规范的函数名示例：
            
            ```solidity
            function getBalance() public view returns (uint256) {
                // ...
            }
            function updateBalance(address _user, uint256 _amount) public {
                // ...
            }
            function transferFunds(address payable _recipient, uint256 _amount) public {
                // ...
            }
            
            ```
            
            请注意，虽然Solidity不强制要求特定的命名规范，但遵循上述规则可以使智能合约的代码更加清晰和易于理解，这也是Solidity社区普遍采用的实践。
            
        - **函数参数**
            - 可以是任何基本类型，如 `uint`, `int`, `bool`, `address` 等。
            - 也可以是复杂的类型，如 `struct`, `mapping`, `array` 等。
            - 如果函数不需要**参数**，可以省略。
        - 函数可见性
            - `public`: 函数可以从合约外部和内部调用。
            - `private`: 函数只能从定义它的合约内部调用。
            - `external`: 函数只能从合约外部调用。
            - `internal`: 函数只能从合约内部或通过继承的合约调用。
        - 状态可变性
            
            ### **状态可变性（mutability）[​](https://decert.me/tutorial/solidity/solidity-basic/solidity_layout#%E7%8A%B6%E6%80%81%E5%8F%AF%E5%8F%98%E6%80%A7mutability)**
            
            有些函数还还会有一个关键字来描述该函数，会怎样修改区块链状态，形容函数的可变性有 3 个关键字：
            
            - `view`：用 `view` 修饰的函数，称为视图函数，它只能读取状态，而不能修改状态。
            - `pure`：用 `pure` 修饰的函数，称为纯函数，它既不能读取也不能修改状态。纯函数仅做计算
            - `payable`：用 `payable` 修饰的函数表示可以[接受以太币](https://decert.me/tutorial/solidity/solidity-basic/receive)，如果未指定，该函数将自动拒绝所有发送给它的以太币。
            
            > `view` , `pure` , `payable` 通常被称为**修饰符**
            > 
            
            如果函数是 `view` 或 `pure`，则它不能修改状态。
            
            - `view` 函数承诺不修改状态。
            - `pure` 函数承诺不读取或修改状态。
            - `view` 与 `pure`的区别
                
                在Solidity中，`view` 和 `pure` 是两种特殊的函数修饰符，它们用来声明函数不会修改合约的状态。以下是关于这两种修饰符的详细说明：
                
                ### `view` 函数
                
                - `view` 函数声明表示该函数不会修改区块链的状态。
                - 它可以读取合约的状态变量，但不能修改它们。
                - 调用 `view` 函数不会消耗任何gas（在以太坊主网上），因为它们不会改变任何状态。
                - 如果一个函数只读取状态变量，而不进行任何写操作，它应该被声明为 `view`。
                
                ```solidity
                pragma solidity ^0.8.0;
                contract ViewExample {
                    uint public x;
                    function getX() public view returns (uint) {
                        return x;
                    }
                }
                
                ```
                
                在上面的例子中，`getX` 函数是一个 `view` 函数，因为它只是读取状态变量 `x`。
                
                ### `pure` 函数
                
                - `pure` 函数声明表示该函数既不会读取也不会修改区块链的状态。
                - `pure` 函数不能访问或修改任何状态变量。
                - 调用 `pure` 函数不会消耗任何gas，因为它们完全独立于区块链的状态。
                - 如果一个函数既不读取也不写入状态变量，它应该被声明为 `pure`。
                
                ```solidity
                pragma solidity ^0.8.0;
                contract PureExample {
                    function add(uint a, uint b) public pure returns (uint) {
                        return a + b;
                    }
                }
                
                ```
                
                在上面的例子中，`add` 函数是一个 `pure` 函数，因为它不读取也不写入任何状态变量，只是执行计算。
                
                ### 注意事项
                
                - 在Solidity 0.5.0之前，`view` 和 `pure` 的概念是通过 `constant` 和 `pure` 关键字来表达的，但从Solidity 0.5.0开始，`constant` 关键字被废弃，并改为使用 `view`。
                - 使用 `view` 和 `pure` 修饰符可以帮助其他开发者理解函数的意图，同时也有助于编译器进行优化。
                - 不遵循这些规则可能会导致不可预见的行为，例如在声明为 `view` 或 `pure` 的函数中修改状态可能会使合约无法正常工作。
        - 返回类型
            - 可以是任何基本类型，如 `uint`, `int`, `bool`, `address` 等。
            - 也可以是复杂的类型，如 `struct`, `mapping`, `array` 等。
            - 如果函数不需要返回值，可以省略返回类型或使用 `void`（从Solidity 0.4.16开始，`void` 是可选的）。
        - 示例代码
            
            ```solidity
            // 示例1
            pragma solidity ^0.8.0;
            
            contract InternalFunction {
                // 定义一个内部函数，只能内部或通过继承调用
                function internalFunc() internal pure returns (uint) {
                    return 100;
                }
                
                // 另一个内部函数调用上面的内部函数
                function callInternalFunc() internal view returns (uint) {
                    return internalFunc();
                }
            }
            
            // 注意
            // view仅用来查询合约状态，不消耗gas
            // pure仅用来计算，也不消耗gas
            
            // 示例2
            // SPDX-License-Identifier: MIT
            pragma solidity ^0.8.0;
            
            contract ExampleContract {
                // 状态变量
                uint public balance;
                mapping(address => uint) public balances;
            
                // 构造函数
                constructor() {
                    balance = 0;
                }
            
                // payable 函数，可以接收以太币
                function deposit() public payable {
                    require(msg.value > 0, "必须发送一些以太币");
                    balances[msg.sender] += msg.value;
                    balance += msg.value;
                }
            
                // view 函数，承诺不修改状态
                function viewBalance() public view returns (uint) {
                    return balances[msg.sender];
                }
            
                // pure 函数，承诺不读取也不修改状态
                function calculate(uint a, uint b) public pure returns (uint) {
                    return a + b;
                }
            
                // 允许用户提取他们的以太币
                function withdraw(uint amount) public {
                    require(amount <= balances[msg.sender], "提取金额超过余额");
                    balances[msg.sender] -= amount;
                    balance -= amount;
                    payable(msg.sender).transfer(amount);
                }
            }
            ```
            
    - **修饰符**
        
        ```solidity
        function 函数名(<参数类型> <参数名>) <可见性> <状态可变性> [returns(<返回类型>)]{
        
        }
        ```
        
        ### **状态可变性（mutability）[​](https://decert.me/tutorial/solidity/solidity-basic/solidity_layout#%E7%8A%B6%E6%80%81%E5%8F%AF%E5%8F%98%E6%80%A7mutability)**
        
        有些函数还还会有一个关键字来描述该函数，会怎样修改区块链状态，形容函数的可变性有 3 个关键字：
        
        - `view`：用 `view` 修饰的函数，称为视图函数，它只能读取状态，而不能修改状态。
        - `pure`：用 `pure` 修饰的函数，称为纯函数，它既不能读取也不能修改状态。
        - `payable`：用 `payable` 修饰的函数表示可以[接受以太币](https://decert.me/tutorial/solidity/solidity-basic/receive)，如果未指定，该函数将自动拒绝所有发送给它的以太币。
        
        > `view` , `pure` , `payable` 通常被称为修饰符
        > 
        
        关于 `payable` 函数的几个关键点：
        
        - 当一个地址没有声明为 `payable` 时，尝试向其发送以太币会导致交易失败并抛出异常。
        - `msg.value` 是一个全局变量，表示当前函数调用发送的以太币数量（以wei为单位）。
        - `address(this).balance` 用来获取合约当前余额。
        - 使用 `transfer` 函数可以将合约中的以太币发送到另一个地址。`transfer` 是一种安全的方式来发送以太币，因为它限制了转账中可用的gas（2300 gas），足够完成转账但不足以执行复杂的操作。
        
        ```solidity
        // 示例2
        // SPDX-License-Identifier: MIT
        pragma solidity ^0.8.0;
        
        contract ExampleContract {
            // 状态变量
            uint public balance;
            mapping(address => uint) public balances;
        
            // 构造函数
            constructor() {
                balance = 0;
            }
        
            // payable 函数，可以接收以太币
            function deposit() public payable {
                require(msg.value > 0, "必须发送一些以太币");
                balances[msg.sender] += msg.value;
                balance += msg.value;
            }
        
            // view 函数，承诺不修改状态
            function viewBalance() public view returns (uint) {
                return balances[msg.sender];
            }
        
            // pure 函数，承诺不读取也不修改状态
            function calculate(uint a, uint b) public pure returns (uint) {
                return a + b;
            }
        
            // 允许用户提取他们的以太币
            function withdraw(uint amount) public {
                require(amount <= balances[msg.sender], "提取金额超过余额");
                balances[msg.sender] -= amount;
                balance -= amount;
                payable(msg.sender).transfer(amount);
            }
        }
        ```
        
    - **函数修饰器**
        
        函数修饰器可以被用来以声明的方式修改函数的语义(见合约部分的 [函数修饰器](https://docs.soliditylang.org/zh/latest/contracts.html#modifiers))。
        
        重载，也就是具有同一个修饰器的名字但有不同的参数，是不可能的。
        
        与函数一样，修饰器也可以被 [重载](https://docs.soliditylang.org/zh/latest/contracts.html#modifier-overriding)。
        
        [open in Remix](https://remix.ethereum.org/?#language=solidity&version=0.8.22&code=Ly8gU1BEWC1MaWNlbnNlLUlkZW50aWZpZXI6IEdQTC0zLjAKcHJhZ21hIHNvbGlkaXR5ID49MC40LjIyIDwwLjkuMDsKCmNvbnRyYWN0IFB1cmNoYXNlIHsKICAgIGFkZHJlc3MgcHVibGljIHNlbGxlcjsKCiAgICBtb2RpZmllciBvbmx5U2VsbGVyKCkgeyAvLyDkv67ppbDlmagKICAgICAgICByZXF1aXJlKAogICAgICAgICAgICBtc2cuc2VuZGVyID09IHNlbGxlciwKICAgICAgICAgICAgIk9ubHkgc2VsbGVyIGNhbiBjYWxsIHRoaXMuIgogICAgICAgICk7CiAgICAgICAgXzsKICAgIH0KCiAgICBmdW5jdGlvbiBhYm9ydCgpIHB1YmxpYyB2aWV3IG9ubHlTZWxsZXIgeyAvLyDkv67ppbDlmajnmoTkvb/nlKgKICAgICAgICAvLyAuLi4KICAgIH0KfQ==)
        
        ```solidity
        // SPDX-License-Identifier: GPL-3.0
        pragma solidity >=0.4.22 <0.9.0;
        
        contract Purchase {
            address public seller;
        
            modifier onlySeller() { // 修饰器
                require(
                    msg.sender == seller,
                    "Only seller can call this."
                );
                _;
            }
        
            function abort() public view onlySeller { // 修饰器的使用
                // ...
            }
        }
        ```
        
        [**函数修饰器**](https://docs.soliditylang.org/zh/latest/contracts.html#modifiers)可以用来以声明的方式改变函数的行为。 例如，您可以使用修饰器在执行函数之前自动检查一个条件。
        
        修饰器是合约的可继承属性，可以被派生合约重载， 但只有当它们被标记为 `virtual` 时，才能被重载。 详情请见 [修饰器重载](https://docs.soliditylang.org/zh/latest/contracts.html#modifier-overriding)。
        
        ```solidity
        // SPDX-License-Identifier: GPL-3.0
        pragma solidity >=0.7.1 <0.9.0;
        // 这将报告一个由于废弃的 selfdestruct 而产生的警告
        
        contract owned {
            constructor() { owner = payable(msg.sender); }
            address payable owner;
        
            // 这个合约只定义了一个修饰器，但没有使用它：
            // 它将在派生合约中使用。
            // 修饰器所修饰的函数体会被插入到特殊符号 `_;` 的位置。
            // 这意味着，如果所有者调用这个函数，这个函数就会被执行，
            // 否则就会抛出一个异常。
            modifier onlyOwner {
                require(
                    msg.sender == owner,
                    "Only owner can call this function."
                );
                _;
            }
        }
        
        contract destructible is owned {
            // 这个合约从 `owned` 合约继承了 `onlyOwner` 修饰器，
            // 并将其应用于 `destroy` 函数，
            // 只有在合约里保存的 owner 调用 `destroy` 函数，才会生效。
            function destroy() public onlyOwner {
                selfdestruct(owner);
            }
        }
        
        contract priced {
            // 修饰器可以接受参数：
            modifier costs(uint price) {
                if (msg.value >= price) {
                    _;
                }
            }
        }
        
        contract Register is priced, destructible {
            mapping(address => bool) registeredAddresses;
            uint price;
        
            constructor(uint initialPrice) { price = initialPrice; }
        
            // 在这里也使用关键字 `payable` 非常重要，
            // 否则函数会自动拒绝所有发送给它的以太币。
            function register() public payable costs(price) {
                registeredAddresses[msg.sender] = true;
            }
        
            function changePrice(uint price_) public onlyOwner {
                price = price_;
            }
        }
        
        contract Mutex {
            bool locked;
            modifier noReentrancy() {
                require(
                    !locked,
                    "Reentrant call."
                );
                locked = true;
                _;
                locked = false;
            }
        
            /// 这个函数受互斥量保护，这意味着 `msg.sender.call` 中的重入调用不能再次调用  `f`。
            /// `return 7` 语句指定返回值为 7，但修饰器中的语句 `locked = false` 仍会执行。
            function f() public noReentrancy returns (uint) {
                (bool success,) = msg.sender.call("");
                require(success);
                return 7;
            }
        }
        ```
        
- 条件语句（if-else）和循环（for, while）
    - 条件语句（if-else）
        
        大多数从大括号语言中知道的控制结构都可以在Solidity中使用：
        有： `if， else， while， do， for， break， continue， return`， 这些在 C 或者 JavaScript 中表达相同语义的关键词。
        
        ```solidity
        if (表达式) {
           被执行语句(如果表达式为真)
        }
        
        // SPDX-License-Identifier: MIT
        pragma solidity ^0.8.13;
        
        contract Loop {
            function loop() public {
                // 遍历
                for (uint256 i = 0; i < 10; i++) {
                    if (i == 3) {
                        
                        // 忽略当前遍历，继续下一个遍历操作
                        continue;
                    }
                    if (i == 5) {
                        // break 退出遍历
                        break;
                    }
                }
        
                // while 遍历语法
                uint256 j;
                while (j < 10) {
                    j++;
                }
            }
        }
        ```
        
    - 循环（for, while）
        - for
            
            ```solidity
            for (初始化; 测试条件; 迭代语句) {
               被执行语句(如果表达式为真)
            }
            
            pragma solidity ^0.5.0;
            
            contract SolidityTest {
               uint storedData; 
               constructor() public{
                  storedData = 10;   
               }
            
               function getResult() public view returns(string memory){
                  uint a = 10; 
                  uint b = 2;
                  uint result = a + b;
                  return integerToString(result); 
               }
            
               function integerToString(uint _i) internal pure returns (string memory) {
                  if (_i == 0) {
                     return "0";
                  }
                  uint j=0;
                  uint len;
                  for (j = _i; j != 0; j /= 10) {  //for循环的例子
                     len++;         
                  }
                  bytes memory bstr = new bytes(len);
                  uint k = len - 1;
                  while (_i != 0) {
                     bstr[k--] = byte(uint8(48 + _i % 10));
                     _i /= 10;
                  }
                  return string(bstr);//访问局部变量
               }
            }
            ```
            
        - while
            
            ```solidity
            while (表达式) {
               被执行语句(如果表达式为真)
            }
            
            pragma solidity ^0.5.0;
            
            contract SolidityTest {
               uint storedData; 
               constructor() public{
                  storedData = 10;   
               }
               function getResult() public view returns(string memory){
                  uint a = 10; 
                  uint b = 2;
                  uint result = a + b;
                  return integerToString(result); 
               }
               
               function integerToString(uint _i) internal pure returns (string memory) {
            
                  if (_i == 0) {
                     return "0";
                  }
                  uint j = _i;
                  uint len;
            
                  while (j != 0) {
                     len++;
                     j /= 10;
                  }
                  bytes memory bstr = new bytes(len);
                  uint k = len - 1;
            
                  while (_i != 0) { // while 循环
                     bstr[k--] = byte(uint8(48 + _i % 10));
                     _i /= 10;
                  }
                  return string(bstr);
               }
            }
            ```
            
- 函数的可见性（public, private, internal, external）
    - 函数可见性
        - `public`: 函数可以从合约外部和内部调用。
        - `private`: 函数只能从定义它的合约内部调用。
        - `external`: 函数只能从合约外部调用。
        - `internal`: 函数只能从合约内部或通过继承的合约调用。
    - 函数可见性详细介绍
        
        在Solidity中，函数的可见性决定了函数可以在智能合约的哪个部分被调用。
        
        Solidity提供了四种可见性修饰符：`public`、`private`、`internal`和`external`。
        
        ### 1. `public`
        
        - **解释**：`public`修饰符使得函数可以被合约内部以及外部调用。
        - **使用场景**：当你想要合约的任何用户都可以调用某个函数时，可以使用`public`。
        - **代码示例**：
        
        ```solidity
        pragma solidity ^0.8.0;
        contract MyContract {
            uint256 private data;
            function setData(uint256 _data) public {
                data = _data;
            }
            function getData() public view returns (uint256) {
                return data;
            }
        }
        
        ```
        
        在上面的例子中，`setData`和`getData`函数都是`public`的，可以被合约外部调用。
        
        ### 2. `private`
        
        - **解释**：`private`修饰符使得函数只能在定义它们的合约内部调用。
        - **使用场景**：当你想要限制函数只在合约内部使用，不希望外部能够调用时，可以使用`private`。
        - **代码示例**：
        
        ```solidity
        pragma solidity ^0.8.0;
        contract MyContract {
            uint256 private data;
            function setData(uint256 _data) private {
                data = _data;
            }
            function internalSetData(uint256 _data) public {
                setData(_data);
            }
        }
        
        ```
        
        在上面的例子中，`setData`是`private`的，只能被合约内部的`internalSetData`函数调用。
        
        ### 3. `internal`
        
        - **解释**：`internal`修饰符使得函数只能在合约内部以及继承该合约的子合约内部调用。
        - **使用场景**：当你想要创建可以被继承的函数，但又不希望它们被外部直接调用时，可以使用`internal`。
        - **代码示例**：
        
        ```solidity
        pragma solidity ^0.8.0;
        contract BaseContract {
            function internalFunc() internal view returns (string memory) {
                return "internal function called";
            }
        }
        contract ChildContract is BaseContract {
            function callInternalFunc() public view returns (string memory) {
                return internalFunc();
            }
        }
        
        ```
        
        在上面的例子中，`internalFunc`是`internal`的，可以在`ChildContract`中调用。
        
        ### 4. `external`
        
        - **解释**：`external`修饰符使得函数只能从合约外部调用，合约内部无法直接调用，但可以使用`this.`来调用。
        - **使用场景**：当你想要减少合约的内部调用开销时，可以使用`external`，因为它比`public`调用开销更小。
        - **代码示例**：
        
        ```solidity
        pragma solidity ^0.8.0;
        contract MyContract {
            function externalFunc() external pure returns (bool) {
                return true;
            }
        }
        contract Caller {
            MyContract myContract;
            constructor(address _contractAddress) {
                myContract = MyContract(_contractAddress);
            }
            function callExternalFunc() public view returns (bool) {
                return myContract.externalFunc();
            }
        }
        
        ```
        
        在上面的例子中，`externalFunc`是`external`的，只能从外部调用，例如通过`Caller`合约的`callExternalFunc`函数。
        如果没有指定可见性修饰符，默认情况下函数的可见性是`public`。对于状态变量，默认的可见性是`internal`。
        
- 函数的返回值
    
    以下函数定义了返回值：
    
    ```solidity
      function addAB(uint a, uint b) public returns (uint result) {
          counter = counter + a + b;
          result = counter;// return counter;
      }
    
    // SPDX-License-Identifier: GPL-3.0
    pragma solidity >=0.4.16 <0.9.0;
    
    contract Simple {
        function arithmetic(uint a, uint b)
            public
            pure
            returns (uint sum, uint product)
        {
            sum = a + b;
            product = a * b;
        }
    }
    ```
    
    其实在Solidity 中，返回值与参数的处理方式是一样的，代码中 返回值 `result` 也称为输出参数，我们可以在函数体里直接为它赋值，或直接在 `return` 语句中提供返回值。
    
    返回值可以仅指定其类型，省略名称，例如：
    
    ```solidity
    function addAB(uint a, uint b) public returns (uint) {
          ....
        return counter + a + b;
    }
    
    // SPDX-License-Identifier: GPL-3.0
    pragma solidity >=0.4.16 <0.9.0;
    
    contract Simple {
        function arithmetic(uint a, uint b)
            public
            pure
            returns (uint sum, uint product)
        {
            return (a + b, a * b);
        }
    }
    // 如果您过早使用 return 来结束一个有返回变量的函数，您必须在返回语句中同时提供返回值。
    ```
    
    Solidity 支持函数有多个返回值，例如：
    
    ```solidity
    pragma solidity >0.5.0;
    
    contract C {
    		function f() public pure returns (uint, bool, uint) {
    				return (7, true, 2);
    		}
    		function g() public {
    				// 获取返回值
    				(uint x, bool b, uint y) = f();
    		}
    }
    
    // 返回多个值
    // 当一个函数有多个返回类型时，语句 return (v0, v1, ..., vn) 可以用来返回多个值。 
    // 声明的数量必须与返回变量的数量相同，并且它们的类型必须匹配， 有可能是经过 隐式转换。
    ```
    
    解构返回值
    
    ```solidity
    // 命名式返回
    function returnNamed() public pure returns(uint256 _number, bool _bool, uint256[3] memory _array){
    		_number = 2;
    		_bool = false; 
    		_array = [uint256(3),2,1];
    }
    
    // 命名式返回，依然支持return
    function returnNamed2() public pure returns(uint256 _number, bool _bool, uint256[3] memory _array){
    	 return(1, true, [uint256(1),2,5]);
    }
    
    ```
    
    解构式赋值
    solidity使用解构式赋值的规则，支持读取函数的全部或部分返回值。
    
    读取所有返回值：**声明变量**，并且将要赋值的变量用`,`隔开，按顺序排列。
    
    ```solidity
    
    uint256 _number;
    bool _bool;
    uint256[3] memory _array;
    (_number, _bool, _array) = returnNamed();
    ```
    
    **读取部分返回值：声明要读取的返回值对应的变量，不读取的留空**。
    
    下面这段代码中，我们只读取`_bool`，而不读取返回的`_number`和`_array`：
    
    ```solidity
    bool _bool;
    (, _bool2, ) = returnNamed();
    ```