## 环境搭建
- 本次测试环境 centos7
- 下载geth [官网地址](https://geth.ethereum.org/downloads/)
- 解压后得到执行程序 geth
- cp geth /usr/local/bin/
- 测试 geth version

## 配置
- 新建目录 /root/eth
- 执行以下脚本生成配置文件

```shell
geth --identity "zyytest" --networkid 123 \
    --port '30303' --syncmode 'fast' --nodiscover --maxpeers 0 \
    --rpc --rpcaddr '0.0.0.0' --rpcport 8545 --rpcapi 'db,eth,net,web3,personal' --rpccorsdomain '*' \
    --ws --wsaddr '0.0.0.0' --wsport 8546 --wsapi 'db,eth,net,web3,personal' --wsorigins '*' \
    --datadir /root/eth/data --cache 1024 dumpconfig > /root/eth/config.toml
```

## 创世块
- 新建配置 genesis.json[参考地址](https://hub.docker.com/r/ethereum/client-go)

```javascript
{
  "config": {
    "chainId": 123,
    "homesteadBlock": 0,
    "eip150Block": 0,
    "eip155Block": 0,
    "eip158Block": 0,
    "byzantiumBlock": 0,
    "constantinopleBlock": 0,
    "petersburgBlock": 0
  },
  "alloc": {
  },
  "coinbase": "0x0000000000000000000000000000000000000000",
  "difficulty": "0x01",
  "extraData": "",
  "gasLimit": "0xffffffff",
  "nonce": "0x0000000000000042",
  "mixhash": "0x0000000000000000000000000000000000000000000000000000000000000000",
  "parentHash": "0x0000000000000000000000000000000000000000000000000000000000000000",
  "timestamp": "0x00"
}
```

- 生成创世区块
```shell
geth init ./genesis.json --datadir /root/eth/data
```

## 目录结构
执行以上步骤后目录结构如下

```
/root/eth
├── config.toml
├── data
│   └── keystore
├── genesis.json
```

## 启动节点
- 后台运行

```shell
# 控制台消息会写入到 nohup.out
nohup geth --config /root/eth/config.toml &

# 查看nohup.out中ipc路径，如：
# IPC endpoint opened                      url=/root/eth/data/geth.ipc
# 进入控制台
geth attach /root/eth/data/geth.ipc
```

- 前台运行并进入控制台

```shell
geth --config /root/eth/config.toml console 2>>/root/eth/geth.log
```

- 创建账户挖矿等

```shell
# 创建账户，多创建几个
personal.newAccount('123123')
# 查看账户
eth.accounts

# 账户余额
eth.getBalance(eth.accounts[0])

# 配置挖矿奖励账户
miner.setEtherbase(eth.accounts[0])
# 查看挖矿奖励账户
eth.coinbase

# 启动1个线程挖矿
miner.start(1)
# 停止挖矿
miner.stop()

```