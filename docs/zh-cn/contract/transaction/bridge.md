# Codex bridge 交易合约

## 合约说明

codex.bridge 合约是Codex链上进行代币交易的合约
codex.bridge目前只提供等比例兑换功能.
+ 1.等比例兑换 提供代币1 和 代币2以一种固定比例的形式进行兑换

## 1. Action

### 1.1 addmarket 创建交易对

创建两个代币进行兑换的交易对
```C++
void addmarket(name trade,account_name trade_maker,trade_type type,name base_chain,asset base_amount,uint64_t base_weight,name market_chain,asset market_amount,uint64_t market_weight);
```

参数：
+ trade:交易对名称
+ trade_maker：创建交易对的账户的名称
+ type：交易对的类型     1.等比例兑换。    2.bancor兑换
+ base_chain:第一种代币所在的链
+ base_amount：第一个代币的金额    amount仅仅指明币种
+ base_weight：第一个代币所占的权重          两个代币之间交换的比例是两个代币权重的比例决定的
+ market_chain:第二种代币所在的链
+ market_amount：第二个代币的金额      amount仅仅指明币种
+ market_weight：第二个代币所占的权重        两个代币之间交换的比例是两个代币权重的比例决定的

  
### 1.2 addmortgage 增加抵押

增加抵押就是往交易对上充值,以便其他用户可以直接进行交易
```C++
void addmortgage(name trade,account_name trade_maker,account_name recharge_account,name coin_chain，asset recharge_amount,coin_type type);
```
示例：
```bash
cleos push action relay.token trade '["eosforce","sys.bridge","side","10000.0000 EOS",2,"eos.sys;biosbpa;1"]' -p eosforce@active
```
参数:
+ trade:交易对名称
+ trade_maker：创建交易对的账户的名称
+ recharge_account：付款的账户
+ recharge_amount：付款的金额
+ type：付款代币的类型            1代表base_coin 2代表market_coin

**不能直接进行调用,只能通过token合约的trade功能触发**

### 1.3 claimmortgage 赎回抵押

赎回抵押就是把交易对上的代币转移到交易对创建者的账户上面
```C++
void claimmortgage(name trade,account_name market_maker,account_name recv_account,asset claim_amount,coin_type type);
```

参数:
+ trade:交易对名称
+ trade_maker：创建交易对的账户的名称
+ recv_account：赎回是转入的账户名称
+ recharge_amount：赎回的金额
+ type：赎回代币的类型            1代表base_coin 2代表market_coin

### 1.4 exchange 代币交易

用户通过这个功能在交易对上兑换代币
```C++
void exchange(name trade,account_name trade_maker,account_name account_covert,account_name account_recv,name coin_chain,asset amount,coin_type type);
```
示例：
```bash
cleos push action relay.token trade '["eosforce","sys.bridge","eosforce", "100.0000 SYS",3,"eos.sys;biosbpa;eosforce;2"]' -p eosforce@active
```
参数:
+ trade:交易对名称
+ trade_maker：创建交易对的账户的名称
+ account_covert:付款的账户
+ account_recv：收款的账户
+ type：交易的类型      1代币支付base_coin 获得market_coin  2代币支付market_coin获得base_coin

**不能直接进行调用,只能通过token合约的trade功能触发**

### 1.5 frozenmarket 冻结交易对

将交易对冻结,被冻结的交易对,用户不能进行exchange操作
```C++
void frozenmarket(name trade,account_name trade_maker);
```

参数:
+ trade:交易对名称
+ trade_maker：创建交易对的账户的名称

### 1.6 trawmarket 解冻交易对

解冻交易对,解冻后用户就能进行exchange操作了
```C++
void trawmarket(name trade,account_name trade_maker);
```

参数:
+ trade:交易对名称
+ trade_maker：创建交易对的账户的名称

### 1.7 setfixedfee 设置固定费用

交易对的创建者使用,可以给交易对设置一个固定的费用
```C++
void setfixedfee(name trade,account_name trade_maker,asset base,asset market);
```

参数:
+ trade:交易对名称
+ trade_maker：创建交易对的账户的名称
+ base:购买base_coin的收取的费用
+ market：购买market_coin时收取的费用

### 1.8 setprofee 设置固定比例的费用

交易对的创建者使用,可以给交易对设置一个固定比例的费用,根据用户的交易量来收取费用
```C++
void setprofee(name trade,account_name trade_maker,uint64_t base_ratio,uint64_t market_ratio);
```

参数:
+ trade:交易对名称
+ trade_maker：创建交易对的账户的名称
+ base_ratio:购买base_coin的收取的费用比例  基数为10000
+ market：购买market_coin时收取的费用比例  基数为10000

### 1.9 setprominfee 设置固定比例费用含最低收费

交易对的创建者使用,可以给交易对设置一个固定比例的费用和最低的费用,如果用户的交易量大则根据固定比例收费,如果交易量小则收取最低费用
```C++
void setprominfee(name trade,account_name trade_maker,uint64_t base_ratio,uint64_t market_ratio,asset base,asset market);
```

