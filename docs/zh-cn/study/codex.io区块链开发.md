# 开始

在本教程中，你将基于codex.io构建一个区块链，在学习的过程中你将学到如何修改得到一条属于自己的区块链，合约的编写，rpc查询接口的编写，插件的编写.该示例将展示如何基于codex.io快速、轻松地**构建自己的区块链**

在本教程的最后，你会得到一个可以储存一串hash数据的区块链，用户可以储存hash数据在区块链上，并支持查询，http推送等功能，储存到区块上的hash数据不能被修改和删除。
    
# 需要

+ 需要有eosforce或eos上面开发dapp的经验，对eosforce链或eos链有自己的理解
+ 创造属于你自己区块链的意愿！

# 设计

程序目标：构造一个可以储存hash的区块链.

首先我们需要启动一条属于自己的区块链，这个可以基于codex.io框架快速启动。储存hash的部分应该放在合约层，我们需要编写一个合约来储存hash。查询hash的部分在rpc接口层，我们需要在chan_plugin上面添加上对应的查询功能.储存后的推送部分需要单独写插件完成。

# 起链

 基于codex.io框架可以快速启动一条区块链。codex.io的github地址：https://github.com/codexnetwork/codex.io 。首先把代码fork到自己的项目下面，下载到本地以后，再把这条链修改成自己的链。

codex.io是基于eosforce修改的一套区块链框架，可以用来构建多资产股权证明(POS)的区块链。由于codex.io是基于eosforce的，所以和eosforce，eosio和基于eos发起的所有分叉链都能很好的进行交互，和原力团队开发的中继链codex也能无缝进行通信，后续可以通过中继链和其他EOS链进行代币交易。支持并适配wasm，开发者可以很方便的在这个链上开发并部署DAPP。

拿到这个框架以后我们需要把这份代码Fork到我们自己的工程目录下，然后基于框架修改成一条属于我们自己的区块链。我们需要修改一些名称和属性来标识我们的区块链,所以我们需要修改链名，系统帐户的名字，出块节点的个数，生产一个区块的所用的时间，一个BP一次生产的区块个数，经济模型，公钥的前缀，核心代币的名称等。

+ 链名：一个链的标识是chain_id，codex.io的chain_id取的是genesis文件的hash，所以一般情况下不会有两条链的chain_id是一样的，这个不需要手动修改。
+ 系统帐户的名字：在eosio_build.sh上面ROOT_ACCOUNT="hash" ，这里我们修改成hash。注意：不要超过6个字符，因为会有一个带.token的系统帐户来管理token，所有帐户名都不能超过12个字符
+ 出块节点的个数：在eosio_build.sh上面MAX_PRODUCERS=21，这里我们修改成21个。如果是条测试链，可以改的很小，但不能小于4个，参照eosio修改成21个。
+ 生产一个区块的所用的时间：在eosio_build.sh上面 BLOCK_INTERVAL_MS=1000  这里修改成1S,太快的话对系统性能要求比较高，如果系统性能不好建议修改成3S，但最好不要超过3S
+ 一个BP一次生产的区块个数:在eosio_build.sh上面 PRODUCER_REPETITIONS=3  参照eos，一个BP每次占用时间为3S,也就是说如果是1S1个块的话一个BP一次出3个块，如果是3S1个块的话1个BP每次出1个块
+ 经济模型：在eosio_build.sh上面 RESOURCE_MODEL=0  codex.io提供3种经济模型可供选择：0代表不限制，也就是CPU，NET，RAM所有用户均可以无限使用。1代币手续费模式，也就是每个操作都要支付一定的系统代币作为手续费。2代表抵押模式，也就是需要先抵押CPU和NET，才能使用，不需要消耗代币。这里我们选择不消耗模式
+ 公钥的前缀：在CMakeList.txt上面set(PUB_KEY_LEGACY_PREFIX "HASH")  这里我们就使用HASH作为公钥的前缀。
  
以上信息修改以后重新编译代码就可以起链了。codex.io提供了一个专门的脚本进行起链：`bios_boot_eosforce.py`。脚本在`tutorials/bios-script`下面。使用命令

```bash
./bios_boot_eosforce.py  -c && ./bios_boot_eosforce.py  -a
```

