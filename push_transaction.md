一、发送交易     
其中的字段根据具体的信息填写，args对应的合约中需要的字段     
1.发送一笔转账交易，先把交易的数据转换成bin格式
```
  接口： curl http://127.0.0.1:8888/v1/chain/abi_json_to_bin -X POST -d  data
  例：send： curl http://127.0.0.1:8888/v1/chain/abi_json_to_bin -X POST -d '{"code":"currency", "action":"transfer", "args":{"from":"dan", "to":"inita", "quantity":"100 CUR","memo":"dan"}}'
     return：{"binargs":"000000000000a649000000000093dd74640000000000000000435552000000000c64616e20746f20696e697461","required_scope":[],"required_auth":[]}
```
2.发送一笔交易时候先获取当前节点的信息   
```
     send：  curl http://127.0.0.1:8888/v1/chain/get_info
     return：{"server_version":"d9ad8eec",
                    "head_block_num":209529,
                    "last_irreversible_block_num":209528,
                    "head_block_id":"000332792fe3591151505d26208cace66d1ffa8c2a7c6f288fe4cc2e0bf1afe1",
                    "head_block_time":"2018-04-16T08:33:30",
                    "head_block_producer":"eosio"}
```
3.根据块高度获得block信息  
```
  接口：curl http://127.0.0.1:8888/v1/chain/get_block -X POST -d  data
    send：curl  http://127.0.0.1:8888/v1/chain/get_block -X POST -d '{"block_num_or_id":210307}'
    return：{"previous":"000335820e3f9c8555e80f0e8eb6c7f16b2cb87d50b3c6676ea47c321aa2bdef",
                       "timestamp":"2018-04-16T08:39:59.000",
                       "transaction_mroot":"4141bcd63ffd0f532d810944718528890723178ab11aec181e6bfc0e31d98020",
                       "action_mroot":"de4dc92ef45492be1b23d2d77424443ef32c9c580e431ac94299cd25f38e96c2",
                       "block_mroot":"05f36c2f4e08f8de3646541c7bc017ef5f59c183974073a86cda7533fa8706f4",
                       "producer":"eosio",
                       "schedule_version":0,
                       "new_producers":null,
                       "producer_signature":"EOSJxATHsCrFbMu6vsKNAX4orMnhQfCETUjFi2dHyqE6CosPNzm5PdZXsi2cpDzLEgwtxvL9kkEuStJrrDFeMeynNTiJDdxXP",
                       "regions":[{"region":0,"cycles_summary":[[{"read_locks":[],"write_locks":[],"transactions":[{"status":"executed","kcpu_usage":2,"net_usage_words":38,"id":"14c3160b8e148a81bb76b6bc06dac75b0c08dcb662a44a2783e836c994d5d796"}]}]]}],
                       "input_transactions":[],
                       "id":"000335830b9596c0bed3c87ccb62ba3b64005722e5710c4f8b3f0754bd218ace",
                       "block_num":210307,
                       "ref_block_prefix":2093536190}
```
4.把第1步生成的binargs值，和第3步获取block_num、ref_block_prefix内容的通过push_transaction封装后发出，其中expiration设置的时间要比当前时间延后一段，data填写的内容对应的就是binargs
 ```
  接口：curl http://localhost:8888/v1/chain/push_transaction -X POST -d  data
    send： curl http://localhost:8888/v1/chain/push_transaction -X POST -d
                     '{"transaction":{"ref_block_num":"1260","ref_block_prefix":"4068433785","expiration":"2018-04-17T03:50:49","scope":["currency","tester"],
                                              "actions":[{"code":"currency",
                                                                "recipients":["currency","tester"],
                                                                "account":"currency",
                                                                "name":"transfer",
                                                                "authorization":[{"actor":"currency","permission":"active"}],
                                                                "data":"0000001e4d75af46000000005c95b1ca6400000000000000004355520000000006746573746572"}]},
                   "signatures":[],"authorizations":[],"compression":0}'
```
