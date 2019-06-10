# producer相关指令

## 节点相关

Codex关于节点的功能是比较多的.

- 1.BP注册
- 2.给BP投票
- 3.添加出块抵押
- 4.领取出块分红
- 5.领取投票分红

## cleos 命令

### 注册BP

```bash
cleos push action codex updatebp '["testa","CDX6MRyAjQq8ud7hVNYcfnVPJqcVpscN5So8BhtHuGYqET5GDW5CV",100,"www"]' -p testa
```

参数：

- bpname : BP的名字
- producer_key:节点的公钥
- commission_rate : 佣金比例
- url : url

### 给BP投票

```bash
cleos push action codex freeze '["eosforce","50000.0000 CDX"]' -p eosforce
cleos push action codex vote '["eosforce","testa","45000.0000 CDX"]' -p eosforce
```

参数

freeze:

- voter : 投票账户
- stake : 对话投票的系统代币的数量

vote:

- voter : 投票者
- bpbame : 节点
- statke : 票数（eos金额）

### 添加出块抵押

```bash
cleos push action codex addmortgage '["testa","eosforce","20000.0000 CDX"]' -p eosforce
```

参数

- bpbame : 节点
- payer : 付款账户
- quantity : 抵押代币数目

### 领取出块分红

```bash
cleos push action codex.token opencast '["eosforce"]' -p eosforce
cleos push action codex  claimbp '["biosbpa","eosforce"]' -p biosbpa
```

参数

opencast:

- to : 开辟铸币池的用户

claimbp:

- bpname : bp的名字
- receiver : 接受出块分红账户的名字

### 领取投票分红

```bash
cleos push action codex.token opencast '["eosforce"]' -p eosforce
cleos push action codex  claimvote '["biosbpa","eosforce"]' -p eosforce
```

参数

opencast:

- to : 开辟铸币池的用户

claimbp:

- bpname : bp的名字
- receiver : 接受投票分红账户的名字

## abi 数据结构
  
```json
   {
      "name": "updatebp",
      "base": "",
      "fields": [
         {"name":"bpname",            "type":"account_name"},
         {"name":"block_signing_key", "type":"public_key"},
         {"name":"commission_rate",   "type":"uint32"},
         {"name":"url",               "type":"string"}
      ]
    },{
      "name": "freeze",
      "base": "",
      "fields": [
        {"name":"voter",     "type":"account_name"},
        {"name":"stake",     "type":"asset"}
      ]
    },{
      "name": "vote",
      "base": "",
      "fields": [
        {"name":"voter",     "type":"account_name"},
        {"name":"bpname",    "type":"account_name"},
        {"name":"stake",     "type":"asset"}
      ]
    },{
         "name": "addmortgage",
         "base": "",
         "fields": [
            {"name":"bpname",                "type":"account_name"},
            {"name":"payer",              "type":"account_name"},
            {"name":"quantity",              "type":"asset"}
         ]
       },{
         "name": "claimbp",
         "base": "",
         "fields": [
            {"name":"bpname",                "type":"account_name"},
            {"name":"receiver",              "type":"account_name"}
         ]
       },{
         "name": "claimvote",
         "base": "",
         "fields": [
            {"name":"bpname",                "type":"account_name"},
            {"name":"receiver",              "type":"account_name"}
         ]
       }
```