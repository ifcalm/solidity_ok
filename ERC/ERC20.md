## 编写一个 ERC20 代币合约

以下是一个标准的 **ERC20 代币合约**，使用 Solidity 编写。该合约实现了 ERC20 标准的所有功能，包括代币的转账、批准和授权，以及代币的发行和查询。

### ERC20 代币合约

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

interface IERC20 {
    // 返回总供应量
    function totalSupply() external view returns (uint256);

    // 查询某个账户的余额
    function balanceOf(address account) external view returns (uint256);

    // 转账代币
    function transfer(address recipient, uint256 amount) external returns (bool);

    // 查询某个账户可以从另一个账户提取的剩余授权额度
    function allowance(address owner, address spender) external view returns (uint256);

    // 授权某个账户可以使用一定额度的代币
    function approve(address spender, uint256 amount) external returns (bool);

    // 从某个账户转移代币（基于授权）
    function transferFrom(address sender, address recipient, uint256 amount) external returns (bool);

    // 转账事件
    event Transfer(address indexed from, address indexed to, uint256 value);

    // 授权事件
    event Approval(address indexed owner, address indexed spender, uint256 value);
}

contract ERC20 is IERC20 {
    string public name;
    string public symbol;
    uint8 public decimals;
    uint256 private _totalSupply;

    // 记录每个地址的余额
    mapping(address => uint256) private _balances;

    // 记录授权额度：owner -> (spender -> amount)
    mapping(address => mapping(address => uint256)) private _allowances;

    // 构造函数，初始化代币名称、符号、总供应量和小数位数
    constructor(string memory _name, string memory _symbol, uint8 _decimals, uint256 initialSupply) {
        name = _name;
        symbol = _symbol;
        decimals = _decimals;
        _mint(msg.sender, initialSupply);
    }

    // 返回总供应量
    function totalSupply() external view override returns (uint256) {
        return _totalSupply;
    }

    // 查询余额
    function balanceOf(address account) external view override returns (uint256) {
        return _balances[account];
    }

    // 转账
    function transfer(address recipient, uint256 amount) external override returns (bool) {
        _transfer(msg.sender, recipient, amount);
        return true;
    }

    // 查询授权额度
    function allowance(address owner, address spender) external view override returns (uint256) {
        return _allowances[owner][spender];
    }

    // 授权
    function approve(address spender, uint256 amount) external override returns (bool) {
        _approve(msg.sender, spender, amount);
        return true;
    }

    // 基于授权的转账
    function transferFrom(address sender, address recipient, uint256 amount) external override returns (bool) {
        uint256 currentAllowance = _allowances[sender][msg.sender];
        require(currentAllowance >= amount, "ERC20: transfer amount exceeds allowance");
        _transfer(sender, recipient, amount);
        _approve(sender, msg.sender, currentAllowance - amount);
        return true;
    }

    // 内部转账函数
    function _transfer(address sender, address recipient, uint256 amount) internal {
        require(sender != address(0), "ERC20: transfer from the zero address");
        require(recipient != address(0), "ERC20: transfer to the zero address");
        require(_balances[sender] >= amount, "ERC20: transfer amount exceeds balance");

        _balances[sender] -= amount;
        _balances[recipient] += amount;
        emit Transfer(sender, recipient, amount);
    }

    // 内部授权函数
    function _approve(address owner, address spender, uint256 amount) internal {
        require(owner != address(0), "ERC20: approve from the zero address");
        require(spender != address(0), "ERC20: approve to the zero address");

        _allowances[owner][spender] = amount;
        emit Approval(owner, spender, amount);
    }

    // 内部铸造函数
    function _mint(address account, uint256 amount) internal {
        require(account != address(0), "ERC20: mint to the zero address");

        _totalSupply += amount;
        _balances[account] += amount;
        emit Transfer(address(0), account, amount);
    }

    // 内部销毁函数
    function _burn(address account, uint256 amount) internal {
        require(account != address(0), "ERC20: burn from the zero address");
        require(_balances[account] >= amount, "ERC20: burn amount exceeds balance");

        _balances[account] -= amount;
        _totalSupply -= amount;
        emit Transfer(account, address(0), amount);
    }
}
```

### 合约功能说明

1. **基本信息**：
   - `name`：代币名称。
   - `symbol`：代币符号（通常是代币的缩写，如 `ETH`）。
   - `decimals`：代币的小数位数（`18` 是最常见的选择，表示支持 10^18 最小单位）。

2. **基本功能**：
   - `transfer`：将代币从调用者账户转移到指定账户。
   - `approve`：授权其他账户可以使用一定额度的代币。
   - `transferFrom`：从授权账户转移代币。
   - `balanceOf`：查询账户的代币余额。
   - `allowance`：查询某账户对其他账户的授权额度。

3. **事件**：
   - `Transfer`：在代币转移时触发，用于记录转账信息。
   - `Approval`：在授权操作时触发，用于记录授权信息。

4. **铸造和销毁**：
   - `_mint`：内部函数，用于向指定账户铸造代币。
   - `_burn`：内部函数，用于销毁指定账户的代币。

### 如何使用

#### 部署代币合约
在部署合约时，可以通过构造函数指定代币的名称、符号、小数位数和初始供应量。例如：

```javascript
const ERC20 = await ethers.getContractFactory("ERC20");
const token = await ERC20.deploy("MyToken", "MTK", 18, ethers.utils.parseUnits("1000000", 18));
await token.deployed();
```

- 代币名称：`MyToken`
- 代币符号：`MTK`
- 小数位数：`18`
- 初始供应量：`1,000,000` 个（18 位小数）

#### 前端与代币合约交互
使用 Web3.js 或 Ethers.js 与合约交互，以下是一些常见操作：

```javascript
// 查询账户余额
let balance = await token.balanceOf(userAddress);
console.log(`用户余额: ${ethers.utils.formatUnits(balance, 18)} MTK`);

// 转账
await token.transfer(recipientAddress, ethers.utils.parseUnits("100", 18));

// 授权
await token.approve(spenderAddress, ethers.utils.parseUnits("50", 18));

// 查询授权额度
let allowance = await token.allowance(userAddress, spenderAddress);
console.log(`授权额度: ${ethers.utils.formatUnits(allowance, 18)} MTK`);
```

### 扩展功能

1. **代币铸造和销毁**：
   - 合约管理员可以使用 `_mint` 和 `_burn` 方法来增加或减少代币供应。

2. **限制铸造权限**：
   - 可以扩展合约，加入 `Ownable` 模式，限制只有管理员可以调用 `_mint` 和 `_burn`。

3. **交易费用**：
   - 实现 `transfer` 和 `transferFrom` 的自定义逻辑，例如收取手续费。

4. **Pausable 功能**：
   - 增加暂停功能，防止在紧急情况下进行代币交易。

### 总结

上述合约是标准的 **ERC20 代币** 实现，涵盖了 ERC20 代币的核心功能。开发者可以在此基础上扩展更多自定义功能，如铸造权限控制、交易费、代币锁仓等，以满足不同项目的需求。

---

