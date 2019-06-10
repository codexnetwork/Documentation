# newaccount 创建账号

## 创建规则

账户名不超过12位，并且只能由这些字符组成：.12345abcdefghijklmnopqrstuvwxyz

> 注意：提交了创建用户名的交易，需等待到进入不可逆块。如果有其他人抢注这个账号，这个账号可能不属于你，如果刚创建就马上就给这个账号转账的话，可能钱就打水漂啦。

## cleos 命令

```bash
cleos push action codex newaccount '{"creator":"eosforce","name":"tesss","owner":{"threshold": 1,"keys": [{"key": "CDX6MRyAjQq8ud7hVNYcfnVPJqcVpscN5So8BhtHuGYqET5GDW5CV","weight": 1}],"accounts": [],"waits":[]},"active":{"threshold": 1,"keys": [{"key": "CDX6MRyAjQq8ud7hVNYcfnVPJqcVpscN5So8BhtHuGYqET5GDW5CV","weight": 1}],"accounts": [],"waits":[]}}' -p eosforce

```

参数：
  - creator TEXT                执行创建的账号 (必要)
  - name TEXT                   创建的新账号名 (required)
  - owner TEXT               拥有者的账号公钥 (required)
  - active TEXT              新账号的激活公钥，默认同拥有者的账号公钥


## abi 数据结构
- [eosio.bio.abi](https://github.com/eosforce/eosforce/blob/release/contracts/eosio.bios/eosio.bios.abi)
  
```json
    {
        "name": "authority",
        "base": "",
        "fields": [
          {"name":"threshold", "type":"uint32"},
          {"name":"keys",      "type":"key_weight[]"},
          {"name":"accounts",  "type":"permission_level_weight[]"},
          {"name":"waits",     "type":"wait_weight[]"}
        ]
      },{
       "name": "newaccount",
       "base": "",
       "fields": [
         {"name":"creator", "type":"account_name"},
         {"name":"name",    "type":"account_name"},
         {"name":"owner",   "type":"authority"},
         {"name":"active",  "type":"authority"}
       ]
     }

```