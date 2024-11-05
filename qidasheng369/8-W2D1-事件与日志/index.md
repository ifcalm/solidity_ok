# 8-W2D1-事件与日志

**目标：学习事件的使用及其重要性**

- 事件（event）的定义与触发
- 使用事件进行日志记录
- web3.js或ethers.js与事件交互

**笔记**

- 事件（event）的定义与触发
    - [**为什么需要事件？**](https://decert.me/tutorial/solidity/solidity-basic/event)
        
        以太坊虚拟机（EVM）是一个封闭的**沙盒环境**，如何解决以太坊和外部世界的通信问题呢？
        
        答案：是通过事件，在合约触发事件，将在链上生成**日志**，链下通过监听日志，获取沙盒环境内状态的变化。
        
    - **event事件的定义**
        
        事件是通过关键字`event`来声明的，`event` 不需要实现，只需要定义其事件名和参数。
        
        通过 `emit` 关键字可以触发事件，此时会在链上生成一个日志条目。
        
        代码示例：
        
        ```solidity
        pragma solidity >0.8.0;
        
        contract testEvent {
            constructor() public {
            }
            
            // 事件定义1
            event Deposit(address _from, uint _value);  // 定义事件
        
            function deposit(uint value) public {
                // 忽略其他的代码
                emit Deposit(msg.sender, value);  // 触发事件
            }
        }
        
        // 事件索引 indexed
        // 事件定义2
        event Deposit(address indexed _from, uint _value);  // 定义事件
        ```
        
        两种事件定义会有一些区别。
        
        1. 事件定义1：[日志1](https://sepolia.etherscan.io/tx/0x694f489b3b6ecb5cdbe9e718d5493cc5bb842ddf878fb0c70bdc7e3545c2a3e6#eventlog)
        2. 事件定义2：[日志2](https://sepolia.etherscan.io/tx/0xf43b5dfb69b4fe027dc1ace70fef16367be4f19506ddaeb41826c1f46c09f2c7#eventlog)
        
        有索引的参数放在 `topics` 下，没有索引的参数放在 `data` 下，以太坊会为日志地址及主题创建Bloom过滤器，以便更快的对数据检索。
        
    - **event事件的触发**
        
        通过 `emit` 关键字可以触发事件，此时会在链上生成一个日志条目。
        
        ```solidity
        pragma solidity >0.8.0;
        
        contract testEvent {
            constructor() public {
            }
                
            event Deposit(address _from, uint _value);  // 定义事件
        
            function deposit(uint value) public {
                // 忽略其他的代码
                emit Deposit(msg.sender, value);  // 触发事件
            }
        }
        ```
        
- 使用事件进行日志记录
    
    [**事件日志**](https://decert.me/tutorial/solidity/solidity-adv/event_logs)
    
    - **获取交易日志**
        
        事件日志由两部分组成：主题（topics）和数据（data）。
        
        一个[交易](https://etherscan.io/tx/0x81b886145c37afe0e42353e81f8f2896dd69fb86531a6d2ee9a13ced4d9321fb)日志例子：
        
        ```solidity
        {
            "jsonrpc": "2.0",
            "id": 1,
            "result": {
                // ...
                "logs": [
                    {
                        "address": "0xa0b86991c6218b36c1d19d4a2e9eb0ce3606eb48",
                        "topics": [
                            "0xddf252ad1be2c89b69c2b068fc378daa952ba7f163c4a11628f55a4df523b3ef",
                            "0x000000000000000000000000e4de99fbf078eff6ba02b076e503b304c2a7b1c5",
                            "0x0000000000000000000000006177d42837d88b1f6dd0ad14e2f80c17df3d0617"
                        ],
                        "data": "0x000000000000000000000000000000000000000000000000000000000ee6b280",
                        "blockNumber": "0x12d11ab",
                        "transactionHash": "0xe26a8add9f159c22d8d10f4b4069ed20ec9245ba07bf742dc2054d6b2561654a",
                        "transactionIndex": "0x4b",
                        "blockHash": "0x6a0e591eb3c266bd1d499f17caea929dc3a1b396b6e08d3d12b1df7abb4b1daf",
                        "logIndex": "0xb2",
                        "removed": false
                    }
                ],
                // ...
            }
        }
        ```
        
    - **事件签名**
        
        `事件签名`是由事件的名称和输入参数的类型列表按顺序组合后形成的一个字符串。
        
        例如，ERC20 转账事件：`event Transfer(address indexed from, address indexed to, uint256 value);`
        
        其事件签名为：`Transfer(address,address,uint256)`。
        
        对事件签名进行 keccak256 运算得到事件签名哈希值。
        
        上面这个真实交易的第一个主题值就是事件签名哈希值
        
        （`keccak256(Transfer(address,address,uint256)) = "0xddf252ad1be2c89b69c2b068fc378daa952ba7f163c4a11628f55a4df523b3ef"`）
        
        事件签名的哈希值作为事件的第一个主题，是事件唯一性的关键，使得不同的事件即使名称相同，只要参数类型列表不同，其签名也会不同，从而确保了区块链上的事件可以被准确地识别和搜索。
        
    - [**主题值的计算**](https://decert.me/tutorial/solidity/solidity-adv/event_logs/#%E4%B8%BB%E9%A2%98%E5%80%BC%E7%9A%84%E8%AE%A1%E7%AE%97)
        
        见⬆️链接🔗
        
    - [**匿名事件**](https://decert.me/tutorial/solidity/solidity-adv/event_logs/#%E4%BA%8B%E4%BB%B6%E7%AD%BE%E5%90%8D)
        
        见⬆️链接🔗
        
    - [**事件日志数据**](https://decert.me/tutorial/solidity/solidity-adv/event_logs/#%E4%BA%8B%E4%BB%B6%E6%97%A5%E5%BF%97%E6%95%B0%E6%8D%AE)
        
        `event Transfer(address indexed from, address indexed to, uint256 value);` 在这个例子中，value 是数据参数。
        
- web3.js或ethers.js与事件交互
    - [**获取事件**](https://decert.me/tutorial/solidity/solidity-basic/event#%E8%8E%B7%E5%8F%96%E4%BA%8B%E4%BB%B6)
        - 从外部如何获取到事件信息，通常我们有**三个方法**：
            1. 通过交易收据获取事件
            2. 使用过滤器获取过去事件
            3. 使用过滤器获取实时事件
        - **通过交易收据获取事件**
            
            在交易收据中，会记录交易完整的日志，知道交易的Hash，就可以获取事件。
            
            JSON-RPC 提供[eth_gettransactionreceipt](https://ethereum.org/en/developers/docs/apis/json-rpc/#eth_gettransactionreceipt) 获取交易收集， [Web3.js](https://learnblockchain.cn/docs/web3.js/) 、 [ethers.js](https://learnblockchain.cn/ethers_v5/) 等库包含了JSON-RPC。
            
            [Remix 控制台](https://decert.me/tutorial/solidity/tools/remix)，使用`web3.eth.getTransactionReceipt(hash)` 获取收据。
            
            ```solidity
            // 控制台
            web3.eth.getTransactionReceipt("0x5bc2d1fe7d696191ab70bc14a65e90b3c5fc4156c4a1bee979d0d4c5a0a5bc36").then(console.log)
            ```
            
        - **使用过滤器获取事件**
            
            Web3.js 对应的接口为 [getpastlogs](https://web3js.readthedocs.io/en/v1.2.11/web3-eth.html#getpastlogs)， Ethers.js 对应的接口为 [getLogs](https://learnblockchain.cn/ethers_v5/api/providers/provider/#Provider--log-methods)
            
            ```solidity
            web3.eth.getPastLogs({
                address: "0xd9145CCE52D386f254917e481eB44e9943F39138",
                topics: ["0xe1fffcc4923d04b559f4d29a8bfc6cda04eb5b0d3c460751c2402c5c5cc9109c"]
            })
            .then(console.log);
            ```
            
        - **使用过滤器获取实时事件**
            
             JSON-RPC 提供的 [eth_subscribe](https://docs.infura.io/networks/ethereum/json-rpc-methods/subscription-methods/eth_subscribe) 订阅方法，Web3.js 对应的接口 [web3.eth.subscribe](https://learnblockchain.cn/docs/web3.js/web3-eth-subscribe.html?highlight=subscribe%20logs#web3-eth-subscribe)， Ethers.js 在 Provider 使用 [`on` 进行监听](https://learnblockchain.cn/ethers_v5/api/providers/provider/)。需要注意的是， 要订阅需要和节点建立**Web Socket 长连接**。
            
            ```solidity
            // Web3.js 示例：
            const web3 = new Web3("ws://localhost:8545");  
            
            var subscription = web3.eth.subscribe('logs', {
                address: '0x123456..',
                topics: ['0x12345...']
            }, function(error, result){
                if (!error)
                    console.log(result);
            });
            
            // Ethers.js 示例：
            let provider = new ethers.providers.WebSocketProvider('ws://127.0.0.1:8545/')
            
            filter = {
                address: "0x123456",
                topics: [
                    '0x12345...' // utils.id("Deposit(address,uint256)")
                ]
            }
            provider.on(filter, (log, event) => {
                //  
            })
            ```
            
    - [**善用事件**](https://decert.me/tutorial/solidity/solidity-basic/event#%E5%96%84%E7%94%A8%E4%BA%8B%E4%BB%B6)
        - 优先考虑使用事件的场景
            
            以下两个场景应该尽量优先考虑使用事件。
            
            1. **如果合约中没有使用该变量，应该考虑用事件存储数据**
            2. **如果需要完整的交易历史，请使用事件**
        - **用事件存储数据**
            
            智能合约开发的最佳实践是：**如无必要，勿加存储。**
            
            倘若在合约中，**没有任何函数读取该变量，我们应该使用事件来存储数据**，Gas 成本降低很多。
            
            使用事件版本的`deposit()` 的Gas 消耗是 22750 。
            
            ```solidity
            // gas: 22750
            function deposit(uint value) public {
                emit Deposit(msg.sender, value);  // 触发事件
            }
            ```
            
            对比看一下用映射来存储数据的版本：
            
            ```solidity
            contract testDeposit {
            
                mapping(address => uint) public deposits;
                
                // Gas: 43577
                function deposit(uint value) public {
                    deposits[msg.sender] = value;
                }
            }
            ```
            
            `deposit()` 的Gas 消耗是 43577 。
            
        - **事件是“只写的数据库“[​](https://decert.me/tutorial/solidity/solidity-basic/event#%E4%BA%8B%E4%BB%B6%E6%98%AF%E5%8F%AA%E5%86%99%E7%9A%84%E6%95%B0%E6%8D%AE%E5%BA%93)**
            
            每次我们在触发事件时，这个事件的日志就会记录在区块链上，每次事件追加一条记录，因此事件实际上就是一个只写的数据库（只添加数据）。我们可以按照自己想要的方式在关系型数据库中重建所有的记录。
            
            当然要实现这一点，**所有的状态变化必须触发事件**才行。
            
            而存储状态则不同，状态变量是一个可修改的”数据库“， 读取变量获取的是当前值。
            
            **如果需要完整的交易历史，就需要使用事件**。