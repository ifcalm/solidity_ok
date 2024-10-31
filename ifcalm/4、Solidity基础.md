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

