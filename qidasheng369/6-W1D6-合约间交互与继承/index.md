# 6-W1D6-合约间交互与继承

**目标：学习合约之间的交互和继承**

- 合约之间的调用（`this`, `call`, `delegatecall`）
- 继承（`inheritance`）和重写（`override`）
- 接口与抽象合约的定义和使用
- `super`关键字

**笔记**

- 合约之间的调用（`this`, `call`, `delegatecall`）
    - **底层调用**
        
        地址类型还有3个底层的成员函数：
        
        - `targetAddr.call(bytes memory abiEncodeData) returns (bool, bytes memory)`
        - `targetAddr.delegatecall(bytes memory abiEncodeData) returns (bool, bytes memory)`
        - `targetAddr.staticcall(bytes memory abiEncodeData) returns (bool, bytes memory)`
            
            `call` 是常规调用，`delegatecall` 为委托调用，`staticcall` 是静态调用（不修改合约状态， 相当于调用 `view` 方法）。
            
    - **`this` 关键字**
        
        ### [**this 关键字**](https://professional-solidity.readthedocs.io/zh-cn/latest/source/01.hello.html#this)
        
        Solidity 中 `this` 代表合约对象本身；
        
        - 可以通过 `address(this)` 获取合约地址。
        - 可以通过 `this.fnName` 获取 `external` 函数
        
        ```solidity
        // SPDX-License-Identifier: MIT
        pragma solidity ^0.8.18;
        
        contract Demo {
            function contractAds() external view returns (address) {
                return address(this);
            }
        
            function testExternal() external view returns (address) {
                return this.contractAds();
            }
        }
        ```
        
        ### [**三种地址身份**](https://professional-solidity.readthedocs.io/zh-cn/latest/source/01.hello.html#id21)
        
        - 合约地址
        - 合约创建者地址
        - 合约调用者地址
        
        这三个地址概念要完全理解。
        
        ```solidity
        // SPDX-License-Identifier: MIT
        pragma solidity ^0.8.18;
        
        // 这三个地址的概念一定要理解清楚
        contract Demo {
            address public owner;
        
            constructor() {
                // 可以用在 constructor 内获取当前合约地址
                owner = address(this);
        
                // 不可以在构造函数内调用函数，因为此时合约还没有完成构建好。
                // this.caller(); 相当于从外部调用 caller 方法
                // owner = this.caller();
            }
        
            function caller() external view returns (address) {
                return this.contractAds(); // 内部调用 external 可见性的函数
            }
        
            function contractAds() external view returns (address) {
                return address(this);
            }
        }
        ```
        
    - **`call`常规调用**
        
        `call` 是一个低级函数，用于向其他合约发送消息。它返回一个布尔值，指示调用是否成功，以及一个 `bytes memory`，其中包含返回的数据。
        
    - **`delegatecall`委托调用**
        
        `delegatecall` 是一个非常低级的函数，它与 `call` 类似，但有两个关键区别：
        
        1. 它不改变调用合约的存储状态，而是使用调用者的存储状态。
        2. 它在调用者的上下文中执行代码。
        
        这意味着 `delegatecall` 可以用于执行合约的代码，同时保留原始合约的状态。
        
    - `this`, `call`, `delegatecall`的区别
        1. `this`一般是代表当前和合约
        2. `call`是调用别的合约时，会切换上下文到调用的合约中去，所以修改的是调用的合约的数据状态。
        3. `delegatecall` 调用别的合约时，不会切换上下文，只是调用合约的代码，所以修改是当前合约的数据状态，常用来合约升级。
        4. code 对比
            
            ```solidity
            pragma solidity ^0.8.0;
            
            contract Counter {
                uint public counter;
                address public sender;
            
                function count() public {
                    counter += 1;
                    sender = msg.sender;
                }
            }
            
            contract CallTest {
                uint public counter;
                address public sender;
            
                function lowCallCount(address addr) public {
                //  (Counter(c)).count();
                    bytes memory methodData =abi.encodeWithSignature("count()");
                    addr.call(methodData);
                }
            
                // 只是调用代码，合约环境还是当前合约。
                function lowDelegatecallCount(address addr) public {
                    bytes memory methodData = abi.encodeWithSignature("count()");
                    addr.delegatecall(methodData);
                }
            
            }
            
            // 结果：
            
            // lowCallCount()  ->  Counter::counter + 1   
            
            // lowDelegatecallCount() -> CallTest::counter + 1  
            
            // lowCallCount 函数中使用call，上下文从 CallTest 地址空间跳到了 Counter地址空间， 因此是Counter内部的 counter 值 + 1 了。
            
            // lowDelegatecallCount 函数中使用delegatecall，上下文保证在 CallTest 地址空间，因此是CallTest的 counter 值 + 1 了。
            ```
            
    - `staticcall` 是静态调用（不修改合约状态， 相当于普通的 `view` 方法调用）