就可以启动21个创世节点把链启动。

+ ./bios_boot_eosforce.py  -c 是关闭系统上所有的node进程，并清理中间文件。
+ ./bios_boot_eosforce.py  -a命令是生成所有的中间文件，启动所有的创世BP ，生成钱包等所有的工作。
  
这些工作完成以后一条区块链就成功启动了。通过cleos的get info可以获取链上的相关信息

```bash
../../build/programs/cleos/cleos --wallet-url http://127.0.0.1:8666 --url http://127.0.0.1:8001 get info
{
  "server_version": "8c986886",
  "chain_id": "f4c9a73252b1abab51e57da19ddcb78f5762e8b87184d0b81acd766df2e4f7a7",
  "head_block_num": 1523,
  "last_irreversible_block_num": 1439,
  "last_irreversible_block_id": "0000059fd4c543168be6287add273a0ad61f885df65579bfb77b462f08863796",
  "head_block_id": "000005f35e7c9c7c098a723b66eb938e0bb7ee0031f05f02bae7abb38667e33e",
  "head_block_time": "2019-07-11T06:50:29.000",
  "head_block_producer": "biosbpb",
  "virtual_block_cpu_limit": 4583099,
  "virtual_block_net_limit": 4805810,
  "block_cpu_limit": 999900,
  "block_net_limit": 1048576,
  "server_version_string": "hash.v0.1.0"
}
```

# 合约

codex.io的合约和eos合约是一样的，作为预编译的WASM部署到区块链中，WASM使用LLVM和clang从C/C++进行编译，也就是从事codex.io合约开发的人需要具备C/C++的开发知识。

codex.io的合约在contract目录下面，我们建一个record的合约，目录结构如下：
```bash
./codex.io
└── contracts
    └── nameservice
        └── record
            ├── CMakeLists.txt
            ├── record.abi
            ├── record.cpp
            └── record.hpp
```

首先我们需要创建一个结构体来储存hash

```cpp
 struct hash_record {
    uint64_t          id;
    checksum256 hash;
    uint32_t           current_block;     
    uint64_t primary_key()const { return id; }
    key256 get_index_byhash()const { 
       return get_key_id(hash);
     }
 };
```

hash的类型是checksum256,由于multi_index使用key256类型作为二级主键，所以需要有一个从checksum256转到key256的函数

```cpp
 static key256 get_key_id(const checksum256 &hash_data) {
    const uint64_t *p64 = reinterpret_cast<const uint64_t *>(&hash_data);
    return key256::make_from_word_sequence<uint64_t>(p64[0], p64[1], p64[2], p64[3]); 
 }
```

最后就是需要有一个函数取让外部调用，储存hash

```cpp
void record::pushrecord(checksum256 hash) {
  require_auth(_self);
  hashrecords record_tbl(_self,_self);
  
  auto idx = record_tbl.get_index<N(byhash)>();
  auto to = idx.find(get_key_id(hash));
  eosio_assert(to == idx.end(),"the hash has exist");
  
  record_tbl.emplace( _self, [&]( auto& s ) {
     s.id = record_tbl.available_primary_key();
     s.current_block = current_block_num();
     s.hash = hash;
  });
}
```

这里通过byhash这个二级索引来判断是否是重复的hash

合约编写好以后创建一个叫record的用户并设置record的合约。设置好以后就可以通过cleos来调用功能储存hash了

```bash
cleos push action record pushrecord '["7af0be9a825ee84acf41efe13300b4eb0574fd67c1adc1da4080775554bb6eb0"]' -p record
```

然后查看表格里面的内容

```bash
cleos get table record record hashrecord 
{
  "rows": [{
      "id": 0,
      "hash": "7af0be9a825ee84acf41efe13300b4eb0574fd67c1adc1da4080775554bb6eb0",
      "current_block": 243
    }
  ],
  "more": false
}
```

# RPC查询接口

codex.io已经提供了较为完善的查询接口来查询内存表中的内容，像`cleos get table record record hashrecord` 调用的就是`v1/chain/get_table_rows`接口。但是根据256位的二级索引查询数据并没有提供，需要自己写。这里就介绍下如何添加一个通过256位二级索引查询数据的实现。

