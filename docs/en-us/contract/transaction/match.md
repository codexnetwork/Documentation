# Codex match trading contract

codex.match 合约是Codex链上进行代币撮合交易的合约

## 1. Actions

### 1.1 freeze system coin

```cpp
void trade(account_name from, account_name to, asset quantity, trade_type type, string memo);
```

parameters:

+ from: 	  user account   
+ to:      escrow account
+ quantity: deposit quantity
+ type:    trade type (1 is matching trading)
+ memo:    parameters, format points，for example, "freeze"
!> Note: you  must freeze 1000 or more CDX to register exchange account and to open trading pair

### 1.2 register exchange account

```cpp
void regex(account_name exc_acc);
```

parameters:

+ exc_acc:    exchange account

!> Note: you  must freeze 1000 or more CDX to register exchange account, only exchange account can create trading pairs
  
### 1.3 Creating trading pairs:  (located in sys.match contract)

```cpp
void create(symbol_type base, name base_chain, symbol_type base_sym, symbol_type quote, name quote_chain, symbol_type quote_sym, account_name exc_acc);
```

parameters:

+ base:        base token symbol and precision, such as 4,BTC
+ base_chain:  from which chain   
+ base_sym:    from which token
+ quote:	      quote token symbol and precision，such as 2,USDT
+ quote_chain: from which chain
+ quote_sym:   from which token
+ exc_acc:     exchange account
note：for example, BTC/USDT pair，BTC is base token，USDT is quote token   

### 1.4 open trading pair 

```cpp
void open(name base_chain, symbol_type base_sym, name quote_chain, symbol_type quote_sym, account_name exc_acc);
```

parameters:

+ base_chain: from which chain   
+ base_sym:   from which token
+ quote_chain:from which chain
+ quote_sym:  from which token
+ exc_acc:    exchange account
note: you  must freeze 1000 or more CDX to open trading pair, only exchange account can open trading pair

### 1.5 freeze the trading pair

```cpp
void freeze(account_name exc_acc, uint32_t id);
```

parameters:

+ exc_acc:  exchange account
+ id:       pair id

### 1.6 unfreeze the trading pair

```cpp
void unfreeze(account_name exc_acc, uint32_t pair_id);
```

parameters:

+ exc_acc:  exchange account
+ id:       pair id

### 1.7 close trading pair

```cpp
void close(name base_chain, symbol_type base_sym, name quote_chain, symbol_type quote_sym, account_name exc_acc);
```

parameters:

+ base_chain: from which chain   
+ base_sym:   from which token
+ quote_chain:from which chain
+ quote_sym:  from which token
+ exc_acc:    exchange account

### 1.8 deposit exchange token

```cpp
void trade(account_name from, account_name to, asset quantity, trade_type type, string memo);
```

parameters:

+ from: 	  user account   
+ to:      escrow account
+ quantity: deposit quantity
+ type:    trade type (1 is matching trading)
+ memo:    parameters, format points，for example, "points"

### 1.9 withdraw exchange token

```cpp
void withdraw(account_name to, asset quantity);
```

parameters:

+ to:       withdraw to which account 
+ quantity: withdraw token amount

### 1.10 set trading pair fee rate

```cpp
void setfee(account_name exc_acc, uint32_t pair_id, uint32_t rate);
```

parameters:

+ exc_acc:  exchange account
+ pair_id: pair id
+ rate:    fee rate, for example, 10 means  10 / 10000

### 1.11 enable exchange tokens

```cpp
void enpoints(account_name exc_acc, uint32_t pair_id, symbol_type points_sym);
```

parameters:

+ exc_acc:    exchange account
+ pair_id:    pair id
+ points_sym: exchange token symbol

### 1.12 set minimum trading quantity

```cpp
void setminordqty(account_name exc_acc, uint32_t pair_id, asset min_qty);
```

parameters:

+ exc_acc:  exchange account
+ pair_id:    pair id
+ min_qty:    minimum base trading quantity

### 1.13 trade (located in relay.token contract)

```cpp
void trade(account_name from, account_name to, name chain, asset quantity, trade_type type, string memo);
```

parameters:

+ from: 	   transfer orginating account    
+ to:       escrow account, now must be 'sys.match'
+ chain: 	from which chain  
+ quantity: transfer token amount
+ type:     trade type (1 is matching trading)
+ memo:     trade parameters, format trade;receiver;trading pair ID;price;buy or sell (1 is buying, 0 is selling);exchange account;referer;fee type(1 means pay by ratio, 2 means pay by exchange token)，for example, "trade;testa;0;4000.00 CUSDT;0;biosbpa;testa;1"

### 1.14 Cancel the order

```cpp
void cancel(account_name maker, uint32_t type, uint64_t order_or_pair_id);
```

parameters:

+ maker:            the account who made the order
+ type:             0 - cancel designated order, 1 - cancel designated pairs' order, 2 - cancel all orders
+ order_or_pair_id: order ID when type is 0, pair_id when type is 1

### 1.15 mark the trading pair for counting trading turnover

```cpp
void mark(name base_chain, symbol_type base_sym, name quote_chain, symbol_type quote_sym);
```

parameters:

+ base_chain: from which chain   
+ base_sym:   from which token
+ quote_chain:from which chain
+ quote_sym:  from which token

### 1.16 claim the trading commissions mannually

```cpp
void claim(name base_chain, symbol_type base_sym, name quote_chain, symbol_type quote_sym, account_name exc_acc, account_name fee_acc);
```

parameters:

