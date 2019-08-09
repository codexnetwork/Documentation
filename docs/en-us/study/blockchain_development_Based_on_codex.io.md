# Getting Started

In this tutorial, You will build a blockchain based on codex.io，In the process of learning, you will learn how to modify and get a blockchain of your own, the preparation of the contract, the writing of the rpc query interface, the writing of the plugin.This example will show how to **build your own blockchain** quickly and easily based on codex.io

At the end of this tutorial, you will get a blockchain that can store a bunch of hash data. Users can store hash data on the blockchain and support queries, http push, etc. The hash data stored in the block cannot be Modified and deleted.

# Requirements

+ Need you to have experience in developing dapp on eosforce or eos, have an understanding of the eosforce chain or eos chain
+ Desire to create your own blockchain!

# programming

Program goal: Construct a blockchain that can store hashes.

First we need to start a blockchain of our own, which can be started quickly based on the codex.io framework. The part that stores the hash should be placed at the contract level, we need to write a contract to store the hash. The part of the query hash is in the rpc interface layer. We need to add the corresponding query function to the chan_plugin. The stored push part needs to be written separately.

# start a blockchain

Based on the codex.io framework we can start a blockchain quickly。Github address of codex.io：https://github.com/codexnetwork/codex.io 。First fork the code to your own project, download it to the local, and then modify this chain into your own chain.

Codex.io is a blockchain framework based on eosforce modification，it can build multi-asset public Proof-of-Stake (PoS) blockchains。Since codex.io is based on eosforce, it can interact well with eosforce, eosio and all forked chains based on eos. The relay chain codex developed by the Force team can also communicate seamlessly. Token transactions through relay chains and other EOS chains. Support and adaptation of wasm, developers can easily develop and deploy DAPP on this chain.

After getting this framework, we need to put this code into our own project directory, and then modify it into a blockchain of our own based on the framework. We need to modify some names and attributes to identify our blockchain, so we need to change the chain name, the name of the system account, the number of block nodes, the time spent producing a block, and a block produced once by BP. Number, economic model, prefix of public key, name of core token, etc.

+ Chain name: The identifier of a chain is chain_id. The chain_id of codex.io takes the hash of the genesis file, so in general, there will be no same chain_id of the two chains. This does not need to be manually modified.
+ The name of the system account: ROOT_ACCOUNT="hash" above eosio_build.sh, here we modify it to hash. Note: Do not exceed 6 characters, as there will be a system account with .token to manage tokens, all account names cannot exceed 12 characters.
+ The number of outbound nodes: MAX_PRODUCERS=21 on eosio_build.sh, here we change to 21. If it is a test chain, it can be changed very small, but not less than 4, with reference to eosio modified to 21.
+ The time taken to produce a block: on the eosio_build.sh BLOCK_INTERVAL_MS=1000 This is modified to 1S. If it is too fast, the system performance requirements are relatively high. If the system performance is not good, it is recommended to modify it to 3S, but it is best not to exceed 3S.
+ The number of blocks produced by a BP once: PRODUCER_REPETITIONS=3 on eosio_build.sh refers to eos. A BP takes 3S each time, that is, if it is 1S1 blocks, one BP will output 3 blocks at a time, if it is 3S1 One block, one BP, one block at a time
+ Economic model: above eosio_build.sh RESOURCE_MODEL=0 codex.io provides 3 economic models to choose from: 0 means no limit, that is, CPU, NET, RAM, all users can use unlimited. The 1st generation fee mode, that is, each operation must pay a certain system token as a handling fee. 2 represents the mortgage mode, that is, you need to mortgage the CPU and NET before you can use it. You don't need to consume tokens. Here we choose no limit mode
+ The prefix of the public key: set(PUB_KEY_LEGACY_PREFIX "HASH") above CMakeList.txt Here we use HASH as the prefix of the public key.

After the above information is modified, the code can be recompiled and it can be chained. Codex.io provides a dedicated script for chaining: bios_boot_eosforce.py. The script is under the tutorials/bios-script. Use the command

```bash
./bios_boot_eosforce.py  -c && ./bios_boot_eosforce.py  -a
```

to start 21 creation nodes to start the chain.

+ ./bios_boot_eosforce.py  -c Is to shut down all node processes on the system and clean up intermediate files.
+ ./bios_boot_eosforce.py  -a The command is to generate all the intermediate files, start all the creation BP, generate wallets and so on.

After the completion of these work, a blockchain will be successfully launched. Get information about the chain through the get info of cleos

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

# Contract

The codex.io contract is the same as the eos contract. It is deployed as a pre-compiled WASM in the blockchain. WASM uses LLVM and clang to compile from C/C++. That is, people who are engaged in codex.io contract development need to have C/ C++ development knowledge.

The codex.io contract is under the contract directory, we build a record contract, the directory structure is as follows：

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

First we need to create a structure to store the hash

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

The type of hash is checksum256. Since multi_index uses key256 type as the secondary primary key, you need to have a function from checksum256 to key256.