参数:
+ trade:交易对名称
+ trade_maker：创建交易对的账户的名称
+ base_ratio:购买base_coin的收取的费用比例  基数为10000
+ market：购买market_coin时收取的费用比例  基数为10000
+ base:购买base_coin的收取的最低费用
+ market：购买market_coin时收取的最低费用

### 1.10 setweight 设置两个币的兑换比例

交易对的创建者使用,设置两个币种的兑换比例
```C++
void setweight(name trade,account_name trade_maker,uint64_t base_weight,uint64_t market_weight);
```

参数:
+ trade:交易对名称
+ trade_maker：创建交易对的账户的名称
+ base_weight:第一个代币所占的权重          两个代币之间交换的比例是两个代币权重的比例决定的
+ market_weight:  第二个代币所占的权重          两个代币之间交换的比例是两个代币权重的比例决定的

### 1.11 removemarket 移除交易对

交易对的创建者使用,将交易对移除,将交易对上面的币取出来
```C++
void removemarket(name trade,account_name trade_maker,account_name base_recv,account_name maker_recv);
```

参数:
+ trade:交易对名称
+ trade_maker：创建交易对的账户的名称
+ base_recv：当前市场中base_coin的余额转入的账户
+ maker_recv：当前市场中market_coin的余额转入的账户


## ABI

