# Codex System 系统合约

## 合约说明

System合约是Codex系统关键功能的实现合约，替换原eosio.system系统合约。
包括投票、分红等，对eos原有逻辑的修改。
改合约使用eosio账户发布，固定在初始化代码中。

## 合约方法 action

### 1. 设置BP（区块生产者）

```cpp
void updatebp( const account_name bpname, const public_key producer_key,
const uint32_t commission_rate, const std::string & url );
```

添加、修改节点信息

参数：

- bpnbame : 节点账号
- producer_key : 节点公钥
- commission_rate : 分红比列 单位:万分之1, 范围:大于等于1，小于等于10000
- url : 官网链接， 不超过64字节

### 1. 换票

```cpp
void freeze( const account_name voter, const asset stake );
```

将系统代币兑换为可投票的选票

参数：

- voter : 投票账户
- stake : 对话投票的系统代币的数量

### 3. 投票

```cpp
void vote( const account_name voter, const account_name bpname, const asset stake );
```

给超级节点投票

参数:

- voter : 投票者
- bpbame : 节点
- statke : 票数（eos金额）

逻辑:

1. 设置用户的投票信息：
投票数大于当前票数，增加投票；
投票数小于当前票数，撤回投票；
2. 根据投票数减少相应用户余额
3. 增加节点的总票数， 结算节点当前总票龄

### 4. 解冻

```cpp
void unfreeze( const account_name voter, const account_name bpname );
```

撤回投票要冻结3天(根据块数确定，每1秒一块)，3天后可执行解冻。
逻辑：将撤回票数 加到可用余额中。清空撤回票数。

### 5. 领取投票分红

```cpp
void claimbp( const account_name voter, const account_name bpname );
```

参数:

- voter : 投票者
- bpname : 节点名

逻辑:

1. 计算投票账号最新票龄：上次票龄+票数*(当前块高度-上次结算时块高度)
2. 计算节点的总票龄：上次票龄+票数*(当前块高度-节点上次结算时块高度)
3. 计算分红数：节点奖池总数*投票者总票龄/节点总票龄
4. 增加投票账号余额 +=分红数
5. 投票信息票龄清零，更新结算块高度
6. 减少节点奖池 -=分红数，总票龄减少账号票龄，更新计算块高度

### 6. 出块回调函数    (系统内部使用)

```cpp
void onblock(…const account_name bpname,const uint32_t schedule_version);
```

参数:

- bpname : 节点名
- schedule_version : 节点排行换届版本
  
逻辑:

1. 出块节点bpname，producer.amount出块数加1
2. 每个出块奖励9个EOS，按分红比例，将奖励加入出块节点余额，并将剩余分红加入出块节点的奖池中。
3. 每个出块将奖励1个EOS给b1账号(block.one)
4. 每100个块(即5分钟)更新一次超级节点排行. 刷新选举的23个超级节点，遍历所有bp查前23个，调用wasm接口set_proposed_producers重置BP,修改数据库数据

### 7. 设置链紧急状态

```cpp
void setemergency( const account_name bpname, const bool emergency );
```

超过三分之二的节点设置紧急状态，则链将处于紧急状态。将紧急停用所有功能。

参数: 

- bpname : 节点
- emergency : 紧急状态

### 8. 增加抵押

```cpp
void addmortgage(const account_name bpname,const account_name payer,asset quantity);
```

BP用此功能添加出块抵押,用于竞选出块BP

参数:

- bpname : 节点
- payer :  付款账户
- quantity : 抵押金额

### 9. 解除抵押

```cpp
void claimmortgage(const account_name bpname,const account_name receiver,asset quantity);
```

BP用此功能添加取出出块抵押

参数:

- bpname : 节点
- receiver :  收款账户
- quantity : 抵押金额

### 10. 领取投票分红

```cpp
void claimvote(const account_name bpname,const account_name receiver);
```

领取BP上面的出块分红

参数: 

- bpname : 节点
- receiver :  领取账户

### 11. 领取出块分红

```cpp
void claimbp(const account_name bpname,const account_name receiver);
```

出块BP领取出块分红

参数:

- bpname : 节点
- receiver :  收款账户

### 12. 发起提议惩罚BP

```cpp
void punishbp(const account_name initiator,const account_name bpname);
```

