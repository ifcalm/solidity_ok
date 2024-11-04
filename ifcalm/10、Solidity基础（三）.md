## 合约接收 ETH

在 Solidity 中，合约可以通过多种方式接收 ETH。为了使合约能够接收 ETH，通常需要使用特殊函数，例如 `payable` 函数和 `receive`、`fallback` 函数。以下是 Solidity 合约接收 ETH 的几种常用方法和注意事项。

### 1、使用 `payable` 修饰符

在 Solidity 中，任何需要接收 ETH 的函数必须使用 `payable` 修饰符。这使得该函数能够接收并处理来自外部账户或其他合约的 ETH。

#### 示例：`payable` 函数接收 ETH

```solidity
pragma solidity ^0.8.0;

contract PayableExample {
    // 合约的余额（可以查询合约地址余额）
    function getBalance() public view returns (uint) {
        return address(this).balance;
    }

    // 使用 payable 修饰符的函数，可以接收 ETH
    function deposit() public payable {
        // msg.value 是发送的 ETH 数量（单位是 Wei）
    }
}
```

在上述例子中，`deposit` 函数使用了 `payable` 修饰符，因此可以接收发送到合约的 ETH。调用时可以通过 `msg.value` 获取发送的 ETH 数量。

### 2、使用 `receive` 函数

`receive` 函数是 Solidity 中的一个特殊函数，专门用于接收 ETH。`receive` 函数没有函数名，只能接收 ETH，不能执行其他操作。

#### `receive` 函数的特点

- 只能有一个 `receive` 函数。
- 没有参数和返回值。
- 必须是 `payable` 类型。
- 如果合约接收到直接发送的 ETH（没有指定函数），则会调用 `receive` 函数。

#### 示例：`receive` 函数接收 ETH

```solidity
pragma solidity ^0.8.0;

contract ReceiveExample {
    // 接收 ETH 的特殊函数
    receive() external payable {}

    function getBalance() public view returns (uint) {
        return address(this).balance;
    }
}
```

在此例中，合约没有任何 `payable` 函数调用时，可以直接向合约地址发送 ETH，`receive` 函数会自动执行并接收 ETH。

### 3、使用 `fallback` 函数

`fallback` 函数也是 Solidity 中的特殊函数，用于在调用合约中不存在的函数时执行。如果没有 `receive` 函数，合约也可以使用 `fallback` 函数接收 ETH。

#### `fallback` 函数的特点

- 必须使用 `payable` 修饰符，才能接收 ETH。
- 可以有或没有参数，通常用于处理未定义的函数调用。
- 可以在没有指定函数的转账交易中执行。

#### 示例：`fallback` 函数接收 ETH

```solidity
pragma solidity ^0.8.0;

contract FallbackExample {
    // fallback 函数，接收未定义函数调用或 ETH
    fallback() external payable {}

    function getBalance() public view returns (uint) {
        return address(this).balance;
    }
}
```

在此例中，`fallback` 函数将处理所有未定义的函数调用，并在该调用中接收 ETH。

### 4、`receive` 与 `fallback` 的区别

- **`receive` 函数**：专门用于接收 ETH 转账，无参数或返回值。
- **`fallback` 函数**：用于处理未定义的函数调用，且可以接收 ETH。如果合约同时定义了 `receive` 和 `fallback`，那么当合约接收 ETH 时，`receive` 优先执行，`fallback` 用于处理其他未定义的调用。

#### 使用优先级

1. 直接转账给合约时，`receive` 函数优先执行。
2. 如果没有 `receive`，则执行 `fallback`（如果 `fallback` 是 `payable` 类型）。
3. 如果合约中都没有 `receive` 和 `payable` 的 `fallback`，则直接转账会失败。

### 5、事件日志

为便于追踪和调试，通常在接收 ETH 时会触发事件日志，记录转账信息。

```solidity
pragma solidity ^0.8.0;

contract PayableWithEvent {
    event Received(address sender, uint amount);

    receive() external payable {
        emit Received(msg.sender, msg.value);
    }

    fallback() external payable {
        emit Received(msg.sender, msg.value);
    }
}
```

在此例中，`Received` 事件记录了 `sender`（发送方）和 `amount`（发送金额），无论是通过 `receive` 还是 `fallback` 接收 ETH 都会触发该事件。

### 6、合约之间的 ETH 转账

合约可以通过以下几种方式将 ETH 转账到其他合约或账户：

#### 6.1、使用 `transfer`

- `transfer` 向指定地址发送 2300 Gas，适用于简单转账。
- 如果失败，会自动回滚交易。

```solidity
function transferETH(address payable recipient) public payable {
    recipient.transfer(msg.value);
}
```

#### 6.2、使用 `send`

- `send` 向指定地址发送 2300 Gas，但返回一个布尔值表示成功或失败。
- 需要手动处理失败情况。

