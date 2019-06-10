# Codex System 系统合约

## 合约说明

relay.token 合约是Codex映射其他链资产的代币发行合约

## 1. Action

### 1.1 create 创建代币

创建代币符号
```cpp
void create( account_name issuer,
            name chain,
            account_name side_account,
            action_name side_action,
            asset maximum_supply );
```

参数：
- issuer : 发行人
- chain : 链名
- side_account : 链上的合约名
- side_action : 链上转账的操作名
- maximum_supply :  代币及最大供应量

  
### 1.2 issue 发行代币

发行一定数量代币到指定账户
```cpp
void issue( name chain, account_name to, asset quantity, string memo );
```

参数:
- chain : 链名
- to : 接收币的账户
- quantity :增发的币的数量
- memo : 备注

### 1.3 transfer 代币转账

代币转账操作
```cpp
   void transfer( account_name from,
                  account_name to,
                  name chain,
                  asset quantity,
                  string memo );
```

参数:
- from :转账的账户
- to : 接收币的账户
- chain : 链名
- quantity :转账的币
- memo : 备注

### 1.4 trade 代币交易

用于调用其他交易合约的功能的action
```cpp
   void trade( account_name from,
               account_name to,
               name chain,
               asset quantity,
               trade_type type,
               string memo);
```

参数:
- from : 转账的账户
- to : 接收币的账户,一般是其他交易合约
- chain : 链名
- quantity : 转账的币
- type : 类型 用于识别是哪个功能
- memo : 备注

### 1.5 销毁代币

将代币从Codex上销毁,被销毁的代币会分发到对应的链上对应的账户
```cpp
void destroy( name chain, account_name from, asset quantity, string memo );
```

参数:
- chain : 链名
- from : 销毁代币的账户
- quantity : 销毁的代币
- memo : 备注

### 1.6 领取铸币分红

领取分红到铸币池
```cpp
void claim(name chain,asset quantity,account_name receiver);
```

参数:
- chain : 链名
- quantity : 领取分红的代币 数量是无效的
- receiver : 领取分红的账户




## ABI

