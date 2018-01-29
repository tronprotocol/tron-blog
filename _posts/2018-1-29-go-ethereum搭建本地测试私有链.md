---
title: go-ethereum 搭建本地测试私有链
date: 2018-01-29 19:48:42
categories:
- 区块链
tags:
- go-ethereum
- 区块链
---
# 1. 初始化私有链
## 1.1 创世区块配置文件
在绝对路径 /home/vagrant/ 下创建 genesis.json（文件名可换成其他，下面进行操作时，文件名保持一致即可）文件，内容为：
```
{
    "config": {
          "chainId": 0,
          "homesteadBlock": 0,
          "eip155Block": 0,
          "eip158Block": 0
      },
    "alloc"      : {},
    "coinbase"  : "0x0000000000000000000000000000000000000000",
    "difficulty" : "0x20000",
    "extraData"  : "",
    "gasLimit"  : "0x2fefd8",
    "nonce"      : "0x0000000000000042",
    "mixhash"    : "0x0000000000000000000000000000000000000000000000000000000000000000",
    "parentHash" : "0x0000000000000000000000000000000000000000000000000000000000000000",
    "timestamp"  : "0x00"
}
```
注意：Windows 系统下，需要注意文件编码为 UTF-8，LF 格式换行。
参数简介：

| 参数 | 描述 |
| --- | --- |
| alloc | 用来预置账号以及账号的以太币数量，因为私有链挖矿简单，所以不需要预置有币的账号，需要的时候可以自己创建 |
| coinbase | 矿工的账号，随便填 |
| difficulty | 设置当前区块的难度，值越大，难度越大，越难挖矿，私有链可将该值设置小点，容易挖矿 |
| extraData | 附加信息，随便填 |
| gasLimit | GAS 的消耗总量限制，用来限制区块能包含的交易信息总和，最大值：0x0000ffff |
| nonce | 一个 64 位随机数，用于挖矿，注意它和 mixhash 的设置需要满足以太坊的 Yellow paper，4.3.4. Block Header Validity，(44) 章节所描述的条件 |
| mixhash | 与 nonce 配合用于挖矿，由于上一个区块的一部分生成的 hash |
| parentHash | 上一个区块的 hash 值，只有创世块的该值为 0，因为创世块是第一个区块 |
| timestamp | 设置创世块的时间戳 |

## 1.2 写入创世区块
在绝对路径 /home/vagrant/ 下执行下面命令进行初始化：
```
geth --datadir /home/vagrant/node init genesis.json
```
参数简介：

| 参数 | 描述 |
| --- | --- |
| datadir | 指定数据目录，不同节点指定不同目录，注意所指目录权限 |
| init | 初始化命令，这里指定 genesis.json 为配置文件 |

# 2. 启动一个私有链节点
执行下面命令进行私有链节点的启动，开发方式登录：
```
geth --identity "private ethereum" --rpc --rpccorsdomain "*" --datadir "/home/vagrant/node" --port "30303" --rpcapi "db,eth,net,web3" --networkid "95518" console
```
参数简介：

| 参数 | 描述 |
| --- | --- |
| identity | 区块链的标识，用于标识目前网络的名字 |
| rpc | 启动 RPC 通信，可以进行智能合约的部署和测试 |
| rpccorsdomain | 逗号分隔的域列表，用来接受交叉源请求（浏览器强制执行） |
| datadir | 设置当前区块链网络数据存放的位置 |
| port | 网络监听端口 |
| rpcapi | 设置允许连接的 RPC 的客户端，一般为 db, eth,net,web3 |
| networkid | 设置当前区块链的网络 ID，用于区分不同的网络 |
| console | 启动命令行模式，方便执行命令 |

运行后台服务需要去掉 console，否则会自动停止：
```
nohup geth --identity "private etherum" --rpc --rpccorsdomain "*" --datadir "/home/vagrant/node" --port "30303" --rpcapi "db,eth,net,web3" --networkid "95518" &
```

会在 /home/vagrant/data0/chain 目录生成 geth.ipc 文件，可以通过执行下面命令连接 geth：
```
geth attach ipc:/home/vagrant/node/chain/geth.ipc
```
参考 ***get attach*** 可以通过下面几种方式进行连接：
```
geth attach ipc:/some/costom/path
geth attach http://192.168.1.1:8545
geth attach ws://192.168.1.1:8545
```

# 3. console 下的操作
## 3.1 用户
### 3.1.1 用户列表
```
eth.accounts
```

### 3.1.2 添加用户
```
personal.newAccount('密码')
```

### 3.1.3 解锁用户
```
personal.unlockAccount('用户值', '对应密码')
```

补充示例：
```
user1=eth.accounts[0]
user2=eth.accounts[1]
personal.unlockAccount(user1, '123456')
```

### 3.1.4 发送交易
```
eth.sendTransaction({from:user1, to:user2, value:web3.toWei(3,"ether")})
```

补充说明：交易之前，需要解锁用户 user1，交易之后，需要执行挖矿操作让交易有效。

### 3.1.5 查看余额
```
eth.getBalance(user1)
```

## 3.2 挖矿
### 3.2.1 开始
```
miner.start()
```

### 3.2.2 结束
```
miner.stop()
```

### 3.2.3 查看区块数量
```
eth.blockNumber
```

### 3.2.4 获取区块信息
```
eth.getBlock(1)
```

## 3.3 节点
### 3.3.1 获取节点信息
```
admin.nodeInfo
admin.nodeInfo.enode
admin.nodeInfo.id
admin.nodeInfo.ports.listener
```