所有的rpc调用的实现是在`chain_plugin`这个插件上面，在类read_only上面。如果你仔细研究一下`get_table_rows`的方法，你就会发现它根据key调用了很多不同的函数，我们先编写一个通用的通过256位二级索引查询数据的函数。代码如下

```cpp
read_only::get_table_rows_result get_table_rows_bysec256( const read_only::get_table_rows_params& p, const abi_def& abi )const {
      read_only::get_table_rows_result result;
      const auto& d = db.db();

      uint64_t scope = convert_to_type<uint64_t>(p.scope, "scope");

      abi_serializer abis;
      abis.set_abi(abi, abi_serializer_max_time);
      const auto* t_id = d.find<chain::table_id_object, chain::by_code_scope_table>(boost::make_tuple(p.code, scope, p.table));
      const auto& idx = d.get_index<chain::index256_index, chain::by_secondary>();
      fc::sha256 uv = convert_to_type<fc::sha256>( p.table_key, "table key" );
      
      const uint64_t *p64 = reinterpret_cast<const uint64_t *>(&uv);
      auto key = key256::make_from_word_sequence<uint64_t>(p64[0], p64[1], p64[2], p64[3]); 

      auto itr = idx.lower_bound(boost::make_tuple(t_id->id, key.get_array()));
      if (itr != idx.end() && itr->t_id == t_id->id && itr->secondary_key == key.get_array()) {
         auto primary_key = itr->primary_key;
         const auto& idx = d.get_index<chain::key_value_index, chain::by_scope_primary>();

         auto itr = idx.lower_bound(boost::make_tuple(t_id->id, primary_key));
         FC_ASSERT( itr != idx.end() && itr->t_id == t_id->id && primary_key == itr->primary_key,
                    "Record found in secondary index, but not found in primary index."
         );
         vector<char> data;
         copy_inline_row(*itr, data);

         fc::variant data_var;
         if( p.json ) {
            data_var = abis.binary_to_variant( abis.get_table_type(p.table), data, abi_serializer_max_time, shorten_abi_errors );
         } else {
            data_var = fc::variant( data );
         }

         if( p.show_payer && *p.show_payer ) {
            result.rows.emplace_back( fc::mutable_variant_object("data", std::move(data_var))("payer", itr->payer) );
         } else {
            result.rows.emplace_back( std::move(data_var) );
         }
      } 
      return result;
   }
```

然后我们需要查询record合约的hashrecord表，可以为这个单独写一个接口get_record_byhash.首先定义这个函数和这个函数的入参：

```cpp
   struct get_record_byhash_params {
      string   hashstr;
      bool     json;
   };
   get_table_rows_result get_record_byhash( const get_record_byhash_params& p );
```

然后 实现就很简单，构造`get_table_rows_params`和`abi_def`，然后调用`get_table_rows_bysec256`就可以了。

```cpp
read_only::get_table_rows_result read_only::get_record_byhash( const read_only::get_record_byhash_params& p1 ) {
   read_only::get_table_rows_params p;
   p.json = p1.json;
   p.code = N(record);
   p.scope = "record";
   p.table = N(hashrecord);
   p.table_key = p1.hashstr;
   p.key_type = chain_apis::i256;
   p.index_position = "two";
   p.reverse = false;
   p.show_payer = false;

   const abi_def abi = eosio::chain_apis::get_abi( db, p.code );
   return get_table_rows_bysec256(p, abi);
}
```

最后就是这个功能的调用部分

在`chain_api_plugin.cpp`上面添加

```cpp
CHAIN_RO_CALL(get_record_byhash, 200),
```

在`chain_plugin.cpp`上面添加

```cpp
FC_REFLECT( eosio::chain_apis::read_only::get_record_byhash_params, (hashstr)(json) );
```

然后重新编译代码启动一个同步节点，就可以通过同步节点进行查询了,我们可以用http post进行查询

```bash
curl -d '{"hashstr":"7af0be9a825ee84acf41efe13300b4eb0574fd67c1adc1da4080775554bb6eb1","json":1}' http://127.0.0.1:8001/v1/chain/get_record_byhash
{"rows":[{"id":1,"hash":"7af0be9a825ee84acf41efe13300b4eb0574fd67c1adc1da4080775554bb6eb1","current_block":82}],"more":false}
```

