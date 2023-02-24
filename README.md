# swarm-bzz
## bzz(swarm)教程

### 1、官方链接
[官网](https://www.ethswarm.org/)
[文档说明](https://docs.ethswarm.org/docs/)

### 2、前期准备
* linux服务器(20G+硬盘)
* 服务器开启1634端口
* xdai代币 
* xbzz代币 一个节点 10+

xdai代币和xbzz代币获取方式: （xdai不能直接从交易所转账）

1、从matic/arb/op链先兑换好dai代币, 可用swap：[uni](https://app.uniswap.org/)、[sushi](https://app.sushi.com/)

2、可以通过跨链软件[hop](https://hop.exchange/) 将兑换好的 dai 跨入 gnosis链

3、用xdai兑换xbzz 10个以上,swap [sushi](https://app.sushi.com/)、[CowSwap](https://swap.cow.fi/), xbzz的合约地址 0xdBF3Ea6F5beE45c02255B2c26a16F300502F68da

### 3、使用docker-compose安装swarm

#### 3.1、docker和docker-compose安装
```shell
# 安装docker
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh
rm -f get-docker.sh

# 安装docker-compose
curl -SL https://github.com/docker/compose/releases/download/v2.15.1/docker-compose-linux-x86_64 -o /usr/local/bin/docker-compose
chmod +x /usr/local/bin/docker-compose

# 安装jq
apt install jq -y
```

#### 3.2、配置bzz的 .env
* 注册一个免费的xdai链rpc https://getblock.io/
* Select PROTOCOL > Select Network > My endpoints(选择ws)

```shell
BEE_CLEF_SIGNER_ENABLE=true
BEE_DB_OPEN_FILES_LIMIT=8000
BEE_DEBUG_API_ENABLE=true
BEE_FULL_NODE=true
BEE_MAINNET=true
BEE_P2P_ADDR=:1634
BEE_P2P_QUIC_ENABLE=true
BEE_P2P_WS_ENABLE=true
BEE_PASSWORD=<your passwd>
BEE_SWAP_ENABLE=true
BEE_SWAP_ENDPOINT=<your ws xdai endpoint>
BEE_SWAP_INITIAL_DEPOSIT=0
CLEF_CHAINID=100
```
*** 备注: 官方env下载 wget -q https://raw.githubusercontent.com/ethersphere/bee/v1.4.1/packaging/docker/env -O .env ***

#### 3.3、启动 bzz
```shell
# 下载bzz docker-compose启动文件
wget -q https://raw.githubusercontent.com/ethersphere/bee/v1.4.1/packaging/docker/docker-compose.yml

# 修改docker-compose文件 (修改 clef image为最新的)
sed -i 's/ethersphere\/clef\:0.6.0/ethersphere\/clef\:latest/g' docker-compose.yml

# 启动bzz
docker-compose up -d

# 向钱包转账（以下日志获取钱包地址）
"msg"="using ethereum address" "address"="。。。"
curl localhost:1635/addresses | jq -r ".ethereum"

# 导出私钥的json文件
docker cp  bzz_clef-1_1:/app/data/keystore/ ./
# 导出密码
docker cp bzz_clef-1_1:/app/data/password ./
```

### 4、检测状态是否正确
```shell
# 检查对等节点数量
curl -s localhost:1635/peers | jq ".peers | length"

# 检查stake xbzz数量
curl -XPOST localhost:1635/stake

# 查看本机多种地址
curl localhost:1635/addresses|jq

# api参考（该api文档很多使用端口是1633,但是实际应该是1635）
https://docs.ethswarm.org/api/
```
### 5、stake bzz
```shell
# 确保钱包有10个bzz
curl -XPOST localhost:1635/stake/100000000000000000
```

### 6、常见问题
* xdai数量不足
* ENDPOINT报错
* 质押报错（使用最新的clef）