用户发起提议惩罚漏块的BP 漏块BP记录在表格lastdrainbp上面

参数:

- initiator :  发起提议的账户
- bpname : 节点

### 13. 撤销惩罚BP提议

```cpp
void canclepunish(const account_name initiator,const account_name bpname); 
```

用户撤销惩罚漏块的BP的提议

参数:

- initiator :  发起提议的账户
- bpname : 节点

### 13.同意惩罚BP的提议

```cpp
void apppunish(const account_name bpname,const account_name punishbpname);
```

出块节点同意惩罚BP的提议

参数:

- bpname : 节点
- punishbpname :  待惩罚的BP

### 13.撤销对惩罚BP的提议的同意

```cpp
void unapppunish(const account_name bpname,const account_name punishbpname);
```

出块节点同意惩罚BP的提议

参数:

- bpname : 节点
- punishbpname :  待惩罚的BP

### 14.结束惩罚

```cpp
void bailpunish(const account_name bpname);
```

被惩罚的节点在惩罚期过后可以使用这个功能结束对自己的惩罚

参数:

- bpname : 被惩罚的BP

### 15.增加CPU和NET的抵押

```cpp
void delegatebw( account_name from, account_name receiver,
             asset stake_net_quantity, asset stake_cpu_quantity, bool transfer );
```

抵押CPU和NET

参数:

- from : 付款的账户
- receiver:接受CPU和NET的账户
- stake_net_quantity : 增加的NET的抵押
- stake_cpu_quantity : 增加的CPU的抵押
- transfer : 是否会把币转个receiver

### 15.减少CPU和NET的抵押

```cpp
void undelegatebw( account_name from, account_name receiver,
               asset unstake_net_quantity, asset unstake_cpu_quantity );
```

抵押CPU和NET

参数:

- from : 付款的账户
- receiver:减少CPU和NET的账户
- stake_net_quantity : 减少的NET的抵押
- stake_cpu_quantity : 减少的CPU的抵押

### 16.取出CPU和NET赎回的部分

```cpp
void refund( account_name owner );
```

取出CPU和NET赎回的币

参数:

- owner : 赎回的账户

## abi

- [System]https://github.com/codexnetwork/codex.relay/blob/develop/contracts/force.system/force.system.abi)
  
