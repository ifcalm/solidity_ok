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

---

## 变量与函数的可见性
在 Solidity 中，变量和函数的可见性控制着它们在合约内部或外部的访问权限。理解可见性修饰符对于编写安全、优化的合约非常重要。

Solidity 中的可见性修饰符主要包括 `public`、`internal`、`external` 和 `private`，这些修饰符用于控制变量和函数的访问范围。

### 1、变量的可见性

Solidity 中的变量可以使用 `public`、`internal` 和 `private` 可见性修饰符来定义。可见性控制了变量是否可以从合约外部、继承合约或合约内部访问。

#### 1.1、`public` 变量

- **定义**：`public` 修饰的变量可以被合约内部和外部访问。
- **自动生成访问器**：当状态变量声明为 `public` 时，Solidity 会自动为它生成一个“getter”函数，使外部账户可以访问变量的值。
- **适用场景**：`public` 变量适合用于需要公开访问的状态数据，例如代币合约中的名称、符号、总供应量等。

```solidity
pragma solidity ^0.8.0;

contract Example {
    uint public publicVar = 10; // 生成一个名为 `publicVar()` 的getter函数
}
```

在上述示例中，`publicVar` 可以通过 `Example.publicVar()` 来从合约外部访问。

#### 1.2、`internal` 变量

- **定义**：`internal` 变量只能在合约内部和继承合约中访问，无法从合约外部直接访问。
- **适用场景**：适用于希望在合约及其子合约中使用，但不希望外部访问的数据。例如内部配置参数、敏感的状态变量等。

```solidity
pragma solidity ^0.8.0;

contract Example {
    uint internal internalVar = 20;
}
```
在此示例中，`internalVar` 只能在 `Example` 合约内部或继承自 `Example` 的合约中访问。

#### 1.3、`private` 变量

- **定义**：`private` 变量只能在声明它的合约中访问，继承合约也无法访问。
- **适用场景**：适用于完全不希望暴露的私密数据，如内部计数器、权限管理变量等。

```solidity
pragma solidity ^0.8.0;

contract Example {
    uint private privateVar = 30;
}
```
在上述代码中，`privateVar` 只能在 `Example` 合约内部访问，即使有合约继承 `Example`，也无法访问 `privateVar`。

### 2、函数的可见性

Solidity 中函数的可见性分为 `public`、`internal`、`external` 和 `private`。每个修饰符都控制了函数的调用范围。

#### 2.1、`public` 函数

- **定义**：`public` 函数可以在合约内部和外部调用，任何人都可以调用这些函数。
- **自动生成接口**：在合约 ABI 中，`public` 函数会自动生成公开的接口。
- **适用场景**：适用于需要被外部用户或合约调用的函数，如公开的查询和操作函数。

```solidity
pragma solidity ^0.8.0;

contract Example {
    function publicFunction() public view returns (string memory) {
        return "This is a public function";
    }
}
```

在此示例中，`publicFunction` 可以被 `Example.publicFunction()` 调用，也可以在合约内部调用。

#### 2.2、`external` 函数

- **定义**：`external` 函数只能从合约外部调用，不能被当前合约的内部函数直接调用，但可以通过 `this` 调用自身的 `external` 函数。
- **Gas 优化**：`external` 函数比 `public` 函数更节省 Gas，适合只被外部访问的场景。
- **适用场景**：适用于专门提供给外部的接口，例如被用户或其他合约直接调用的函数。

```solidity
pragma solidity ^0.8.0;

contract Example {
    function externalFunction() external view returns (string memory) {
        return "This is an external function";
    }
}
```

在上述代码中，`externalFunction` 只能在合约外部调用。如果要在内部调用，必须通过 `this.externalFunction()`，但这样会消耗额外的 Gas。

#### 2.3、`internal` 函数

- **定义**：`internal` 函数只能在当前合约和继承合约中调用，外部无法调用。
- **适用场景**：用于封装合约内部逻辑的函数，常用于继承结构中的共享函数，例如计算函数、权限控制函数等。

```solidity
pragma solidity ^0.8.0;

contract Example {
    function internalFunction() internal view returns (string memory) {
        return "This is an internal function";
    }
}
```

在此示例中，`internalFunction` 只能在 `Example` 合约内部或继承自 `Example` 的合约中调用。

#### 2.4、`private` 函数

- **定义**：`private` 函数只能在当前合约中调用，继承合约也无法调用。
- **适用场景**：用于完全封装合约内部的逻辑，适合敏感的辅助函数或不希望暴露的逻辑处理。

```solidity
pragma solidity ^0.8.0;

contract Example {
    function privateFunction() private view returns (string memory) {
        return "This is a private function";
    }
}
```

在此示例中，`privateFunction` 只能在 `Example` 合约内部调用，无法从外部访问，即使继承 `Example` 也无法调用此函数。

### 3、可见性修饰符的总结和对比

