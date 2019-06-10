# Codex Token 系统合约

## 合约说明

force.token 合约是Codex链上代币发行合约

## 1. Action

### 1.1 create 创建代币

创建代币符号

```cpp
 void create( account_name issuer,
              asset        maximum_supply);
```

参数：

- issuer : 发行人
- maximum_supply :  代币及最大供应量

### 1.2 issue 发行代币

发行一定数量代币到指定账户

```cpp
void issue( account_name to, asset quantity, string memo );
```

参数:

- to : 接收币的账户
- quantity :增发的币的数量
- memo : 备注

### 1.3 transfer 代币转账

代币转账操作

```cpp
 void transfer( account_name from,
                account_name to,
                asset        quantity,
                string       memo );
```

参数:

- from :转账的账户
- to : 接收币的账户
- quantity :转账的币
- memo : 备注

### 1.4 trade 代币交易

用于调用其他交易合约的功能的action

```cpp
 void trade( account_name   from,
             account_name   to,
             asset          quantity,
             func_type      type,
             string           memo);
```

参数:

- from : 转账的账户
- to : 接收币的账户,一般是其他交易合约
- quantity : 转账的币
- type : 类型 用于识别是哪个功能
- memo : 备注 用于

### 1.5 takecoin 取铸币

将币从铸币池中取出来

```cpp
void takecoin(account_name to);
```

参数:

- to : 取铸币的账户

### 1.6 opencast 开启铸币池

在当前块高度开启铸币池

```cpp
void opencast(account_name to);
```

参数:

- to : 开启铸币池的账户

### 1.7 closecast 关闭铸币池

关闭没有铸币的铸币池

```cpp
void closecast(account_name to,int32_t finish_block);
```

参数:

- to : 关闭铸币池的账户
- finish_block : 要关闭的铸币池的铸币结束时间

## ABI

- [force.token](https://github.com/codexnetwork/codex.relay/blob/develop/contracts/force.token/force.token.abi)

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
      "name": "transfer",
      "base": "",
      "fields": [
        {
          "name": "from",
          "type": "account_name"
        },
        {
          "name": "to",
          "type": "account_name"
        },
        {
          "name": "quantity",
          "type": "asset"
        },
        {
          "name": "memo",
          "type": "string"
        }
      ]
    },
    {
      "name": "create",
      "base": "",
      "fields": [
        {
          "name": "issuer",
          "type": "account_name"
        },
        {
          "name": "maximum_supply",
          "type": "asset"
        }
      ]
    },
      {
         "name":"trade",
         "base":"",
         "fields":[
            {
               "name":"from",
               "type":"account_name"
            },
            {
               "name":"to",
               "type":"account_name"
            },
            {
               "name":"quantity",
               "type":"asset"
            },
            {
               "name":"type",
               "type":"uint64"
            },
            {
               "name":"memo",
               "type":"string"
            }
         ]
      },
    {
      "name": "issue",
      "base": "",
      "fields": [
        {
          "name": "to",
          "type": "account_name"
        },
        {
          "name": "quantity",
          "type": "asset"
        },
        {
          "name": "memo",
          "type": "string"
        }
      ]
    },
    {
      "name": "account",
      "base": "",
      "fields": [
        {
          "name": "balance",
          "type": "asset"
        }
      ]
    },
    {
      "name": "currency_stats",
      "base": "",
      "fields": [
        {
          "name": "supply",
          "type": "asset"
        },
        {
          "name": "max_supply",
          "type": "asset"
        },
        {
          "name": "issuer",
          "type": "account_name"
        }
      ]
    },
    {
      "name": "fee",
      "base": "",
      "fields": [
        {
          "name": "payer",
          "type": "account_name"
        },
        {
          "name": "quantity",
          "type": "asset"
        }
      ]
    },
    {
      "name": "coin_cast",
      "base": "",
      "fields": [
        {
          "name": "balance",
          "type": "asset"
        },
        {
          "name":"finish_block",
          "type":"uint32"
        }
      ]
    },
    {
      "name": "castcoin",
      "base": "",
      "fields": [
        {
          "name": "from",
          "type": "account_name"
        },
        {
          "name": "to",
          "type": "account_name"
        },
        {
          "name": "quantity",
          "type": "asset"
        }
      ]
    },
    {
      "name": "takecoin",
      "base": "",
      "fields": [
        {
          "name": "to",
          "type": "account_name"
        }
      ]
    },
    {
      "name": "opencast",
      "base": "",
      "fields": [
        {
          "name": "to",
          "type": "account_name"
        }
      ]
    },
    {
      "name": "closecast",
      "base": "",
      "fields": [
        {
          "name": "to",
          "type": "account_name"
        },
        {
          "name": "finish_block",
          "type": "int32"
        }
      ]
    }
  ],
  "actions": [
    {
      "name": "transfer",
      "type": "transfer",
      "ricardian_contract": ""
    },
    {
      "name": "issue",
      "type": "issue",
      "ricardian_contract": ""
    },
    {
      "name": "create",
      "type": "create",
      "ricardian_contract": ""
    },
    {
      "name": "fee",
      "type": "fee",
      "ricardian_contract": ""
    },
    {
      "name":"trade",
      "type":"trade",
      "ricardian_contract":""
    },
    {
      "name":"castcoin",
      "type":"castcoin",
      "ricardian_contract":""
    },
    {
      "name":"takecoin",
      "type":"takecoin",
      "ricardian_contract":""
    },
    {
      "name":"opencast",
      "type":"opencast",
      "ricardian_contract":""
    },
    {
      "name":"closecast",
      "type":"closecast",
      "ricardian_contract":""
    }
  ],
  "tables": [
    {
      "name": "accounts",
      "type": "account",
      "index_type": "i64",
      "key_names": [ "currency" ],
      "key_types": [ "uint64" ]
    },
    {
      "name": "stat",
      "type": "currency_stats",
      "index_type": "i64",
      "key_names": [ "currency" ],
      "key_types": [ "uint64" ]
    },
    {
      "name": "coincast",
      "type": "coin_cast",
      "index_type": "i64",
      "key_names": [ "finishblock" ],
      "key_types": [ "uint64" ]
    }
  ],
  "ricardian_clauses": [],
  "abi_extensions": []
}
```
