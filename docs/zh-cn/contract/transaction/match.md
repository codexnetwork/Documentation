# Codex match 交易合约

codex.match 合约是Codex链上进行代币撮合交易的合约

## 1. Actions

### 1.1 冻结系统代币

```cpp
void trade(account_name from, account_name to, asset quantity, trade_type type, string memo);
```

参数：

+ from: 	用户账户   
+ to:      托管账户
+ quantity: 充值数量
+ type:    类型 (目前只能填1)
+ memo:    参数说明, 冻结交易所代币必须填 "freeze"
!> 注意：注册交易所账户，开通交易对必须至少冻结1000 CDX

### 1.2 regex 注册交易所账户

创建两个代币进行兑换的交易对

```cpp
void regex(account_name exc_acc);
```

参数：

+ exc_acc:    交易所账户

!> 注意: 注册交易所账户必须冻结1000个CDX, 只有交易所账户才能创建交易对
  
### 1.3 create 创建交易对

创建一个交易对

```cpp
void create(symbol_type base, name base_chain, symbol_type base_sym, symbol_type quote, name quote_chain, symbol_type quote_sym, account_name exc_acc);
```

参数:

+ base:       base币的名称和精度.比如:4,BTC
+ base_chain: base币所在的链名
+ base_sym:   base币种
+ quote:  quote币的名称和精度.比如:2,USDT
+ quote_chain:quote币所在的链名
+ quote_sym:  quote币种
+ exc_acc:    交易所账户

### 1.4 open 打开交易对

开放交易对,只有开放的交易对用户才能进行交易

```cpp
void open(name base_chain, symbol_type base_sym, name quote_chain, symbol_type quote_sym, account_name exc_acc);
```

参数:

+ base_chain: base币所在的链名
+ base_sym:   base币种
+ quote_chain:quote币所在的链名
+ quote_sym:  quote币种
+ exc_acc:    交易所账户

### 1.5 freeze 冻结交易对

```cpp
void freeze(account_name exc_acc, uint32_t id);
```

参数:

+ exc_acc:  交易所账户
+ id:       交易对id

### 1.6 unfreeze 解冻交易对

```cpp
void unfreeze(account_name exc_acc, uint32_t pair_id);
```

参数:

+ exc_acc:  交易所账户
+ id:交易对名称

### 1.7 close 下架交易对

```cpp
void close(name base_chain, symbol_type base_sym, name quote_chain, symbol_type quote_sym, account_name exc_acc);
```

参数:

+ base_chain: base币所在的链名
+ base_sym:   base币种
+ quote_chain:quote币所在的链名
+ quote_sym:  quote币种
+ exc_acc:    交易所账户

### 1.8 充值交易所代币

```cpp
void trade(account_name from, account_name to, asset quantity, trade_type type, string memo);
```

参数:

+ from: 	用户账户   
+ to:      托管账户
+ quantity: 充值数量
+ type:    类型 (目前只能填1)
+ 参数说明, 充值交易所代币必须填 "points"

### 1.9 提取交易所代币

```cpp
void withdraw(account_name to, asset quantity);
```

参数:

+ to:       提取账户名，必须与充值账户名一致
+ quantity: 提取数量

### 1.10 setfee 给交易对设置费用

```cpp
void setfee(account_name exc_acc, uint32_t pair_id, uint32_t rate);
```

参数:

+ exc_acc:  交易所账户
+ id:       交易对id
+ rate:     交易对的收费比例, 例如, 10 代表  10 / 10000

### 1.11 设置交易所代币

```cpp
void enpoints(account_name exc_acc, uint32_t pair_id, symbol_type points_sym);
```

参数:

+ exc_acc:     交易所账户
+ id:          交易对id
+ points_sym:  交易所代币符号

### 1.12 setminordqty 设置最小交易量

```cpp
void setminordqty(account_name exc_acc, uint32_t pair_id, asset min_qty);
```