| 修饰符      | 适用于         | 合约内部可调用 | 继承合约可调用 | 合约外部可调用 | 典型应用场景                 |
|-------------|----------------|----------------|----------------|----------------|------------------------------|
| `public`    | 变量、函数     | ✅             | ✅             | ✅             | 公开变量和公开接口           |
| `external`  | 函数           | ❌（可通过`this`调用） | ❌             | ✅             | 专门用于外部接口，减少 Gas  |
| `internal`  | 变量、函数     | ✅             | ✅             | ❌             | 继承结构中的共享逻辑         |
| `private`   | 变量、函数     | ✅             | ❌             | ❌             | 合约内部的敏感逻辑和数据     |


### 4、示例应用

以下是一个结合了不同可见性修饰符的示例，展示了在智能合约中如何使用它们：

```solidity
pragma solidity ^0.8.0;

contract VisibilityExample {
    address public owner;           // public变量，外部可访问
    uint private secretData;        // private变量，只有内部可访问

    constructor(uint _secretData) {
        owner = msg.sender;         // 设置合约所有者
        secretData = _secretData;   // 初始化私密数据
    }

    function getSecretData() public view returns (uint) {
        return secretData;          // 通过 public 函数暴露部分私密信息
    }

    function updateSecretData(uint _newSecretData) public onlyOwner {
        secretData = _newSecretData;
    }

    function onlyOwnerFunction() internal view onlyOwner returns (string memory) {
        return "This function is only accessible by the owner internally";
    }

    function externalFunction() external view returns (string memory) {
        return "This is accessible externally but not internally";
    }

    modifier onlyOwner() {
        require(msg.sender == owner, "Not the contract owner");
        _;
    }
}
```

- **`public`**：`owner` 和 `getSecretData` 是公开的变量和函数，可以从外部访问。
- **`private`**：`secretData` 是私有变量，只能在合约内部访问。
- **`internal`**：`onlyOwnerFunction` 是内部函数，合约内和子合约都能访问。
- **`external`**：`externalFunction` 只能在合约外部访问，适用于外部接口。

### 5、选择可见性修饰符的最佳实践

- **仅暴露必要的函数和变量**：尽量将数据和函数限制在最小可见性，以提高合约安全性。
- **减少 Gas 消耗**：如果函数只用于外部调用，优先选择 `external` 以节省 Gas。
- **使用 `internal` 共享逻辑**：将共享的逻辑封装为 `internal` 函数，在继承合约中重复使用。

---

## 变量

Solidity 中的变量用于存储数据并在智能合约中使用。根据变量的存储位置和生命周期，Solidity 中的变量主要分为 **状态变量**、**局部变量** 和 **全局变量**。此外，不同类型的变量在 Solidity 中有不同的存储方式和使用方法。

### 1、变量的分类

#### 1.1、状态变量（State Variables）

状态变量是合约中的永久性存储数据，存储在区块链上。

- **定义**：声明在合约内部且位于函数之外的变量，具有持久性。
- **生命周期**：状态变量的生命周期与合约相同，它们的值会随着合约的生命周期而持久保存。
- **消耗 Gas**：由于状态变量需要存储在区块链中，读写操作都需要消耗 Gas。
- **可见性修饰符**：状态变量可以使用 `public`、`internal`、`private` 等可见性修饰符控制访问范围。

```solidity
pragma solidity ^0.8.0;

contract MyContract {
    uint public counter = 0;  // 状态变量

    function incrementCounter() public {
        counter += 1;
    }
}
```

在上面的例子中，`counter` 是一个状态变量，存储在区块链上。每次调用 `incrementCounter` 函数时，`counter` 的值都会增加，并永久记录在区块链上。

#### 1.2、局部变量（Local Variables）

局部变量是在函数内部声明和使用的临时变量。

- **定义**：声明在函数内部的变量，仅在函数执行期间有效。
- **生命周期**：局部变量的生命周期仅限于函数的执行，函数结束后局部变量即被销毁。
- **不消耗存储成本**：局部变量存储在内存或栈中，不存储在区块链上，因此不会消耗存储 Gas。
- **不可使用可见性修饰符**：局部变量的可见性只能在函数内，不需要 `public`、`internal` 等修饰符。

```solidity
pragma solidity ^0.8.0;

contract MyContract {
    function add(uint a, uint b) public pure returns (uint) {
        uint sum = a + b;  // 局部变量
        return sum;
    }
}
```

在上面的例子中，`sum` 是一个局部变量，它只在 `add` 函数的执行过程中存在，不会被永久存储在区块链上。

#### 1.3、全局变量（Global Variables）

全局变量是 Solidity 提供的一组内置变量，用于获取区块链相关的信息。

- **定义**：由 Solidity 提供，包含了区块、交易和合约调用等信息。
- **常用全局变量**：
  - `msg.sender`：调用合约的账户地址。
  - `msg.value`：调用交易中发送的以太币数量。
  - `block.timestamp`：当前区块的时间戳。
  - `block.number`：当前区块的区块号。
  - `tx.origin`：最初发起交易的账户地址。
- **应用场景**：获取交易发送者地址、合约调用时的传递数据、区块信息等，广泛用于控制逻辑和权限验证等场景。