```solidity
function sendETH(address payable recipient) public payable {
    bool success = recipient.send(msg.value);
    require(success, "Send failed");
}
```

#### 6.3、使用 `call`

- `call` 是低级调用方法，允许自定义 Gas 限制，适合复杂的合约调用。
- 返回一个布尔值，需手动检查成功状态。

```solidity
function callETH(address payable recipient) public payable {
    (bool success, ) = recipient.call{value: msg.value}("");
    require(success, "Call failed");
}
```

### 7、示例：多种接收 ETH 的方法合约

下面是一个完整示例，包含了 `payable`、`receive` 和 `fallback` 三种接收 ETH 的方法：

```solidity
pragma solidity ^0.8.0;

contract MultiReceiveExample {
    event Received(address sender, uint amount);

    // 通过 payable 函数接收 ETH
    function deposit() public payable {
        emit Received(msg.sender, msg.value);
    }

    // 使用 receive 接收 ETH
    receive() external payable {
        emit Received(msg.sender, msg.value);
    }

    // 使用 fallback 接收 ETH
    fallback() external payable {
        emit Received(msg.sender, msg.value);
    }

    // 查询合约余额
    function getBalance() public view returns (uint) {
        return address(this).balance;
    }
}
```

此合约支持三种接收 ETH 的方式，并记录每次接收的 ETH 信息，方便用户查询和调试。

### 8、注意事项和最佳实践

- **明确使用 `payable` 修饰符**：所有接收 ETH 的函数必须使用 `payable` 修饰符。
- **限制合约的 ETH 接收方式**：只允许 `receive` 或 `fallback` 之一接收 ETH，避免未定义行为。
- **检查转账成功状态**：使用 `send` 和 `call` 时需手动检查转账结果。
- **记录交易信息**：使用事件记录接收 ETH 的地址和金额，方便追踪。
- **安全考虑**：小心使用 `call` 方法，因为 `call` 方法更灵活，但也带来更高的复杂性和潜在的安全风险。尽量选择 `transfer` 或 `send`，除非特殊需求。

### 总结

Solidity 提供了多种方式让合约接收 ETH，包括使用 `payable` 函数、`receive` 和 `fallback` 函数。不同的方式适合不同的场景，开发者应根据需求选择合适的接收方式，并注意事件日志和安全性检查，以确保合约在处理 ETH 时的安全和可追踪性。

---

## 函数修改器

在 Solidity 中，**函数修改器（Function Modifiers）** 是用于修饰和增强函数行为的特殊语法结构。通过函数修改器，开发者可以在函数执行前后添加自定义的逻辑，使得代码复用性和可读性更高。函数修改器广泛应用于访问控制、权限验证、状态检查、费用支付等场景。

### 1、函数修改器的定义和基本语法

函数修改器通过 `modifier` 关键字定义，可以用于一个或多个函数。定义修改器时，可以在内部插入 `_`，表示函数主体在修改器代码中的插入位置。

#### **语法示例**

```solidity
pragma solidity ^0.8.0;

contract ModifierExample {
    address public owner;

    constructor() {
        owner = msg.sender;
    }

    // 定义一个修改器，检查调用者是否为合约的所有者
    modifier onlyOwner() {
        require(msg.sender == owner, "Not authorized");
        _;
    }

    // 使用 onlyOwner 修改器修饰的函数
    function restrictedAction() public onlyOwner {
        // 只有所有者可以执行的操作
    }
}
```

在此例中，`onlyOwner` 是一个修改器，用于检查调用者是否为合约所有者。如果 `msg.sender` 不是 `owner`，则会抛出异常并终止函数调用；如果验证通过，则函数主体会在 `_` 位置插入并继续执行。

### 2、函数修改器的执行流程

- 当带有修改器的函数被调用时，首先执行修改器的代码。
- 如果修改器中的代码包含 `_`，则会在 `_` 处插入并执行函数主体。
- 修改器可以包含前置、后置或中间逻辑，灵活控制函数执行流程。

### 3、常用的函数修改器示例

函数修改器在合约中具有广泛的应用场景，以下是一些常见的修改器示例：

#### 3.1、权限控制修改器

`onlyOwner` 是常见的权限控制修改器，确保只有合约的所有者（通常是部署者）才能调用某些函数。

```solidity
modifier onlyOwner() {
    require(msg.sender == owner, "Not authorized");
    _;
}
```

使用 `onlyOwner` 修改器修饰函数，确保只有合约所有者可以调用该函数。

#### 3.2、状态检查修改器

状态检查修改器用于确保函数只能在特定状态下执行。例如，可以限制某个操作只能在合约的某种状态下执行。

```solidity
enum ContractState { Inactive, Active, Paused }
ContractState public state;

modifier isActive() {
    require(state == ContractState.Active, "Contract is not active");
    _;
}

function performAction() public isActive {
    // 合约状态为 Active 时才可以执行的操作
}
```

