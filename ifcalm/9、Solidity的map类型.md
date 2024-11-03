在 Solidity 中，**映射（Mapping）** 是一种数据结构，用于将 **键** 映射到 **值**。映射类型类似于哈希表或字典，通常用于快速查找和存储数据。映射的数据以 `key-value` 形式存储，键可以是唯一标识符（如地址、数字等），值可以是任意类型。映射在合约中被广泛用于管理账户余额、权限控制和数据索引等场景。

### 1、映射的定义与基础语法

映射使用 `mapping` 关键字定义，其语法为：

```solidity
mapping(keyType => valueType) public mappingName;
```

- **keyType**：映射的键类型，必须是 Solidity 中的值类型，如 `uint`、`address` 或 `bytes32`。键不能是引用类型（如数组、结构体或另一个映射）。
- **valueType**：映射的值类型，可以是任何类型，包括值类型、引用类型（如结构体、数组）或嵌套的映射。
- **public**：可选的可见性修饰符，使得 Solidity 自动生成 `getter` 方法来访问映射中的值。

#### 示例

```solidity
pragma solidity ^0.8.0;

contract MappingExample {
    // 定义一个从地址到 uint 的映射
    mapping(address => uint) public balances;
}
```

在此例中，`balances` 是一个从地址（`address`）到无符号整数（`uint`）的映射，可以用于记录每个地址的余额。

### 2、映射的基本操作

映射的基本操作包括设置值、读取值和删除值。

#### 2.1、设置值

可以通过映射的键直接设置值，例如 `mappingName[key] = value;`。

```solidity
function setBalance(address user, uint amount) public {
    balances[user] = amount;
}
```

在此例中，`setBalance` 函数接受用户地址和余额，并在 `balances` 映射中为该地址设置余额。

#### 2.2、读取值

映射的值可以通过键来读取。如果键不存在，映射会返回值类型的默认值（例如 `uint` 为 `0`，`address` 为空地址 `0x0`）。

```solidity
function getBalance(address user) public view returns (uint) {
    return balances[user];
}
```

在此例中，`getBalance` 函数通过用户地址查找并返回其余额。

#### 2.3、删除值

可以通过 `delete` 关键字删除映射中的键值对。删除键值对会将键对应的值重置为默认值，而不是从映射中移除键。

```solidity
function removeBalance(address user) public {
    delete balances[user];
}
```

在此例中，`removeBalance` 函数删除指定地址的余额信息，将其值重置为 `0`。

### 3、映射的特性

- **无法遍历**：映射在 Solidity 中是不可遍历的，无法枚举所有键值对。因此，如果需要遍历映射中的数据，通常需要辅助数组来记录键值。
- **默认返回值**：如果映射中的键不存在，返回值会是该值类型的默认值。
- **存储在 storage**：映射总是存储在 `storage` 中，不支持 `memory` 或 `calldata`。
- **Gas 效率高**：映射在读取和写入时的操作效率较高，但无法删除键，删除操作只是将值重置为默认值。

### 4、映射与结构体结合使用

映射的值可以是结构体类型，这在管理多种属性时非常有用。例如，可以将映射的值类型定义为结构体，用于存储用户的详细信息。

#### 示例：映射与结构体结合

```solidity
pragma solidity ^0.8.0;

contract MappingWithStruct {
    struct UserInfo {
        string name;
        uint age;
    }

    mapping(address => UserInfo) public userInfos;

    function setUserInfo(address user, string memory name, uint age) public {
        userInfos[user] = UserInfo(name, age);
    }

    function getUserInfo(address user) public view returns (string memory, uint) {
        UserInfo storage info = userInfos[user];
        return (info.name, info.age);
    }
}
```

在此例中，`userInfos` 是一个从地址到 `UserInfo` 结构体的映射，`setUserInfo` 函数用于设置用户的详细信息，`getUserInfo` 函数用于查询用户的信息。

### 5、嵌套映射

映射的值类型还可以是另一个映射，这种结构称为嵌套映射（Nested Mapping），适用于需要多个键来定位数据的场景，例如权限控制、复杂数据存储等。

#### 示例：嵌套映射

```solidity
pragma solidity ^0.8.0;

contract NestedMappingExample {
    mapping(address => mapping(uint => bool)) public permissions;

    function setPermission(address user, uint permissionId, bool granted) public {
        permissions[user][permissionId] = granted;
    }

    function hasPermission(address user, uint permissionId) public view returns (bool) {
        return permissions[user][permissionId];
    }
}
```

在此例中，`permissions` 是一个嵌套映射，表示每个用户的权限控制。外层键是用户地址，内层键是权限 ID，值为 `bool`，表示是否授予该权限。

### 6、映射与数组结合使用

由于映射不可遍历，如果需要列出映射中的所有数据，可以借助数组存储键来实现。在更新映射时同时更新数组，方便后续遍历。

#### 示例：映射与数组结合

```solidity
pragma solidity ^0.8.0;

contract MappingWithArray {
    mapping(address => uint) public balances;
    address[] public users; // 用于存储所有键

    function setBalance(address user, uint amount) public {
        if (balances[user] == 0) {
            users.push(user); // 新用户，添加到数组
        }
        balances[user] = amount;
    }

    function getAllUsers() public view returns (address[] memory) {
        return users;
    }
}
```

在此例中，`balances` 是从地址到余额的映射，`users` 数组记录了所有键（地址）。通过 `getAllUsers` 可以获取映射中所有地址。

### 7、典型的映射应用场景

映射在 Solidity 中有多种典型应用场景：

- **账户余额管理**：通过 `address => uint` 映射存储用户的余额信息。
- **权限控制**：通过 `address => bool` 映射来表示用户是否有某项权限。
- **用户信息存储**：通过 `address => struct` 映射存储用户的详细信息。
- **多级关系映射**：通过嵌套映射表示复杂关系，例如用户的角色权限等。

### 8、映射的限制和注意事项

- **不可遍历**：映射无法遍历，因此无法列出所有键值对，解决方法是配合数组存储键来实现遍历。
- **无默认初始化**：在 Solidity 中，所有映射初始状态都是空的，键值未设置时会返回值类型的默认值。
- **不可删除键**：映射不支持完全删除键，删除操作只是将键对应的值重置为默认值。
- **存储成本高**：映射的值存储在 `storage` 中，频繁写入和更新映射可能会消耗较多 Gas，因此应尽量减少不必要的写入操作。

### 总结

在 Solidity 中，映射是一种灵活且高效的 `key-value` 数据结构，广泛应用于存储账户余额、权限控制、数据索引等场景。映射具有快速读写性能，结合结构体、嵌套映射和数组可以实现复杂的数据存储结构。理解映射的特性和限制，有助于开发者在 Solidity 中更高效地组织和管理数据。