- **继承（inheritance）和重写（override）**
    - **继承**
        - **概念理解**
            
            继承是面向对象编程中的重要概念之一，它允许一个类（称为子类或派生类）从另一个类（称为父类或基类）继承属性和方法。
            
            Solidity 支持继承，派生合约通过继承获得了父合约的特性
            
            继承的好处：
            
            1. **代码更好重用**：派生合约可以直接获得父合约的属性和方法，不需要重复编写相同的代码。
            2. **方便扩展**：在继承的基础上添加新的属性和方法，更方便扩展和定制父合约的功能。
            3. **提高维护性和可读性**：继承可以使合约之间建立清晰的层次关系，使代码更加易于维护和理解。
            4. Solidity 使用关键字 `is` 来表示合约的继承关系：
                
                ```solidity
                pragma solidity ^0.8.0;
                
                contract Base {
                    uint public a;
                }
                
                // highlight-next-line
                contract Sub is Base {
                    uint public b ;
                    constructor() {
                        b = 2;
                    }
                }
                ```
                
            
            > 注意⚠️：
            > 
            > 
            > 派生合约会继承父合约内的所有非私有（private）成员：
            > 
            > |  | public | external | internal | private |
            > | --- | --- | --- | --- | --- |
            > | 可被继承 | ✓ | ✓ | ✓ |  |
            > 
            > 内部（internal）函数和公开的状态变量在派生合约里是可以直接使用，派生合约也会继承 `external` 方法，但不能在内部访问。
            > 
            > 部署派生合约时，**父合约不会连带被部署**，可以理解为，在编译时，编译器会把父合约的代码拷贝进派生合约。
            > 
        - **多重继承**
            1. Solidity也支持多重继承，即可以从多个父合约继承，直接在is后面接多个父合约即可，例如：
            2. 如果多个父合约之间也有继承关系，is 后面的合约的书写顺序就很重要，正确的顺序应该是：父合约在前，子合约在后
            3. 在多重继承下，如果有多个父合约有相同定义的函数，在函数重写时，**override 关键字后必须指定所有的父合约名**。
            
            ```solidity
            contract Sub is Base1, Base2 {
                
            }
            
            // 2-父合约之间也有继承
            contract X {}
            contract A is X {}
            
            // 编译出错
            contract C is A, X {}
            
            // 正确的写法
            contract C is X, A {}
            
            // 3-多重继承，重写函数
            pragma solidity >=0.8.0;
            
            contract Base1 {
                function foo() virtual public {}
            }
            
            contract Base2 {
                function foo() virtual public {}
            }
            
            contract Inherited is Base1, Base2 {
                // 继承自隔两个父合约定义的foo(), 必须显式的指定override
                function foo() public override(Base1, Base2) {}
            }
            ```
            
    - **重写（override）**
        
        > 只有父合约中的虚函数（使用了`virtual` 关键字修饰的函数）可以在派生被重写。函数需要使用关键字`override`修饰表示正在重写父合约的某个函数。
        > 
        > 
        > 
        > 注意⚠️：
        > 
        > 重写的函数也可以再次重写，当需要被重写时，需要使用 `virtual` 来修饰， 例如：
        > 
        
        show me code
        
        ```solidity
        pragma solidity >=0.8.0;
        
        // Base声明虚函数foo
        contract Base {
            uint public a;
            function foo() virtual public {
              a += 2;
            }
        }
        
        // Sub继承Base，并使用override重写了foo函数
        contract Sub is Base {
            // highlight-next-line
            function foo() public override {
                a += 1;
            }
        }
        
        // Inherited继承了Sub合约，并再次重写啦foo函数。
        // 但是需要注意的，多次重写的时候需要手动声明为virtual虚函数
        contract Inherited is Sub {
              function foo() public virtual override {
                  a += 3;
              }
        }
        
        ```
        
