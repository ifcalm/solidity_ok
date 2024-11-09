# 10-W2D3-安全性（2）

**目标：深入了解更多安全问题与解决方案**

- 自毁函数（selfdestruct）的风险
- `delegatecall` 的安全风险
- 访问控制与权限管理
- 防止拒绝服务（DOS）攻击

**笔记**

- 自毁函数（selfdestruct）的风险
    
    [**销毁合约**](https://decert.me/tutorial/solidity/solidity-adv/destroy_contract)
    
    - **selfdestruct[​](https://decert.me/tutorial/solidity/solidity-adv/destroy_contract#selfdestruct)**
        
        `selfdestruct` 是 Solidity 中的一种特殊函数，用于销毁合约。销毁合约后，合约的代码和存储都将从区块链中移除，合约剩余的以太币将被发送到指定的地址。
        
        - **语法[​](https://decert.me/tutorial/solidity/solidity-adv/destroy_contract#%E8%AF%AD%E6%B3%95)**
            
            ```jsx
            selfdestruct(address payable recipient);
            ```
            
            `recipient`: 当合约被销毁时，剩余的资金将被发送到这个地址。该地址必须是 `payable` 的。
            
        - **使用示例**
            
            ```solidity
            // SPDX-License-Identifier: MIT
            pragma solidity ^0.8.0;
            
            contract SimpleContract {
                address payable public owner;
            
                // 构造函数，设置合约拥有者
                constructor() {
                    owner = msg.sender;
                }
            
                // 接受以太币的函数
                function deposit() public payable {}
            
                // 合约拥有者可以销毁合约
                function destroy() public {
                    require(msg.sender == owner, "You are not the owner");
                    selfdestruct(payable(owner));
                }
            }
            ```
            
            在这个示例中，`SimpleContract` 合约有一个 `deposit` 函数可以接收以太币存款。合约的拥有者可以调用 `destroy` 函数来销毁合约。 `destroy` 函数使用了 `selfdestruct(payable(owner))` 把合约剩余的以太币发送回拥有者地址，并销毁合约。
            
        - **应用场景**
            1. 紧急情况处理
                
                在发现严重漏洞或遭受攻击时，可以通过 `selfdestruct` 快速销毁合约并确保它无法再被调用，防止进一步损失。
                
            2. 资金返还
                
                当合约的某些部分完成使命后，可能需要清空并返还剩余资金。使用 `selfdestruct` 可以将资金发送到特定地址。注意，ERC-20 等代币无法返还。
                
    - 自毁函数的风险
        
        ## **注意事项[​](https://decert.me/tutorial/solidity/solidity-adv/destroy_contract#%E6%B3%A8%E6%84%8F%E4%BA%8B%E9%A1%B9)**
        
        1. 确保只有授权用户可以调用 `selfdestruct` 函数
        2. 在使用 `selfdestruct` 之前，考虑是否有其他更安全的替代方案
        3. 记住，即使合约被销毁，它的交易历史仍会保留在区块链上
        
        > `selfdestruct` 是 Solidity 中一个功能强大的函数，用于安全有效地销毁合约并返还剩余资金。正确地使用 `selfdestruct` 可以有效地管理智能合约的生命周期，但错误的使用也可能导致不可预见的风险。在使用该函数时，务必做好周全的设计和测试工作，以确保合约的安全性和可靠性。
        > 
- `delegatecall` 的安全风险
    - 回顾**`call`与`delegatecall`**
        
        背景：有时我们在编写合约时，还不知道目标合约的接口，甚至是目标合约还没有创建。显然我们无法在编写智能合约钱包时，预知未来要交互的合约接口。
        
        这个问题该如何解决呢？
        
        使用地址的底层调用功能，是在运行时动态地决定调用目标合约和函数， 因此在编译时，可以不知道具体要调用的函数或方法。
        
        ## **底层调用[​](https://decert.me/tutorial/solidity/solidity-adv/addr_call#%E5%BA%95%E5%B1%82%E8%B0%83%E7%94%A8)**
        
        地址类型还有3个底层的成员函数：
        
        - `targetAddr.call(bytes memory abiEncodeData) returns (bool, bytes memory)`
        - `targetAddr.delegatecall(bytes memory abiEncodeData) returns (bool, bytes memory)`
        - `targetAddr.staticcall(bytes memory abiEncodeData) returns (bool, bytes memory)`
            
            `call` 是常规调用，`delegatecall` 为委托调用，`staticcall` 是静态调用（不修改合约状态， 相当于调用 `view` 方法）。
            
        
        这三个函数都可以用于与目标合约（`targetAddr`）交互，三个函数均接受 [abi 编码数据](https://decert.me/tutorial/solidity/solidity-adv/ABI.md)作为参数（`abiEncodeData`）来调用对应的函数。
        
        ### **调用举例[​](https://decert.me/tutorial/solidity/solidity-adv/addr_call#%E8%B0%83%E7%94%A8%E4%B8%BE%E4%BE%8B)**
        
        在 [接口与函数调用](https://decert.me/tutorial/solidity/solidity-adv/17_interface.md) 一节中，我们介绍过通过 `ICounter(_counter).set(10);` 调用以下`set`方法：
        
        ```solidity
        contract Counter {
          uint public counter;
        
          function set(uint x) public {
              counter = x;
          }
        }
        
        ```
        
        在 [ABI 一节](https://decert.me/tutorial/solidity/solidity-adv/ABI.md) 我们知道调用 `set()`函数，实际上发送的是 ABI 编码数据`0x60fe47b1000000000000000000000000000000000000000000000000000000000000000a`
        
        通过`call` 就可以直接使用编码数据发起调用：
        
        ```solidity
        bytes memory payload = abi.encodeWithSignature("set(uint256)", 10);
        (bool success, bytes memory returnData) = address(_counter).call(payload);
        require(success);
        ```
        
        这段代码在功能上和 `ICounter(_counter).set(10);` 等价，但 `call`的方式可以动态构造 `payload` 编码数据对函数进行调用，从而实现对任意函数、任何类型及任意数量的参数的调用。
        
        ## **call 与 delegatecall[​](https://decert.me/tutorial/solidity/solidity-adv/addr_call#call-%E4%B8%8E-delegatecall-1)**
        
        `call` 是常规调用，`delegatecall` 为委托调用，`staticcall` 是静态调用，不修改合约状态， 相当于普通的 `view` 方法调用。
        
        常规调用 `call` 与 委托调用 `delegatecall` 的区别是什么呢？
        
        1. 使用`call`，上下文从 `原合约`的地址空间跳到了`调用合约`地址空间
        2. 使用`delegatecall`，上下文保证在`原合约`的地址空间
    - `delegatecall` 的安全风险
        
        [**Delegate Call 和 存储冲突**](https://decert.me/tutorial/solidity/solidity-adv/proxy#delegate-call-%E5%92%8C-%E5%AD%98%E5%82%A8%E5%86%B2%E7%AA%81)
        
        `delegatecall`是一种特殊的函数调用，使得一个合约（比如代理合约）能够调用另一个合约（比如逻辑合约）的函数，并在代理合约的存储环境中执行这些函数。
        
        然而，这也带来了存储冲突的风险。存储冲突主要发生在逻辑合约和代理合约的存储布局不匹配的情况下。
        
        两种可能的场景：
        
        - 场景1：存储布局不一致
            
            假设逻辑合约`Logic`中先声明的是`uint public count;`，而代理合约 Proxy 中先声明的是`address public logicAddress;`。这种情况下，当`Proxy`使用`delegatecall`调用`Logic`中的`incrementCounter()`方法时，它本意是修改`count`的值
            
            但由于存储布局的不匹配，实际上它会错误地改变代理合约`logicAddress`的存储位置的内容。如下所示：
            
            | Proxy | Logic |  |
            | --- | --- | --- |
            | address logicAddress | uint256 count | <=== 存储冲突 |
            | uint256 count | address not_used |  |
        - 场景2：升级导致的冲突
            
            即使最初的存储布局是匹配的，合约升级也可能引入新的存储冲突。
            
            假设`Logic`合约在某次升级中添加了新的状态变量或者改变了变量的顺序。如果新的逻辑合约被代理合约引用，而没有相应调整代理合约的存储布局，那么执行`delegatecall`时就会出现预期之外的存储修改。
            
            比如，`Logic V2`中，调整了变量`foo`和`bar`的位置，会导致存储冲突：
            
            | Proxy | Logic V1 | Logic V2 |  |
            | --- | --- | --- | --- |
            | address logicAddress | address not_used | address not_used |  |
            | uint256 count | uint256 count | uint256 count |  |
            | address foo | address foo | address bar | <=== 存储冲突，V2 bar 变量对应 Proxy 中的 foo |
            | address bar | address bar | address foo | <=== 存储冲突，V2 foo 变量对应 Proxy 中的 bar |
        
        `delegatecall`是一个非常强大的功能，它允许合约逻辑的重用和灵活的升级。然而，它也带来了存储冲突的风险，这要求开发者在设计和实施时必须极其谨慎。
        
        在实际的区块链开发工作中，为了确保代理合约的安全和升级的灵活性，通常采用成熟的开源库，如 OpenZeppelin。OpenZeppelin 提供了一些模块来处理代理合约的升级和存储布局问题，还提供了常见的几种代理合约升级模式。下一章节将介绍常用的代理合约升级模式。
        
        ## **总结[​](https://decert.me/tutorial/solidity/solidity-adv/proxy#%E6%80%BB%E7%BB%93)**
        
        代理合约是智能合约开发中一个极其强大且必需的模式，特别是在需要可升级性解决方案时。然而，要正确实现该模式，需要对 Solidity 和智能合约的行为有深入的理解。正确的设计和实现可确保系统的安全性和可靠性，同时也提供了卓越的灵活性和可维护性。
        
- 访问控制与权限管理
    
    在Solidity中，访问控制和权限管理是智能合约安全性的关键组成部分。以下是一些常用的方法来在Solidity中实现访问控制和权限管理：
    
    ### 1. 使用`private`, `internal`, `public`, `external`可见性修饰符
    
    Solidity提供了几种可见性修饰符来限制函数和状态变量的访问：
    
    - `private`: 只在当前合约中可见。
    - `internal`: 在当前合约和继承的合约中可见。
    - `public`: 在合约内外都可见。
    - `external`: 只能从合约外部调用。
    
    ### 2. 使用`onlyOwner`模式
    
    你可以创建一个合约，其中有一个`owner`状态变量，并定义一个修饰器来确保只有合约的拥有者可以调用某些函数。
    
    ```solidity
    pragma solidity ^0.8.0;
    contract Ownable {
        address private _owner;
        constructor() {
            _owner = msg.sender;
        }
        modifier onlyOwner() {
            require(msg.sender == _owner, "Not owner");
            _;
        }
        function owner() public view returns (address) {
            return _owner;
        }
    }
    
    ```
    
    ### 3. 角色基权限控制
    
    你可以定义不同的角色，并为每个角色赋予不同的权限。
    
    ```solidity
    pragma solidity ^0.8.0;
    contract AccessControl {
        bytes32 public constant ADMIN_ROLE = keccak256("ADMIN_ROLE");
        bytes32 public constant USER_ROLE = keccak256("USER_ROLE");
        mapping(bytes32 => mapping(address => bool)) public roles;
        modifier onlyRole(bytes32 role) {
            require(roles[role][msg.sender], "Not authorized");
            _;
        }
        constructor() {
            roles[ADMIN_ROLE][msg.sender] = true;
        }
        function grantRole(bytes32 role, address account) public onlyRole(ADMIN_ROLE) {
            roles[role][account] = true;
        }
        function revokeRole(bytes32 role, address account) public onlyRole(ADMIN_ROLE) {
            roles[role][account] = false;
        }
    }
    
    ```
    
    ### 4. 使用OpenZeppelin的访问控制库
    
    OpenZeppelin是一个流行的Solidity库，它提供了许多经过审查的智能合约组件，包括访问控制。
    
    ```solidity
    pragma solidity ^0.8.0;
    import "@openzeppelin/contracts/access/Ownable.sol";
    contract MyContract is Ownable {
        // 使用Ownable合约的onlyOwner修饰器
        function restrictedFunction() public onlyOwner {
            // 只有合约拥有者可以调用的代码
        }
    }
    
    ```
    
    ### 5. 多签钱包
    
    对于需要多个人同意的操作，可以实现一个多签钱包，其中只有当足够多的预定义签名人同意时，交易才会被执行。
    
    ### 注意事项：
    
    - 使用权限控制时，请确保只有必要的函数是公开的，其他函数应尽可能保持私有或内部可见。
    - 记得撤销不必要的权限，特别是在合约部署后。
    - 对于权限更改操作，应进行适当的日志记录，以便于审计。
    通过以上方法，你可以在Solidity中有效地实现访问控制和权限管理，确保智能合约的安全性和可靠性。
- 防止拒绝服务（DOS）攻击
    
    在Solidity中，拒绝服务（DoS）攻击是一种常见的攻击方式，攻击者通过某种方式使得合约的功能不可用或非常昂贵，从而使合法用户无法使用合约。以下是一些策略来帮助防止DoS攻击：
    
    ### 1. 防止过多的gas消耗
    
    - **限制循环和递归**：确保合约中的循环有明确的退出条件，并且不要递归调用可能消耗大量gas的函数。
    - **避免过大的数组操作**：大数组操作可能消耗大量gas，应该尽量减少。
    - **使用检查-效果-交互模式**：在执行任何状态更改之前，先进行所有必要的检查。
    
    ### 2. 设置合理的费用
    
    - **收取足够的交易费用**：确保调用合约的函数需要支付合理的费用，这样即使有人尝试DoS攻击，也会付出高昂的经济代价。
    - **设置交易的上限**：为函数调用设置最大可能的gas消耗，防止攻击者通过发送大量交易来消耗合约的余额。
    
    ### 3. 限制外部调用
    
    - **限制外部合约调用**：避免对外部合约的不受信任调用，因为这些调用可能会失败或故意被设计为消耗大量gas。
    - **使用`reentrancy guard`**：防止合约在调用外部合约时被重新进入。
    
    ### 4. 确保操作的可终止性
    
    - **设置超时机制**：对于需要一定时间完成的操作，设置超时机制，确保操作在特定时间后可以被终止。
    - **提供紧急停止功能**：合约应该有一个可以被管理员触发的紧急停止机制，以暂停合约功能。
    
    ### 5. 限制合约的存储使用
    
    - **避免无限制的存储增长**：确保合约不会因为不断增长的存储需求而导致性能下降。
    
    ### 6. 限制地址操作
    
    - **避免地址白名单**：如果可能，避免使用地址白名单，因为攻击者可以通过创建大量地址来填充白名单，导致合法用户无法加入。
    - **限制地址数量**：如果必须使用白名单，限制可以加入的地址数量。
    
    ### 7. 使用时间锁
    
    - **延迟执行关键操作**：对于关键操作，可以设置一个时间锁，使得操作在被执行前有一段时间的等待期。
    
    ### 8. 防止整数溢出和下溢
    
    - **使用SafeMath库**：使用OpenZeppelin的SafeMath库来防止整数溢出和下溢。
    
    ### 9. 审计和测试
    
    - **代码审计**：对合约进行彻底的审计，以发现潜在的安全漏洞。
    - **单元测试和压力测试**：进行充分的单元测试和压力测试，确保合约在极端条件下也能正常工作。
    通过上述措施，可以大大降低Solidity智能合约遭受DoS攻击的风险。然而，安全性是一个持续的过程，需要开发者不断地学习和适应新的攻击手段。