参数:

+ exc_acc:  交易所账户
+ id:       交易对id
+ min_qty:  最小交易量

### 1.13 交易 (位于 relay.token 合约)

```cpp
void trade(account_name from, account_name to, name chain, asset quantity, trade_type type, string memo);
```

参数:

+ from: 	用户账户  
+ to:      托管账户，必须是 'codex.match'
+ chain: 	转账货币链
+ quantity: 转账货币数量
+ type:    交易类型 (1 是撮合交易)
+ memo:    交易参数, 格式为: trade;接收账户;交易 ID;价格;买卖类型 (1 是买, 0 is selling);exchange account;referer;fee type(1 means pay by ratio, 2 means pay by exchange token)，for example, "trade;testa;0;4000.00 CUSDT;0;biosbpa;testa;1"

### 1.14 取消订单

```cpp
void cancel(account_name maker, uint32_t type, uint64_t order_or_pair_id);
```

参数:

+ maker:            下单账户
+ type:             0 - 取消指定订单, 1 - 取消指定交易对订单, 2 - 取消所有订单
+ order_or_pair_id: 类型为0时填订单 ID , 类型为1时填交易对 ID

### 1.15 标记交易对，统计成交量、成交额

```cpp
void mark(name base_chain, symbol_type base_sym, name quote_chain, symbol_type quote_sym);
```

参数:

+ base_chain: 基础货币链   
+ base_sym:   基础货币符号
+ quote_chain:报价货币链
+ quote_sym:  报价货币符号

### 1.16 手工领取交易手续费

```cpp
void claim(name base_chain, symbol_type base_sym, name quote_chain, symbol_type quote_sym, account_name exc_acc, account_name fee_acc);
```

参数:

+ base_chain: 基础货币链   
+ base_sym:   基础货币符号
+ quote_chain:报价货币链
+ quote_sym:  报价货币符号
+ exc_acc:    交易所账户
+ fee_acc:    费用账户 

## 2. ABI