```solidity
pragma solidity ^0.8.0;

contract MyContract {
    address public owner;

    constructor() {
        owner = msg.sender; // 使用全局变量 msg.sender
    }

    function getBlockTimestamp() public view returns (uint) {
        return block.timestamp; // 使用全局变量 block.timestamp
    }
}
```

在上面的例子中，`msg.sender` 和 `block.timestamp` 是全局变量，分别用于获取合约调用者的地址和当前区块的时间戳。

### 2、变量的存储位置

Solidity 中的变量根据存储位置的不同，分为三种存储类型：**storage**、**memory** 和 **calldata**。

#### 2.1、Storage（存储）

- **定义**：存储在区块链中的永久性数据，状态变量默认存储在 `storage` 中。
- **特性**：`storage` 是区块链的持久存储，写入和读取都需要消耗 Gas。
- **适用对象**：状态变量和引用类型变量（如数组、结构体等）在合约函数中直接赋值时默认位于 `storage`。

```solidity
pragma solidity ^0.8.0;

contract MyContract {
    uint[] public numbers; // 存储在 storage 中

    function addNumber(uint number) public {
        numbers.push(number); // 修改 storage 中的 numbers 数组
    }
}
```

在这个例子中，`numbers` 数组存储在 `storage` 中，所有对它的修改都会永久存储在区块链上。

#### 2.2、Memory（内存）

- **定义**：函数执行期间的临时存储，用于临时数据，函数结束后释放。
- **特性**：`memory` 是临时存储，不会保存到区块链中，因此不消耗存储 Gas，但在处理数据时仍需一定的 Gas 。
- **适用对象**：局部变量和函数参数可以存储在 `memory` 中，尤其是引用类型（如数组、结构体、字符串）在函数中使用时通常需要显式声明为 `memory`。

```solidity
pragma solidity ^0.8.0;

contract MyContract {
    function setArray(uint[] memory data) public pure returns (uint) {
        return data.length; // data 是 memory 中的数组，仅在函数期间存在
    }
}
```

在此例中，`data` 是一个 `memory` 数组，仅在 `setArray` 函数调用期间有效。

#### 2.3、Calldata（调用数据）

- **定义**：只读数据，特定于外部函数参数，节省 Gas 的一种数据存储方式。
- **特性**：`calldata` 是不可修改的临时存储，用于函数的外部调用数据，通常用于接收外部传入的数组、结构体等复杂数据。
- **适用对象**：外部函数参数（特别是引用类型），适用于只读的传入数据。

```solidity
pragma solidity ^0.8.0;

contract MyContract {
    function getDataLength(uint[] calldata data) external pure returns (uint) {
        return data.length; // data 是 calldata 数组，不可修改
    }
}
```

在此例中，`data` 被声明为 `calldata`，只能读取，不可更改。`calldata` 存储方式在不需要修改数据时比 `memory` 更省 Gas。

### 3、变量的类型

Solidity 中支持多种变量类型，包括 **值类型** 和 **引用类型**。

#### 3.1、值类型

值类型的变量直接存储数据，数据赋值时会创建副本，独立存储。

 **常见值类型**：
  - `uint`、`int`：无符号和有符号整数，如 `uint256`、`int8`。
  - `bool`：布尔类型，只有 `true` 和 `false` 两种值。
  - `address`：以太坊地址类型，表示账户地址。
  - `bytes`：固定大小字节数组，常用 `bytes1` 到 `bytes32`。

```solidity
pragma solidity ^0.8.0;

contract MyContract {
    uint public count = 0;
    bool public isActive = true;
    address public owner = msg.sender;
}
```

#### 3.2、引用类型

引用类型的变量存储的是数据的地址，而非数据本身。引用类型的数据赋值会产生指针引用。

- **常见引用类型**：
  - `string`：字符串类型。
  - `array`：数组类型，分为定长数组和动态数组。
  - `struct`：结构体类型，用户可以自定义复杂数据结构。
  - `mapping`：映射类型，用于存储键值对。

```solidity
pragma solidity ^0.8.0;

contract MyContract {
    struct Person {
        string name;
        uint age;
    }

    mapping(address => uint) public balances; // mapping
    Person[] public people; // 动态数组

    function addPerson(string memory name, uint age) public {
        people.push(Person(name, age));
    }
}
```

在此例中，`Person` 是自定义的结构体类型，`people` 是一个动态数组，`balances` 是一个 `mapping`。

### 4、变量的修饰符

变量的可见性修饰符控制了变量的访问权限，常见修饰符包括：

- **`public`**：变量可以被合约内部和外部访问。
- **`private`**：变量仅可被当前合约内部访问。
- **`internal`**：变量可被当前合约及其继承合约访问。

修饰符示例：
```solidity
pragma solidity ^0.8.0;

contract MyContract {
    uint public publicVar = 10;  // 可被内部和外部访问
    uint private privateVar = 20;  // 仅内部访问
    uint internal internalVar = 30;  // 内部和继承合约可访问
}
```

Solidity 中的变量类型多样，包括状态变量、局部变量和全局变量。不同的变量类型适用于不同的场景和存储需求。理解变量的存储位置（`storage`、`memory`、`calldata`）以及可见性控制对于编写安全、优化的 Solidity 合约至关重要。

---

