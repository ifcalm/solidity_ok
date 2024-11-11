# 12-W2D5-Solidity最佳实践

**目标：学习Solidity开发的最佳实践**

- 代码风格与可读性
- 使用OpenZeppelin库进行合约开发
- 使用Hardhat进行测试与调试
- 合约的升级模式（如代理合约）

**笔记：**

- 代码风格与可读性
    - **登链社区：[风格指南](https://learnblockchain.cn/docs/solidity/style-guide.html)**
        
        ## **介绍**
        
        本指南旨在提供编写 Solidity 代码的编码规范。 本指南应被视为一个不断发展的文档，随着有用规范的发现和旧规范的淘汰而变化。
        
        许多项目将实施自己的风格指南。在发生冲突时，项目特定的风格指南优先。
        
        本风格指南的结构和许多建议来自 Python 的 [pep8 风格指南](https://peps.python.org/pep-0008/)。
        
        本指南的目标 **不推荐** 编写 Solidity 代码的正确方式或最佳方式。 本指南的目标是 **一致性**。 Python 的 [pep8](https://peps.python.org/pep-0008/#a-foolish-consistency-is-the-hobgoblin-of-little-minds) 很好地捕捉了这一概念。
        
        > **备注**
        > 
        > 
        > 风格指南是关于一致性的。遵循本风格指南的一致性很重要。项目内部的一致性更为重要。模块或函数内部的一致性是最重要的。
        > 
        > 但最重要的是：**知道何时不一致** – 有时风格指南根本不适用。当有疑问时，使用你的最佳判断。查看其他示例并决定什么看起来最好。并且不要犹豫去询问！
        > 
        
        ## **代码布局**
        
        ### **缩进**
        
        每个缩进级别使用 4 个空格。
        
        ### **制表符或空格**
        
        空格是首选的缩进方法。
        
        应避免混合使用制表符和空格。
        
        ### **空行**
        
        在 Solidity 源代码的顶层声明周围留出两个空行。
        
        推荐:
        
        [open in Remix](https://remix.ethereum.org/?#language=solidity&version=0.8.28&code=Ly8gU1BEWC1MaWNlbnNlLUlkZW50aWZpZXI6IEdQTC0zLjAKcHJhZ21hIHNvbGlkaXR5ID49MC40LjAgPDAuOS4wOwoKY29udHJhY3QgQSB7CiAgICAvLyAuLi4KfQoKCmNvbnRyYWN0IEIgewogICAgLy8gLi4uCn0KCgpjb250cmFjdCBDIHsKICAgIC8vIC4uLgp9)
        
        ```solidity
        // SPDX-License-Identifier: GPL-3.0
        pragma solidity >=0.4.0 <0.9.0;
        
        contract A {
            // ...
        }
        
        contract B {
            // ...
        }
        
        contract C {
            // ...
        }
        ```
        
        不推荐：
        
        [open in Remix](https://remix.ethereum.org/?#language=solidity&version=0.8.28&code=Ly8gU1BEWC1MaWNlbnNlLUlkZW50aWZpZXI6IEdQTC0zLjAKcHJhZ21hIHNvbGlkaXR5ID49MC40LjAgPDAuOS4wOwoKY29udHJhY3QgQSB7CiAgICAvLyAuLi4KfQpjb250cmFjdCBCIHsKICAgIC8vIC4uLgp9Cgpjb250cmFjdCBDIHsKICAgIC8vIC4uLgp9)
        
        ```solidity
        // SPDX-License-Identifier: GPL-3.0
        pragma solidity >=0.4.0 <0.9.0;
        
        contract A {
            // ...
        }
        contract B {
            // ...
        }
        
        contract C {
            // ...
        }
        ```
        
        在合约内部，函数声明之间留出一个空行。
        
        在相关的一行代码组之间可以省略空行（例如抽象合约的存根函数）
        
        推荐：
        
        [open in Remix](https://remix.ethereum.org/?#language=solidity&version=0.8.28&code=Ly8gU1BEWC1MaWNlbnNlLUlkZW50aWZpZXI6IEdQTC0zLjAKcHJhZ21hIHNvbGlkaXR5ID49MC42LjAgPDAuOS4wOwoKYWJzdHJhY3QgY29udHJhY3QgQSB7CiAgICBmdW5jdGlvbiBzcGFtKCkgcHVibGljIHZpcnR1YWwgcHVyZTsKICAgIGZ1bmN0aW9uIGhhbSgpIHB1YmxpYyB2aXJ0dWFsIHB1cmU7Cn0KCgpjb250cmFjdCBCIGlzIEEgewogICAgZnVuY3Rpb24gc3BhbSgpIHB1YmxpYyBwdXJlIG92ZXJyaWRlIHsKICAgICAgICAvLyAuLi4KICAgIH0KCiAgICBmdW5jdGlvbiBoYW0oKSBwdWJsaWMgcHVyZSBvdmVycmlkZSB7CiAgICAgICAgLy8gLi4uCiAgICB9Cn0=)
        
        ```solidity
        // SPDX-License-Identifier: GPL-3.0
        pragma solidity >=0.6.0 <0.9.0;
        
        abstract contract A {
            function spam() public virtual pure;
            function ham() public virtual pure;
        }
        
        contract B is A {
            function spam() public pure override {
                // ...
            }
        
            function ham() public pure override {
                // ...
            }
        }
        ```
        
        不推荐：
        
        [open in Remix](https://remix.ethereum.org/?#language=solidity&version=0.8.28&code=Ly8gU1BEWC1MaWNlbnNlLUlkZW50aWZpZXI6IEdQTC0zLjAKcHJhZ21hIHNvbGlkaXR5ID49MC42LjAgPDAuOS4wOwoKYWJzdHJhY3QgY29udHJhY3QgQSB7CiAgICBmdW5jdGlvbiBzcGFtKCkgdmlydHVhbCBwdXJlIHB1YmxpYzsKICAgIGZ1bmN0aW9uIGhhbSgpIHB1YmxpYyB2aXJ0dWFsIHB1cmU7Cn0KCgpjb250cmFjdCBCIGlzIEEgewogICAgZnVuY3Rpb24gc3BhbSgpIHB1YmxpYyBwdXJlIG92ZXJyaWRlIHsKICAgICAgICAvLyAuLi4KICAgIH0KICAgIGZ1bmN0aW9uIGhhbSgpIHB1YmxpYyBwdXJlIG92ZXJyaWRlIHsKICAgICAgICAvLyAuLi4KICAgIH0KfQ==)
        
        ```solidity
        // SPDX-License-Identifier: GPL-3.0
        pragma solidity >=0.6.0 <0.9.0;
        
        abstract contract A {
            function spam() virtual pure public;
            function ham() public virtual pure;
        }
        
        contract B is A {
            function spam() public pure override {
                // ...
            }
            function ham() public pure override {
                // ...
            }
        }
        ```
        
        # **代码行的最大长度**
        
        建议的最大行长度为 120 个字符。
        
        换行应符合以下准则。
        
        1. 第一个参数不应附加到开括号。
        2. 应仅使用一个缩进。
        3. 每个参数应单独占一行。
        4. 终止元素 `);` 应单独放在最后一行。
        
        函数调用
        
        推荐：
        
        [open in Remix](https://remix.ethereum.org/?#language=solidity&version=0.8.28&code=dGhpc0Z1bmN0aW9uQ2FsbElzUmVhbGx5TG9uZygKICAgIGxvbmdBcmd1bWVudDEsCiAgICBsb25nQXJndW1lbnQyLAogICAgbG9uZ0FyZ3VtZW50MwopOw==)
        
        ```solidity
        thisFunctionCallIsReallyLong(
            longArgument1,
            longArgument2,
            longArgument3
        );
        ```
        
        不推荐：
        
        [open in Remix](https://remix.ethereum.org/?#language=solidity&version=0.8.28&code=dGhpc0Z1bmN0aW9uQ2FsbElzUmVhbGx5TG9uZyhsb25nQXJndW1lbnQxLAogICAgICAgICAgICAgICAgICAgICAgICAgICAgICBsb25nQXJndW1lbnQyLAogICAgICAgICAgICAgICAgICAgICAgICAgICAgICBsb25nQXJndW1lbnQzCik7Cgp0aGlzRnVuY3Rpb25DYWxsSXNSZWFsbHlMb25nKGxvbmdBcmd1bWVudDEsCiAgICBsb25nQXJndW1lbnQyLAogICAgbG9uZ0FyZ3VtZW50MwopOwoKdGhpc0Z1bmN0aW9uQ2FsbElzUmVhbGx5TG9uZygKICAgIGxvbmdBcmd1bWVudDEsIGxvbmdBcmd1bWVudDIsCiAgICBsb25nQXJndW1lbnQzCik7Cgp0aGlzRnVuY3Rpb25DYWxsSXNSZWFsbHlMb25nKApsb25nQXJndW1lbnQxLApsb25nQXJndW1lbnQyLApsb25nQXJndW1lbnQzCik7Cgp0aGlzRnVuY3Rpb25DYWxsSXNSZWFsbHlMb25nKAogICAgbG9uZ0FyZ3VtZW50MSwKICAgIGxvbmdBcmd1bWVudDIsCiAgICBsb25nQXJndW1lbnQzKTs=)
        
        ```solidity
        thisFunctionCallIsReallyLong(longArgument1,
                                      longArgument2,
                                      longArgument3
        );
        
        thisFunctionCallIsReallyLong(longArgument1,
            longArgument2,
            longArgument3
        );
        
        thisFunctionCallIsReallyLong(
            longArgument1, longArgument2,
            longArgument3
        );
        
        thisFunctionCallIsReallyLong(
        longArgument1,
        longArgument2,
        longArgument3
        );
        
        thisFunctionCallIsReallyLong(
            longArgument1,
            longArgument2,
            longArgument3);
        ```
        
        赋值语句
        
        推荐：
        
        [open in Remix](https://remix.ethereum.org/?#language=solidity&version=0.8.28&code=dGhpc0lzQUxvbmdOZXN0ZWRNYXBwaW5nW2JlaW5nXVtzZXRdW3RvU29tZVZhbHVlXSA9IHNvbWVGdW5jdGlvbigKICAgIGFyZ3VtZW50MSwKICAgIGFyZ3VtZW50MiwKICAgIGFyZ3VtZW50MywKICAgIGFyZ3VtZW50NAopOw==)
        
        ```solidity
        thisIsALongNestedMapping[being][set][toSomeValue] = someFunction(
            argument1,
            argument2,
            argument3,
            argument4
        );
        ```
        
        不推荐：
        
        [open in Remix](https://remix.ethereum.org/?#language=solidity&version=0.8.28&code=dGhpc0lzQUxvbmdOZXN0ZWRNYXBwaW5nW2JlaW5nXVtzZXRdW3RvU29tZVZhbHVlXSA9IHNvbWVGdW5jdGlvbihhcmd1bWVudDEsCiAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICBhcmd1bWVudDIsCiAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICBhcmd1bWVudDMsCiAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICBhcmd1bWVudDQpOw==)
        
        ```solidity
        thisIsALongNestedMapping[being][set][toSomeValue] = someFunction(argument1,
                                                                           argument2,
                                                                           argument3,
                                                                           argument4);
        ```
        
        事件定义和事件发射器
        
        推荐：
        
        [open in Remix](https://remix.ethereum.org/?#language=solidity&version=0.8.28&code=ZXZlbnQgTG9uZ0FuZExvdHNPZkFyZ3MoCiAgICBhZGRyZXNzIHNlbmRlciwKICAgIGFkZHJlc3MgcmVjaXBpZW50LAogICAgdWludDI1NiBwdWJsaWNLZXksCiAgICB1aW50MjU2IGFtb3VudCwKICAgIGJ5dGVzMzJbXSBvcHRpb25zCik7CgplbWl0IExvbmdBbmRMb3RzT2ZBcmdzKAogICAgc2VuZGVyLAogICAgcmVjaXBpZW50LAogICAgcHVibGljS2V5LAogICAgYW1vdW50LAogICAgb3B0aW9ucwopOw==)
        
        ```solidity
        event LongAndLotsOfArgs(
            address sender,
            address recipient,
            uint256 publicKey,
            uint256 amount,
            bytes32[] options
        );
        
        emit LongAndLotsOfArgs(
            sender,
            recipient,
            publicKey,
            amount,
            options
        );
        ```
        
        不推荐：
        
        [open in Remix](https://remix.ethereum.org/?#language=solidity&version=0.8.28&code=ZXZlbnQgTG9uZ0FuZExvdHNPZkFyZ3MoYWRkcmVzcyBzZW5kZXIsCiAgICAgICAgICAgICAgICAgICAgICAgIGFkZHJlc3MgcmVjaXBpZW50LAogICAgICAgICAgICAgICAgICAgICAgICB1aW50MjU2IHB1YmxpY0tleSwKICAgICAgICAgICAgICAgICAgICAgICAgdWludDI1NiBhbW91bnQsCiAgICAgICAgICAgICAgICAgICAgICAgIGJ5dGVzMzJbXSBvcHRpb25zKTsKCmVtaXQgTG9uZ0FuZExvdHNPZkFyZ3Moc2VuZGVyLAogICAgICAgICAgICAgICAgICByZWNpcGllbnQsCiAgICAgICAgICAgICAgICAgIHB1YmxpY0tleSwKICAgICAgICAgICAgICAgICAgYW1vdW50LAogICAgICAgICAgICAgICAgICBvcHRpb25zKTs=)
        
        ```solidity
        event LongAndLotsOfArgs(address sender,
                                address recipient,
                                uint256 publicKey,
                                uint256 amount,
                                bytes32[] options);
        
        emit LongAndLotsOfArgs(sender,
                          recipient,
                          publicKey,
                          amount,
                          options);
        ```
        
        ## **源文件编码**
        
        首选 UTF-8 或 ASCII 编码。
        
        ### **导入**
        
        导入语句应始终放在文件的顶部。
        
        推荐：
        
        [open in Remix](https://remix.ethereum.org/?#language=solidity&version=0.8.28&code=Ly8gU1BEWC1MaWNlbnNlLUlkZW50aWZpZXI6IEdQTC0zLjAKcHJhZ21hIHNvbGlkaXR5ID49MC40LjAgPDAuOS4wOwoKaW1wb3J0ICIuL093bmVkLnNvbCI7Cgpjb250cmFjdCBBIHsKICAgIC8vIC4uLgp9CgoKY29udHJhY3QgQiBpcyBPd25lZCB7CiAgICAvLyAuLi4KfQ==)
        
        ```solidity
        // SPDX-License-Identifier: GPL-3.0
        pragma solidity >=0.4.0 <0.9.0;
        
        import "./Owned.sol";
        
        contract A {
            // ...
        }
        
        contract B is Owned {
            // ...
        }
        ```
        
        不推荐：
        
        [open in Remix](https://remix.ethereum.org/?#language=solidity&version=0.8.28&code=Ly8gU1BEWC1MaWNlbnNlLUlkZW50aWZpZXI6IEdQTC0zLjAKcHJhZ21hIHNvbGlkaXR5ID49MC40LjAgPDAuOS4wOwoKY29udHJhY3QgQSB7CiAgICAvLyAuLi4KfQoKCmltcG9ydCAiLi9Pd25lZC5zb2wiOwoKCmNvbnRyYWN0IEIgaXMgT3duZWQgewogICAgLy8gLi4uCn0=)
        
        ```solidity
        // SPDX-License-Identifier: GPL-3.0
        pragma solidity >=0.4.0 <0.9.0;
        
        contract A {
            // ...
        }
        
        import "./Owned.sol";
        
        contract B is Owned {
            // ...
        }
        ```
        
        ## **函数顺序**
        
        排序有助于读者识别可以调用的函数，并更容易找到构造函数和回退定义。
        
        函数应根据其可见性进行分组并排序：
        
        - 构造函数
        - 接收函数（如果存在）
        - 回退函数（如果存在）
        - 外部
        - 公共
        - 内部
        - 私有
        
        在一个分组内，将 `view` 和 `pure` 函数放在最后。
        
        推荐：
        
        [open in Remix](https://remix.ethereum.org/?#language=solidity&version=0.8.28&code=Ly8gU1BEWC1MaWNlbnNlLUlkZW50aWZpZXI6IEdQTC0zLjAKcHJhZ21hIHNvbGlkaXR5ID49MC43LjAgPDAuOS4wOwpjb250cmFjdCBBIHsKICAgIGNvbnN0cnVjdG9yKCkgewogICAgICAgIC8vIC4uLgogICAgfQoKICAgIHJlY2VpdmUoKSBleHRlcm5hbCBwYXlhYmxlIHsKICAgICAgICAvLyAuLi4KICAgIH0KCiAgICBmYWxsYmFjaygpIGV4dGVybmFsIHsKICAgICAgICAvLyAuLi4KICAgIH0KCiAgICAvLyDlpJbpg6jlh73mlbAKICAgIC8vIC4uLgoKICAgIC8vIOinhuWbvueahOWklumDqOWHveaVsAogICAgLy8gLi4uCgogICAgLy8g57qv55qE5aSW6YOo5Ye95pWwCiAgICAvLyAuLi4KCiAgICAvLyDlhazlhbHlh73mlbAKICAgIC8vIC4uLgoKICAgIC8vIOWGhemDqOWHveaVsAogICAgLy8gLi4uCgogICAgLy8g56eB5pyJ5Ye95pWwCiAgICAvLyAuLi4KfQ==)
        
        ```solidity
        // SPDX-License-Identifier: GPL-3.0
        pragma solidity >=0.7.0 <0.9.0;
        contract A {
            constructor() {
                // ...
            }
        
            receive() external payable {
                // ...
            }
        
            fallback() external {
                // ...
            }
        
            // 外部函数
            // ...
        
            // 视图的外部函数
            // ...
        
            // 纯的外部函数
            // ...
        
            // 公共函数
            // ...
        
            // 内部函数
            // ...
        
            // 私有函数
            // ...
        }
        ```
        
        不推荐：
        
        [open in Remix](https://remix.ethereum.org/?#language=solidity&version=0.8.28&code=Ly8gU1BEWC1MaWNlbnNlLUlkZW50aWZpZXI6IEdQTC0zLjAKcHJhZ21hIHNvbGlkaXR5ID49MC43LjAgPDAuOS4wOwpjb250cmFjdCBBIHsKCiAgICAvLyDlpJbpg6jlh73mlbAKICAgIC8vIC4uLgoKICAgIGZhbGxiYWNrKCkgZXh0ZXJuYWwgewogICAgICAgIC8vIC4uLgogICAgfQogICAgcmVjZWl2ZSgpIGV4dGVybmFsIHBheWFibGUgewogICAgICAgIC8vIC4uLgogICAgfQoKICAgIC8vIOengeacieWHveaVsAogICAgLy8gLi4uCgogICAgLy8g5YWs5YWx5Ye95pWwCiAgICAvLyAuLi4KCiAgICBjb25zdHJ1Y3RvcigpIHsKICAgICAgICAvLyAuLi4KICAgIH0KCiAgICAvLyDlhoXpg6jlh73mlbAKICAgIC8vIC4uLgp9)
        
        ```solidity
        // SPDX-License-Identifier: GPL-3.0
        pragma solidity >=0.7.0 <0.9.0;
        contract A {
        
            // 外部函数
            // ...
        
            fallback() external {
                // ...
            }
            receive() external payable {
                // ...
            }
        
            // 私有函数
            // ...
        
            // 公共函数
            // ...
        
            constructor() {
                // ...
            }
        
            // 内部函数
            // ...
        }
        ```
        
        ## **表达式中的空格**
        
        避免在以下情况下出现多余的空格：
        
        除单行函数声明外，紧接着小括号，中括号或者大括号的内容应该避免使用空格。
        
        推荐：
        
        [open in Remix](https://remix.ethereum.org/?#language=solidity&version=0.8.28&code=c3BhbShoYW1bMV0sIENvaW4oe25hbWU6ICJoYW0ifSkpOw==)
        
        ```solidity
        spam(ham[1], Coin({name: "ham"}));
        ```
        
        不推荐：
        
        [open in Remix](https://remix.ethereum.org/?#language=solidity&version=0.8.28&code=c3BhbSggaGFtWyAxIF0sIENvaW4oIHsgbmFtZTogImhhbSIgfSApICk7)
        
        ```solidity
        spam( ham[ 1 ], Coin( { name: "ham" } ) );
        ```
        
        例外：
        
        [open in Remix](https://remix.ethereum.org/?#language=solidity&version=0.8.28&code=ZnVuY3Rpb24gc2luZ2xlTGluZSgpIHB1YmxpYyB7IHNwYW0oKTsgfQ==)
        
        ```solidity
        function singleLine() public { spam(); }
        ```
        
        在逗号、分号之前：
        
        推荐：
        
        [open in Remix](https://remix.ethereum.org/?#language=solidity&version=0.8.28&code=ZnVuY3Rpb24gc3BhbSh1aW50IGksIENvaW4gY29pbikgcHVibGljOw==)
        
        ```solidity
        function spam(uint i, Coin coin) public;
        ```
        
        推荐：
        
        [open in Remix](https://remix.ethereum.org/?#language=solidity&version=0.8.28&code=ZnVuY3Rpb24gc3BhbSh1aW50IGkgLCBDb2luIGNvaW4pIHB1YmxpYyA7)
        
        ```solidity
        function spam(uint i , Coin coin) public ;
        ```
        
        在赋值或其他运算符周围有多个空格以对齐另一个：
        
        推荐：
        
        ```solidity
        x = 1; y = 2; longVariable = 3;
        ```
        
        不推荐：
        
        [open in Remix](https://remix.ethereum.org/?#language=solidity&version=0.8.28&code=eCAgICAgICAgICAgID0gMTsKeSAgICAgICAgICAgID0gMjsKbG9uZ1ZhcmlhYmxlID0gMzs=)
        
        ```solidity
        x            = 1;
        y            = 2;
        longVariable = 3;
        ```
        
        在接收和回退函数中不要包含空白：
        
        推荐： .. code-block:: solidity
        
        ```solidity
        receive() external payable {
        		…
        }
        
        fallback() external {
        		…
        }
        ```
        
        不推荐：
        
        [open in Remix](https://remix.ethereum.org/?#language=solidity&version=0.8.28&code=cmVjZWl2ZSAoKSBleHRlcm5hbCBwYXlhYmxlIHsKICAgIC4uLgp9CgpmYWxsYmFjayAoKSBleHRlcm5hbCB7CiAgICAuLi4KfQ==)
        
        ```solidity
        receive () external payable {
            ...
        }
        
        fallback () external {
            ...
        }
        ```
        
        ## **控制结构**
        
        表示合约、库、函数和结构体主体的大括号应：
        
        - 在声明的同一行打开
        - 在与声明开始相同的缩进级别上关闭
        - 开括号前应有一个空格
        
        推荐：
        
        ```solidity
        // SPDX-License-Identifier: GPL-3.0 pragma solidity >=0.4.0 <0.9.0;
        
        contract Coin {
        		struct Bank {
        				address owner; uint balance;
        		}
        }
        ```
        
        不推荐：
        
        [open in Remix](https://remix.ethereum.org/?#language=solidity&version=0.8.28&code=Ly8gU1BEWC1MaWNlbnNlLUlkZW50aWZpZXI6IEdQTC0zLjAKcHJhZ21hIHNvbGlkaXR5ID49MC40LjAgPDAuOS4wOwoKY29udHJhY3QgQ29pbgp7CiAgICBzdHJ1Y3QgQmFuayB7CiAgICAgICAgYWRkcmVzcyBvd25lcjsKICAgICAgICB1aW50IGJhbGFuY2U7CiAgICB9Cn0=)
        
        ```solidity
        // SPDX-License-Identifier: GPL-3.0
        pragma solidity >=0.4.0 <0.9.0;
        
        contract Coin
        {
            struct Bank {
                address owner;
                uint balance;
            }
        }
        ```
        
        同样的建议适用于控制结构 `if`, `else`, `while`, 和 `for`。
        
        此外，控制结构 `if`, `while`, 和 `for` 与表示条件的括号块之间应有一个空格，以及条件括号块与开括号之间也应有一个空格。
        
        推荐： .. code-block:: solidity
        
        ```solidity
        if (…) {
        		…
        }
        
        for (…) {
        		…
        }
        ```
        
        不推荐：
        
        [open in Remix](https://remix.ethereum.org/?#language=solidity&version=0.8.28&code=aWYgKC4uLikKewogICAgLi4uCn0KCndoaWxlKC4uLil7Cn0KCmZvciAoLi4uKSB7CiAgICAuLi47fQ==)
        
        ```solidity
        if (...)
        {
            ...
        }
        
        while(...){
        }
        
        for (...) {
            ...;}
        ```
        
        对于包含单个语句的控制结构，*如果* 语句包含在单行中，则可以省略大括号。
        
        推荐：
        
        ```solidity
        if (x < 10)
        x += 1;
        ```
        
        不推荐：
        
        [open in Remix](https://remix.ethereum.org/?#language=solidity&version=0.8.28&code=aWYgKHggPCAxMCkKICAgIHNvbWVBcnJheS5wdXNoKENvaW4oewogICAgICAgIG5hbWU6ICdzcGFtJywKICAgICAgICB2YWx1ZTogNDIKICAgIH0pKTs=)
        
        ```solidity
        if (x < 10)
            someArray.push(Coin({
                name: 'spam',
                value: 42
            }));
        ```
        
        对于具有 `else` 或 `else if` 子句的 `if` 块，`else` 应放在 `if` 的闭合大括号的同一行。这与其他块状结构的规则有所不同。
        
        推荐：
        
        ```solidity
        if (x < 3) {
        		x += 1;
        } else if (x > 7) {
        		x -= 1;
        } else {
        		x = 5;
        }
        
        if (x < 3)
        		x += 1;
        else
        		x -= 1;
        ```
        
        不推荐：
        
        [open in Remix](https://remix.ethereum.org/?#language=solidity&version=0.8.28&code=aWYgKHggPCAzKSB7CiAgICB4ICs9IDE7Cn0KZWxzZSB7CiAgICB4IC09IDE7Cn0=)
        
        ```solidity
        if (x < 3) {
            x += 1;
        }
        else {
            x -= 1;
        }
        
        ```
        
        ## **函数声明**
        
        对于短函数声明，建议将函数体的开括号与函数声明保持在同一行。
        
        闭合大括号应与函数声明保持相同的缩进级别。
        
        开括号前应有一个空格。
        
        推荐： 
        
        ```solidity
        function increment(uint x) public pure returns (uint) {
            return x + 1;
        }
        
        function increment(uint x) public pure onlyOwner returns (uint) {
            return x + 1;
        }
        ```
        
        不推荐：
        
        [open in Remix](https://remix.ethereum.org/?#language=solidity&version=0.8.28&code=ZnVuY3Rpb24gaW5jcmVtZW50KHVpbnQgeCkgcHVibGljIHB1cmUgcmV0dXJucyAodWludCkKewogICAgcmV0dXJuIHggKyAxOwp9CgpmdW5jdGlvbiBpbmNyZW1lbnQodWludCB4KSBwdWJsaWMgcHVyZSByZXR1cm5zICh1aW50KXsKICAgIHJldHVybiB4ICsgMTsKfQoKZnVuY3Rpb24gaW5jcmVtZW50KHVpbnQgeCkgcHVibGljIHB1cmUgcmV0dXJucyAodWludCkgewogICAgcmV0dXJuIHggKyAxOwogICAgfQoKZnVuY3Rpb24gaW5jcmVtZW50KHVpbnQgeCkgcHVibGljIHB1cmUgcmV0dXJucyAodWludCkgewogICAgcmV0dXJuIHggKyAxO30=)
        
        ```solidity
        function increment(uint x) public pure returns (uint)
        {
            return x + 1;
        }
        
        function increment(uint x) public pure returns (uint){
            return x + 1;
        }
        
        function increment(uint x) public pure returns (uint) {
            return x + 1;
            }
        
        function increment(uint x) public pure returns (uint) {
            return x + 1;}
        ```
        
        函数的修改器顺序应为：
        
        1. 可见性
        2. 可变性
        3. 虚拟
        4. 重写
        5. 自定义修改器
        
        推荐：
        
        ```solidity
        function balance(uint from) public view override returns (uint) {
            return balanceOf[from];
        }
        
        function increment(uint x) public pure onlyOwner returns (uint) {
            return x + 1;
        }
        ```
        
        不推荐：
        
        [open in Remix](https://remix.ethereum.org/?#language=solidity&version=0.8.28&code=ZnVuY3Rpb24gYmFsYW5jZSh1aW50IGZyb20pIHB1YmxpYyBvdmVycmlkZSB2aWV3IHJldHVybnMgKHVpbnQpICB7CiAgICByZXR1cm4gYmFsYW5jZU9mW2Zyb21dOwp9CgpmdW5jdGlvbiBpbmNyZW1lbnQodWludCB4KSBvbmx5T3duZXIgcHVibGljIHB1cmUgcmV0dXJucyAodWludCkgewogICAgcmV0dXJuIHggKyAxOwp9)
        
        ```solidity
        function balance(uint from) public override view returns (uint)  {
            return balanceOf[from];
        }
        
        function increment(uint x) onlyOwner public pure returns (uint) {
            return x + 1;
        }
        ```
        
        对于长函数声明，建议将每个参数放在与函数体相同的缩进级别的单独一行。闭合括号和开括号也应放在与函数声明相同的缩进级别的单独一行。
        
        推荐：
        
        ```solidity
        function thisFunctionHasLotsOfArguments(
            address a, address b, address c, address d, address e, address f
        ) public {
            doSomething();
        }
        ```
        
        不推荐：
        
        [open in Remix](https://remix.ethereum.org/?#language=solidity&version=0.8.28&code=ZnVuY3Rpb24gdGhpc0Z1bmN0aW9uSGFzTG90c09mQXJndW1lbnRzKGFkZHJlc3MgYSwgYWRkcmVzcyBiLCBhZGRyZXNzIGMsCiAgICBhZGRyZXNzIGQsIGFkZHJlc3MgZSwgYWRkcmVzcyBmKSBwdWJsaWMgewogICAgZG9Tb21ldGhpbmcoKTsKfQoKZnVuY3Rpb24gdGhpc0Z1bmN0aW9uSGFzTG90c09mQXJndW1lbnRzKGFkZHJlc3MgYSwKICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgIGFkZHJlc3MgYiwKICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgIGFkZHJlc3MgYywKICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgIGFkZHJlc3MgZCwKICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgIGFkZHJlc3MgZSwKICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgIGFkZHJlc3MgZikgcHVibGljIHsKICAgIGRvU29tZXRoaW5nKCk7Cn0KCmZ1bmN0aW9uIHRoaXNGdW5jdGlvbkhhc0xvdHNPZkFyZ3VtZW50cygKICAgIGFkZHJlc3MgYSwKICAgIGFkZHJlc3MgYiwKICAgIGFkZHJlc3MgYywKICAgIGFkZHJlc3MgZCwKICAgIGFkZHJlc3MgZSwKICAgIGFkZHJlc3MgZikgcHVibGljIHsKICAgIGRvU29tZXRoaW5nKCk7Cn0=)
        
        ```solidity
        function thisFunctionHasLotsOfArguments(address a, address b, address c,
            address d, address e, address f) public {
            doSomething();
        }
        
        function thisFunctionHasLotsOfArguments(address a,
                                                address b,
                                                address c,
                                                address d,
                                                address e,
                                                address f) public {
            doSomething();
        }
        
        function thisFunctionHasLotsOfArguments(
            address a,
            address b,
            address c,
            address d,
            address e,
            address f) public {
            doSomething();
        }
        ```
        
        如果长函数声明有修改器，则每个修改器应放在自己的行上。
        
        推荐：
        
        ```solidity
        function thisFunctionNameIsReallyLong(address x, address y, address z)
            public onlyOwner priced returns (address)
        {
            doSomething();
        }
        
        function thisFunctionNameIsReallyLong(
            address x, address y, address z
        )
            public onlyOwner priced returns (address)
        {
            doSomething();
        }
        ```
        
        不推荐：
        
        [open in Remix](https://remix.ethereum.org/?#language=solidity&version=0.8.28&code=ZnVuY3Rpb24gdGhpc0Z1bmN0aW9uTmFtZUlzUmVhbGx5TG9uZyhhZGRyZXNzIHgsIGFkZHJlc3MgeSwgYWRkcmVzcyB6KQogICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgIHB1YmxpYwogICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgIG9ubHlPd25lcgogICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgIHByaWNlZAogICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgIHJldHVybnMgKGFkZHJlc3MpIHsKICAgIGRvU29tZXRoaW5nKCk7Cn0KCmZ1bmN0aW9uIHRoaXNGdW5jdGlvbk5hbWVJc1JlYWxseUxvbmcoYWRkcmVzcyB4LCBhZGRyZXNzIHksIGFkZHJlc3MgeikKICAgIHB1YmxpYyBvbmx5T3duZXIgcHJpY2VkIHJldHVybnMgKGFkZHJlc3MpCnsKICAgIGRvU29tZXRoaW5nKCk7Cn0KCmZ1bmN0aW9uIHRoaXNGdW5jdGlvbk5hbWVJc1JlYWxseUxvbmcoYWRkcmVzcyB4LCBhZGRyZXNzIHksIGFkZHJlc3MgeikKICAgIHB1YmxpYwogICAgb25seU93bmVyCiAgICBwcmljZWQKICAgIHJldHVybnMgKGFkZHJlc3MpIHsKICAgIGRvU29tZXRoaW5nKCk7Cn0=)
        
        ```solidity
        function thisFunctionNameIsReallyLong(address x, address y, address z)
                                              public
                                              onlyOwner
                                              priced
                                              returns (address) {
            doSomething();
        }
        
        function thisFunctionNameIsReallyLong(address x, address y, address z)
            public onlyOwner priced returns (address)
        {
            doSomething();
        }
        
        function thisFunctionNameIsReallyLong(address x, address y, address z)
            public
            onlyOwner
            priced
            returns (address) {
            doSomething();
        }
        ```
        
        多行输出参数和返回语句应遵循 [代码行的最大长度](https://learnblockchain.cn/docs/solidity/style-guide.html#maximum-line-length) 部分推荐的长行换行样式。
        
        推荐： 
        
        ```solidity
        function thisFunctionNameIsReallyLong(
            address a, address b, address c
        )
        public returns (
            address someAddressName, uint256 LongArgument, uint256 Argument
        )
        {
            doSomething()
        
            return (
                veryLongReturnArg1, veryLongReturnArg2, veryLongReturnArg3
            );
        }
        ```
        
        不推荐：
        
        [open in Remix](https://remix.ethereum.org/?#language=solidity&version=0.8.28&code=ZnVuY3Rpb24gdGhpc0Z1bmN0aW9uTmFtZUlzUmVhbGx5TG9uZygKICAgIGFkZHJlc3MgYSwKICAgIGFkZHJlc3MgYiwKICAgIGFkZHJlc3MgYwopCiAgICBwdWJsaWMKICAgIHJldHVybnMgKGFkZHJlc3Mgc29tZUFkZHJlc3NOYW1lLAogICAgICAgICAgICB1aW50MjU2IExvbmdBcmd1bWVudCwKICAgICAgICAgICAgdWludDI1NiBBcmd1bWVudCkKewogICAgZG9Tb21ldGhpbmcoKQoKICAgIHJldHVybiAodmVyeUxvbmdSZXR1cm5BcmcxLAogICAgICAgICAgICB2ZXJ5TG9uZ1JldHVybkFyZzEsCiAgICAgICAgICAgIHZlcnlMb25nUmV0dXJuQXJnMSk7Cn0=)
        
        ```solidity
        function thisFunctionNameIsReallyLong(
            address a,
            address b,
            address c
        )
            public
            returns (address someAddressName,
                    uint256 LongArgument,
                    uint256 Argument)
        {
            doSomething()
        
            return (veryLongReturnArg1,
                    veryLongReturnArg1,
                    veryLongReturnArg1);
        }
        ```
        
        对于需要参数的继承合约的构造函数，如果函数声明较长或难以阅读，建议将基构造函数换行，方式与修改器相同。
        
        推荐： 
        
        ```solidity
        // SPDX-License-Identifier: GPL-3.0 pragma solidity >=0.7.0 <0.9.0; 
        
        // 基合约仅用于使其编译 
        contract B {
            constructor(uint) { }
        }
        
        contract C {
            constructor(uint, uint) { }
        }
        
        contract D {
            constructor(uint) { }
        }
        
        contract A is B, C, D {
            uint x;
        
            constructor(uint param1, uint param2, uint param3, uint param4, uint param5)
            B(param1) C(param2, param3) D(param4)
            {
                // 使用 param5 做一些事情 x = param5;
            }
        
        }
        ```
        
        不推荐：
        
        [open in Remix](https://remix.ethereum.org/?#language=solidity&version=0.8.28&code=Ly8gU1BEWC1MaWNlbnNlLUlkZW50aWZpZXI6IEdQTC0zLjAKcHJhZ21hIHNvbGlkaXR5ID49MC43LjAgPDAuOS4wOwoKLy8g5Z+65ZCI57qm5LuF55So5LqO5L2/5YW257yW6K+RCmNvbnRyYWN0IEIgewogICAgY29uc3RydWN0b3IodWludCkgewogICAgfQp9CgoKY29udHJhY3QgQyB7CiAgICBjb25zdHJ1Y3Rvcih1aW50LCB1aW50KSB7CiAgICB9Cn0KCgpjb250cmFjdCBEIHsKICAgIGNvbnN0cnVjdG9yKHVpbnQpIHsKICAgIH0KfQoKCmNvbnRyYWN0IEEgaXMgQiwgQywgRCB7CiAgICB1aW50IHg7CgogICAgY29uc3RydWN0b3IodWludCBwYXJhbTEsIHVpbnQgcGFyYW0yLCB1aW50IHBhcmFtMywgdWludCBwYXJhbTQsIHVpbnQgcGFyYW01KQogICAgQihwYXJhbTEpCiAgICBDKHBhcmFtMiwgcGFyYW0zKQogICAgRChwYXJhbTQpIHsKICAgICAgICB4ID0gcGFyYW01OwogICAgfQoKCmNvbnRyYWN0IFggaXMgQiwgQywgRCB7CiAgICB1aW50IHg7CgogICAgY29uc3RydWN0b3IodWludCBwYXJhbTEsIHVpbnQgcGFyYW0yLCB1aW50IHBhcmFtMywgdWludCBwYXJhbTQsIHVpbnQgcGFyYW01KQogICAgICAgIEIocGFyYW0xKQogICAgICAgIEMocGFyYW0yLCBwYXJhbTMpCiAgICAgICAgRChwYXJhbTQpIHsKICAgICAgICAgICAgeCA9IHBhcmFtNTsKICAgICAgICB9Cn0=)
        
        ```solidity
        // SPDX-License-Identifier: GPL-3.0
        pragma solidity >=0.7.0 <0.9.0;
        
        // 基合约仅用于使其编译
        contract B {
            constructor(uint) {
            }
        }
        
        contract C {
            constructor(uint, uint) {
            }
        }
        
        contract D {
            constructor(uint) {
            }
        }
        
        contract A is B, C, D {
            uint x;
        
            constructor(uint param1, uint param2, uint param3, uint param4, uint param5)
            B(param1)
            C(param2, param3)
            D(param4) {
                x = param5;
            }
        
        contract X is B, C, D {
            uint x;
        
            constructor(uint param1, uint param2, uint param3, uint param4, uint param5)
                B(param1)
                C(param2, param3)
                D(param4) {
                    x = param5;
                }
        }
        ```
        
        在声明只有一条语句的短函数时，可以将其放在同一行。
        
        允许：
        
        [open in Remix](https://remix.ethereum.org/?#language=solidity&version=0.8.28&code=ZnVuY3Rpb24gc2hvcnRGdW5jdGlvbigpIHB1YmxpYyB7IGRvU29tZXRoaW5nKCk7IH0=)
        
        ```solidity
        function shortFunction() public { doSomething(); }
        ```
        
        这些函数声明的指南旨在提高可读性。 作者应根据自己的最佳判断使用这些指南，因为该指南并未尝试涵盖所有可能的函数声明排列。
        
        ## **映射**
        
        在变量声明中，不要在关键字 `mapping` 和其类型之间添加空格。不要在任何嵌套的 `mapping` 关键字与其类型之间添加空格。
        
        推荐： .. code-block:: solidity
        
        ```solidity
        mapping(uint => uint) map;
        mapping(address => bool) registeredAddresses;
        mapping(uint => mapping(bool => Data[])) public data;
        mapping(uint => mapping(uint => s)) data;
        ```
        
        不推荐：
        
        [open in Remix](https://remix.ethereum.org/?#language=solidity&version=0.8.28&code=bWFwcGluZyAodWludCA9PiB1aW50KSBtYXA7Cm1hcHBpbmcoIGFkZHJlc3MgPT4gYm9vbCApIHJlZ2lzdGVyZWRBZGRyZXNzZXM7Cm1hcHBpbmcgKHVpbnQgPT4gbWFwcGluZyAoYm9vbCA9PiBEYXRhW10pKSBwdWJsaWMgZGF0YTsKbWFwcGluZyh1aW50ID0+IG1hcHBpbmcgKHVpbnQgPT4gcykpIGRhdGE7)
        
        ```solidity
        mapping (uint => uint) map;
        mapping( address => bool ) registeredAddresses;
        mapping (uint => mapping (bool => Data[])) public data;
        mapping(uint => mapping (uint => s)) data;
        ```
        
        ## **变量声明**
        
        数组变量的声明之间不应在类型和括号之间添加空格。
        
        推荐： .. code-block:: solidity
        
        ```solidity
        uint[] x;
        ```
        
        不推荐：
        
        [open in Remix](https://remix.ethereum.org/?#language=solidity&version=0.8.28&code=dWludCBbXSB4Ow==)
        
        ```solidity
        uint [] x;
        ```
        
        ## **其他建议**
        
        - 字符串应使用双引号而不推荐单引号。
        
        推荐： .. code-block:: solidity
        
        ```solidity
        str = “foo”;
        str = “Hamlet says, ‘To be or not to be…’”;
        ```
        
        不推荐：
        
        [open in Remix](https://remix.ethereum.org/?#language=solidity&version=0.8.28&code=c3RyID0gJ2Jhcic7CnN0ciA9ICciQmUgeW91cnNlbGY7IGV2ZXJ5b25lIGVsc2UgaXMgYWxyZWFkeSB0YWtlbi4iIC1Pc2NhciBXaWxkZSc7)
        
        ```solidity
        str = 'bar';
        str = '"Be yourself; everyone else is already taken." -Oscar Wilde';
        ```
        
        - 操作符两侧应各有一个空格。
        
        推荐： .. code-block:: solidity
        
        > force
        > 
        > 
        > ```solidity
        > x = 3;
        > x = 100 / 10;
        > x += 3 + 4;
        > x |= y && z;
        > ```
        > 
        
        不推荐：
        
        [open in Remix](https://remix.ethereum.org/?#language=solidity&version=0.8.28&code=eD0zOwp4ID0gMTAwLzEwOwp4ICs9IDMrNDsKeCB8PSB5JiZ6Ow==)
        
        ```solidity
        x=3;
        x = 100/10;
        x += 3+4;
        x |= y&&z;
        ```
        
        - 优先级高于其他操作符的操作符可以省略周围的空格，以表示优先级。这是为了提高复杂语句的可读性。你应始终在操作符的两侧使用相同数量的空格：
        
        推荐：
        
        ```solidity
        x = 2**3 + 5;
        x = 2*y + 3*z;
        x = (a+b) * (a-b);
        ```
        
        不推荐：
        
        [open in Remix](https://remix.ethereum.org/?#language=solidity&version=0.8.28&code=eCA9IDIqKiAzICsgNTsKeCA9IHkrejsKeCArPTE7)
        
        ```solidity
        x = 2** 3 + 5;
        x = y+z;
        x +=1;
        ```
        
        ## **布局顺序**
        
        合约元素应按以下顺序布局：
        
        1. Pragma 语句
        2. 导入语句
        3. 事件
        4. 错误
        5. 接口
        6. 库
        7. 合约
        
        在每个合约、库或接口内部，使用以下顺序：
        
        1. 类型声明
        2. 状态变量
        3. 事件
        4. 错误
        5. 修改器
        6. 函数
        
        > **备注**
        > 
        > 
        > 在事件或状态变量中，声明类型可能更清晰。
        > 
        
        推荐：
        
        ```solidity
        // SPDX-License-Identifier: GPL-3.0 pragma solidity >=0.8.4 <0.9.0;
        
        abstract contract Math {
            error DivideByZero();
            function divide(int256 numerator, int256 denominator) public virtual returns (uint256);
        }
        ```
        
        不推荐：
        
        [open in Remix](https://remix.ethereum.org/?#language=solidity&version=0.8.28&code=Ly8gU1BEWC1MaWNlbnNlLUlkZW50aWZpZXI6IEdQTC0zLjAKcHJhZ21hIHNvbGlkaXR5ID49MC44LjQgPDAuOS4wOwoKYWJzdHJhY3QgY29udHJhY3QgTWF0aCB7CiAgICBmdW5jdGlvbiBkaXZpZGUoaW50MjU2IG51bWVyYXRvciwgaW50MjU2IGRlbm9taW5hdG9yKSBwdWJsaWMgdmlydHVhbCByZXR1cm5zICh1aW50MjU2KTsKICAgIGVycm9yIERpdmlkZUJ5WmVybygpOwp9)
        
        ```solidity
        // SPDX-License-Identifier: GPL-3.0
        pragma solidity >=0.8.4 <0.9.0;
        
        abstract contract Math {
            function divide(int256 numerator, int256 denominator) public virtual returns (uint256);
            error DivideByZero();
        }
        ```
        
        ## **命名规范**
        
        命名规范在广泛采用和使用时是强大的。使用不同的规范可以传达重要的 *元* 信息，这些信息在其他情况下可能不会立即可用。
        
        这里给出的命名建议旨在提高可读性，因此它们不推荐规则，而是旨在帮助通过事物的名称传达最多信息的指南。
        
        最后，代码库中的一致性应始终优先于本文件中概述的任何规范。
        
        ## **命名风格**
        
        为避免混淆，以下名称将用于指代不同的命名风格。
        
        - `b` (单个小写字母)
        - `B` (单个大写字母)
        - `lowercase` （小写）
        - `UPPERCASE` （大写）
        - `UPPER_CASE_WITH_UNDERSCORES` （大写和下划线）
        - `CapitalizedWords` (驼峰式，首字母大写）
        - `mixedCase` (混合式，与驼峰式的区别在于首字母小写！)
        
        > **备注**
        > 
        > 
        > 在使用 CapWords 中的首字母缩略词时，所有字母都应大写。 因此，HTTPServerError 比 HttpServerError 更好。 在使用 mixedCase 中的首字母缩略词时，所有字母都应大写，除非名称开头的第一个字母小写。 因此，xmlHTTPRequest 比 XMLHTTPRequest 更好。
        > 
        
        ### **避免使用的名称**
        
        - `l` - el的小写方式
        - `O` - oh的大写方式
        - `I` - eye的大写方式
        
        切勿将这些用作单字母变量名。它们通常与数字 1 和 0 无法区分。
        
        ### **合约和库名称**
        
        - 合约和库应使用 CapWords 风格命名。示例：`SimpleToken`、`SmartBank`、`CertificateHashRepository`、`Player`、`Congress`、`Owned`。
        - 合约和库名称应与其文件名匹配。
        - 如果一个合约文件包含多个合约和/或库，则文件名应与 *核心合约* 匹配。然而，如果可以避免，这并不推荐。
        
        如下面的示例所示，如果合约名称为 `Congress`，库名称为 `Owned`，则它们的关联文件名应为 `Congress.sol` 和 `Owned.sol`。
        
        推荐：
        
        ```solidity
        // SPDX-License-Identifier: GPL-3.0 pragma solidity >=0.7.0 <0.9.0;
        
        // Owned.sol
        contract Owned {
        
            address public owner;
        
            modifier onlyOwner {
                require(msg.sender == owner); _;
            }
        
            constructor() {
                owner = msg.sender;
            }
        
            function transferOwnership(address newOwner) public onlyOwner {
                owner = newOwner;
            }
        }
        ```
        
        在 `Congress.sol` 中：
        
        [open in Remix](https://remix.ethereum.org/?#language=solidity&version=0.8.28&code=Ly8gU1BEWC1MaWNlbnNlLUlkZW50aWZpZXI6IEdQTC0zLjAKcHJhZ21hIHNvbGlkaXR5ID49MC40LjAgPDAuOS4wOwoKaW1wb3J0ICIuL093bmVkLnNvbCI7CgoKY29udHJhY3QgQ29uZ3Jlc3MgaXMgT3duZWQsIFRva2VuUmVjaXBpZW50IHsKICAgIC8vLi4uCn0=)
        
        ```solidity
        // SPDX-License-Identifier: GPL-3.0
        pragma solidity >=0.4.0 <0.9.0;
        
        import "./Owned.sol";
        
        contract Congress is Owned, TokenRecipient {
            //...
        }
        ```
        
        不推荐：
        
        [open in Remix](https://remix.ethereum.org/?#language=solidity&version=0.8.28&code=Ly8gU1BEWC1MaWNlbnNlLUlkZW50aWZpZXI6IEdQTC0zLjAKcHJhZ21hIHNvbGlkaXR5ID49MC43LjAgPDAuOS4wOwoKLy8gb3duZWQuc29sCmNvbnRyYWN0IG93bmVkIHsKICAgIGFkZHJlc3MgcHVibGljIG93bmVyOwoKICAgIG1vZGlmaWVyIG9ubHlPd25lciB7CiAgICAgICAgcmVxdWlyZShtc2cuc2VuZGVyID09IG93bmVyKTsKICAgICAgICBfOwogICAgfQoKICAgIGNvbnN0cnVjdG9yKCkgewogICAgICAgIG93bmVyID0gbXNnLnNlbmRlcjsKICAgIH0KCiAgICBmdW5jdGlvbiB0cmFuc2Zlck93bmVyc2hpcChhZGRyZXNzIG5ld093bmVyKSBwdWJsaWMgb25seU93bmVyIHsKICAgICAgICBvd25lciA9IG5ld093bmVyOwogICAgfQp9)
        
        ```solidity
        // SPDX-License-Identifier: GPL-3.0
        pragma solidity >=0.7.0 <0.9.0;
        
        // owned.sol
        contract owned {
            address public owner;
        
            modifier onlyOwner {
                require(msg.sender == owner);
                _;
            }
        
            constructor() {
                owner = msg.sender;
            }
        
            function transferOwnership(address newOwner) public onlyOwner {
                owner = newOwner;
            }
        }
        ```
        
        在 `Congress.sol` 中：
        
        [open in Remix](https://remix.ethereum.org/?#language=solidity&version=0.8.28&code=Ly8gU1BEWC1MaWNlbnNlLUlkZW50aWZpZXI6IEdQTC0zLjAKcHJhZ21hIHNvbGlkaXR5IF4wLjcuMDsKCgppbXBvcnQgIi4vb3duZWQuc29sIjsKCgpjb250cmFjdCBDb25ncmVzcyBpcyBvd25lZCwgdG9rZW5SZWNpcGllbnQgewogICAgLy8uLi4KfQ==)
        
        ```solidity
        // SPDX-License-Identifier: GPL-3.0
        pragma solidity ^0.7.0;
        
        import "./owned.sol";
        
        contract Congress is owned, tokenRecipient {
            //...
        }
        ```
        
        ## **结构体名称**
        
        结构体名称应该使用驼峰式风格。示例：`MyCoin`、`Position`、`PositionXY`。
        
        ## **事件名称**
        
        事件名称应该使用驼峰式风格。示例：`Deposit`、`Transfer`、`Approval`、`BeforeTransfer`、`AfterTransfer`。
        
        ## **函数名称**
        
        函数应该使用混合式命名风格。示例：`getBalance`、`transfer`、`verifyOwner`、`addMember`、`changeOwner`。
        
        ## **函数参数名称**
        
        函数参数命名应该使用混合式命名风格。示例：`initialSupply`、`account`、`recipientAddress`、`senderAddress`、`newOwner`。
        
        在编写操作自定义结构的库函数时，结构应为第一个参数，并始终命名为 `self`。
        
        ## **局部和状态变量名称**
        
        使用混合式命名风格。示例：`totalSupply`、`remainingSupply`、`balancesOf`、`creatorAddress`、`isPreSale`、`tokenExchangeRate`。
        
        ## **常量**
        
        常量应使用全大写字母，并用下划线分隔单词。示例：`MAX_BLOCKS`、`TOKEN_NAME`、`TOKEN_TICKER`、`CONTRACT_VERSION`。
        
        ## **修改器名称**
        
        使用混合式命名风格。示例：`onlyBy`、`onlyAfter`、`onlyDuringThePreSale`。
        
        ## **枚举**
        
        在声明简单类型时，枚举应该使用驼峰式风格。示例：`TokenGroup`、`Frame`、`HashStyle`、`CharacterLocation`。
        
        ## **避免命名冲突**
        
        - `singleTrailingUnderscore_`
        
        当所需名称与现有状态变量、函数、内置或其他保留名称冲突时，建议使用此规范。
        
        ## **非外部函数和变量的下划线前缀**
        
        - `_singleLeadingUnderscore`
        
        建议对非外部函数和状态变量（`private` 或 `internal`）使用此规范。未指定可见性的状态变量默认是 `internal`。
        
        在设计智能合约时，面向公众的 API（任何账户都可以调用的函数）是一个重要的考虑因素。 前导下划线使你能够立即识别此类函数的意图，但更重要的是，如果你将函数从非外部更改为外部（包括 `public`）并相应重命名，这将迫使你在重命名时检查每个调用点。 这可以作为防止意外外部函数的重要手动检查，并且是常见的安全漏洞来源（避免使用查找替换所有工具进行此更改）。
        
        # **NatSpec**
        
        Solidity 合约还可以包含 NatSpec 注释。它们使用三重斜杠（`///`）或双星号块（`/** ... */`）编写，应该直接放在函数声明或语句上方。
        
        例如，来自 [a simple smart contract](https://learnblockchain.cn/docs/solidity/introduction-to-smart-contracts.html#simple-smart-contract) 的合约，添加注释后的样子如下：
        
        [open in Remix](https://remix.ethereum.org/?#language=solidity&version=0.8.28&code=Ly8gU1BEWC1MaWNlbnNlLUlkZW50aWZpZXI6IEdQTC0zLjAKcHJhZ21hIHNvbGlkaXR5ID49MC40LjE2IDwwLjkuMDsKCi8vLyBAYXV0aG9yIFRoZSBTb2xpZGl0eSBUZWFtCi8vLyBAdGl0bGUgQSBzaW1wbGUgc3RvcmFnZSBleGFtcGxlCmNvbnRyYWN0IFNpbXBsZVN0b3JhZ2UgewogICAgdWludCBzdG9yZWREYXRhOwoKICAgIC8vLyBTdG9yZSBgeGAuCiAgICAvLy8gQHBhcmFtIHggdGhlIG5ldyB2YWx1ZSB0byBzdG9yZQogICAgLy8vIEBkZXYgc3RvcmVzIHRoZSBudW1iZXIgaW4gdGhlIHN0YXRlIHZhcmlhYmxlIGBzdG9yZWREYXRhYAogICAgZnVuY3Rpb24gc2V0KHVpbnQgeCkgcHVibGljIHsKICAgICAgICBzdG9yZWREYXRhID0geDsKICAgIH0KCiAgICAvLy8gUmV0dXJuIHRoZSBzdG9yZWQgdmFsdWUuCiAgICAvLy8gQGRldiByZXRyaWV2ZXMgdGhlIHZhbHVlIG9mIHRoZSBzdGF0ZSB2YXJpYWJsZSBgc3RvcmVkRGF0YWAKICAgIC8vLyBAcmV0dXJuIHRoZSBzdG9yZWQgdmFsdWUKICAgIGZ1bmN0aW9uIGdldCgpIHB1YmxpYyB2aWV3IHJldHVybnMgKHVpbnQpIHsKICAgICAgICByZXR1cm4gc3RvcmVkRGF0YTsKICAgIH0KfQ==)
        
        ```solidity
        // SPDX-License-Identifier: GPL-3.0
        pragma solidity >=0.4.16 <0.9.0;
        
        /// @author The Solidity Team
        /// @title A simple storage example
        contract SimpleStorage {
            uint storedData;
        
            /// Store `x`.
            /// @param x the new value to store
            /// @dev stores the number in the state variable `storedData`
            function set(uint x) public {
                storedData = x;
            }
        
            /// Return the stored value.
            /// @dev retrieves the value of the state variable `storedData`
            /// @return the stored value
            function get() public view returns (uint) {
                return storedData;
            }
        }
        ```
        
        建议 Solidity 合约对所有公共接口（ABI 中的所有内容）进行全面注释，使用 [NatSpec](https://learnblockchain.cn/docs/solidity/natspec-format.html#natspec)。
        
        有关详细说明，请参见 [NatSpec](https://learnblockchain.cn/docs/solidity/natspec-format.html#natspec) 部分。
        
- 使用OpenZeppelin库进行合约开发
    - [**什么是 ERC20?**](https://decert.me/tutorial/solidity/solidity-practice/erc20)
        
        [ERC20](https://eips.ethereum.org/EIPS/eip-20) 是 Ethereum 网络上最出名且应用最广的代币标准之一。它提供了一个统一的接口标准，用于创建可互换代币，这些代币可以用来代表任何事物，从货币到积分等。
        
        该标准定义了一组 API（应用程序编程接口），涉及到代币在智能合约中的转移方式，如何获取数据（比如各账户的代币余额），以及如何接收、记录和使用这些代币。
        
        ### **ERC20 核心方法和事件[​](https://decert.me/tutorial/solidity/solidity-practice/erc20#erc20-%E6%A0%B8%E5%BF%83%E6%96%B9%E6%B3%95%E5%92%8C%E4%BA%8B%E4%BB%B6)**
        
        ERC20 标准主要定义了以下几个方法和两个事件：
        
        方法：
        
        - `name() public view returns (string)`：可选；返回一个字符串值，表示代币的名称
        - `symbol() public view returns (string)`：可选；返回一个字符串值，表示代币的简写或缩写。
        - `decimals() public view returns (uint8)`：可选；返回一个 uint8 类型的值，表示代币可以分割到的小数位数。许多代币选择`18`为其小数值，因为这是 Ether(ETH) 使用的小数位数
        - `totalSupply() public view returns (uint256)`：返回代币的总供应量
        - `balanceOf(address _owner) public view returns (uint256 balance)`：返回特定地址(_owner)的代币余额
        - `transfer(address _to, uint256 _value) public returns (bool success)`：从调用者的地址转移 _value 量的代币到地址 _to，成功返回 true
        - `transferFrom(address _from, address _to, uint256 _value) public returns (bool success)`：允许 _spender 从 _from 转移 _value 量的代币到 _to
        - `approve(address _spender, uint256 _value) public returns (bool success)`：允许 _spender 从调用者的账户多次取回总共 _value 量的代币
        - `allowance(address _owner, address _spender) public view returns (uint256 remaining)`：返回 _spender 仍然被允许从 _owner 提取的代币数量
        
        事件：
        
        - `Transfer(address indexed _from, address indexed _to, uint256 _value)`：在代币被转移时触发。
        - `Approval(address indexed _owner, address indexed _spender, uint256 _value)`：在调用 approve 方法时触发。
        
        ## **编写一个简单的 ERC20 代币合约[​](https://decert.me/tutorial/solidity/solidity-practice/erc20#%E7%BC%96%E5%86%99%E4%B8%80%E4%B8%AA%E7%AE%80%E5%8D%95%E7%9A%84-erc20-%E4%BB%A3%E5%B8%81%E5%90%88%E7%BA%A6)**
        
        下面是一个简单的 ERC20 代币合约的实现
        
        ```solidity
        // SPDX-License-Identifier: MIT
        pragma solidity ^0.8.0;
        
        contract BaseERC20 {
            string public name; 
            string public symbol; 
            uint8 public decimals; 
        
            uint256 public totalSupply; 
        
            mapping (address => uint256) balances; 
        
            mapping (address => mapping (address => uint256)) allowances; 
        
            event Transfer(address indexed from, address indexed to, uint256 value);
            event Approval(address indexed owner, address indexed spender, uint256 value);
        
            constructor() {
                name = "MyToken"; 
                symbol = "MTK"; 
                decimals = 18; 
                totalSupply = 100000000 * 10 ** uint256(decimals);
        
                balances[msg.sender] = totalSupply;  
            }
        
            function balanceOf(address _owner) public view returns (uint256 balance) {
                return balances[_owner];    
            }
        
            function transfer(address _to, uint256 _value) public returns (bool success) {
                require(balances[msg.sender] >= _value, "ERC20: transfer amount exceeds balance");
        
                balances[msg.sender] -= _value;    
                balances[_to] += _value;   
        
                emit Transfer(msg.sender, _to, _value);  
                return true;   
            }
        
            function transferFrom(address _from, address _to, uint256 _value) public returns (bool success) {
                require(balances[_from] >= _value, "ERC20: transfer amount exceeds balance");
                require(allowances[_from][msg.sender] >= _value,"ERC20: transfer amount exceeds allowance");
        
                balances[_from] -= _value; 
                balances[_to] += _value; 
        
                allowances[_from][msg.sender] -= _value;
                
                emit Transfer(_from, _to, _value); 
                return true; 
            }
        
            function approve(address _spender, uint256 _value) public returns (bool success) {
                allowances[msg.sender][_spender] = _value; 
                emit Approval(msg.sender, _spender, _value); 
                return true; 
            }
        
            function allowance(address _owner, address _spender) public view returns (uint256 remaining) {
                return allowances[_owner][_spender];
            }
        }
        ```
        
    - [**使用 OpenZeppelin 创建 ERC20 代币**](https://decert.me/tutorial/solidity/solidity-practice/erc20#%E4%BD%BF%E7%94%A8-openzeppelin-%E5%88%9B%E5%BB%BA-erc20-%E4%BB%A3%E5%B8%81)
        
        [OpenZeppelin](https://docs.openzeppelin.com/contracts/5.x/) 是一个开源的区块链开发框架，它提供了安全的合约模板来简化开发过程。
        
        对于想要创建 ERC20 代币的开发者来说，使用 OpenZeppelin 可以极大地简化开发过程，因为它内置了遵守 ERC20 标准的可复用合约，这些合约经过严格审计，能够减少潜在的安全风险。
        
        安装好 OpenZeppelin 库后（安装方法见前面的 [Hardhat 开发框架](https://decert.me/tutorial/solidity/tools/hardhat) 或者 [Foundry 开发框架](https://decert.me/tutorial/solidity/tools/foundry)），你可以开始编写你的 ERC20 代币合约。
        
        OpenZeppelin 提供了一些基础合约，你可以通过继承和扩展这些合约来创建你自己的代币合约。
        
        下面是使用 OpenZeppelin 创建一个简单的 ERC20 代币的示例：
        
        ```solidity
        // SPDX-License-Identifier: MIT
        pragma solidity ^0.8.0;
        
        // 导入 OpenZeppelin 提供的 ERC20 标准合约
        import "@openzeppelin/contracts/token/ERC20/ERC20.sol";
        
        // 创建一个新的合约，继承自 OpenZeppelin 的 ERC20 合约
        contract MyToken is ERC20 {
            // 构造函数将初始化 ERC20 供应量和代币名称
            constructor(uint256 initialSupply) ERC20("MyToken", "MTK") {
                // 通过 _mint 函数铸造初始供应量的代币到部署合约的地址
                _mint(msg.sender, initialSupply);
            }
        }
        ```
        
        ### **常见扩展合约[​](https://decert.me/tutorial/solidity/solidity-practice/erc20#%E5%B8%B8%E8%A7%81%E6%89%A9%E5%B1%95%E5%90%88%E7%BA%A6)**
        
        在使用 OpenZeppelin 库时，有多种用于扩展功能的合约可用。这些合约经过专门的设计以增加如访问控制、代币经济机制（如燃烧和铸造）以及安全功能（如防止重入攻击）等功能。
        
        常见的扩展合约示例
        
        1. 燃烧代币:
            
            ERC20Burnable：允许代币持有者销毁（burn）一定数量的代币，从而从流通中永久移除这些代币。
            
        2. 暂停合约:
            
            ERC20Pausable：允许合约的管理员暂停合约的所有操作，这在遇到安全问题时是一种非常有用的应急措施。
            
        3. 授权代币使用：
            
            ERC20Permit：允许代币持有者通过签署一个允许他人在其帐户上花费特定数量代币的许可，从而通过一次交易执行授权。这种机制使用了 EIP-2612 提案中定义的方法。
            
        
        ### **实现扩展合约[​](https://decert.me/tutorial/solidity/solidity-practice/erc20#%E5%AE%9E%E7%8E%B0%E6%89%A9%E5%B1%95%E5%90%88%E7%BA%A6)**
        
        要实现一个扩展合约，开发者需要根据需求选择合适的 OpenZeppelin 基础合约，并通过 Solidity 的 `is` 关键字来继承它。
        
        下面是一个简单的示例，说明如何创建一个可燃烧的 ERC20 代币：
        
        ```solidity
        // SPDX-License-Identifier: MIT
        pragma solidity ^0.8.0;
        
        import "@openzeppelin/contracts/token/ERC20/ERC20.sol";
        import "@openzeppelin/contracts/token/ERC20/extensions/ERC20Burnable.sol";
        
        contract MyToken is ERC20, ERC20Burnable {
            constructor(uint256 initialSupply) ERC20("MyToken", "MTK") {
                _mint(msg.sender, initialSupply);
            }
        }
        
        ```
        
        在这个例子中，MyToken 继承了 OpenZeppelin 的 ERC20 和 ERC20Burnable 合约。这样，它就拥有了 ERC20 代币的标准功能，并加上了可以燃烧代币的能力。
        
        在 ERC20Burnable 合约中，`burn` 函数的实现通常如下：
        
        ```solidity
        function burn(uint256 amount) public virtual {
            _burn(_msgSender(), amount);
        }
        ```
        
        此函数由代币的持有者调用，并需要一个参数 `amount`，表示要销毁的代币数量。函数调用 `_burn` 方法，此方法定义在 OpenZeppelin 的 ERC20 基础合约中，用于从执行操作的地址中减少相应数量的代币，并相应减少总供应量。
        
        ## **总结[​](https://decert.me/tutorial/solidity/solidity-practice/erc20#%E6%80%BB%E7%BB%93)**
        
        此章节的目的是向你展示如何从零开始创建一个遵循 ERC20 标准的代币合约，理解其方法的实际应用，以及如何利用 OpenZeppelin 合约来简化开发过程。
        
- 使用Hardhat进行测试与调试
    - [**Hardhat 开发框架**](https://decert.me/tutorial/solidity/tools/hardhat)
        
        # **Hardhat 开发框架**
        
        > 如果你已经熟练使用 Hardhat ，可以直接跳到Hardhat 挑战 ，完成挑战领取技能认证 NFT。
        > 
        
        Hardhat 提供了一个灵活且易于使用的开发环境，可以轻松地编写、测试和部署智能合约。Hardhat 使用 Node 进行包管理，如果你熟悉 Node 及 Javascript， Hardhat 将非常容易上手。
        
        **Hardhat**还内置了**Hardhat 网络（Hardhat Node）**，它是为开发而设计的本地以太坊网络。 用来部署合约，运行测试和**调试代码**。
        
        在本文中，我们将介绍：
        
        1. 创建及配置Hardhat项目
        2. 编写智能合约
        3. Hardhat 编译合约
        4. 使用 [Ethers.js](https://learnblockchain.cn/ethers_v5/) 来为合约编写自动化测试
        5. 使用 `console.log()`调试 Solidity
        6. 使用 Hardhat 部署合约
        7. 使用 Hardhat Etherscan 进行开源验证。
        
        本文对应的代码在：[https://github.com/xilibi2003/training_camp_2/tree/main/w1_hardhat](https://github.com/xilibi2003/training_camp_2/tree/main/w1_hardhat)
        
    - [**编写测试用例**](https://decert.me/tutorial/solidity/tools/hardhat)
        
        ## **编写测试用例[​](https://decert.me/tutorial/solidity/tools/hardhat#%E7%BC%96%E5%86%99%E6%B5%8B%E8%AF%95%E7%94%A8%E4%BE%8B)**
        
        为智能合约编写自动化测试至关重要，因为事关用户资金。
        
        在我们的测试中，使用 Harhdat 内置的网络，使用[ethers.js](https://learnblockchain.cn/ethers_v5/)与前面的合约进行交互，并使用 [Mocha](https://mochajs.org/) 作为测试运行器。
        
        在项目 `test`下，并创建一个名为`Counter.js`的新文件：
        
        **Counter.js**
        
        ```solidity
        const { ethers } = require("hardhat");
        const { expect } = require("chai");
        
        let counter;
        
        describe("Counter", function () {
          async function init() {
            const [owner, otherAccount] = await ethers.getSigners();
            const Counter = await ethers.getContractFactory("Counter");
            counter = await Counter.deploy();
            await counter.deployed();
            console.log("counter:" + counter.address);
          }
        
          before(async function () {
            await init();
          });
        
        //
          it("init equal 0", async function () {
            expect(await counter.get()).to.equal(0);
          });
        
          it("add 1 equal 1", async function () {
            let tx = await counter.count();
            await tx.wait();
            expect(await counter.get()).to.equal(1);
          });
        
        });
        ```
        
        在终端上运行`npx hardhat test`。 你应该看到以下输出：
        
        ```solidity
        > npx hardhat test
        
          Counter
        counter:0x5FbDB2315678afecb367f032d93F642f64180aa3
            ✔ init equal 0
            ✔ add 1 equal 1
        
          2 passing (1s)
        
        ```
        
        这意味着测试通过了。 现在我们解释下主要代码：
        
        ```solidity
          const Counter = await ethers.getContractFactory("Counter");
        ```
        
        ethers.js中的`ContractFactory`是用于部署新智能合约的抽象，因此此处的`Counter`是用来实例合约的工厂。
        
        ```solidity
        counter = await Counter.deploy();
        ```
        
        在`ContractFactory`上调用`deploy()`将启动部署，并返回解析为`Contract`的`Promise`。 该对象包含了智能合约所有函数的方法。
        
        ```solidity
        let tx = await counter.count();
        await tx.wait();
        ```
        
        在`counter` 上调用合约方法， 并等待交易执行完毕。
        
        注意，默认情况下， `ContractFactory`和`Contract`实例连接到第一个[签名者（Singer）](https://learnblockchain.cn/ethers_v5/)。
        
        若需要使用其他的签名者， 可以使用[合约实例connect 到另一个签名者](https://learnblockchain.cn/ethers_v5/api/contract/example/#example-erc-20-contract--methods)， 如 `counter.connect(otherAccount)`
        
        ```solidity
        expect(await counter.get()).to.equal(0);
        ```
        
        判断相等，我们使用[Chai](https://www.chaijs.com/)，这是一个断言库。 这些断言函数称为“匹配器”，在此实际上使用的“匹配器”来自[Hardhat Chai Matchers](https://hardhat.org/hardhat-runner/plugins/nomicfoundation-hardhat-chai-matchers)。
        
- 合约的升级模式（如代理合约）
    - [**代理合约**](https://decert.me/tutorial/solidity/solidity-adv/proxy)
        
        ## **简介[​](https://decert.me/tutorial/solidity/solidity-adv/proxy#%E7%AE%80%E4%BB%8B)**
        
        代理合约是一种特定类型的智能合约，其主要作用是作为其他合约的代表或中介。这些代理合约允许用户通过它们与区块链进行直接交互，同时管理一个指向实际执行逻辑的逻辑合约（也称为实现合约）的地址。通过此机制，即使底层业务逻辑发生变化，用户与区块链的交互方式也能保持一致。
        
        ## **代理合约的作用：[​](https://decert.me/tutorial/solidity/solidity-adv/proxy#%E4%BB%A3%E7%90%86%E5%90%88%E7%BA%A6%E7%9A%84%E4%BD%9C%E7%94%A8)**
        
        1. 可升级性
            
            区块链上一旦部署的合约代码是不可改变的，这意味着无法直接修改合约中的错误。通过使用代理合约，开发者可以简单地更新指向一个新的、已修改的逻辑合约，从而不需要更改原有代理合约的情况下更新智能合约逻辑。
            
            例如，如果发现原合约中存在安全隐患，通过更换逻辑合约可以快速响应，修复漏洞。
            
        2. 节省成本
            
            在以太坊网络上部署新的智能合约通常需要消耗大量的 gas。在一些应用中，如代币发行，可能需要为每个新项目部署一个新的合约。如果每个合约都有类似的功能，这将导致巨大的 gas 费用。
            
            可以为所有项目只部署一个代币逻辑合约，并部署轻量级的代理合约，指向同一逻辑合约。这样，每个代理合约的部署成本都会比完整功能合约要低得多，从而大量节约 gas。
            
        
        ## **基本原理及组件[​](https://decert.me/tutorial/solidity/solidity-adv/proxy#%E5%9F%BA%E6%9C%AC%E5%8E%9F%E7%90%86%E5%8F%8A%E7%BB%84%E4%BB%B6)**
        
        1. 代理合约:
            - 用户实际上是与代理合约进行交互
            - 代理合约管理自身的状态变量
            - 代理合约通常包含一个指向逻辑合约的地址变量
        2. 逻辑合约:
            - 负责业务逻辑的实现
            - 可以升级而不影响由代理合约维护的数据。例如，可以更新计算公式或增加新的功能，而不改变用户的数据结构
        
        ## **实现步骤[​](https://decert.me/tutorial/solidity/solidity-adv/proxy#%E5%AE%9E%E7%8E%B0%E6%AD%A5%E9%AA%A4)**
        
        1. 创建逻辑合约
            
            首先，开发者需要编写一个包含实际业务逻辑的合约，比如：
            
            ```solidity
            // SPDX-License-Identifier: MIT
             pragma solidity ^0.8.0;
            
             contract Logic{
                 address public logicAddress;// 没用上，但是这里占位是为了防止存储冲突
                 uint public count;
            
                 function incrementCounter() public {
                     count += 1;
                 }
            
                 function getCount() public view returns (uint) {
                     return count;
                 }
             }
            
            ```
            
            注意合约中的状态变量`logicAddress`，是为了避免与`Proxy`合约存储冲突，下面会介绍。
            
        2. 编写代理合约
            
            在代理合约中，任何非直接调用的函数都会通过`fallback`函数被重定向并使用`delegatecall`在逻辑合约上执行。这种机制允许代理合约借用逻辑合约的代码进行操作，但是在自己的存储上下文中执行。
            
            代码示例如下
            
            ```solidity
            // SPDX-License-Identifier: MIT
             pragma solidity ^0.8.0;
            
                 contract Proxy {
                     address public logicAddress;
                     uint public count;
            
                     constructor(address _logic) {
                         logicAddress = _logic;
                     }
            
            // 确保只有可信的地址可以更新逻辑合约地址
                     function upgradeLogic(address _newLogic) public {
                         logicAddress = _newLogic;
                     }
            
                     fallback() external payable {
                         _fallback(logicAddress);
                     }
            
                     receive() external payable {
                         _fallback(logicAddress);
                     }
            
                     function _fallback(address logic) internal {
            // 通过 delegatecall 调用逻辑合约的函数，并返回数据
                         assembly {
            // Copy msg.data. We take full control of memory in this inline assembly
            // block because it will not return to Solidity code. We overwrite the
            // Solidity scratch pad at memory position 0.
                             calldatacopy(0, 0, calldatasize())
            
            // Call the implementation.
            // out and outsize are 0 because we don't know the size yet.
                             let result := delegatecall(gas(), logic, 0, calldatasize(), 0, 0)
            
            // Copy the returned data.
                             returndatacopy(0, 0, returndatasize())
            
                             switch result
            // delegatecall returns 0 on error.
                             case 0 {
                                 revert(0, returndatasize())
                             }
                             default {
                                 return(0, returndatasize())
                             }
                         }
                     }
                 }
             }
            
            ```
            
        3. 合约部署与使用:
            - 部署逻辑合约，并记下其地址
            - 使用逻辑合约的地址作为参数，部署代理合约
            - 通过代理合约地址调用逻辑合约的功能
        
        当需要升级合约时，只需部署新的逻辑合约并更新（调用`upgradeLogic()`方法）代理合约中的逻辑合约地址。这允许合约升级而不丢失任何现有数据。
        
        安全考虑
        
        - 权限控制：确保只有可信的地址（如合约的拥有者或管理员）可以更新逻辑合约地址
        - 数据和代码分离：正确处理存储布局和逻辑实现的分离，防止存储冲突（下面会介绍）
        
        [**Delegate Call 和 存储冲突**](https://decert.me/tutorial/solidity/solidity-adv/proxy#delegate-call-%E5%92%8C-%E5%AD%98%E5%82%A8%E5%86%B2%E7%AA%81)
        
    - [**可升级合约**](https://decert.me/tutorial/solidity/solidity-adv/proxy-upgradeable)
        
        在区块链开发中，智能合约一旦部署即无法更改是一个众所周知的限制。随着业务需求的变化，我们可能需要对已部署的合约进行修改或升级，而代理合约是解决这个问题的一种有效方式。
        
        本章将深入介绍代理合约的不同升级模式，帮助开发者实现灵活且安全的智能合约系统。
        
        前面已介绍了代理合约。通过代理合约进行升级的关键在于将状态存储与业务逻辑分离，其中代理负责状态存储，而业务逻辑则是可以替换的。
        
        ## **代理合约升级模式[​](https://decert.me/tutorial/solidity/solidity-adv/proxy-upgradeable#%E4%BB%A3%E7%90%86%E5%90%88%E7%BA%A6%E5%8D%87%E7%BA%A7%E6%A8%A1%E5%BC%8F)**
        
        常见的升级模式：透明代理模式、UUPS（Universal Upgradeable Proxy Standard，通用升级代理标准）模式，每种模式有其特点和使用场景，接下来将逐一介绍。
        
        - **透明代理模式[​](https://decert.me/tutorial/solidity/solidity-adv/proxy-upgradeable#%E9%80%8F%E6%98%8E%E4%BB%A3%E7%90%86%E6%A8%A1%E5%BC%8F)**
            
            在透明代理模式中，存在一个管理员地址独有的权限来升级智能合约。用户与合约的交互对用户完全透明，即用户不需要了解幕后的实现合约。
            
            所有的调用都是通过代理合约进行的。当调用代理合约时，它会检查调用者是否为管理员。如果是管理员调用管理功能（例如更改实现合约），调用直接在代理合约中处理。如果是普通用户调用业务逻辑，代理合约将使用`delegatecall`指令将调用委托给当前的实现合约。
            
            简单透明代理合约例子：
            
            ```solidity
            // SPDX-License-Identifier: MIT
            pragma solidity ^0.8.0;
            
            // 逻辑合约
            contract TransparentLogic {
                address public logicAddress; // 防止存储冲突
                address public adminAddress; // 防止存储冲突
                uint public count;
            
                function incrementCounter() public {
                    count += 1;
                }
            
                function getCount() public view returns (uint) {
                    return count;
                }
            }
            
            // 代理合约
            contract TransparentProxy {
                address public logicAddress; // 逻辑合约地址
                address public adminAddress; // 管理员地址
                uint public count;
            
                constructor(address logic) {
                    logicAddress = logic;
                    adminAddress = msg.sender;
                }
            
                function upgrade(address newLogic) public {
                    require(msg.sender == adminAddress, "Only admin"); // 限制了只能是管理员才能调用
                    logicAddress = newLogic;
                }
            
                fallback() external payable {
                    require(msg.sender != adminAddress, "Admin not allowed"); // 限制了调用者不能是管理员
                    _fallback(logicAddress);
                }
            
                receive() external payable {
                    _fallback(logicAddress);
                }
            
                function _fallback(address logic) internal {
                    assembly {
                        // Copy msg.data. We take full control of memory in this inline assembly
                        // block because it will not return to Solidity code. We overwrite the
                        // Solidity scratch pad at memory position 0.
                        calldatacopy(0, 0, calldatasize())
            
                        // Call the implementation.
                        // out and outsize are 0 because we don't know the size yet.
                        let result := delegatecall(gas(), logic, 0, calldatasize(), 0, 0)
            
                        // Copy the returned data.
                        returndatacopy(0, 0, returndatasize())
            
                        switch result
                        // delegatecall returns 0 on error.
                        case 0 {
                            revert(0, returndatasize())
                        }
                        default {
                            return(0, returndatasize())
                        }
                    }
                }
            }
            ```
            
            合约代码解释：
            
            - TransparentProxy.upgrade() 函数用来升级合约（替换旧的实现合约地址），限制了只能是管理员才能调用
            - TransparentProxy.fallback() 函数会检查调用者是不是管理员，调用者为非管理员时才通过`delegatecall`委托实现合约调用
        - **UUPS 模式[​](https://decert.me/tutorial/solidity/solidity-adv/proxy-upgradeable#uups-%E6%A8%A1%E5%BC%8F)**
            
            UUPS（Universal Upgradeable Proxy Standard，通用升级代理标准）模式通过实现合约本身的逻辑来控制升级的过程。
            
            UUPS 中的实现合约包括业务逻辑和升级逻辑。实现合约内有一个专门的函数用于修改存储实现合约地址的变量，这使得实现合约可以更改其自身的逻辑。当需要升级合约时，通过在实现合约中调用一个特殊的更新函数来更改指向新合约的地址，从而实现逻辑的更换，同时保留存储在代理合约中的状态数据。
            
            简单 UUPS 代理合约例子：
            
            ```solidity
            // 实现合约
            contract UUPSLogic {
                address public logicAddress; // 防止存储冲突
                address public adminAddress; // 防止存储冲突
                uint public count;
            
                function incrementCounter() public {
                    count += 1;
                }
            
                function getCount() public view returns (uint) {
                    return count;
                }
            
                function upgrade(address newLogic) public {
                    require(msg.sender == adminAddress, "Only admin");
                    logicAddress = newLogic;
                }
            }
            
            // 代理合约
            contract UUPSProxy {
                address public logicAddress; // 逻辑合约地址
                address public adminAddress; // 管理员地址
                uint public count;
            
                constructor(address logic) {
                    logicAddress = logic;
                    adminAddress = msg.sender;
                }
            
                fallback() external payable {
                    _fallback(logicAddress);
                }
            
                receive() external payable {
                    _fallback(logicAddress);
                }
            
                function _fallback(address logic) internal {
                    // ... 
                }
            }
            ```
            
            与透明代理模式不同，升级函数（`upgrade()`）位于实现合约中，而代理合约中不存在升级的逻辑。另外，`fallback()`函数不需要检查调用者是否是管理员，可以节省gas。
            
        - [**EIP-1967**](https://decert.me/tutorial/solidity/solidity-adv/proxy-upgradeable#eip-1967)
            
            在实际生产活动中，未了避免存储冲突（见上一章节），可以采用 EIP-1967 提出的代理存储槽标准。
            
            EIP-1967 旨在为智能合约的升级模式提供一种标准化和安全的实现方法。该标准主要关注使用代理模式进行智能合约升级的流程，提高智能合约系统的透明性和可操作性。