在此例中，`isActive` 修改器确保函数只有在 `state` 为 `Active` 时才能执行。

#### 3.3、支付费用修改器

支付费用的修改器用于检查函数调用时是否附带了足够的 ETH。例如，在购买商品或服务时，确保支付了指定的费用。

```solidity
uint public price = 1 ether;

modifier costs(uint amount) {
    require(msg.value >= amount, "Insufficient ETH");
    _;
}

function buy() public payable costs(price) {
    // 函数调用时附带的 ETH 不小于指定的价格，才会继续执行
}
```

在此例中，`costs` 修改器确保调用 `buy` 函数时支付的 ETH 数量至少为 `price`。

#### 3.4、重新入锁（Reentrancy Guard）

重入锁修改器用于防止重入攻击，这在涉及外部调用和资金转移的场景中特别重要。重入攻击可能导致合约资金被恶意消耗。

```solidity
bool private locked;

modifier noReentrant() {
    require(!locked, "Reentrant call");
    locked = true;
    _;
    locked = false;
}

function withdraw(uint amount) public noReentrant {
    // 提款逻辑，防止重入攻击
    payable(msg.sender).transfer(amount);
}
```

在此例中，`noReentrant` 修改器使用一个布尔变量 `locked` 来防止重入攻击，确保函数主体只能执行一次。

### 4、嵌套修改器

Solidity 支持多个修改器嵌套使用，可以将多个修改器组合在一起，以实现更复杂的逻辑控制。修改器的执行顺序是从左到右依次执行。

```solidity
modifier onlyOwner() {
    require(msg.sender == owner, "Not authorized");
    _;
}

modifier isActive() {
    require(state == ContractState.Active, "Contract is not active");
    _;
}

function restrictedActiveAction() public onlyOwner isActive {
    // 只有当合约状态为 Active 且调用者为所有者时才可执行
}
```

在此例中，`restrictedActiveAction` 函数同时使用了 `onlyOwner` 和 `isActive` 修改器，确保只有所有者并且合约状态为 `Active` 时才可以执行该函数。

### 5、带参数的修改器

修改器可以接收参数，这样在调用函数时，可以传入不同的参数值来调整修改器的行为。

```solidity
modifier minBalance(uint _minBalance) {
    require(address(this).balance >= _minBalance, "Insufficient contract balance");
    _;
}

function withdraw(uint amount) public minBalance(1 ether) {
    // 函数主体代码，确保合约余额至少为 1 ether 才可执行
}
```

在此例中，`minBalance` 修改器接收一个 `uint` 类型的参数 `_minBalance`，用于检查合约余额是否满足最低余额要求。

### 6、执行顺序和嵌套规则

- 如果多个修改器应用于一个函数，则修改器会按从左到右的顺序执行。
- 修改器中的 `_` 表示函数主体的插入位置。如果修改器前后包含代码，函数主体会被夹在两者之间。

```solidity
modifier logStart() {
    emit Log("Starting function execution");
    _;
}

modifier logEnd() {
    _;
    emit Log("Function execution completed");
}

function loggedFunction() public logStart logEnd {
    // 函数主体
}
```

在此例中，`logStart` 和 `logEnd` 修改器分别在函数开始和结束时记录日志事件。`logStart` 先执行，然后是函数主体，最后执行 `logEnd`。

### 7、函数修改器的实际应用场景

函数修改器在 Solidity 中具有广泛的应用场景，以下是一些常见的实际应用：

- **权限管理**：通过 `onlyOwner` 或 `onlyAdmin` 修改器来确保特定角色访问某些功能。
- **多重签名控制**：在需要多重签名授权的合约中，修改器可以用于确保满足所有签名条件。
- **状态机控制**：将修改器与状态机结合使用，确保合约的功能只能在特定状态下执行。
- **费用支付**：在某些交易或功能中，修改器可以用于检查是否支付了正确的费用。
- **安全锁机制**：通过重入锁机制防止重入攻击，确保每个操作的独立性和安全性。

### 8、修改器的最佳实践

- **清晰的命名**：给修改器起清晰且有意义的名称，以便其他开发者理解其用途。
- **合理的权限分配**：使用修改器进行权限控制时，确保不同权限角色的适当隔离，避免权限过度集中。
- **避免复杂嵌套**：尽量减少修改器的复杂嵌套，以提高代码的可读性和维护性。
- **重入保护**：在涉及外部调用的函数中加入重入保护，避免重入攻击的风险。
- **善用事件**：在修改器中适时记录事件，帮助调试和交易记录追踪。

### 总结

函数修改器是 Solidity 中强大的工具，通过修改器可以将特定逻辑封装起来，用于控制函数的执行条件，简化代码，提升代码的复用性。函数修改器广泛应用于权限控制、状态检查、支付验证和安全保护等场景。掌握函数修改器的定义、使用、执行顺序和最佳实践，对于编写更加安全、简洁的智能合约非常重要。

---

## 事件（Event）

