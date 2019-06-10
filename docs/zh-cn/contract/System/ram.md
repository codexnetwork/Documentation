# vote4ram 租赁内存

## 内存租赁
Codex的内存是通过抵押投票的方式租赁的,使用vote4ram功能将票以租赁内存的方式投给BP,即可获得相应的内存,投票之前需要先将代币冻结

## cleos 命令

```bash
cleos push action codex freeze '["eosforce","100.0000 CDX"]' -p eosforce
cleos push action codex vote4ram '["eosforce","biosbpa","90.0000 CDX"]' -p eosforce

```
### freeze
参数：
- voter : 投票账户
- stake : 对话投票的系统代币的数量
### vote4ram
- voter : 投票者
- bpbame : 节点
- statke : 票数（eos金额）



## abi 数据结构
  
```json
    {
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
    }

```