+ base_chain: from which chain   
+ base_sym:   from which token
+ quote_chain:from which chain
+ quote_sym:  from which token
+ exc_acc:    exchange account
+ fee_acc:    fee account 

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
## 3. exchange operational examples
for convenience, first set alias
alias efc='/home/yinhp/work/FORCEIO/build/programs/cleos/cleos --wallet-url http://127.0.0.1:6666 --url http://127.0.0.1:8001'

### 3.1 authorization 
exchange account authorization ( for example, exchange account is biosbpa )
set account permission biosbpa active '{"threshold": 1,"keys": [{"key": "CDX5muUziYrETi5b6G2Ev91dCBrEm3qir7PK4S2qSFqfqcmouyzCr","weight": 1}],"accounts": [{"permission":{"actor":"codex.match","permission":"codex.code"},"weight":1}]}' owner -p biosbpa

escrow account authorization
efc set account permission codex.match active '{"threshold": 1,"keys": [{"key": "CDX6MRyAjQq8ud7hVNYcfnVPJqcVpscN5So8BhtHuGYqET5GDW5CV","weight": 1}],"accounts": [{"permission":{"actor":"codex.match","permission":"codex.code"},"weight":1}]}' owner -p codex.match

### 3.2 register exchange account name
efc push action codex.token trade '{"from":"biosbpa", "to":"codex.match","quantity":"1000.0000 CDX", "type":"1","memo":"freeze;regex;0"}' -p biosbpa
efc push action codex.match regex '{"exc_acc":"biosbpa"}' -p biosbpa

### 3.3 create trading pairs
efc push action codex.match create '{"base":"4,BTC","base_chain":"eosforce","base_sym":"4,CBTC","quote":"2,USDT","quote_chain":"eosforce","quote_sym":"2,CUSDT","exc_acc":"biosbpa"}' -p biosbpa

### 3.4 open trading pairs
efc push action codex.token trade '{"from":"biosbpa", "to":"codex.match","quantity":"1000.0000 CDX", "type":"1","memo":"freeze;open;0"}' -p biosbpa
efc push action codex.match open '{"base_chain":"eosforce","base_sym":"4,CBTC","quote_chain":"eosforce","quote_sym":"2,CUSDT","exc_acc":"biosbpa"}' -p biosbpa

### 3.5 set exchange trading fees
efc push action codex.match setfee '{"exc_acc":"biosbpa","pair_id":"1","rate":"10"}' -p biosbpa

### 3.6 set minimum trading quantity
efc push action codex.match setminordqty '{"exc_acc":"biosbpa","pair_id":"1","min_qty":"10.0 CBTC"}' -p biosbpa

### 3.7 enable exchange tokens
efc push action codex.match enpoints '{"exc_acc":"biosbpa","pair_id":"1","points_sym":"4,CDX"}' -p biosbpa

### 3.8 close trading pairs
efc push action codex.match close '{"base_chain":"eosforce","base_sym":"4,CBTC","quote_chain":"eosforce","quote_sym":"2,CUSDT","exc_acc":"biosbpa"}' -p biosbpa

### 3.9 freeze the trading pair
efc push action codex.match freeze '{"exc_acc":"biosbpa","pair_id":"1"}' -p biosbpa

### 3.10 unfreeze the trading pair
efc push action codex.match unfreeze '{"exc_acc":"biosbpa","pair_id":"1"}' -p biosbpa

### 3.11 14、view orderbook
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

### 3.12 claim fees
efc push action codex.match claim '{"base_chain":"eosforce","base_sym":"4,CBTC","quote_chain":"eosforce","quote_sym":"2,CUSDT","exc_acc":"biosbpa","fee_acc":"biosbpa"}' -p biosbpa

## 4 user exchange examples
user 1:testb, user 2:testa, 

### 4.1 deposit exchange tokens
efc push action codex.token transfer '{"from":"testb", "to":"codex.match","quantity":"4000.0000 CDX", "memo":"points"}' -p testb

### 4.2 withdraw exchange tokens
efc push action codex.token withdraw '{"to":"testb", "quantity":"4000.0000 CDX"}' -p testb

### 4.3 buy tokens  
efc push action relay.token trade '{"from":"testb", "to":"codex.match","chain":"eosforce","quantity":"39500.0000 CUSDT", "type":"1","memo":"trade;1;3950.00 CUSDT;1;biosbpa;;2"}' -p testb

### 4.4 sell tokens   
efc push action relay.token trade '{"from":"testa", "to":"codex.match","chain":"eosforce","quantity":"4.0000 CBTC", "type":"1","memo":"trade;1;4000.00 CUSDT;0;biosbpa;testc;1"}' -p testa

### 4.5 cancel the order   

note：only can cancel orders made by themself

a) cancel the specified order (type is 0)
efc push action codex.match cancel '{"maker":"testa", "type":"0", "order_or_pair_id_str":"0"}' -p testa

or:

efc match cancel testa 0 0 -p testa

b) cancel designated pairs' order (type is 1)
efc push action codex.match cancel '{"maker":"testa", "type":"1", "order_or_pair_id_str":"1"}' -p testa

or:

efc match cancel testa 1 1 -p testa

c) cancel all orders (type is 2)
efc push action codex.match cancel '{"maker":"testb", "type":"2", "order_or_pair_id_str":"0"}' -p testb

or:

efc match cancel testb 2 0 -p testb

## 5. memory tables

### 5.1 exchange account table (exchanges) 
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

### 5.2 trading pair table (pairs)
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

### 5.3 orderbook
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

### 5.4 deals
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

### 5.5 fees
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

### 5.6 escrow asset table (accounts) 
efc get table codex.match testa accounts

{
  "rows": [{
      "balance": "4.0000 CBTC"
    }
  ],
  "more": false
}

### 5.7 freezed
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