- [sys.bridge](https://github.com/codexnetwork/codex.relay/blob/develop/contracts/sys.bridge/sys.bridge.abi)

```json
{
  "version": "eosio::abi/1.0",
  "types": [
    {
      "new_type_name": "account_name",
      "type": "name"
    }
  ],
  "structs": [
    {
      "name": "addmarket",
      "base": "",
      "fields": [
        {
          "name": "trade",
          "type": "name"
        },
        {
          "name": "trade_maker",
          "type": "account_name"
        },
        {
          "name": "type",
          "type": "uint64"
        },
        {
          "name": "base_chain",
          "type": "name"
        },
        {
          "name": "base_amount",
          "type": "asset"
        },
        {
          "name": "base_weight",
          "type": "uint64"
        },
        {
          "name": "market_chain",
          "type": "name"
        },
        {
          "name": "market_amount",
          "type": "asset"
        },
        {
          "name": "market_weight",
          "type": "uint64"
        }
      ]
    },
    {
      "name": "addmortgage",
      "base": "",
      "fields": [
        {
          "name": "trade",
          "type": "name"
        },
        {
          "name": "trade_maker",
          "type": "account_name"
        },
        {
          "name": "recharge_account",
          "type": "account_name"
        },
        {
          "name": "coin_chain",
          "type": "name"
        },
        {
          "name": "recharge_amount",
          "type": "asset"
        },
        {
          "name": "type",
          "type": "uint64"
        }
      ]
    },
    {
      "name": "claimmortgage",
      "base": "",
      "fields": [
        {
          "name": "trade",
          "type": "name"
        },
        {
          "name": "trade_maker",
          "type": "account_name"
        },
        {
          "name": "recv_account",
          "type": "account_name"
        },
        {
          "name": "claim_amount",
          "type": "asset"
        },
        {
          "name": "type",
          "type": "uint64"
        }
      ]
    },
    {
      "name": "exchange",
      "base": "",
      "fields": [
        {
          "name": "trade",
          "type": "name"
        },
        {
          "name": "trade_maker",
          "type": "account_name"
        },
        {
          "name": "account_covert",
          "type": "account_name"
        },
        {
          "name": "account_recv",
          "type": "account_name"
        },
        {
          "name": "coin_chain",
          "type": "name"
        },
        {
          "name": "convert_amount",
          "type": "asset"
        },
        {
          "name": "type",
          "type": "uint64"
        }
      ]
    },
    {
      "name": "frozenmarket",
      "base": "",
      "fields": [
        {
          "name": "trade",
          "type": "name"
        },
        {
          "name": "trade_maker",
          "type": "account_name"
        }
      ]
    },
    {
      "name": "trawmarket",
      "base": "",
      "fields": [
        {
          "name": "trade",
          "type": "name"
        },
        {
          "name": "trade_maker",
          "type": "account_name"
        }
      ]
    },
    {
      "name": "setfixedfee",
      "base": "",
      "fields": [
        {
          "name": "trade",
          "type": "name"
        },
        {
          "name": "trade_maker",
          "type": "account_name"
        },
        {
          "name": "base",
          "type": "asset"
        },
        {
          "name": "market",
          "type": "asset"
        }
      ]
    },
    {
      "name": "setprofee",
      "base": "",
      "fields": [
        {
          "name": "trade",
          "type": "name"
        },
        {
          "name": "trade_maker",
          "type": "account_name"
        },
        {
          "name": "base_ratio",
          "type": "uint64"
        },
        {
          "name": "market_ratio",
          "type": "uint64"
        }
      ]
    },
    {
      "name": "setprominfee",
      "base": "",
      "fields": [
        {
          "name": "trade",
          "type": "name"
        },
        {
          "name": "trade_maker",
          "type": "account_name"
        },
        {
          "name": "base_ratio",
          "type": "uint64"
        },
        {
          "name": "market_ratio",
          "type": "uint64"
        },
        {
          "name": "base",
          "type": "asset"
        },
        {
          "name": "market",
          "type": "asset"
        }
      ]
    },
    {
      "name": "setweight",
      "base": "",
      "fields": [
        {
          "name": "trade",
          "type": "name"
        },
        {
          "name": "trade_maker",
          "type": "account_name"
        },
        {
          "name": "base_weight",
          "type": "uint64"
        },
        {
          "name": "market_weight",
          "type": "uint64"
        }
      ]
    },
    {
      "name": "settranscon",
      "base": "",
      "fields": [
        {
          "name": "chain",
          "type": "name"
        },
        {
          "name": "quantity",
          "type": "asset"
        },
        {
          "name": "contract_name",
          "type": "account_name"
        }
      ]
    },
    {
      "name": "removemarket",
      "base": "",
      "fields": [
        {
          "name": "trade",
          "type": "name"
        },
        {
          "name": "trade_maker",
          "type": "account_name"
        },
        {
          "name": "base_recv",
          "type": "account_name"
        },
        {
          "name": "market_recv",
          "type": "account_name"
        },
      ]
    },
    {
      "name": "claimfee",
      "base": "",
      "fields": [
        {
          "name": "trade",
          "type": "name"
        },
        {
          "name": "market_maker",
          "type": "account_name"
        },
        {
          "name": "recv_account",
          "type": "account_name"
        },
        {
          "name": "type",
          "type": "uint64"
        }
      ]
    },
    {
      "name": "trans_contract",
      "base": "",
      "fields": [
        {
            "name":"id",
            "type":"uint64"
        },
        {
          "name": "chain",
          "type": "name"
        },
        {
          "name": "quantity",
          "type": "asset"
        },
        {
          "name": "contract_name",
          "type": "account_name"
        }
      ]
    },
    {
      "name": "trade_fee",
      "base": "",
      "fields": [
        {
          "name": "base",
          "type": "asset"
        },
        {
          "name": "market",
          "type": "asset"
        },
        {
          "name": "base_ratio",
          "type": "uint64"
        },
        {
          "name": "market_ratio",
          "type": "uint64"
        },
        {
          "name": "fee_type",
          "type": "uint64"
        }
      ]
    },
    {
      "name": "coin",
      "base": "",
      "fields": [
        {
          "name": "chain",
          "type": "name"
        },
        {
          "name": "amount",
          "type": "asset"
        },
        {
          "name": "weight",
          "type": "uint64"
        },
        {
          "name": "fee_amount",
          "type": "asset"
        }
      ]
    },
    {
      "name": "trade_pair",
      "base": "",
      "fields": [
        {
          "name": "trade_name",
          "type": "name"
        },
        {
          "name": "type",
          "type": "uint64"
        },
        {
          "name": "base",
          "type": "coin"
        },
        {
          "name": "market",
          "type": "coin"
        },
        {
          "name": "trade_maker",
          "type": "account_name"
        },
        {
          "name": "isactive",
          "type": "bool"
        },
        {
          "name": "fee",
          "type": "trade_fee"
        }
      ]
    }
  ],
  "actions": [
    {
      "name": "addmarket",
      "type": "addmarket",
      "ricardian_contract": ""
    },
    {
      "name": "addmortgage",
      "type": "addmortgage",
      "ricardian_contract": ""
    },
    {
      "name": "claimmortgage",
      "type": "claimmortgage",
      "ricardian_contract": ""
    },
    {
      "name": "exchange",
      "type": "exchange",
      "ricardian_contract": ""
    },
    {
      "name": "frozenmarket",
      "type": "frozenmarket",
      "ricardian_contract": ""
    },
    {
      "name": "trawmarket",
      "type": "trawmarket",
      "ricardian_contract": ""
    },
    {
      "name": "setfixedfee",
      "type": "setfixedfee",
      "ricardian_contract": ""
    },
    {
      "name": "setprofee",
      "type": "setprofee",
      "ricardian_contract": ""
    },
    {
      "name": "setprominfee",
      "type": "setprominfee",
      "ricardian_contract": ""
    },
    {
      "name": "setweight",
      "type": "setweight",
      "ricardian_contract": ""
    },
    {
      "name": "settranscon",
      "type": "settranscon",
      "ricardian_contract": ""
    },
    {
      "name": "removemarket",
      "type": "removemarket",
      "ricardian_contract": ""
    },
    {
      "name": "claimfee",
      "type": "claimfee",
      "ricardian_contract": ""
    }
  ],
  "tables": [
    {
      "name": "tradepairs",
      "type": "trade_pair",
      "index_type": "i64",
      "key_names": [ "trade" ],
      "key_types": [ "name" ]
    },
    {
      "name":"transcon",
      "type":"trans_contract",
      "index_type":"i64",
      "key_names":[
         "chainsymbol"
      ],
      "key_types":[
         "uint64"
      ]
    }
  ],
  "ricardian_clauses": [],
  "abi_extensions": []
}



```