```json
{
  "version": "eosio::abi/1.0",
  "types": [{
      "new_type_name": "permission_name",
      "type": "name"
   },{
      "new_type_name": "action_name",
      "type": "name"
   },{
      "new_type_name": "transaction_id_type",
      "type": "checksum256"
   },{
      "new_type_name": "weight_type",
      "type": "uint16"
   }],
  "structs": [{
      "name": "updatebp",
      "base": "",
      "fields": [
         {"name":"bpname",            "type":"account_name"},
         {"name":"block_signing_key", "type":"public_key"},
         {"name":"commission_rate",   "type":"uint32"},
         {"name":"url",               "type":"string"}
      ]
    },{
      "name": "unfreeze",
      "base": "",
      "fields": [
         {"name":"voter",      "type":"account_name"}
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
      "name": "voteproducer",
      "base": "",
      "fields": [
        {"name":"voter",     "type":"account_name"},
        {"name":"producers", "type":"account_name[]"}
      ]
    },{
      "name": "vote_info",
      "base": "",
      "fields": [
        {"name":"bpname",                "type":"account_name"},
        {"name":"vote",                  "type":"asset"},
        {"name":"voteage",               "type":"int64"},
        {"name":"voteage_update_height", "type":"uint32"},
        {"name":"total_reward", "type":"asset"}
      ]
    },{
      "name": "votes_info",
      "base": "",
      "fields": [
        {"name":"owner",     "type":"account_name"},
        {"name":"producers", "type":"account_name[]"},
        {"name":"staked",    "type":"asset"}
      ]
    },{
      "name": "freeze_info",
      "base": "",
      "fields": [
        {"name":"staked",         "type":"asset"},
        {"name":"unstaking",      "type":"asset"},
        {"name":"voter",          "type":"account_name"},
        {"name":"unstake_height", "type":"uint32"}
      ]
    },{
      "name": "vote_reward_info",
      "base": "",
      "fields": [
        {"name":"total_voteage",         "type":"int64"},
        {"name":"total_reward",      "type":"asset"},
        {"name":"reward_block_num",          "type":"int32"}
      ]
    },{
      "name": "bp_info",
      "base": "",
      "fields": [
        {"name":"name",                 "type":"account_name"},
        {"name":"block_signing_key",    "type":"public_key"},
        {"name":"commission_rate",      "type":"uint32"},
        {"name":"total_staked",         "type":"int64"},
        {"name":"rewards_block",         "type":"asset"},
        {"name":"total_voteage",        "type":"int64"},
        {"name":"voteage_update_height","type":"uint32"},
        {"name":"url",                  "type":"string"},
        {"name":"emergency",            "type":"bool"},
        {"name":"active_type",             "type":"int32"},
        {"name":"block_age",            "type":"int64"},
        {"name":"last_block_amount",    "type":"uint32"},
        {"name":"block_weight",    "type":"int64"},
        {"name":"bp_mortgageage",               "type":"asset"},
        {"name":"total_drain_block",             "type":"int32"},
        {"name":"remain_punish",             "type":"asset"},
        {"name":"active_change_block_num",             "type":"int32"},
        {"name":"reward_size",             "type":"int32"}
      ]
    },{
      "name": "setemergency",
      "base": "",
      "fields": [
        {"name":"bpname",    "type":"account_name"},
        {"name":"emergency", "type":"bool"}
      ]
    },{
      "name": "chain_status",
      "base": "",
      "fields": [
        {"name":"name",       "type":"account_name"},
        {"name":"emergency",  "type":"bool"}
      ]
    },{
      "name": "producer",
      "base": "",
      "fields": [
        {"name":"bpname",  "type":"account_name"},
        {"name":"amount",  "type":"uint32"}
      ]
    },{
      "name": "schedule_info",
      "base": "",
      "fields": [
        {"name":"version",     "type":"uint64"},
        {"name":"block_height","type":"uint32"},
        {"name":"producers",   "type":"producer[]"}
      ]
    },{
        "name": "permission_level",
        "base": "",
        "fields": [
          {"name":"actor",      "type":"account_name"},
          {"name":"permission", "type":"permission_name"}
        ]
      },{
        "name": "key_weight",
        "base": "",
        "fields": [
          {"name":"key",    "type":"public_key"},
          {"name":"weight", "type":"weight_type"}
        ]
      },{
        "name": "permission_level_weight",
        "base": "",
        "fields": [
          {"name":"permission", "type":"permission_level"},
          {"name":"weight",     "type":"weight_type"}
        ]
      },{
        "name": "wait_weight",
        "base": "",
        "fields": [
          {"name":"wait_sec", "type":"uint32"},
          {"name":"weight",   "type":"weight_type"}
        ]
      },{
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
     },{
       "name": "setcode",
       "base": "",
       "fields": [
         {"name":"account",   "type":"account_name"},
         {"name":"vmtype",    "type":"uint8"},
         {"name":"vmversion", "type":"uint8"},
         {"name":"code",      "type":"bytes"}
       ]
     },{
       "name": "setabi",
       "base": "",
       "fields": [
         {"name":"account", "type":"account_name"},
         {"name":"abi",     "type":"bytes"}
       ]
     },{
       "name": "updateauth",
       "base": "",
       "fields": [
         {"name":"account",    "type":"account_name"},
         {"name":"permission", "type":"permission_name"},
         {"name":"parent",     "type":"permission_name"},
         {"name":"auth",       "type":"authority"}
       ]
     },{
       "name": "deleteauth",
       "base": "",
       "fields": [
         {"name":"account",    "type":"account_name"},
         {"name":"permission", "type":"permission_name"}
       ]
     },{
       "name": "linkauth",
       "base": "",
       "fields": [
         {"name":"account",     "type":"account_name"},
         {"name":"code",        "type":"account_name"},
         {"name":"type",        "type":"action_name"},
         {"name":"requirement", "type":"permission_name"}
       ]
     },{
       "name": "unlinkauth",
       "base": "",
       "fields": [
         {"name":"account",     "type":"account_name"},
         {"name":"code",        "type":"account_name"},
         {"name":"type",        "type":"action_name"}
       ]
     },{
       "name": "canceldelay",
       "base": "",
       "fields": [
         {"name":"canceling_auth", "type":"permission_level"},
         {"name":"trx_id",         "type":"transaction_id_type"}
       ]
     },{
       "name": "setconfig",
       "base": "",
       "fields": [
         {"name":"typ",  "type":"account_name"},
         {"name":"num",  "type":"int64"},
         {"name":"key",  "type":"account_name"},
         {"name":"fee",  "type":"asset"}
       ]
     },{
      "name": "blockchain_parameters",
      "base": "",
      "fields": [

         {"name":"max_block_net_usage",                 "type":"uint64"},
         {"name":"target_block_net_usage_pct",          "type":"uint32"},
         {"name":"max_transaction_net_usage",           "type":"uint32"},
         {"name":"base_per_transaction_net_usage",      "type":"uint32"},
         {"name":"net_usage_leeway",                    "type":"uint32"},
         {"name":"context_free_discount_net_usage_num", "type":"uint32"},
         {"name":"context_free_discount_net_usage_den", "type":"uint32"},
         {"name":"max_block_cpu_usage",                 "type":"uint32"},
         {"name":"target_block_cpu_usage_pct",          "type":"uint32"},
         {"name":"max_transaction_cpu_usage",           "type":"uint32"},
         {"name":"min_transaction_cpu_usage",           "type":"uint32"},
         {"name":"max_transaction_lifetime",            "type":"uint32"},
         {"name":"deferred_trx_expiration_window",      "type":"uint32"},
         {"name":"max_transaction_delay",               "type":"uint32"},
         {"name":"max_inline_action_size",              "type":"uint32"},
         {"name":"max_inline_action_depth",             "type":"uint16"},
         {"name":"max_authority_depth",                 "type":"uint16"}

      ]
    },{
      "name": "setparams",
      "base": "",
      "fields": [
        {"name":"params", "type":"blockchain_parameters"}
      ]
    },{
       "name": "removebp",
       "base": "",
       "fields": [
         {"name":"bpname",  "type":"account_name"}
       ]
     },{
         "name": "delegatebw",
         "base": "",
         "fields": [
            {"name":"from", "type":"account_name"},
            {"name":"receiver", "type":"account_name"},
            {"name":"stake_net_quantity", "type":"asset"},
            {"name":"stake_cpu_quantity", "type":"asset"},
            {"name":"transfer", "type":"bool"}
         ]
       },{
         "name": "undelegatebw",
         "base": "",
         "fields": [
            {"name":"from", "type":"account_name"},
            {"name":"receiver", "type":"account_name"},
            {"name":"unstake_net_quantity", "type":"asset"},
            {"name":"unstake_cpu_quantity", "type":"asset"}
         ]
       },{
         "name": "refund",
         "base": "",
         "fields": [
            {"name":"owner", "type":"account_name"}
         ]
       },{
         "name": "delegated_bandwidth",
         "base": "",
         "fields": [
            {"name":"from", "type":"account_name"},
            {"name":"to", "type":"account_name"},
            {"name":"net_weight", "type":"asset"},
            {"name":"cpu_weight", "type":"asset"}
         ]
       },{
         "name": "user_resources",
         "base": "",
         "fields": [
            {"name":"owner", "type":"account_name"},
            {"name":"net_weight", "type":"asset"},
            {"name":"cpu_weight", "type":"asset"},
            {"name":"ram_bytes", "type":"uint64"}
         ]
       },{
         "name": "total_resources",
         "base": "",
         "fields": [
            {"name":"owner", "type":"account_name"},
            {"name":"net_weight", "type":"asset"},
            {"name":"cpu_weight", "type":"asset"},
            {"name":"ram_bytes", "type":"uint64"}
         ]
       },{
         "name": "refund_request",
         "base": "",
         "fields": [
            {"name":"owner", "type":"account_name"},
            {"name":"request_time", "type":"time_point_sec"},
            {"name":"net_amount", "type":"asset"},
            {"name":"cpu_amount", "type":"asset"}
         ]
       },{
         "name": "reward_info",
         "base": "",
         "fields": [
            {"name":"id", "type":"uint64" },
            {"name":"reward_block_out",      "type":"asset"},
            {"name":"reward_budget",             "type":"asset"},
            {"name":"total_block_out_age",   "type":"int64"},
            {"name":"cycle_reward",          "type":"int64"},
            {"name":"gradient",              "type":"int32"},
            {"name":"total_reward_time",              "type":"int32"},
            {"name":"last_reward_block_num",              "type":"int32"},
            {"name":"last_producer_name",              "type":"account_name"},
            {"name":"reward_block_num",              "type":"int32[]"}
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
         "name": "claimmortgage",
         "base": "",
         "fields": [
            {"name":"bpname",                "type":"account_name"},
            {"name":"receiver",              "type":"account_name"},
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
       },{
         "name": "creation_bp",
         "base": "",
         "fields": [
            {"name":"bpname",                "type":"account_name"},
            {"name":"total_staked",   "type":"int64"},
            {"name":"mortgage",   "type":"int64"}
         ]
       },{
         "name": "fee",
         "base": "",
         "fields": [
            {"name":"payer",                 "type":"account_name"}
            {"name":"bpname",                "type":"account_name"}
            {"name":"voteage",               "type":"int64"}
         ]
       },{
         "name": "punish_bp_info",
         "base": "",
         "fields": [
            {"name":"initiator",                "type":"account_name"},
            {"name":"bpname",                "type":"account_name"},
            {"name":"drain_num",   "type":"int32"},
            {"name":"update_block_num",   "type":"int32"}
         ]
       },{
         "name": "last_drain_block",
         "base": "",
         "fields": [
            {"name":"bpname",                "type":"account_name"},
            {"name":"drain_num",   "type":"int32"},
            {"name":"update_block_num",   "type":"int32"}
         ]
       },{
         "name": "approve_punish_bp",
         "base": "",
         "fields": [
            {"name":"bpname",                "type":"account_name"},
            {"name":"approve_producer",   "type":"account_name[]"}
         ]
       },{
         "name": "punishbp",
         "base": "",
         "fields": [
            {"name":"initiator",                "type":"account_name"},
            {"name":"bpname",                "type":"account_name"}
         ]
       },{
         "name": "canclepunish",
         "base": "",
         "fields": [
            {"name":"initiator",                "type":"account_name"},
            {"name":"bpname",                "type":"account_name"}
         ]
       },{
         "name": "apppunish",
         "base": "",
         "fields": [
            {"name":"bpname",                "type":"account_name"},
            {"name":"punishbpname",                "type":"account_name"}
         ]
       },{
         "name": "unapppunish",
         "base": "",
         "fields": [
            {"name":"bpname",                "type":"account_name"},
            {"name":"punishbpname",                "type":"account_name"}
         ]
       },{
         "name": "bailpunish",
         "base": "",
         "fields": [
            {"name":"bpname",                "type":"account_name"}
         ]
       },{
         "name": "rewardmine",
         "base": "",
         "fields": [
            {"name":"reward_num",                "type":"int64"}
         ]
       }
  ],
  "actions": [{
      "name": "updatebp",
      "type": "updatebp",
      "ricardian_contract": ""
    },{
      "name": "freeze",
      "type": "freeze",
      "ricardian_contract": ""
    },{
      "name": "vote",
      "type": "vote",
      "ricardian_contract": ""
    },{
      "name": "unfreeze",
      "type": "unfreeze",
      "ricardian_contract": ""
    },{
      "name": "vote4ram",
      "type": "vote",
      "ricardian_contract": ""
    },{
      "name": "voteproducer",
      "type": "voteproducer",
      "ricardian_contract": ""
    },{
      "name": "setemergency",
      "type": "setemergency",
      "ricardian_contract": ""
    },{
       "name": "newaccount",
       "type": "newaccount",
       "ricardian_contract": ""
     },{
       "name": "setcode",
       "type": "setcode",
       "ricardian_contract": ""
     },{
       "name": "setabi",
       "type": "setabi",
       "ricardian_contract": ""
     },{
       "name": "updateauth",
       "type": "updateauth",
       "ricardian_contract": ""
     },{
       "name": "deleteauth",
       "type": "deleteauth",
       "ricardian_contract": ""
     },{
       "name": "linkauth",
       "type": "linkauth",
       "ricardian_contract": ""
     },{
       "name": "unlinkauth",
       "type": "unlinkauth",
       "ricardian_contract": ""
     },{
       "name": "canceldelay",
       "type": "canceldelay",
       "ricardian_contract": ""
     },{
       "name": "setconfig",
       "type": "setconfig",
       "ricardian_contract": ""
     },{
       "name": "setparams",
       "type": "setparams",
       "ricardian_contract": ""
     },{
       "name": "removebp",
       "type": "removebp",
       "ricardian_contract": ""
     },{
         "name": "delegatebw",
         "type": "delegatebw",
         "ricardian_contract": ""
      },{
         "name": "undelegatebw",
         "type": "undelegatebw",
         "ricardian_contract": ""
      },{
         "name": "refund",
         "type": "refund",
         "ricardian_contract": ""
      },{
         "name": "addmortgage",
         "type": "addmortgage",
         "ricardian_contract": ""
      },{
         "name": "claimmortgage",
         "type": "claimmortgage",
         "ricardian_contract": ""
      },{
         "name": "claimbp",
         "type": "claimbp",
         "ricardian_contract": ""
      },{
         "name": "claimvote",
         "type": "claimvote",
         "ricardian_contract": ""
      },{
         "name": "fee",
         "type": "fee",
         "ricardian_contract": ""
      },{
         "name": "punishbp",
         "type": "punishbp",
         "ricardian_contract": ""
      },{
         "name": "canclepunish",
         "type": "canclepunish",
         "ricardian_contract": ""
      },{
         "name": "apppunish",
         "type": "apppunish",
         "ricardian_contract": ""
      },{
         "name": "unapppunish",
         "type": "unapppunish",
         "ricardian_contract": ""
      },{
         "name": "bailpunish",
         "type": "bailpunish",
         "ricardian_contract": ""
      },{
         "name": "rewardmine",
         "type": "rewardmine",
         "ricardian_contract": ""
      }
  ],
  "tables": [
        {
          "name":"bps",
          "type":"bp_info",
          "index_type":"i64",
          "key_names": ["name"],
          "key_types": ["account_name"]
       },{
          "name":"freezed",
          "type":"freeze_info",
          "index_type":"i64",
          "key_names": ["voter"],
          "key_types": ["account_name"]
       },{
          "name":"votes",
          "type":"vote_info",
          "index_type":"i64",
          "key_names": ["bpname"],
          "key_types": ["account_name"]
       },{
          "name":"mvotes",
          "type":"votes_info",
          "index_type":"i64",
          "key_names": ["owner"],
          "key_types": ["account_name"]
       },{
          "name":"votes4ram",
          "type":"vote_info",
          "index_type":"i64",
          "key_names": ["bpname"],
          "key_types": ["account_name"]
       },{
          "name":"schedules",
          "type":"schedule_info",
          "index_type":"i64",
          "key_names": ["version"],
          "key_types": ["uint64"]
       },{
           "name": "userres",
           "type": "user_resources",
           "index_type": "i64",
           "key_names" : ["owner"],
           "key_types" : ["uint64"]
       },{
           "name": "delband",
           "type": "delegated_bandwidth",
           "index_type": "i64",
           "key_names" : ["to"],
           "key_types" : ["uint64"]
       },{
            "name": "refunds",
            "type": "refund_request",
            "index_type": "i64",
            "key_names" : ["owner"],
            "key_types" : ["uint64"]
       },{
            "name": "reward",
            "type": "reward_info",
            "index_type": "i64",
            "key_names" : ["id"],
            "key_types" : ["uint64"]
       },{
            "name": "creationbp",
            "type": "creation_bp",
            "index_type": "i64",
            "key_names" : ["bpname"],
            "key_types" : ["uint64"]
       },{
            "name": "lastdrainbp",
            "type": "last_drain_block",
            "index_type": "i64",
            "key_names" : ["bpname"],
            "key_types" : ["uint64"]
       },{
            "name": "punishbps",
            "type": "punish_bp_info",
            "index_type": "i64",
            "key_names" : ["bpname"],
            "key_types" : ["uint64"]
       },{
            "name": "apppunishbps",
            "type": "approve_punish_bp",
            "index_type": "i64",
            "key_names" : ["bpname"],
            "key_types" : ["uint64"]
       },{
            "name": "votereward",
            "type": "vote_reward_info",
            "index_type": "i64",
            "key_names" : ["blocknum"],
            "key_types" : ["uint64"]
       }
  ],
  "ricardian_clauses": [],
  "abi_extensions": []
}
```
