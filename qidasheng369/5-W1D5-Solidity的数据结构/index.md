# 5-W1D5-Solidity的数据结构

**目标：掌握Solidity中的复杂数据结构**

- 数组和映射（`mapping`）
- 结构体（`struct`）和枚举（`enum`）
- 动态与静态数组的使用
- 映射的应用场景与限制

**笔记**

- 数组和映射（`mapping`）
    - [数组Array](https://decert.me/tutorial/solidity/solidity-basic/array)
        
        > 解释：类型后面加上一个`[]`，就构成一个数组类型，表示可以存储一组该类型的值。
        > 
        > 
        > 数组类型是一个[引用类型](https://decert.me/tutorial/solidity/solidity-basic/types)，在申明一个引用类型的变量，需要指定该变量的位置。
        > 
        > 1. **数组属性与方法(成员)**
        >     
        >     数组类型可以通过成员属性内获取数组状态以及可以通过成员函数来修改数组的状态，这些成员有：
        >     
        >     1. `length`属性：表示当前数组的长度（只读属性：不能通过修改 length 属性来更改数组的大小）。 如果是new创建的内存数组，一经创建长度就固定了，不可以修改。
        >     2. `push()`：用来**添加新的零初始化元素**到数组末尾，并返回元素的引用，以便修改元素的内容，如：`x.push().t = 2`或`x.push() = b`，push方法只对存储（storage）中的动态数组有效。
        >     3. `push(x)`：用来添加给定元素到数组末尾。`push(x)` 没有返回值，方法只对存储（`storage`）中的`动态数组有效`。
        >     4. `pop()`：用来从数组末尾删除元素，数组的长度减1，会在移除的元素上隐含调用`delete`，释放存储空间（及时释放不使用的空间，可以节约gas）。pop()没有返回值，`pop()`方法只对存储（`storage`）中的`动态数组有效`。
        > 2. **数组切片**
        >     1. 数组切片仅支持 `calldata` 数组。这意味着不能对 `memory` 或 `storage` 位置的数组进行切片，否则编译会报错。由于 `calldata` 数据是不可更改的，因此您也不能更改数组切片的值。
        >     2. 数组切片的使用方法
        >         1. **基本语法**：表达式 `x[start:end]` 引用 `calldata` 数组 `x` 的一部分，从索引 `start` 开始，到索引 `end` 之前结束。
        >         2. **省略参数**：`start` 和 `end` 都是可选的。如果省略 `start`，则包含从 0 到 `end-1` 的元素；如果省略 `end`，则包含从 `start` 到 `arr.length - 1` 的元素；如果两者都省略，则包含 `arr` 的所有元素。
        >         3. **错误处理**：如果 `start` 大于 `end` 或 `end` 大于数组的长度，则会抛出异常。
        >     3. 示例代码
        >         
        >         ```solidity
        >         // SPDX-License-Identifier: GPL-3.0
        >         pragma solidity >0.8.5 <0.9.0;
        >         contract Example {
        >             function extractFourBytes(string calldata payload) public view returns (string memory) {
        >                 string memory leading4Bytes = string(payload[:4]);
        >                 return leading4Bytes;
        >             }
        >         }
        >         // 在这个例子中，如果输入 "abcdef"，函数将返回 "abcd"。
        >         ```
        >         
        > 3. 代码示例
        >     
        >     ```solidity
        >     // 数组定义
        >     // 数组元素的类型:[<len？>]:数组名称
        >     
        >     contract testArray {
        >         // 状态变量缺省位置为 storage 
        >         uint [10] tens; // 固定长度的数组
        >         uint [] numbers;  // 动态长度的数组
        >         // address [10] admins
        >         // address [] admins
        >         
        >         // 作为参数，使用 calldata 
        >         function copy(uint[] calldata arrs) public {
        >             numbers = arrs;  //  赋值时，不同的数据位置的变量会进行拷贝。 
        >         }
        >         
        >         // 作为参数，使用 memory 
        >         function handle(uint[] memory arrs) internal {
        >         }
        >     }
        >     
        >     contract testArray {
        >         uint [] public u = [1, 2, 3];
        >         string[4] adaArr = ["This", "is", "an", "array"];
        >     }
        >     
        >     contract testArray {
        >         uint[] arr1 = new uint[](1);
        >     
        >         // 函数内
        >         function test(uint len) public {
        >             // 在内存中，
        >             uint[] memory c = new uint[](len);
        >             string[4] memory adaArr = ["This", "is", "an", "array"];
        >         }
        >     
        >     }
        >     
        >     contract testArray {
        >         // storage 位置
        >         uint[] public arr1 = new uint[](1); 
        >         
        >         function add(uint x) public {
        >             arr1.push(x);
        >         }
        >     
        >         function arr1Len() public view  returns (uint len) {
        >             return arr1.length;
        >         }
        >     }
        >     
        >     ```
        >     
        
        其他：
        
        1. [**关注数组 Gas 消耗**](https://decert.me/tutorial/solidity/solidity-basic/array#%E5%85%B3%E6%B3%A8%E6%95%B0%E7%BB%84-gas-%E6%B6%88%E8%80%97)
        2. [如何高效移除数组元素](https://decert.me/tutorial/solidity/solidity-basic/array#%E5%A6%82%E4%BD%95%E9%AB%98%E6%95%88%E7%A7%BB%E9%99%A4%E6%95%B0%E7%BB%84%E5%85%83%E7%B4%A0)
        3. 还有两个特殊的数组类型：[**string** 和 **bytes**](https://decert.me/tutorial/solidity/solidity-basic/array#string-%E5%92%8C-bytes) 。
        
        登链社区的 [Solidity 专栏](https://learnblockchain.cn/column/1) 有更多关于列表、数组的 gas 使用技巧。
        
    - [**映射（mapping）**](https://decert.me/tutorial/solidity/solidity-basic/mapping)
        1. 定义与解释
            1. 映射类型是一种键值对的映射关系存储结构， 在功能上和Java的Map、Python的Dict差不多。
            2. 映射的定义为`mapping(KeyType => ValueType)`， `KeyType` 表示键的类型，`ValueType` 表示值的类型。
            3. 可以通过键来获取到对应的值，例如：`balances[userAddr]` 用来获取某个地址的余额，访问形式很类似于通过下标来获取某个数组元素的值。
        2. **映射特性与限制**
            1. 映射变量只能保存在存储中（`storage`），通常作为状态变量。
            2. 键类型有一些限制，仅支持Solidity内置值类型、`bytes`、`string` 、合约或枚举，不可以是复杂类型， 如：映射、变长数组、结构体。值的类型是没有任何限制，可以为任何类型。
            3. Solidity 里的映射是没有长度的，也没有键集合或值集合的概念，因此是没法对映射进行遍历。
            4. 映射是可以嵌套的， 嵌套映射是指映射的 Value 是另一个映射
        3. 其他
            1. [**可迭代映射**](https://decert.me/tutorial/solidity/solidity-basic/mapping#%E5%8F%AF%E8%BF%AD%E4%BB%A3%E6%98%A0%E5%B0%84)
            2.  [mapping 实现可迭代链表](https://decert.me/tutorial/solidity/solidity-basic/mapping#%E5%8F%AF%E8%BF%AD%E4%BB%A3%E6%98%A0%E5%B0%84)
            3. 多链表实现细节， 例如如何删除、迭代等，可以参考[专栏](https://learnblockchain.cn/column/1) 的[编写 O(1) 复杂度的可迭代映射](https://learnblockchain.cn/article/1632)。
- 结构体（`struct`）和枚举（`enum`）
    - **结构体（`struct`）**
        1. 定义与限制🚫
            1. 定义
                
                使用 `struct` 关键字来定义一个自定义组合类型。
                
            2. 限制
                
                不能在声明一个结构体的同时将自身结构体作为成员
                
                原因是这样的：EVM 会为结构体的成员会分配在一个连续的存储空间，如果结构体包含了自身， EVM 就无法确定存储空间的大小。
                
                ```solidity
                // 错误示例❌
                struct Person {
                    address account;
                    bool gender;
                    uint8 age;
                    Person child;  //  错误
                }
                
                // 合法但不推荐⬇️
                struct Person {
                    address account;
                    bool gender;
                    uint8 age;
                    mapping(string=>Person) childs;  // 或  Person[]  manyChilds; 
                }
                ```
                
        2. **声明与赋值**
            
            show me code
            
            ```solidity
            pragma solidity ^0.8.0;
            
            contract testStruct {
                struct Person {
                    address account;
                    bool gender;
                    uint8 age;
                }
                
                // 声明变量而不初始化
                Person person;   // 默认为storage
                
                // 需要注意参数的类型、顺序的匹配。
                // 只能作为状态变量这样使用
                Person person = Person(address(0x0), false, 18) ;
                // 在函数内声明
                Person memory person = Person(address(0x0), false, 18);
                
                // 使用具名变量初始化
                Person person = Person({account: address(0x0), gender: false, age: 18}) ;
                //在函数内声明
                Person memory person =  Person({account: address(0x0), gender: false, age: 18}) ;
                
                // 在函数内
                function updatePersion() public {
                    person.account = msg.sender;
                    person.gender = true;
                    person.age = 12;
                }
            }
            ```
            
    - [**枚举类型](https://docs.soliditylang.org/zh/latest/structure-of-a-contract.html#structure-enum-types)（`enum`）**
        
        > 详细用法：[**Enum:枚举**](https://professional-solidity.readthedocs.io/zh-cn/latest/source/02.type-of-data.html#enum)
        > 
        
        枚举可用来创建由一定数量的'常量值'构成的自定义类型（参阅类型章节中的 [枚举类型](https://docs.soliditylang.org/zh/latest/types.html#enums)）。
        
        1. 枚举是在 Solidity 中创建用户定义类型的一种方式。
        2. 它们可以显式地转换为所有整数类型，和从整数类型来转换，但不允许隐式转换。 
        3. 从整数的显式转换在运行时检查该值是否在枚举的范围内，否则会导致 [异常](https://docs.soliditylang.org/zh/latest/control-structures.html#assert-and-require)。 
        4. 枚举要求至少有一个成员，其声明时的默认值是第一个成员。 枚举不能有超过256个成员。
        
        [open in Remix](https://remix.ethereum.org/?#language=solidity&version=0.8.22&code=Ly8gU1BEWC1MaWNlbnNlLUlkZW50aWZpZXI6IEdQTC0zLjAKcHJhZ21hIHNvbGlkaXR5ID49MC40LjAgPDAuOS4wOwoKY29udHJhY3QgUHVyY2hhc2UgewogICAgZW51bSBTdGF0ZSB7IENyZWF0ZWQsIExvY2tlZCwgSW5hY3RpdmUgfSAvLyDmnprkuL4KfQ==)
        
        ```solidity
        // SPDX-License-Identifier: GPL-3.0
        pragma solidity >=0.4.0 <0.9.0;
        
        contract Purchase {
            enum State { Created, Locked, Inactive } // 枚举
        }
        ```
        
        数据表示与 C 语言中的枚举相同。选项由后续的从 `0` 开始无符号整数值表示。
        
        使用 `type(NameOfEnum).min` 和 `type(NameOfEnum).max` 您可以得到给定枚举的最小值和最大值。
        
        [open in Remix](https://remix.ethereum.org/?#language=solidity&version=0.8.22&code=Ly8gU1BEWC1MaWNlbnNlLUlkZW50aWZpZXI6IEdQTC0zLjAKcHJhZ21hIHNvbGlkaXR5IF4wLjguODsKCmNvbnRyYWN0IHRlc3QgewogICAgZW51bSBBY3Rpb25DaG9pY2VzIHsgR29MZWZ0LCBHb1JpZ2h0LCBHb1N0cmFpZ2h0LCBTaXRTdGlsbCB9CiAgICBBY3Rpb25DaG9pY2VzIGNob2ljZTsKICAgIEFjdGlvbkNob2ljZXMgY29uc3RhbnQgZGVmYXVsdENob2ljZSA9IEFjdGlvbkNob2ljZXMuR29TdHJhaWdodDsKCiAgICBmdW5jdGlvbiBzZXRHb1N0cmFpZ2h0KCkgcHVibGljIHsKICAgICAgICBjaG9pY2UgPSBBY3Rpb25DaG9pY2VzLkdvU3RyYWlnaHQ7CiAgICB9CgogICAgLy8g55Sx5LqO5p6a5Li+57G75Z6L5LiN5bGe5LqOQUJJ55qE5LiA6YOo5YiG77yM5Zug5q2k5a+55LqO5omA5pyJ5p2l6IeqIFNvbGlkaXR5IOWklumDqOeahOiwg+eUqO+8jAogICAgLy8gImdldENob2ljZSIg55qE562+5ZCN5Lya6Ieq5Yqo6KKr5pS55oiQICJnZXRDaG9pY2UoKSByZXR1cm5zICh1aW50OCki44CCCiAgICBmdW5jdGlvbiBnZXRDaG9pY2UoKSBwdWJsaWMgdmlldyByZXR1cm5zIChBY3Rpb25DaG9pY2VzKSB7CiAgICAgICAgcmV0dXJuIGNob2ljZTsKICAgIH0KCiAgICBmdW5jdGlvbiBnZXREZWZhdWx0Q2hvaWNlKCkgcHVibGljIHB1cmUgcmV0dXJucyAodWludCkgewogICAgICAgIHJldHVybiB1aW50KGRlZmF1bHRDaG9pY2UpOwogICAgfQoKICAgIGZ1bmN0aW9uIGdldExhcmdlc3RWYWx1ZSgpIHB1YmxpYyBwdXJlIHJldHVybnMgKEFjdGlvbkNob2ljZXMpIHsKICAgICAgICByZXR1cm4gdHlwZShBY3Rpb25DaG9pY2VzKS5tYXg7CiAgICB9CgogICAgZnVuY3Rpb24gZ2V0U21hbGxlc3RWYWx1ZSgpIHB1YmxpYyBwdXJlIHJldHVybnMgKEFjdGlvbkNob2ljZXMpIHsKICAgICAgICByZXR1cm4gdHlwZShBY3Rpb25DaG9pY2VzKS5taW47CiAgICB9Cn0=)
        
        ```solidity
        // SPDX-License-Identifier: GPL-3.0
        pragma solidity ^0.8.8;
        
        contract test {
            enum ActionChoices { GoLeft, GoRight, GoStraight, SitStill }
            ActionChoices choice;
            ActionChoices constant defaultChoice = ActionChoices.GoStraight;
        
            function setGoStraight() public {
                choice = ActionChoices.GoStraight;
            }
        
            // 由于枚举类型不属于ABI的一部分，因此对于所有来自 Solidity 外部的调用，
            // "getChoice" 的签名会自动被改成 "getChoice() returns (uint8)"。
            function getChoice() public view returns (ActionChoices) {
                return choice;
            }
        
            function getDefaultChoice() public pure returns (uint) {
                return uint(defaultChoice);
            }
        
            function getLargestValue() public pure returns (ActionChoices) {
                return type(ActionChoices).max;
            }
        
            function getSmallestValue() public pure returns (ActionChoices) {
                return type(ActionChoices).min;
            }
        }
        ```
        
- 动态与静态数组的使用
    - **静态数组**
        
        静态数组在编译时其大小是固定的，这意味着一旦定义，其大小不能改变。
        
        ### 特点：
        
        - 大小在编译时已知。
        - 通常用于存储固定数量的元素。
    - **动态数组**
        
        动态数组的大小在运行时可以改变，它们比静态数组更灵活，但使用起来也更昂贵。
        
        **特点**：
        
        - 大小在运行时可以改变。
        - 通常用于存储未知数量的元素。
    - **总结**
        - **静态数组**：当您知道元素的确切数量时使用，更节省gas。
        - **动态数组**：当元素数量可能变化时使用，但操作成本更高。
    - **代码**
        
        ```solidity
        pragma solidity ^0.8.0;
        
        contract ArrayExample {
            // 声明一个静态数组，存储5个uint类型的元素
            uint[5] public staticArray;
            
            // 声明一个动态数组，存储uint类型的元素
            uint[] public dynamicArray;
        
            constructor() {
                // 初始化静态数组
                staticArray = [1, 2, 3, 4, 5];
            }
        
            // 更新静态数组中的元素
            function updateStaticArray(uint index, uint value) public {
                require(index < staticArray.length, "Index out of bounds");
                staticArray[index] = value;
            }
        
            // 获取静态数组中的元素
            function getStaticArrayElement(uint index) public view returns (uint) {
                require(index < staticArray.length, "Index out of bounds");
                return staticArray[index];
            }
            
            // ======================================================
            
            // 向动态数组添加元素
            function pushToDynamicArray(uint value) public {
                dynamicArray.push(value);
            }
        
            // 从动态数组中删除元素
            function popFromDynamicArray() public {
                require(dynamicArray.length > 0, "Array is empty");
                dynamicArray.pop();
            }
        
            // 获取动态数组的长度
            function getDynamicArrayLength() public view returns (uint) {
                return dynamicArray.length;
            }
        
            // 通过索引获取动态数组中的元素
            function getDynamicArrayElement(uint index) public view returns (uint) {
                require(index < dynamicArray.length, "Index out of bounds");
                return dynamicArray[index];
            }
            
            // Member "push" is not available in uint256[] memory outside of storage.
            function createMemoryArray() public pure returns (uint[] memory) {
                // 在memory中创建一个动态数组，并分配空间
                uint[] memory numbers = new uint[](5);
                
                // 初始化数组
                numbers[0] = 1;
                numbers[1] = 2;
                numbers[2] = 3;
                numbers[3] = 4;
                numbers[4] = 5;
                
                // 返回数组
                return numbers;
            }
        }
        
        ```
        
- 映射的应用场景与限制
    
    Solidity中的映射（`Mapping`）是一种特殊的键值对数据结构，它提供了非常方便的方式来存储和检索数据。以下是映射的应用场景和限制：
    
    - `Mapping`应用场景：
        1. **账户余额**：映射常用于跟踪智能合约中不同账户的余额。
        2. **权限管理**：映射可以用来管理账户权限，例如哪些地址可以执行特定的操作。
        3. **唯一性检查**：映射可以用来检查某个键是否已经存在，从而保证数据的唯一性。
        4. **数据索引**：映射可以用来作为复杂数据结构的索引，例如一个用户可以拥有多个物品，每个物品可以通过映射来索引。
        5. **投票系统**：在投票系统中，映射可以用来记录每个地址的投票状态。
        6. **白名单/黑名单**：映射可以用来创建白名单或黑名单，控制哪些地址可以或不可以进行某些操作。
        
        **show me code**
        
        ```solidity
        mapping(address => uint256) public balances; // **账户余额**
        mapping(address => bool) public isAdmin; // **权限管理**
        mapping(bytes32 => bool) public isUnique; // **唯一性检查**
        mapping(address => uint256[]) public ownedItems; // **数据索引**
        mapping(address => bool) public hasVoted; // **投票系统**
        mapping(address => bool) public isWhitelisted; // **白名单/黑名单**
        ```
        
    - `Mapping`的限制：
        1. **不可遍历**：映射没有长度，也不能被遍历。这意味着你不能直接获取映射中所有键或值的列表。
        2. **仅支持简单类型**：映射的键可以是任何除了映射、动态数组、合约、枚举和结构体之外的类型。值可以是任何类型，包括映射。
        3. **存储成本**：虽然映射在存储方面非常高效，但每个键都会在区块链上占用存储空间，这可能导致较高的存储成本。
        4. **数据删除**：Solidity没有提供直接删除映射中条目的方法。通常的做法是设置一个特殊的值来标记删除，例如使用 `delete` 操作符来重置键对应的值。
        5. **初始化**：映射在声明时不需要初始化，所有键的初始值都是默认值。例如，对于数值类型，默认值是0；对于布尔类型，默认值是false。
        6. **可见性**：映射可以是公共的、私有的或内部的，但不能是外部的。
        映射是Solidity中非常有用的数据结构，但设计合约时需要考虑到这些限制。正确使用映射可以提高智能合约的效率，但也需要谨慎处理其不可遍历和存储成本等特性。