- [relay.token](https://github.com/codexnetwork/codex.relay/blob/develop/contracts/relay.token/relay.token.abi)

```json
{
   "version":"eosio::abi/1.0",
   "types":[
      {
         "new_type_name":"account_name",
         "type":"name"
      },
      {
         "new_type_name":"permission_name",
         "type":"name"
      },
      {
         "new_type_name":"action_name",
         "type":"name"
      },
      {
         "new_type_name":"transaction_id_type",
         "type":"checksum256"
      },
      {
         "new_type_name":"weight_type",
         "type":"uint16"
      }
   ],
   "structs":[
      {
         "name":"permission_level",
         "base":"",
         "fields":[
            {
               "name":"actor",
               "type":"account_name"
            },
            {
               "name":"permission",
               "type":"permission_name"
            }
         ]
      },
      {
         "name":"key_weight",
         "base":"",
         "fields":[
            {
               "name":"key",
               "type":"public_key"
            },
            {
               "name":"weight",
               "type":"weight_type"
            }
         ]
      },
      {
         "name":"permission_level_weight",
         "base":"",
         "fields":[
            {
               "name":"permission",
               "type":"permission_level"
            },
            {
               "name":"weight",
               "type":"weight_type"
            }
         ]
      },
      {
         "name":"wait_weight",
         "base":"",
         "fields":[
            {
               "name":"wait_sec",
               "type":"uint32"
            },
            {
               "name":"weight",
               "type":"weight_type"
            }
         ]
      },
      {
         "name":"authority",
         "base":"",
         "fields":[
            {
               "name":"threshold",
               "type":"uint32"
            },
            {
               "name":"keys",
               "type":"key_weight[]"
            },
            {
               "name":"accounts",
               "type":"permission_level_weight[]"
            },
            {
               "name":"waits",
               "type":"wait_weight[]"
            }
         ]
      },
      {
         "name":"action",
         "base":"",
         "fields":[
            {
               "name":"account",
               "type":"account_name"
            },
            {
               "name":"name",
               "type":"action_name"
            },
            {
               "name":"authorization",
               "type":"permission_level[]"
            },
            {
               "name":"data",
               "type":"bytes"
            }
         ]
      },
      {
         "name":"on",
         "base":"",
         "fields":[
            {
               "name":"chain",
               "type":"name"
            },
            {
               "name":"block_id",
               "type":"checksum256"
            },
            {
               "name":"act",
               "type":"action"
            }
         ]
      },
      {
         "name":"transfer",
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
               "name":"chain",
               "type":"name"
            },
            {
               "name":"quantity",
               "type":"asset"
            },
            {
               "name":"memo",
               "type":"string"
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
               "name":"chain",
               "type":"name"
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
         "name":"create",
         "base":"",
         "fields":[
            {
               "name":"issuer",
               "type":"account_name"
            },
            {
               "name":"chain",
               "type":"name"
            },
            {
               "name":"side_account",
               "type":"account_name"
            },
            {
               "name":"side_action",
               "type":"action_name"
            }
            {
               "name":"maximum_supply",
               "type":"asset"
            }
         ]
      },
      {
         "name":"issue",
         "base":"",
         "fields":[
            {
               "name":"chain",
               "type":"name"
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
               "name":"memo",
               "type":"string"
            }
         ]
      },
      {
         "name":"destroy",
         "base":"",
         "fields":[
            {
               "name":"chain",
               "type":"name"
            },
            {
               "name":"from",
               "type":"account_name"
            },
            {
               "name":"quantity",
               "type":"asset"
            },
            {
               "name":"memo",
               "type":"string"
            }
         ]
      },
      {
         "name":"account",
         "base":"",
         "fields":[
            {
               "name":"id",
               "type":"uint64"
            },
            {
               "name":"balance",
               "type":"asset"
            },
            {
               "name":"chain",
               "type":"name"
            },
            {"name":"mineage",        "type":"int128"},
            {"name":"mineage_update_height","type":"uint32"},
            {"name":"reward",        "type":"asset"}
         ]
      },
      {
         "name":"account_next_id",
         "base":"",
         "fields":[
            {
               "name":"id",
               "type":"uint64"
            },
            {
               "name":"account",
               "type":"account_name"
            }
         ]
      },
      {
         "name":"reward_mine_info",
         "base":"",
         "fields":[
            {
               "name":"total_mineage",
               "type":"int128"
            }, 
            {
               "name":"reward_pool",
               "type":"asset"
            }, 
            {
               "name":"reward_block_num",
               "type":"int32"
            }
         ]
      },
      {
         "name":"currency_stats",
         "base":"",
         "fields":[
            {
               "name":"supply",
               "type":"asset"
            },
            {
               "name":"max_supply",
               "type":"asset"
            },
            {
               "name":"issuer",
               "type":"account_name"
            },
            {
               "name":"chain",
               "type":"name"
            },
            {
               "name":"side_account",
               "type":"account_name"
            },
            {
               "name":"side_action",
               "type":"action_name"
            },
            {"name":"total_mineage",        "type":"int128"},
            {"name":"total_mineage_update_height","type":"uint32"},
            {"name":"reward_scope",        "type":"uint64"},
            {"name":"reward_size",        "type":"int32"}
         ]
      },
      {
         "name":"reward_currency",
         "base":"",
         "fields":[
            {
            "name":"id",
            "type":"uint64"
            },
            {
               "name":"chain",
               "type":"name"
            },
            {
               "name":"supply",
               "type":"asset"
            },
            {
               "name":"reward_now",
               "type":"bool"
            }
         ]
      },
      {
         "name":"addreward",
         "base":"",
         "fields":[
            {
               "name":"chain",
               "type":"name"
            },
            {
               "name":"supply",
               "type":"asset"
            },
            {
               "name":"reward_now",
               "type":"int32"
            }
         ]
      },
      {
         "name":"rewardmine",
         "base":"",
         "fields":[
            {
               "name":"quantity",
               "type":"asset"
            }
         ]
      },
      {
         "name":"claim",
         "base":"",
         "fields":[
            {
               "name":"chain",
               "type":"name"
            },
            {
               "name":"quantity",
               "type":"asset"
            },
            {
               "name":"receiver",
               "type":"account_name"
            }
         ]
      },
      {
         "name":"settlemine",
         "base":"",
         "fields":[
            {
               "name":"system_account",
               "type":"account_name"
            }
         ]
      },
      {
         "name":"activemine",
         "base":"",
         "fields":[
            {
               "name":"system_account",
               "type":"account_name"
            }
         ]
      }
   ],
   "actions":[
      {
         "name":"on",
         "type":"on",
         "ricardian_contract":""
      },
      {
         "name":"create",
         "type":"create",
         "ricardian_contract":""
      },
      {
         "name":"issue",
         "type":"issue",
         "ricardian_contract":""
      },
      {
         "name":"destroy",
         "type":"destroy",
         "ricardian_contract":""
      },
      {
         "name":"transfer",
         "type":"transfer",
         "ricardian_contract":""
      },
      {
         "name":"trade",
         "type":"trade",
         "ricardian_contract":""
      },
      {
         "name":"addreward",
         "type":"addreward",
         "ricardian_contract":""
      },
      {
         "name":"rewardmine",
         "type":"rewardmine",
         "ricardian_contract":""
      },
      {
         "name":"claim",
         "type":"claim",
         "ricardian_contract":""
      },
      {
         "name":"settlemine",
         "type":"settlemine",
         "ricardian_contract":""
      },
      {
         "name":"activemine",
         "type":"activemine",
         "ricardian_contract":""
      }
   ],
   "tables":[
      {
         "name":"accounts",
         "type":"account",
         "index_type":"i64",
         "key_names":[
            "currency"
         ],
         "key_types":[
            "uint64"
         ]
      },
      {
         "name":"accountid",
         "type":"account_next_id",
         "index_type":"i64",
         "key_names":[
            "account"
         ],
         "key_types":[
            "account_name"
         ]
      },
      {
         "name":"stat",
         "type":"currency_stats",
         "index_type":"i64",
         "key_names":[
            "currency"
         ],
         "key_types":[
            "uint64"
         ]
      },
      {
         "name":"reward",
         "type":"reward_currency",
         "index_type":"i64",
         "key_names":[
            "currency"
         ],
         "key_types":[
            "uint64"
         ]
      },
      {
         "name":"minereward",
         "type":"reward_mine_info",
         "index_type":"i64",
         "key_names":[
            "blocknum"
         ],
         "key_types":[
            "uint64"
         ]
      }
   ],
   "ricardian_clauses":[

   ],
   "error_messages":[

   ],
   "abi_extensions":[]
}


```