[sys.match](https://github.com/codexnetwork/codex.relay/blob/develop/contracts/sys.match/sys.match.abi)

```json
{
  "version": "eosio::abi/1.0",
  "types": [
   {
      "new_type_name": "account_name",
      "type": "name"
   }, {
      "new_type_name": "symbol_type",
      "type": "symbol"
   }
  ],
  "structs": [{
      "name": "regex",
      "base": "",
      "fields": [
      {"name": "exc_acc","type": "account_name"}
      ]
    },{
      "name": "create",
      "base": "",
      "fields": [
      {"name": "base","type": "symbol_type"},
      {"name": "base_chain","type": "name"},
      {"name": "base_sym","type": "symbol_type"},
      {"name": "quote","type": "symbol_type"},
      {"name": "quote_chain","type": "name"},
      {"name": "quote_sym","type": "symbol_type"},
      {"name": "exc_acc","type": "account_name"}
      ]
    },{
      "name": "open",
      "base": "",
      "fields": [
      {"name": "base_chain","type": "name"},
      {"name": "base_sym","type": "symbol_type"},
      {"name": "quote_chain","type": "name"},
      {"name": "quote_sym","type": "symbol_type"},
      {"name": "exc_acc","type": "account_name"}
      ]
    },{
      "name": "close",
      "base": "",
      "fields": [
      {"name": "base_chain","type": "name"},
      {"name": "base_sym","type": "symbol_type"},
      {"name": "quote_chain","type": "name"},
      {"name": "quote_sym","type": "symbol_type"},
      {"name": "exc_acc","type": "account_name"}
      ]
    },{
      "name": "close2",
      "base": "",
      "fields": [
      {"name": "pair_id","type": "uint32"},
      {"name": "exc_acc","type": "account_name"}
      ]
    },{
       "name": "trade",
       "base": "",
       "fields": [
        {"name": "pair_id","type": "uint32"},
        {"name": "maker","type": "account_name"},
        {"name": "receiver","type": "account_name"},
        {"name": "chain","type": "name"},
        {"name": "base","type": "asset"},
        {"name": "price","type": "asset"},
        {"name": "bid_or_ask","type": "uint32"},
        {"name": "exc_acc","type": "account_name"},
        {"name": "referer","type": "string"},
        {"name": "fee_type","type": "uint32"}
       ]
     },{
       "name": "cancel",
       "base": "",
       "fields": [
        {"name": "maker","type": "account_name"},
        {"name": "type","type": "uint32"},
        {"name": "order_or_pair_id_str","type": "string"}
       ]
     },{
       "name": "done",
       "base": "",
       "fields": [
        {"name": "id","type": "string"},
        {"name": "taker_exc_acc","type": "account_name"},
        {"name": "maker_exc_acc","type": "account_name"},
        {"name": "quote_chain","type": "name"},
        {"name": "price","type": "asset"},
        {"name": "base_chain","type": "name"},
        {"name": "quantity","type": "asset"},
        {"name": "pair_id","type": "uint32"},
        {"name": "buy_order_id","type": "string"},
        {"name": "buy_fee","type": "asset"},
        {"name": "sell_order_id","type": "string"},
        {"name": "sell_fee","type": "asset"},
        {"name": "bid_or_ask","type": "uint32"},
        {"name": "timestamp","type": "time_point_sec"}
       ]
     },{
       "name": "mark",
       "base": "",
       "fields": [
        {"name": "base_chain","type": "name"},
        {"name": "base_sym","type": "symbol_type"},
        {"name": "quote_chain","type": "name"},
        {"name": "quote_sym","type": "symbol_type"}
       ]
     },{
      "name": "trading_pair",
      "base": "",
      "fields": [
      {"name": "id","type": "uint32"},
      {"name": "base","type": "symbol_type"},
      {"name": "base_chain","type": "name"},
      {"name": "base_sym","type": "symbol_type"},
      {"name": "quote","type": "symbol_type"},
      {"name": "quote_chain","type": "name"},
      {"name": "quote_sym","type": "symbol_type"},
      {"name": "exc_acc","type": "account_name"},
      {"name": "frozen","type": "uint32"}
      ]
    },{
       "name": "order",
       "base": "",
       "fields": [
        {"name": "id","type": "uint64"},
        {"name": "pair_id","type": "uint32"},
        {"name": "exc_acc","type": "account_name"},
        {"name": "maker","type": "account_name"},
        {"name": "receiver","type": "account_name"},
        {"name": "base","type": "asset"},
        {"name": "price","type": "asset"},
        {"name": "bid_or_ask","type": "uint32"},
        {"name": "fee_type","type": "uint32"},
        {"name": "timestamp","type":"time_point_sec"}
       ]
    },{
       "name": "deal_info",
       "base": "",
       "fields": [
        {"name": "id","type": "uint32"},
        {"name": "pair_id","type": "uint32"},
        {"name": "sum","type": "asset"},
        {"name": "vol","type":"asset"},
        {"name": "reset_block_height","type":"uint32"},
        {"name": "block_height_end","type":"uint32"}
       ]
     },{
       "name": "claim",
       "base": "",
       "fields": [
        {"name": "base_chain","type": "name"},
        {"name": "base_sym","type": "symbol_type"},
        {"name": "quote_chain","type": "name"},
        {"name": "quote_sym","type": "symbol_type"},
        {"name": "exc_acc","type":"account_name"},
        {"name": "fee_acc","type":"account_name"}
       ]
     },{
       "name": "freeze",
       "base": "",
       "fields": [
        {"name": "exc_acc","type":"account_name"},
        {"name": "pair_id","type": "uint32"}
       ]
     },{
       "name": "unfreeze",
       "base": "",
       "fields": [
        {"name": "exc_acc","type":"account_name"},
        {"name": "pair_id","type": "uint32"}
       ]
     },{
       "name": "exc",
       "base": "",
       "fields": [
        {"name": "exc","type": "account_name"},
        {"name": "frozen","type": "bool"}
       ]
     },{
       "name": "setfee",
       "base": "",
       "fields": [
        {"name": "exc_acc","type": "account_name"},
        {"name": "pair_id","type": "uint32"},
        {"name": "rate","type": "uint32"},
       ]
     },{
       "name": "fee_info",
       "base": "",
       "fields": [
        {"name": "id","type": "uint32"},
        {"name": "exc_acc","type": "account_name"},
        {"name": "pair_id","type": "uint32"},
        {"name": "frozen","type": "uint32"},
        {"name": "rate","type": "uint32"},
        {"name": "fees_base","type": "asset"},
        {"name": "fees_quote","type": "asset"},
        {"name": "points_enabled","type": "bool"}
        {"name": "points","type": "asset"},
        {"name": "min_qty","type": "asset"}
       ]
     },{
       "name": "account_info",
       "base": "",
       "fields": [
        {"name": "balance","type": "asset"}
       ]
     },{
       "name": "enpoints",
       "base": "",
       "fields": [
        {"name": "exc_acc","type": "account_name"},
        {"name": "pair_id","type": "uint32"},
        {"name": "points_sym","type": "symbol_type"}
       ]
     },{
       "name": "setminordqty",
       "base": "",
       "fields": [
        {"name": "exc_acc","type": "account_name"},
        {"name": "pair_id","type": "uint32"},
        {"name": "min_qty","type": "asset"}
       ]
     },{
       "name": "withdraw",
       "base": "",
       "fields": [
        {"name": "to","type": "account_name"},
        {"name": "quantity","type": "asset"}
       ]
     },{
       "name": "unfreeze4",
       "base": "",
       "fields": [
        {"name": "exc_acc","type": "account_name"},
        {"name": "action","type": "name"},
        {"name": "pair_id","type": "uint32"},
        {"name": "staked","type": "asset"}
       ]
     },{
       "name": "freeze_info",
       "base": "",
       "fields": [
        {"name": "id","type": "uint32"},
        {"name": "exc_acc","type": "account_name"},
        {"name": "action","type": "name"},
        {"name": "pair_id","type": "uint32"},
        {"name": "staked","type": "asset"}
       ]
     },{
       "name": "morder",
       "base": "",
       "fields": [
        {"name": "id","type": "string"},
        {"name": "from","type": "account_name"},
        {"name": "to","type": "account_name"},
        {"name": "chain","type": "name"},
        {"name": "totalQty","type": "asset"},
        {"name": "type","type": "uint32"},
        {"name": "receiver","type": "account_name"},
        {"name": "price","type": "asset"},
        {"name": "pairId","type": "uint32"},
        {"name": "direction","type": "uint32"},
        {"name": "dexAccount","type": "account_name"},
        {"name": "referer","type": "string"},
        {"name": "feeType","type": "uint32"},
        {"name": "matchedQty","type": "asset"},
        {"name": "dataTime","type": "time_point_sec"},
        {"name": "status","type": "uint32"}
       ]
     },{
       "name": "id_info",
       "base": "",
       "fields": [
        {"name": "table_name","type": "name"},
        {"name": "id","type": "uint64"}
       ]
     }
  ],
  "actions": [{
      "name": "regex",
      "type": "regex",
      "ricardian_contract": ""
    },{
      "name": "create",
      "type": "create",
      "ricardian_contract": ""
    },{
      "name": "cancel",
      "type": "cancel",
      "ricardian_contract": ""
    },{
      "name": "match",
      "type": "trade",
      "ricardian_contract": ""
    },{
      "name": "done",
      "type": "done",
      "ricardian_contract": ""
    },{
      "name": "mark",
      "type": "mark",
      "ricardian_contract": ""
    },{
      "name": "claim",
      "type": "claim",
      "ricardian_contract": ""
    },{
      "name": "open",
      "type": "open",
      "ricardian_contract": ""
    },{
      "name": "close",
      "type": "close",
      "ricardian_contract": ""
    },{
      "name": "close2",
      "type": "close2",
      "ricardian_contract": ""
    },{
      "name": "freeze",
      "type": "freeze",
      "ricardian_contract": ""
    },{
      "name": "unfreeze",
      "type": "unfreeze",
      "ricardian_contract": ""
    },{
      "name": "setfee",
      "type": "setfee",
      "ricardian_contract": ""
    },{
      "name": "enpoints",
      "type": "enpoints",
      "ricardian_contract": ""
    },{
      "name": "withdraw",
      "type": "withdraw",
      "ricardian_contract": ""
    },{
      "name": "setminordqty",
      "type": "setminordqty",
      "ricardian_contract": ""
    },{
      "name": "unfreeze4",
      "type": "unfreeze4",
      "ricardian_contract": ""
    },{
      "name": "morder",
      "type": "morder",
      "ricardian_contract": ""
    }
  ],
  "tables": [{
       "name": "exchanges",
       "type": "exc",
       "index_type": "i64",
     },{
       "name": "pairs",
       "type": "trading_pair",
       "index_type": "i64",
       "key_names" : ["idxkey"],
       "key_types" : ["uint128"]
     },{
      "name": "orderbook",
      "type": "order",
      "index_type": "i64",
      "key_names" : ["idxkey"],
      "key_types" : ["uint128"]
    },{
      "name": "deals",
      "type": "deal_info",
      "index_type": "i64"
    },{
      "name": "fees",
      "type": "fee_info",
      "index_type": "i64"
    },{
      "name": "accounts",
      "type": "account_info",
      "index_type": "i64"
    },{
      "name": "freezed",
      "type": "freeze_info",
      "index_type": "i64"
    },{
      "name": "ids",
      "type": "id_info",
      "index_type": "i64"
    }
  ],
  "ricardian_clauses": [],
  "abi_extensions": []
}
```
## 3. 交易所操作示例
为了操作方便先设置别名
alias efc='/home/yinhp/work/FORCEIO/build/programs/cleos/cleos --wallet-url http://127.0.0.1:6666 --url http://127.0.0.1:8001'

### 3.1 授权
交易所账户授权 ( 假设交易所账户为 biosbpa )
set account permission biosbpa active '{"threshold": 1,"keys": [{"key": "CDX5muUziYrETi5b6G2Ev91dCBrEm3qir7PK4S2qSFqfqcmouyzCr","weight": 1}],"accounts": [{"permission":{"actor":"codex.match","permission":"codex.code"},"weight":1}]}' owner -p biosbpa

托管账户授权
efc set account permission codex.match active '{"threshold": 1,"keys": [{"key": "CDX6MRyAjQq8ud7hVNYcfnVPJqcVpscN5So8BhtHuGYqET5GDW5CV","weight": 1}],"accounts": [{"permission":{"actor":"codex.match","permission":"codex.code"},"weight":1}]}' owner -p codex.match

### 3.2 注册交易所账户
efc push action codex.token trade '{"from":"biosbpa", "to":"codex.match","quantity":"1000.0000 CDX", "type":"1","memo":"freeze;regex;0"}' -p biosbpa
efc push action codex.match regex '{"exc_acc":"biosbpa"}' -p biosbpa

### 3.3 创建交易对
efc push action codex.match create '{"base":"4,BTC","base_chain":"eosforce","base_sym":"4,CBTC","quote":"2,USDT","quote_chain":"eosforce","quote_sym":"2,CUSDT","exc_acc":"biosbpa"}' -p biosbpa

### 3.4 开通交易对
efc push action codex.token trade '{"from":"biosbpa", "to":"codex.match","quantity":"1000.0000 CDX", "type":"1","memo":"freeze;open;0"}' -p biosbpa
efc push action codex.match open '{"base_chain":"eosforce","base_sym":"4,CBTC","quote_chain":"eosforce","quote_sym":"2,CUSDT","exc_acc":"biosbpa"}' -p biosbpa

### 3.5 设置交易所交易对费率
efc push action codex.match setfee '{"exc_acc":"biosbpa","pair_id":"1","rate":"10"}' -p biosbpa

### 3.6 设置最小交易量
efc push action codex.match setminordqty '{"exc_acc":"biosbpa","pair_id":"1","min_qty":"10.0 CBTC"}' -p biosbpa

### 3.7 设置交易所代币
efc push action codex.match enpoints '{"exc_acc":"biosbpa","pair_id":"1","points_sym":"4,CDX"}' -p biosbpa

### 3.8 下架交易对
efc push action codex.match close '{"base_chain":"eosforce","base_sym":"4,CBTC","quote_chain":"eosforce","quote_sym":"2,CUSDT","exc_acc":"biosbpa"}' -p biosbpa

### 3.9 冻结交易对
efc push action codex.match freeze '{"exc_acc":"biosbpa","pair_id":"1"}' -p biosbpa

### 3.10 解冻交易对
efc push action codex.match unfreeze '{"exc_acc":"biosbpa","pair_id":"1"}' -p biosbpa

### 3.11 查看订单本
efc get table codex.match codex.match orderbook       

{
  "rows": [{
      "id": 1,
      "pair_id": 0,
      "maker": "testb",
      "base": "1.0000 BTC",
      "price": "3950.00 USDT",
      "bid_or_ask": 1
    },{
      "id": 3,
      "pair_id": 0,
      "maker": "testa",
      "base": "1.0000 BTC",
      "price": "4200.00 USDT",
      "bid_or_ask": 0
    }
  ],
  "more": false
}

### 3.12 领取交易佣金
efc push action codex.match claim '{"base_chain":"eosforce","base_sym":"4,CBTC","quote_chain":"eosforce","quote_sym":"2,CUSDT","exc_acc":"biosbpa","fee_acc":"biosbpa"}' -p biosbpa

## 4 用户操作步骤示例
用户1:testb, 用户2:testa, 

### 4.1 充值交易所代币
efc push action codex.token transfer '{"from":"testb", "to":"codex.match","quantity":"4000.0000 CDX", "memo":"points"}' -p testb

### 4.2 提取交易所代币
efc push action codex.token withdraw '{"to":"testb", "quantity":"4000.0000 CDX"}' -p testb

### 4.3 买入代币
efc push action relay.token trade '{"from":"testb", "to":"codex.match","chain":"eosforce","quantity":"39500.0000 CUSDT", "type":"1","memo":"trade;testb;1;3950.00 CUSDT;1;biosbpa;;2"}' -p testb

### 4.4 卖出代币
efc push action relay.token trade '{"from":"testa", "to":"codex.match","chain":"eosforce","quantity":"4.0000 CBTC", "type":"1","memo":"trade;testa;1;4000.00 CUSDT;0;biosbpa;testc;1"}' -p testa

### 4.5 取消订单  

备注：仅能取消自己的挂单

a) 取消指定订单 (type 为 0)
efc push action codex.match cancel '{"maker":"testa", "type":"0", "order_or_pair_id_str":"0"}' -p testa

