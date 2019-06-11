#中继链relay节点部署

###CODEX节点搭建

codex 中继链接点部署，部署环境最低配置：64位 ubuntu 16.04版本以上系统，4核8G内存，100G 硬盘

####部署步骤
```
    git clone https://github.com/codexnetwork/codex.relay.git
    cd codex.relay && git checkout -b develop && git pull origin develop
    git submodule update --init --recursive
    ./eosio_build.sh
    
    export configpath='~/eosforce/config'
    export datapath='~/eosforce/data'
    mkdir -p ~/eosforce/config
    cp build/contracts/contracts/force.msig/force.msig.abi  ~/eosforce/config/
    cp build/contracts/contracts/force.msig/force.msig.wasm  ~/eosforce/config/
    
    cp build/contracts/contracts/force.relay/force.relay.abi  ~/eosforce/config/
    cp build/contracts/contracts/force.relay/force.relay.wasm  ~/eosforce/config/
    
    cp build/contracts/contracts/force.system/force.system.abi  ~/eosforce/config/
    cp build/contracts/contracts/force.system/force.system.wasm  ~/eosforce/config/
    
    cp build/contracts/contracts/force.token/force.token.abi  ~/eosforce/config/
    cp build/contracts/contracts/force.token/force.token.wasm  ~/eosforce/config/
    
    cp build/contracts/contracts/relay.token  ~/eosforce/config/
    cp build/contracts/contracts/sys.bridge  ~/eosforce/config/
    cp build/contracts/contracts/sys.match  ~/eosforce/config/
    ./build/programs/genesis/genesis && cp genesis.json ~/eosforce/config/
    wget https://updatewallet.oss-cn-hangzhou.aliyuncs.com/codex/config.ini && mv config.ini  ~/eosforce/config/
    wget https://updatewallet.oss-cn-hangzhou.aliyuncs.com/codex/activeacc.json && mv activeacc.json  ~/eosforce/config/
```

####config配置文件

p2p地址列表：

118.31.2.69:10102

成为BP需要添加如下配置：

producer-name = bpname

signature-provider = CDX7R82SaGaJubv23GwXHyKT4qDCVXi66qkQrnjwmBUvdA4dyzEPG=KEY:5JfjatHRwbmY8SfptFRxHnYUctfnuaxANTGDYUtkfrrBDgkh3hB

#####启动服务
```
	nohup ./build/bin/nodeos --config-dir ~/eosforce/config 2>&1 &
```
	
####注册成为BP节点
```
	./build/bin/cleos  push action codex updatebp '{"bpname":"bpname","block_signing_key":"CDX7R82SaGaJubv23GwXHyKT4qDCVXi66qkQrnjwmBUvdA4dyzEPG","commission_rate":"100","url":"https://eosforce.io"}' -p bpname
```