```cpp
 static key256 get_key_id(const checksum256 &hash_data) {
    const uint64_t *p64 = reinterpret_cast<const uint64_t *>(&hash_data);
    return key256::make_from_word_sequence<uint64_t>(p64[0], p64[1], p64[2], p64[3]); 
 }
```

Finally, you need to have a function to fetch external calls, save the hash

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

Here by the secondary index byhash to determine whether it is a duplicate hash

After the contract is written, create a user called record and set the contract for the record. After setting it, you can use the cleos to call the function to store the hash.

```bash
cleos push action record pushrecord '["7af0be9a825ee84acf41efe13300b4eb0574fd67c1adc1da4080775554bb6eb0"]' -p record
```

Then look at the contents of the form

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

# RPC query interface

Codex.io has provided a more complete query interface to query the contents of the memory table, such as cleos get table record record hashrecord is called v1/chain/get_table_rows interface. However, according to the 256-bit secondary index query data is not provided, you need to write it yourself. Here is how to add an implementation of querying data through a 256-bit secondary index.

The implementation of all rpc calls is above the chain_plugin plugin, above the class read_only. If you look closely at the get_table_rows method, you'll find that it calls a lot of different functions based on the key. Let's write a generic function that queries the data through a 256-bit secondary index. code show as below

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

Then we need to query the hashrecord table of the record contract. We can write an interface get_record_byhash for this. First define this function and the input parameters of this function.：

```cpp
   struct get_record_byhash_params {
      string   hashstr;
      bool     json;
   };
   get_table_rows_result get_record_byhash( const get_record_byhash_params& p );
```

Then the implementation is very simple, construct get_table_rows_params and abi_def, then call get_table_rows_bysec256.

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

Finally, the calling part of this function

Add above chain_api_plugin.cpp

```cpp
CHAIN_RO_CALL(get_record_byhash, 200),
```

Add above chain_plugin.cpp

```cpp
FC_REFLECT( eosio::chain_apis::read_only::get_record_byhash_params, (hashstr)(json) );
```

Then recompile the code to start a synchronization node, you can query through the synchronization node, we can use http post for query

```bash
curl -d '{"hashstr":"7af0be9a825ee84acf41efe13300b4eb0574fd67c1adc1da4080775554bb6eb1","json":1}' http://127.0.0.1:8001/v1/chain/get_record_byhash
{"rows":[{"id":1,"hash":"7af0be9a825ee84acf41efe13300b4eb0574fd67c1adc1da4080775554bb6eb1","current_block":82}],"more":false}
```

# Plugin

Many times we need to monitor the behavior in the chain in real time. At this time, we need to write the plugin for monitoring. We write a http_post_plugin plugin here. Once a user stores a hash on the chain, we will give an http address post related action. Information.

If you have studied mongo_db_plugin, then the development of the plugin is very easy to get started. First look at the directory structure

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

The contents of http_post_plugin.hpp are similar to those of mongo_db_plugin. You need to modify the class name and the function class used.

Http_post_plugin.cpp is the implementation part, we need to define the parameters required by this plugin in set_program_options

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

Then parse these parameters on the plugin_initialize function and connect the signal slots

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

The difference with mongo_db_plugin is that we need to load the abi of the record contract. Here we write a function of insert_default_abi to load the record abi.

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

This function will be called on the consume_blocks function and should be called before the loop.

On the architecture of mongo_db_plugins, all our business code is on _process_accepted_transaction, _process_applied_transaction, _process_accepted_block, and _process_irreversible_block. We only use _process_accepted_transaction and _process_irreversible_block two functions, one is to receive the transaction for processing, and the other is to be The block is processed when it is irreversible.

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

After the code is written, add it to the CMakeLists.txt of the plugins.

```bash
add_subdirectory(http_post_plugin)
```

Add above programs/nodes/CMakeLists.txt

```bash
target_link_libraries( ${NODE_EXECUTABLE_NAME} PRIVATE -Wl,${whole_archive_flag} http_post_plugin -Wl,${no_whole_archive_flag} )
```

Then recompile the code.

Add configuration on config.ini

```bash
plugin = eosio::http_post_plugin
http-post-accept = true
http-post-irreversible = true
http-post-url = http://127.0.0.1:25001
```

Then restart the sync node and receive it on the 127.0.0.1:25001 http service.

```bash
{"account":"record","name":"pushrecord","authorization":[{"actor":"record","permission":"active"}],"data":{"hash":"7af0be9a825ee84acf41efe13300b4eb0574fd67c1adc1da40807855547b6e70"},"hex_data":"7af0be9a825ee84acf41efe13300b4eb0574fd67c1adc1da40807855547b6e70"}
```

# final

Through this tutorial we learned how to start a blockchain through codex.io, smart contracts on codex.io, rpc queries, plugin development. Based on codex.io, we can easily start a blockchain, which provides great convenience for the development of dapp and the development of a chain of links for specific functions.Thanks for reading this tutorial, I hope this tutorial will help you learn about blockchain development.