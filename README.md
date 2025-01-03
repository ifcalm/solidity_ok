# solidity 共学第一期规划

## 如何报名和打卡？

### 共学报名：
**Step1:** 直接 Fork 本仓库。  
**Step2:** 在仓库里复制 Template文件夹，以你的GitHub名称命名，创建你的个人学习目录，文件夹内部的排版按照自己的学习风格进行设置。  
**Step3:** 创建一个 PR 到当前仓库，共学助教会对你的 PR 进行 review，review 通过后，你的 PR 会被 merge 到 main 分支。即报名成功。  

*报名成功后添加微信`ifcalm_ok`, 备注`solidity共学`, 与同一期学友群内交流学习。*

### 共学证明打卡：
每日24:00前提交学习记录，并PR到该仓库，merge之后即为完成当日学习任务。

-------------------
## 共学时间与地点
### 报名时间
第 1 期报名从 2024 年 10 月 20 号开始。报名成功即可开始课前的自主学习，截止 10 月 28 号晚 23 点（北京时间），之后不再接受报名。

### 正式共学 
第 1 期从 2024 年 10 月 29 号到 11 月 15 号 ，共 18 天，建议每天投入尽可能多的时间来学习。

### 共学地点
自由安排 / 线上或线下交流会议

-------------------
## 共学内容
- [Solidity开发教程](https://decert.me/tutorial/solidity/intro)
- [Solidity文档](https://docs.soliditylang.org/zh/latest/)
- [区块链入门开发](https://learn.microsoft.com/zh-cn/training/paths/ethereum-blockchain-development/)

--------------------

### 参考学习路线
以下是一个为期2周的 Solidity 学习计划，详细列出了每天的学习内容，涵盖了从基础到高级的各方面知识。
## 第1周：Solidity基础与高级语法

### 第1天：Solidity入门与背景
#### 目标：了解Solidity的历史和应用领域，安装开发环境
- Solidity简介与智能合约的历史背景
- Ethereum虚拟机（EVM）工作原理
- 安装与配置开发环境（Remix, VSCode + Hardhat/Truffle）
- Solidity的开发工具学习

### 第2天：基础语法（1）
#### 目标：掌握Solidity的基础数据类型与变量
- 数据类型（uint, int, bool, address, bytes）
- 变量声明与作用域
- 常量和不可变变量（constant, immutable）
- 数据的存储位置（storage, memory, calldata）

### 第3天：基础语法（2）
#### 目标：掌握Solidity的基本结构与控制流
- 函数定义与修饰符
- 条件语句（if-else）和循环（for, while）
- 函数的可见性（public, private, internal, external）
- 函数的返回值

### 第4天：函数与修饰符
#### 目标：深入理解函数和访问控制
- 函数修饰符（pure, view, payable）
- 函数修饰符的应用（modifier）
- 错误处理（require, assert, revert）

### 第5天：Solidity的数据结构
#### 目标：掌握Solidity中的复杂数据结构
- 数组和映射（mapping）
- 结构体（struct）和枚举（enum）
- 动态与静态数组的使用
- 映射的应用场景与限制

### 第6天：合约间交互与继承
#### 目标：学习合约之间的交互和继承
- 合约之间的调用（this，call, delegatecall）
- 继承（inheritance）和重写（override）
- 接口与抽象合约的定义和使用
- super关键字

### 第7天：项目实战：简单代币合约
#### 目标：完成第一个简单项目，编写一个ERC-20代币
- 学习ERC-20标准
- 编写符合ERC-20标准的简单代币合约
- 在Remix中部署并测试

## 第2周：高级主题与优化

### 第8天：事件与日志
#### 目标：学习事件的使用及其重要性
- 事件（event）的定义与触发
- 使用事件进行日志记录
- web3.js或ethers.js与事件交互

### 第9天：安全性（1）
#### 目标：掌握智能合约中的常见安全问题
- 重入攻击（Reentrancy）与防御（Reentrancy Guard）
- 整数溢出与下溢（Solidity 0.8后的变化）
- `tx.origin`的风险

### 第10天：安全性（2）
#### 目标：深入了解更多安全问题与解决方案
- 自毁函数（selfdestruct）的风险
- `delegatecall` 的安全风险
- 访问控制与权限管理
- 防止拒绝服务（DOS）攻击

### 第11天：Gas优化
#### 目标：理解并应用gas优化技巧
- Gas使用的原理与优化的基本策略
- 减少存储读写操作
- 避免使用动态数组的某些操作
- 内联汇编（Yul）的简单使用

### 第12天：Solidity最佳实践
#### 目标：学习Solidity开发的最佳实践
- 代码风格与可读性
- 使用OpenZeppelin库进行合约开发
- 使用Hardhat进行测试与调试
- 合约的升级模式（如代理合约）

### 第13天：项目实战：众筹合约
#### 目标：完成第二个项目，编写一个简单的众筹合约
- 需求分析：用户可以创建众筹项目，其他人可以向项目捐款
- 编写合约：使用映射来跟踪项目和资金
- 测试与优化：确保安全性和效率

### 第14天：总结与展示
#### 目标：总结学习内容并展示项目成果
- 回顾和总结两周学习中的重点
- 优化两个项目合约，检查并应用最佳实践
- 向同学或导师展示你的项目并接收反馈

### 项目概述
#### 简单ERC-20代币合约
   - 学习ERC-20标准并实现代币合约
   - 目标是理解合约的标准接口和如何发行代币

#### 众筹合约
   - 允许用户创建众筹项目并接受捐赠
   - 重点在于合约的安全性（如防止重入攻击）和用户交互体验

希望这个计划能帮助你系统地学习Solidity，并通过项目巩固知识。


------------
### 附录

- [Remix工具](https://remix-project.org/?lang=zh)
- [MarkDown语法](https://markdown.com.cn/basic-syntax/)
- [速学150个Linux命令](https://www.bilibili.com/video/BV12L411a7Ne)