- **接口与抽象合约的定义和使用**
    - **接口**
        1. **概念：**
            1. 接口（Interface）是一种定义了一组**抽象方法的规范**，接口描述了一组兑现应该具有哪些方法，但并不提供这些方法的具体实现。
            2. 依赖接口与依赖实现两个方法在EVM层面没有区别，最终都是通过合约地址找到对应的函数来执行。
        2. **接口与抽象合约的区别**
            1. **接口只用来定义方法，而没有实现的方法体。抽象合约则可以有方法的实现**，抽象合约可以实现一个或多个接口，以满足接口定义的方法要求。
        3. 由于接口是一组方法规范的，因此接口：
            - 不能继承其他合约，但是它们可以继承其他接口合约。无任何实现的函数
            - 接口中所有声明的函数必须是 external 类型的，即使它们在合约中是 public 类型的。
            - 没有构造方法，不能声明构造函数。
            - 没有状态变量，不能声明状态变量。
            - 它们不能声明修饰器。
        4. [**利用接口调用合约](https://decert.me/tutorial/solidity/solidity-basic/interface#%E5%88%A9%E7%94%A8%E6%8E%A5%E5%8F%A3%E8%B0%83%E7%94%A8%E5%90%88%E7%BA%A6)：**代码演示
            
            ```solidity
            pragma solidity ^0.8.10;
            
            interface ICounter {
                function increment() external;
            }
            
            // 假设Counter是链上已经部署了一个合约
            // 合约地址为0xabcd....， 源代码文件：Counter.sol
            pragma solidity ^0.8.0;
            
            contract Counter is ICounter {
                uint public count;
            
                function increment() external override {
                    count += 1;
                }
            }
            
            // 如何在合约里调用链上Counter合约的increment()方法呢
            import "./ICounter.sol";
            
            contract MyContract {
                function incrementCounter(address _counter) external {
                    ICounter(_counter).increment();
                }
            }
            
            // 解释：
            // ICounter(_counter).increment(); 的含义是：
            // 把合约地址 _counter 类型转化为接口ICounter类型
            //（接口类型与合约类型一样，也是自定义类型），
            // 再调用接口内的increment() 方法。
            
            // SPDX-License-Identifier: MIT
            pragma solidity ^0.8.0;
            
            // 定义一个基础接口
            interface IERC20 {
                function totalSupply() external view returns (uint256);
                function balanceOf(address account) external view returns (uint256);
                function transfer(address recipient, uint256 amount) external returns (bool);
                // 其他ERC20函数...
            }
            
            // 定义一个继承IERC20的接口
            interface IERC20Extended is IERC20 {
                function name() external view returns (string memory);
                function symbol() external view returns (string memory);
                function decimals() external view returns (uint8);
                // 其他扩展功能...
            }
            
            // 现在，IERC20Extended 接口包含了 IERC20 的所有功能，以及其自己定义的功能
            
            ```
            
        5. [**调用 ERC20 合约进行转账**](https://decert.me/tutorial/solidity/solidity-basic/interface#%E8%B0%83%E7%94%A8-erc20-%E5%90%88%E7%BA%A6%E8%BF%9B%E8%A1%8C%E8%BD%AC%E8%B4%A6)
            1. **部署MyToken合约**：首先，您需要部署`MyToken`合约。这个合约实现了ERC20标准，并且一旦部署，就会生成一个合约地址。
            2. **获取MyToken合约地址**：在部署`MyToken`合约后，您需要记录下这个合约的地址，因为您将在下一步中使用它。
            3. **部署Award合约**：在部署`Award`合约时，您需要在构造函数中传入`MyToken`合约的地址。这个地址将被用来初始化`Award`合约中的`IERC20`接口实例。
            4. **向Award合约转账MyToken代币**：在`Award`合约部署完毕后，您需要将一些`MyToken`代币转移到`Award`合约的地址。这是因为`Award`合约需要拥有足够的`MyToken`代币，才能在其`sendAward`函数中执行转账操作。
            
            ```solidity
            // MyToken合约代码
            pragma solidity ^0.8.9;
            
            import "@openzeppelin/contracts/token/ERC20/ERC20.sol";
            
            contract MyToken is ERC20 {
                constructor() ERC20("MyToken", "MTK") {
                    // 部署时可以立即铸造一些代币到合约创建者的地址
                    _mint(msg.sender, 1000000 * 10**18);
                }
            }
            // 假如MyToken合约部署后的地址为addr = 0xabcd....
            
            // Award合约代码
            pragma solidity ^0.8.9;
            
            import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
            
            contract Award {
                IERC20 immutable token;
            
                constructor(address _tokenAddress) {
                    token = IERC20(_tokenAddress);
                }
            
                function sendAward(address user, uint256 amount) public {
                    // 确保Award合约有足够的余额
                    token.transfer(user, amount);
                }
            }
            
            // 部署和转账过程
            // 1. 部署MyToken合约，并记录其地址
            // 2. 部署Award合约，传入MyToken合约的地址 Award(addr)
            // 3. 将MyToken代币从某个地址转移到Award合约地址
            ```
            
    - **抽象合约**
        1. 概念：
            
            为了在合约之间建立清晰的结构关系，不想被部署的合约前加上 `abstract` 关键字，表示这是一个抽象合约。
            
        2. 抽象合约由于不需要部署，因此可以声明没有具体实现的纯虚函数，纯虚函数声明用"`;`"结尾，而不是"{ }"。
        3. 抽象合约中的`get()` 函数，没有函数体，`get()`函数需要有派生的合约来实现。
            
            ```solidity
            abstract contract Base {
                uint public a;
            }
            
            // 2-省略函数体"{}"
            pragma solidity >=0.8.0;
            
            abstract contract Base {
                function get() virtual public;
            }
            ```
            
- **super关键字**
    
    **使用 super 调用父合约函数**
    
    ```solidity
    pragma solidity >=0.8.0;
    
    contract Base {
        uint public a;
        function foo() virtual public {
            a += 2;
        }
    }
    
    contract Sub is Base {
    
          function foo() public override {
              super.foo(); // 或者 Base.foo();
              a += 1;
          }
    }
    
    // 改造后的继承
    contract Base {
        uint public a;
        constructor(uint _a) {
            a = _a;
        }
    }
    
    // 写法1
    contract Sub is Base(1) {
       uint public b ;
       constructor() {
         b = 2;
       }
    }
    
    // 写法2
    contract Sub is Base {
       uint public b ;
    
       constructor() Base(1)  {
            b = 2;
        }
    }
    ```