或:

efc match cancel testa 0 0 -p testa

b) 取消指定交易对挂单 (type 为 1)
efc push action codex.match cancel '{"maker":"testa", "type":"1", "order_or_pair_id_str":"1"}' -p testa

or:

efc match cancel testa 1 1 -p testa

c) 取消所有订单 (type 为 2)
efc push action codex.match cancel '{"maker":"testb", "type":"2", "order_or_pair_id_str":"0"}' -p testb

or:

efc match cancel testb 2 0 -p testb

## 5. 内存表

### 5.1 交易所账户表 exchanges
efc get table codex.match codex.match exchanges

{
  "rows": [{
      "exc": "biosbpa",
      "frozen": 0
    },{
      "exc": "biosbpb",
      "frozen": 0
    }
  ],
  "more": false
}

### 5.2 交易对 pairs
efc get table codex.match codex.match pairs

{
  "rows": [{
      "id": 0,
      "base": "4,BTCCC",
      "base_chain": "eosforce",
      "base_sym": "4,CBTC",
      "quote": "4,CDXX",
      "quote_chain": "",
      "quote_sym": "4,CDX",
      "exc_acc": "biosbpa",
      "frozen": 0
    },{
      "id": 1,
      "base": "4,BTC",
      "base_chain": "eosforce",
      "base_sym": "4,CBTC",
      "quote": "4,USDT",
      "quote_chain": "eosforce",
      "quote_sym": "4,CUSDT",
      "exc_acc": "biosbpa",
      "frozen": 0
    }
  ],
  "more": false
}

