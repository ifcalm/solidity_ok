# 4-W1D4-函数与修饰符

**目标：深入理解函数和访问控制**

- 函数修饰符（pure, view, payable）
- 函数修饰符的应用（modifier）
- 错误处理（require, assert, revert）

**笔记**

- 函数修饰符（`pure`, `view`, `payable`）
    
    一个函数定义形式可以这样表示：
    
    ```solidity
    function 函数名(<参数类型> <参数名>) <可见性> <状态可变性> <>[returns(<返回类型>)]{
    
    }
    ```
    
    ### **状态可变性（`mutability`）[](https://decert.me/tutorial/solidity/solidity-basic/solidity_layout#%E7%8A%B6%E6%80%81%E5%8F%AF%E5%8F%98%E6%80%A7mutability)**
    
    有些函数还还会有一个关键字来描述该函数，会怎样修改区块链状态，形容函数的可变性有 3 个关键字：
    
    - `view`：用 `view` 修饰的函数，称为视图函数，它只能读取状态，而不能修改状态。
    - `pure`：用 `pure` 修饰的函数，称为纯函数，它既不能读取也不能修改状态。纯函数仅做计算
    - `payable`：用 `payable` 修饰的函数表示可以[接受以太币](https://decert.me/tutorial/solidity/solidity-basic/receive)，如果未指定，该函数将自动拒绝所有发送给它的以太币。
    
    > `view` , `pure` , `payable` 通常被称为**修饰符**
    > 
    
    如果函数是 `view` 或 `pure`，则它不能修改状态。
    
    - `view` 函数承诺不修改状态。用来查询合约状态，gas花费略低
    - `pure` 函数承诺不读取或修改状态。纯函数仅做计算，调用 `pure` 函数不会消耗任何gas
    
    关于 `payable` 函数的几个关键点：
    
    - 当一个地址没有声明为 `payable` 时，尝试向其发送以太币会导致交易失败并抛出异常。
    - `msg.value` 是一个全局变量，表示当前函数调用发送的以太币数量（以wei为单位）。
    - `address(this).balance` 用来获取合约当前余额。
    - 使用 `transfer` 函数可以将合约中的以太币发送到另一个地址。`transfer` 是一种安全的方式来发送以太币，因为它限制了转账中可用的gas（2300 gas），足够完成转账但不足以执行复杂的操作。
    - 代码示例
        
        ```solidity
        
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
        
- 函数修饰符的应用（`modifier`）
    - **函数修饰器**（`modifier`）
        
        函数修饰器可以被用来以声明的方式修改函数的语义(见合约部分的 [函数修饰器](https://docs.soliditylang.org/zh/latest/contracts.html#modifiers))。
        
        重载，也就是具有同一个修饰器的名字但有不同的参数，是不可能的。与函数一样，修饰器也可以被 [重载](https://docs.soliditylang.org/zh/latest/contracts.html#modifier-overriding)。
        
        - **`modifier`**：用于改变函数的行为。它可以在执行函数之前自动检查某个条件，例如权限验证或状态检查。`modifier`是合约的可继承属性，可以被派生合约覆盖。
            
            ```solidity
            pragma solidity >0.5.0 <0.7.0;
            contract owned {
                address owner;
                modifier onlyOwner {
                    require(msg.sender == owner, "Only owner can call this function.");
                    _;
                }
            }
            contract mortal is owned {
                function close() public onlyOwner {
                    selfdestruct(owner);
                }
            }
            ```
            
        - 代码示例：[open in Remix](https://remix.ethereum.org/?#language=solidity&version=0.8.22&code=Ly8gU1BEWC1MaWNlbnNlLUlkZW50aWZpZXI6IEdQTC0zLjAKcHJhZ21hIHNvbGlkaXR5ID49MC40LjIyIDwwLjkuMDsKCmNvbnRyYWN0IFB1cmNoYXNlIHsKICAgIGFkZHJlc3MgcHVibGljIHNlbGxlcjsKCiAgICBtb2RpZmllciBvbmx5U2VsbGVyKCkgeyAvLyDkv67ppbDlmagKICAgICAgICByZXF1aXJlKAogICAgICAgICAgICBtc2cuc2VuZGVyID09IHNlbGxlciwKICAgICAgICAgICAgIk9ubHkgc2VsbGVyIGNhbiBjYWxsIHRoaXMuIgogICAgICAgICk7CiAgICAgICAgXzsKICAgIH0KCiAgICBmdW5jdGlvbiBhYm9ydCgpIHB1YmxpYyB2aWV3IG9ubHlTZWxsZXIgeyAvLyDkv67ppbDlmajnmoTkvb/nlKgKICAgICAgICAvLyAuLi4KICAgIH0KfQ==)
            
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
        
        - `modifier`代码示例
            
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
            
    - 总结：函数修饰符（`pure`, `view`, `payable`）与**函数修饰器**（`modifier`）的区别
        - `pure`和`view`主要用于声明函数对合约状态的读取和修改行为。
        - `payable`用于标识函数可以接收以太币。
        - `modifier`用于改变函数的行为，提供了一种在执行函数之前进行条件检查的方式。
        
        这些修饰符在Solidity中用于提高智能合约的安全性和效率，有助于明确函数的行为和限制。
        
- 错误处理（`require`, `assert`, `revert`）
    
    ### [**EVM 中错误处理-DeCert](https://decert.me/tutorial/solidity/solidity-basic/error)[](https://decert.me/tutorial/solidity/solidity-basic/error#evm-%E4%B8%AD%E9%94%99%E8%AF%AF%E5%A4%84%E7%90%86)**
    
    EVM 处理错误和我们常见的语言（如Java、JavaScript等）不一样，当 EVM 在执行时遇到错误，例如：访问越界的数组，除0等，EVM 会回退（`revert`）整个交易，当前交易所有调用（包含子调用）所改变的状态都会被撤销，因此不是出现部分状态被修改的情况。
    
    在以太坊上，**每个交易都是原子操作**，在数据库里事务（`transcation`）一样，**要么保证状态的修改要么全部成功，要么全部失败。\**
    
    1. `require()` ：一般用来做条件校验，用于检查用户输入
    2. `assert()`：用于检查不应该发生的异常情况。尽量少使用assert。
    3. `revert()`：直接调用 `revert()` 来撤销交易
    
    ### **如何抛出异常[​](https://decert.me/tutorial/solidity/solidity-basic/error#%E5%A6%82%E4%BD%95%E6%8A%9B%E5%87%BA%E5%BC%82%E5%B8%B8)**
    
    Solidity 有 3 个方法来抛出异常：`require()` 、`assert()`、`revert()`
    
    - **`require()`[](https://decert.me/tutorial/solidity/solidity-basic/error#require)**
        
        `require`函数通常用来在执行逻辑前检查输入或合约状态变量是否满足条件，以及验证外部调用的返回值时候满足条件，在条件不满足时抛出异常。
        
        `require`函数有两个形式：
        
        - `require(bool condition)`：如果条件不满足，则撤销状态更改；
        - `require(bool condition, string memory message)`：如果条件不满足，则撤销状态更改，可以提供一个错误消息。
        
        以下是`require` 使用例子：
        
        ```solidity
        pragma solidity >=0.8.0;
        
        contract testRequire {
            function vote(uint age) public {
                require(age >= 18, "只有18岁以上才卡一投票");
                        // ...
            }
        
            function transferOwnership(address newOwner) public {
                require(owner() == msg.sender, "调用者不是 Owner");
                    // ...
            }
            
        }
        ```
        
        `vote()` 函数要求 `age >= 18`（表示在18岁以上才可以投票），否则撤销交易。
        
        `transferOwnership()` 函数要求调用者是`owner()`， 否则撤销交易。
        
        除了代码调用 `require()` 不满足表达式，会抛出异常外，下面这些情况也同样会触发 require 式异常（这类异常称为`Error`）：
        
        - 通过消息调用调用某个函数，但该函数没有正确结束（它耗尽了gas，没有匹配函数，或者本身抛出一个异常）。 但不包括[低级别操作](https://decert.me/tutorial/solidity/solidity-adv/addr_call.md)：call、send、delegatecall、staticcall。低级操作不会抛出异常，而通过返回 false 来指示失败。
        - 使用 new 关键字创建合约，但合约创建失败。
        - 调用到了一个不存在的外部函数，即 EVM找不到外部函数的代码。
        - 向一个没法[接收以太币](https://decert.me/tutorial/solidity/solidity-basic/receive) 的合约`transfer()` ， 或附加Ether 调用没有 payable修饰符的函数。
        
        当 require 式异常发生时，EVM 使用 `REVERT` 操作码回滚交易，剩余未使用的 Gas 将返回给交易发起者。
        
    - **`assert()`**
        
        `assert(bool condition))` 函数通常用来检查内部逻辑，assert 总是假定程序满足条件检查（假定`condition`为true），否则说明程序出现了一个未知的错误，如果正确使用`assert()`函数，Solidity 分析工具（如 STMChecker 工具）可以帮我们分析出智能合约中的错误。
        
        以下是`assert` 使用例子：
        
        ```solidity
        pragma solidity >=0.8.0 ;
        
        contract testAsset{
            bool public inited;
        
            function checkInitValue() internal  {
                // inited 应该永远为false
                assert(!inited);
                // 其他的逻辑...
            }
        }
        ```
        
        除了代码调用 `assert()` 不满足表达式，会抛出异常外，下面这些情况也同样会触发 assert 式异常（这类异常称为`Panic`）：
        
        - 访问数组的索引太大或为负数（例如x[i]其中的i >= x.length或i < 0）。
        - 访问固定长度bytesN的索引太大或为负数。
        - 用零当除数做除法或模运算（例如 5 / 0 或 23 % 0 ）。
        - 移位负数位。
        - 将一个太大或负数值转换为一个枚举类型。
        - 调用未初始化的内部函数类型变量。
        
        > 在 0.8.0 及之后版本，当 assert 式异常发生时，EVM 会使用 `REVERT` 操作码回滚交易，剩余未使用的 Gas 将返回给交易发起者。（0.8.0以前则不会）
        > 
    - **`require()` 还是 `assert()` ?**
        
        以下是一些关于使用 `require` 还是 `assert` 的经验总结。
        
        这些情况优先使用`require()`：
        
        （1）用于检查用户输入。
        
        （2）用于检查合约调用返回值，如`require(external.send(amount))`。
        
        （3）用于检查状态，如`msg.send == owner`。
        
        （4）通常用于函数的开头。
        
        （5）不知道使用哪一个的时候，就使用require。
        
        这些情况优先使用`assert()`：
        
        （1）用于检查溢出错误，如`z = x + y ; assert(z >= x);`。
        
        （2）用于检查不应该发生的异常情况。
        
        （3）用于在状态改变之后，检查合约状态。
        
        （4）尽量少使用assert。
        
        （5）通常用于函数中间或结尾。
        
    - **`revert()`**
        
        也可以直接调用 `revert()` 来撤销交易，和`require()` 非常类似， revert 有两种形式：
        
        - `revert CustomError(arg1, arg2);` : 回退交易，并抛出一个自定义错误（从 0.8.4 开始新增的语法）。
        - `revert()` / `revert(string memory reason)`：回退交易，可选择提供一个解释性的字符串。
        
        **推荐使用第一种形式，自定义错误的方式来触发**，因为只需要使用 4 个字节的编码就可以描述错误，比较使用解释性的字符串**消耗更少的GAS**。
        
        ```solidity
        pragma solidity ^0.8.4;
        
        contract testRevert() {
          public owner;
            error NotOwner();
        
          function transferOwnership(address newOwner) public {
             if(owner != msg.sender)  revert NotOwner();
             owner = newOwner;
          }
        
        }
        ```
        
        `require()` 和 `revert()` 在功能上其实是等价的，例如，以下两个写法在功能上一样：
        
        ```solidity
        if(msg.sender != owner) { revert NotOwner(); }
        require(msg.sender == owner, "调用者不是 Owner");
        ```
        
        但使用自定义错误消耗的 Gas 更低。
        
    
    ### **捕获异常 `try/catch`**
    
    - **`try/catch`**
        
        在合约代码里，和[其他的合约进行交互](https://decert.me/tutorial/solidity/solidity-basic/interface)（这个称之为外部调用）是很常见的操作，如果我们不在因外部调用失败而终止我们的交易，这个时候就可以使用 try……catch……来捕获外部调用的异常。
        
        - 下面是一个`try/catch`使用示例：
            
            ```solidity
            contract CalledContract {
                function getTwo() external returns (uint256) {
            // 一些其他逻辑，也许 revert
                    return 2;
                }
            }
            
            contract TryCatcher {
                CalledContract public externalContract;
            
                function executeEx() public returns (uint256, bool) {
            // 外部调用 getTwo()
                    try externalContract.getTwo() returns (uint256 v) {
                        uint256 newValue = v + 2;
                        return (newValue, true);
                    } catch {
                    }
                }
            }
            
            ```
            
        
        在进行try/catch时，允许获得外部调用的返回值。
        
        > 注意，`try/catch` 仅适用于捕获外部调用的异常，内部代码异常是无法被 `catch` 的，
        > 
        - 例如：
            
            ```solidity
            function executeEx() public {
                try externalContract.getTwo() {
                    // 尽管外部调用成功了，依旧会回退交易，无法被catch
                    revert();
                } catch {
                   // ...
                }
            }
            ```
            
        
        注意本地变量newValue和返回值只在try代码块内有效。类似地，也可以在catch块内声明变量。
        
    - **catch 条件子句**
        
        在 catch 语句可以捕获异常的错误提示，错误提示转换为`bytes`（如果由于某种原因转码`bytes`失败，则`try/catch`会失败，会回退整个交易）。
        
        catch 也提供了不同的子句来捕获不同类型的异常，
        
        - 例如：
            
            ```solidity
            contract TryCatcher {
            
                event ReturnDataEvent(bytes someData);
                event CatchStringEvent(string someString);
                event SuccessEvent();
            
                function execute() public {
                    try externalContract.someFunction() {
                        emit SuccessEvent();
                    } catch Error(string memory revertReason) {
                        emit CatchStringEvent(revertReason);
                    } catch (bytes memory returnData) {
                        emit ReturnDataEvent(returnData);
                    }
                }
            }
            
            ```
            
        
        在这段代码中，如果调用 `externalContract.someFunction` 发生 `require` 式异常
        
        （如`require(condition，"reason string")`或`revert("reason string")`），则错误与`catch Error(string memory revertReason)`子句匹配。
        
        在任何其他情况下， 
        
        例如发生 `assert` 式异常，则会执行更通用的`catch(bytes memory returnData)`子句。
        
    
    ### [****](https://decert.me/tutorial/solidity/solidity-basic/error#assert)