# 插件

很多时候我们需要实时监控链上的行为，这个时候就需要自己编写插件进行监控了，我们这里编写一个`http_post_plugin`的插件，一旦有用户在链上存储了一个hash，我们就给一个http地址post相关action的信息。

如果你曾经研究过mongo_db_plugin，那么对插件的开发就很容易上手了。首先看一下目录结构
```bash
    ./codex.io
   ├── plugins
    ├── http_post_plugin
    ├── CMakeLists.txt
    ├── http_post_plugin.cpp
    └── include
        └── eosio
            └── http_post_plugin
                └── http_post_plugin.hpp
```

`http_post_plugin.hpp`里面的内容和`mongo_db_plugin`的差不多，需要把类名和使用的功能类修改一下就可以了。

`http_post_plugin.cpp`是实现部分，我们需要在`set_program_options`定义这个插件需要的参数

```cpp
void http_post_plugin::set_program_options(options_description &cli, options_description &cfg) {
        cfg.add_options()
                ("httppost-queue-size", bpo::value<uint32_t>()->default_value(256),
                 "The target queue size between nodeos and kafka plugin thread.")
                ("httppost-block-start", bpo::value<uint32_t>()->default_value(256),
                 "If specified then only abi data pushed to kafka until specified block is reached.")
                ("http-post-accept", bpo::value<bool>()->default_value(false),
                "Enables http post on accepted transaction.")
                ("http-post-irreversible", bpo::value<bool>()->default_value(false),
                "Enables http post on irreversible transaction.")
                ("http-post-url", bpo::value<std::string>(),
                 "The url to post")
                 ;
    }
```

然后在`plugin_initialize`函数上解析这些参数并连接信号槽

```cpp
void http_post_plugin::plugin_initialize(const variables_map &options) {

        my->abi_serializer_max_time = app().get_plugin<chain_plugin>().get_abi_serializer_max_time();

        try {

            if( options.count( "http-post-accept" )) {
                my->http_accept_post = options.at( "http-post-accept" ).as<bool>();
            }
            if( options.count( "http-post-irreversible" )) {
                my->http_irrvisible_post = options.at( "http-post-irreversible" ).as<bool>();
            }
            if (options.count("http-post-url") != 0) {
                auto http_url_str = options.at("http-post-url").as<std::string>();
                my->http_url = fc::url(http_url_str);
                elog("http_url:${j}", ("j", http_url_str));
            }      

        if ( options.count("http-post-url")) {
            ilog("initializing http_post_plugin");
            my->configured = true;

                if( options.count( "http-post-queue-size" )) {
                    my->queue_size = options.at( "http-post-queue-size" ).as<uint32_t>();
                }
                if( options.count( "http-post-block-start" )) {
                    my->start_block_num = options.at( "http-post-queue-size" ).as<uint32_t>();
                }
                if( my->start_block_num == 0 ) {
                    my->start_block_reached = true;
                }

                my->chain_plug = app().find_plugin<chain_plugin>();
                EOS_ASSERT(my->chain_plug, chain::missing_chain_plugin_exception, "");
                auto &chain = my->chain_plug->chain();
                my->chain_id.emplace(chain.get_chain_id());

                my->irreversible_block_connection.emplace(
                        chain.irreversible_block.connect([&](const chain::block_state_ptr &bs) {
                            my->applied_irreversible_block(bs);
                        }));

                my->accepted_transaction_connection.emplace(
                        chain.accepted_transaction.connect([&](const chain::transaction_metadata_ptr &t) {
                            my->accepted_transaction(t);
                        }));

                my->init();
            } else {
                wlog( "eosio::http_post_plugin configured, but no --kafka-uri specified." );
                wlog( "http_post_plugin disabled." );
            }
        }FC_LOG_AND_RETHROW()        
    }
```

和`mongo_db_plugin`不同的是我们需要自己加载record合约的abi,这里我们写一个`insert_default_abi`的函数去加载record的abi

