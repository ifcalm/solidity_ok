## 第一个合约：Counter

```
pragma solidity >=0.8.0;

// 定义一个合约
contract Counter {
    uint public counter;
    
    constructor() {
        counter = 0;
    }
    
    function count() public {
        counter = counter + 1;
    }
    
    function get() public view returns (uint) {
        return counter;
    }
}
```

### 声明编译器版本
编写合约首先要做的是声明编译器版本， 告诉编译器如何编译当前的合约代码，适合使用什么版本的编译器来编译。 

每个 Solidity 文件都以 `pragma solidity` 声明开始，指定编译器的版本。编译器版本声明的语法如下：
```
pragma solidity >=0.8.0;

contract MyContract {
    // 合约代码
    // contract 关键字用于定义一个智能合约
}
```
它的含义是使用大于等于0.8.0 版本的编译编译合约, 但不兼容 0.9.0 或更高版本。类似的表示还有：
```
pragma solidity >=0.8.0 <0.9.0;

pragma solidity ^0.8.0;
```

### 定义合约
Solidity 使用 `contract` 定义合约，这里定义了一个名为 Counter 的合约:
```
contract Counter {
    // 合约代码
    // contract 关键字用于定义一个智能合约
}
```
在Solidity中，合约本身也是一个数据类型， 称为合约类型。**合约部署到链上后，使用地址来表示一个合约**。 

合约由 合约数据 和 合约函数组成。

### 合约构造函数
在 Solidity 中，构造函数是一种特殊的函数，用于初始化合约状态，在合约创建时自动调用。构造函数主要用于设置合约的初始参数和配置初始状态。

#### 1、构造函数的定义
在 Solidity 中，构造函数使用 `constructor` 关键字定义。构造函数的特点如下：

- 自动调用：合约创建时由 EVM 自动调用，无需显式调用。
- 仅执行一次：构造函数只在合约部署时执行一次，执行完毕后即不再存在。
- 初始化状态变量：用于设置初始状态，例如合约的所有者地址、初始余额等。

```
pragma solidity ^0.8.0;

contract MyContract {
    address public owner;

    constructor() {
        owner = msg.sender;
    }
}
```

#### 2、构造函数参数
构造函数可以接收参数，用于在部署时设定合约的初始状态。例如，你可以在构造函数中设置某个代币的初始供应量，或者设置合约中的某些地址参数。
```
pragma solidity ^0.8.0;

contract Token {
    string public name;
    string public symbol;
    uint public totalSupply;
    address public owner;

    constructor(string memory _name, string memory _symbol, uint _initialSupply) {
        name = _name;
        symbol = _symbol;
        totalSupply = _initialSupply;
        owner = msg.sender;
    }
}
```
在这个代币合约中，构造函数接收 `name`、`symbol` 和 `initialSupply` 参数，用于设置代币的名称、符号和初始供应量。部署时传入这些参数，可以在部署时自定义代币信息。

#### 3、构造函数的可见性
在 Solidity 0.7.0 及以上的版本中，构造函数的可见性不需要显示声明，默认是 `internal`，即构造函数只能在合约部署时被调用。

在较早版本（0.6.x 及以下）中，构造函数使用和合约同名的函数来定义，但这种定义方式在新版本中已经被弃用。
```
// Solidity 0.6.x 及以下版本的构造函数
pragma solidity ^0.6.0;

contract OldStyleConstructor {
    address public owner;

    // 构造函数的名称与合约名称相同
    function OldStyleConstructor() public {
        owner = msg.sender;
    }
}
```
在 0.7.0 及更高版本中，使用 `constructor` 关键字定义构造函数，且无需设置可见性修饰符（如 public）。