### 5.3 订单本 orderbook
efc get table codex.match codex.match orderbook

{
  "rows": [{
      "id": 0,
      "pair_id": 1,
      "exc_acc": "biosbpa",
      "maker": "testb",
      "receiver": "testb",
      "base": "10.0000 BTC",
      "price": "3950.0000 USDT",
      "bid_or_ask": 1,
      "fee_type": 2,
      "timestamp": "2019-06-06T07:46:00"
    },{
      "id": 1,
      "pair_id": 1,
      "exc_acc": "biosbpa",
      "maker": "testa",
      "receiver": "testa",
      "base": "4.0000 BTC",
      "price": "4000.0000 USDT",
      "bid_or_ask": 0,
      "fee_type": 1,
      "timestamp": "2019-06-06T07:46:11"
    }
  ],
  "more": false
}

### 5.4 成交 deals
efc get table codex.match codex.match deals

{
  "rows": [{
      "id": 0,
      "pair_id": 1,
      "sum": "0.0000 CUSDT",
      "vol": "0.0000 CBTC",
      "reset_block_height": 1,
      "block_height_end": 86400
    }
  ],
  "more": false
}

### 5.5 交易所交易对手续费 fees
efc get table codex.match codex.match fees

{
  "rows": [{
      "id": 1,
      "exc_acc": "biosbpb",
      "pair_id": 1,
      "frozen": 0,
      "rate": 0,
      "fees_base": "0.0000 CBTC",
      "fees_quote": "0.0000 CUSDT",
      "points_enabled": 0,
      "points": "0.0000 CDX",
      "min_qty": "0.0000 CBTC"
    },{
      "id": 2,
      "exc_acc": "biosbpa",
      "pair_id": 1,
      "frozen": 0,
      "rate": 0,
      "fees_base": "0.0000 CBTC",
      "fees_quote": "0.0000 CUSDT",
      "points_enabled": 0,
      "points": "0.0000 CDX",
      "min_qty": "0.0000 CBTC"
    }
  ],
  "more": false
}

### 5.6 托管资产表 accounts
efc get table codex.match testa accounts

{
  "rows": [{
      "balance": "4.0000 CBTC"
    }
  ],
  "more": false
}

### 5.7 冻结资产表 freezed
efc get table codex.match biosbpa freezed

{
  "rows": [{
      "id": 0,
      "exc_acc": "biosbpa",
      "action": "regex",
      "pair_id": 0,
      "staked": "2000.0000 CDX"
    },{
      "id": 1,
      "exc_acc": "biosbpa",
      "action": "open",
      "pair_id": 0,
      "staked": "2000.0000 CDX"
    },{
      "id": 2,
      "exc_acc": "biosbpa",
      "action": "open",
      "pair_id": 1,
      "staked": "1000.0000 CDX"
    }
  ],
  "more": false
}
