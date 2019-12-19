---
title: eth的gas估算
date: 2018-06-15 11:00:06
categories: 
- [区块链]
---

`eth`平台消耗的Gas满足如下公式$cost = gasUsage * gasPrice $

- $gasUsage为gas的消耗量$
- $gasPrice为gas的单价，与交易打包确认时间负相关$

*由于`gasPrice`主要影响交易等待打包确认时间，由`eth`平台繁忙程度所影响，可以查看参考链接中关于`gasPrice` 和交易等待时间的影响结合自身的业务决定，故本文只着重介绍普通交易和智能合约运行所需的`gasUsage`的估算，在计算最终`cost`时，统一采用$gasPrice = 1 Gwei$*

#### 普通交易所需的Gas

在`eth`平台执行普通的交易转账需要消耗`gas`，消耗的`gas` 满足一下公式:

$cost = gasUsage * gasPrice = (账户发起者减少的资产 - 账户接收者增加的资产)$

- 其中$gasUsage为固定值=21000$
- $gasPrice为gas的单价，与交易打包确认时间负相关$

#### 智能合约所需的Gas

普通转账交易所需的`gas`固定值为`21000，`调用智能合约方法所需要的`gas`并不一定,主要由占用的资源(`cpu`，内存等)决定（计算量影响`cpu`），占用的资源越多，所需要的`gas`也越多。我们主要分析两个智能合约的gas消耗估算：

- 智能合约`token`转账

  采用`truffle demo`的`MetaCoin.sol`进行智能合约的测试，合约内容如下：

  ```sol
  pragma solidity ^0.4.2;
  import "./ConvertLib.sol";
  
  contract MetaCoin {
  	mapping (address => uint) balances;
  
  	event Transfer(address indexed _from, address indexed _to, uint256 _value);
  
  	function MetaCoin() {
  		balances[tx.origin] = 10000;
  	}
  	// 转账逻辑
  	function sendCoin(address receiver, uint amount) returns(bool sufficient) {
  		if (balances[msg.sender] < amount) return false;
  		balances[msg.sender] -= amount;
  		balances[receiver] += amount;
  		Transfer(msg.sender, receiver, amount);
  		return true;
  	}
  
  	function getBalanceInEth(address addr) returns(uint){
  		return ConvertLib.convert(getBalance(addr),2);
  	}
  
  	function getBalance(address addr) returns(uint) {
  		return balances[addr];
  	}
  }
  ```

  使用以上智能合约，进行一次转账操作(`Send Meta Token,发送的token数量为1`)：

  ```json
  Transaction: 0xc3591a4a8614721ce176f58782455b127d1946dbcf10876c7e7c4fa169da849b
  Gas usage: 36050
  Block Number: 7
  Block Time: Fri Jun 15 2018 11:05:41 GMT+0800 (CST)
  ```

  通过`testrpc`日志可以看到`Gas usage`为`36050`

  

- 智能合约存储

  测试一下简单的智能合约`SimpleStorage.sol`

  ```sol
  pragma solidity ^0.4.2;  
  contract SimpleStorage {  
     string storedData;  
    
     function set(string x) {   
      storedData = x;  
     }  
    
     function get() constant returns (string) {   
        return storedData;  
     }   
  }
  ```

  在`truffle console`执行智能合约`contract.set('8DFCB2DA63B35B9DAA0906FCA18053FD')`存入`32 ASCII Character`

  ```json
  { tx: '0xb49d7d757920514d5ef9f2bbb5c1171d76d9bcfa8e4633f05f73c718164812f3',
    receipt: 
     { transactionHash: '0xb49d7d757920514d5ef9f2bbb5c1171d76d9bcfa8e4633f05f73c718164812f3',
       transactionIndex: 0,
       blockHash: '0x84f864f4999edd4278a714af9ba3c6ea99010c8ef2ce45551e93ad019ae4c03d',
       blockNumber: 8,
       gasUsed: 64764,
       cumulativeGasUsed: 64764,
       contractAddress: null,
       logs: [],
       status: 1 },
    logs: [] }
  ```

  通过`testrpc`日志可以看到`Gas usage`为`64764`(不同的机器环境会有些误差)

#### 单位换算

$ 1eth = 10^9 Gwei = 10^{18} wei = $ `$508.08`

比如普通转账的`gas`估算：

- $gasPrice = 1Gwei$

- $gasUsage = 21000$

`cost = gasUsage * gasPrice = 21000 * 1Gwei / 10 ^{18}wei = 2.1 * 10 ^ {-5}eth = $ 0.01 `

#### 参考链接

- [gasPrice和打包确认时间实时信息](https://ethgasstation.info/)

