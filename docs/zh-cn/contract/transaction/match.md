# Codex match 交易合约

codex.match 合约是Codex链上进行代币撮合交易的合约

## 1. Actions

### 1.1 regex 注册交易所账户

创建两个代币进行兑换的交易对

```cpp
void regex(account_name exc_acc);
```

参数：

+ exc_acc:    交易所账户

!> 注意: 注册交易所账户必须冻结1000个CDX, 只有交易所账户才能创建交易对
  
### 1.2 create 创建交易对

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

### 1.3 open 打开交易对

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

### 1.4 freeze 冻结交易对

```cpp
void freeze(account_name exc_acc, uint32_t id);
```

参数:

+ exc_acc:  交易所账户
+ id:       交易对id

### 1.5 unfreeze 解冻交易对

```cpp
void unfreeze(account_name exc_acc, uint32_t pair_id);
```

参数:

+ exc_acc:  交易所账户
+ id:交易对名称

### 1.6 close 下架交易对

```cpp
void close(name base_chain, symbol_type base_sym, name quote_chain, symbol_type quote_sym, account_name exc_acc);
```

参数:

+ base_chain: base币所在的链名
+ base_sym:   base币种
+ quote_chain:quote币所在的链名
+ quote_sym:  quote币种
+ exc_acc:    交易所账户

### 1.7 setfee 给交易对设置费用

```cpp
void setfee(account_name exc_acc, uint32_t pair_id, uint32_t rate);
```

参数:

+ exc_acc:    交易所账户
+ id:交易对id
+ rate:交易对的收费比例 基数是10000

### 1.8 setminordqty 设置最小交易量

```cpp
void setminordqty(account_name exc_acc, uint32_t pair_id, asset min_qty);
```

参数:

+ exc_acc:    交易所账户
+ id:交易对id
+ min_qty:最小交易量

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