```cpp
void http_post_plugin_impl::insert_default_abi()
{
//record
    {
        auto abiPath = app().config_dir() / "record/record" += ".abi";
        FC_ASSERT( fc::exists( abiPath ), "no abi file found ");
        auto abijson = fc::json::from_file(abiPath).as<abi_def>();
        auto abi = fc::raw::pack(abijson);
        abi_def abi_def = fc::raw::unpack<chain::abi_def>( abi );
        purge_abi_cache(); // make room if necessary
        abi_cache entry;
        entry.account = N(record);
        entry.last_accessed = fc::time_point::now();
        abi_serializer abis;
        abis.set_abi( abi_def, abi_serializer_max_time );
        entry.serializer.emplace( std::move( abis ) );
        abi_cache_index.insert( entry );
    }
}
```

这个函数会在`consume_blocks`函数上进行调用，在循环之前就应该得到调用。

在`mongo_db_plugins`的架构上我们所有的业务代码都是在`_process_accepted_transaction`，`_process_applied_transaction`，`_process_accepted_block`，和`_process_irreversible_block`上面就行，我们这里只使用`_process_accepted_transaction`和`_process_irreversible_block`两个函数，一个是接收到transaction进行处理，一个是当块不可逆的时候进行处理。

```cpp
    void http_post_plugin_impl::_process_accepted_transaction(const chain::transaction_metadata_ptr &t) {
        const auto& pack_trx = t->packed_trx;
        const auto& raw = pack_trx.get_raw_transaction();
        const auto& trx = fc::raw::unpack<transaction>( raw );
        for (auto &act: trx.actions)
        {
            if(act.name == N(pushrecord)){
                auto act_data = to_variant_with_abi(act);
                if (string(http_url) != "" && http_accept_post) {
                    auto deadline =  fc::time_point::now() +  fc::time_point::maximum();
                    app().get_plugin<http_client_plugin>().get_client().post_sync(http_url,act_data, deadline);
                }
            }
        }
    }
    void http_post_plugin_impl::_process_irreversible_block(const chain::block_state_ptr& bs)
    {
         for( const auto& receipt : bs->block->transactions ) {
            string trx_id_str;
            if( receipt.trx.contains<packed_transaction>() ) {
                const auto& pt = receipt.trx.get<packed_transaction>();
                // get id via get_raw_transaction() as packed_transaction.id() mutates internal transaction state
                const auto& raw = pt.get_raw_transaction();
                const auto& trx = fc::raw::unpack<transaction>( raw );

                for (auto &act: trx.actions)
                {
                    if(act.name == N(pushrecord)){
                        auto act_data = to_variant_with_abi(act);
                        if (string(http_url) != "" && http_irrvisible_post) {
                            auto deadline =  fc::time_point::now() +  fc::time_point::maximum();
                            app().get_plugin<http_client_plugin>().get_client().post_sync(http_url,act_data, deadline);
                        }
                    }
                }
            }
         }
    }
```

代码编写好了以后在plugins的`CMakeLists.txt`上面添加

```cpp
add_subdirectory(http_post_plugin)
```

在`programs/nodes/CMakeLists.txt`上面添加

```bash
target_link_libraries( ${NODE_EXECUTABLE_NAME} PRIVATE -Wl,${whole_archive_flag} http_post_plugin -Wl,${no_whole_archive_flag} )
```

然后重新编译代码。

在`config.ini`上添加配置

```bash
plugin = eosio::http_post_plugin
http-post-accept = true
http-post-irreversible = true
http-post-url = http://127.0.0.1:25001
```

然后重启同步节点，在`127.0.0.1:25001` http服务其上就会收到

```bash
{"account":"record","name":"pushrecord","authorization":[{"actor":"record","permission":"active"}],"data":{"hash":"7af0be9a825ee84acf41efe13300b4eb0574fd67c1adc1da40807855547b6e70"},"hex_data":"7af0be9a825ee84acf41efe13300b4eb0574fd67c1adc1da40807855547b6e70"}
```

这样的信息

# 结束语

通过本教程我们了解了如何通过codex.io启动一条区块链，codex.io上面智能合约，rpc查询,插件的开发。基于codex.io我们可以很方便的启动一条区块链，为dapp的开发，以及实现特定功能的联盟链的开发提供了很好的便利。感谢阅读本教程，希望本教程能对您的区块链开发的学习有所帮助。