# Bryllite.NET JSON-RPC
  
[JSON](https://json.org) is a lightweight data-interchange format. It can represent numbers, strings, ordered sequences of values, and collections of name/value pairs.

[JSON-RPC](https://jsonrpc.org/specification) is a stateless, light-weight remote procedure call (RPC) protocol. Primarily this specification defines several data structures and the rules around their processing. It is transport agnostic in that the concepts can be used within the same process, over sockets, over HTTP, or in many various message passing environments. It uses JSON (RFC 4627) as data format.

## JSON-RPC Endpoint
Default JSON-RPC endpoints:

| Service | URL 
|------|-------
| Web4b | http://localhost:8451 

## Hex value encoding
At present there are two key datatypes that are passed over JSON: unformatted byte arrays and quantities. Both are passed with a hex encoding, however with different requirements to formatting:
  
When encoding QUANTITIES (integers, numbers): encode as hex, prefix with "0x", the most compact representation (slight exception: zero should be represented as "0x00").  
Examples:

* `0x41` (`65` in decimal)
* `0x0400` (`1024` in decimal)
* WRONG: `0x` (should always have at least two digit - zero is `0x00`)
* WRONG: `0x400` (hex string shoud be even digit - valid is `0x0400`)
* WRONG: `ff` (must be prefixed `0x`)
  
## The Default block parameter
The following methods have an extra default block parameter:  

* [be4_getBalance](#be4_getbalance)
* [be4_getTransactionCount](#be4_gettransactioncount)
* [be4_getBlockTransactionCountByNumber](#be4_getblocktransactioncountbynumber)
* [be4_getBlockByNumber](#be4_getblockbynumber)
* [be4_getTransactionByBlockNumberAndIndex](#be4_gettransactionbyblocknumberandindex)
  
When requests are made that act on the state of bryllite, the last default block parameter determines the height of the block.
  
The following options are possible for the defaultBlock parameter:

* `HEX string` - an integer block number
* `String "earliest"` - for the earliest/genesis block
* `String "latest"` - for the latest mined block
* `String "pending"` - for the pending state/transactions
  
## Curl Examples Explained
The curl options below might return a response where the node complains about the content type, this is because the --data option sets the content type to application/x-www-form-urlencoded . If your node does complain, manually set the header by placing -H "Content-Type: application/json" at the start of the call.
  
The examples also do not include the URL/IP & port combination which must be the last argument given to curl e.x. 127.0.0.1:9627

## JSON-RPC Methods

### WEB4B API

* [web4b_getVersion](#web4b_getversion)
* [web4b_getTime](#web4b_gettime)
* [web4b_sha3](#web4b_sha3)
* [web4b_mining](#web4b_mining)
* [web4b_getWork](#web4b_getwork)
* [web4b_submitWork](#web4b_submitwork)

### NET API

* [net_listening](#net_listening)
* [net_getAuthoprizedPeersRootHash](#net_getauthorizedpeersroothash)
* [net_getAuthorizedPeersCount](#net_getauthorizedpeerscount)
* [net_getAuthorizedPeers](#net_getauthorizedpeers)

### ADMIN API

* [admin_addPeer](#admin_addpeer)
* [admin_removePeer](#admin_removepeer)
* [admin_startMiner](#admin_startminer)
* [admin_stopMiner](#admin_stopminer)


### BE4 API

* [be4_coinbase](#be4_coinbase)
* [be4_blockNumber](#be4_blocknumber)
* [be4_getBalance](#be4_getbalance)
* [be4_getTransactionCount](#be4_gettransactioncount)
* [be4_getBlockTransactionCountByHash](#be4_getblocktransactioncountbyhash)
* [be4_getBlockTransactionCountByNumber](#be4_getblocktransactioncountbynumber)
* [be4_sendRawTransaction](#be4_sendrawtransaction)
* [be4_getBlockByHash](#be4_getblockbyhash)
* [be4_getBlockByNumber](#be4_getblockbynumber)
* [be4_getTransactionByHash](#be4_gettransactionbyhash)
* [be4_getTransactionByBlockHashAndIndex](#be4_gettransactionbyblockhashandindex)
* [be4_getTransactionByBlockNumberAndIndex](#be4_gettransactionbyblocknumberandindex)
* [be4_getTransactionReceipt](#be4_gettransactionreceipt)
* [be4_pendingTransactions](#be4_pendingtransactions)
* [be4_getTransactionsByAddress](#be4_gettransactionsbyaddress)

## JSON RPC API References

### web4b_getVersion
Returns the current node version.

**Parameters**
None

**Returns**
`String` - The current version

**Example**
~~~js
// request
curl -X POST --data '{"jsonrpc": "2.0", "method": "web4b_getVersion", "params": [], "id": 0}'

// response
{
  "jsonrpc": "2.0",
  "id": 0,
  "result": [
    "0.8.2-alpha"
  ]
}
~~~
***

### web4b_getTime
Returns the current node time. See [Unix Epoch Time](https://www.epochconverter.com/clock)

**Parameters**
None

**Returns**
`QUANTITY` - Hex string of unix epoch time in milliseconds.

**Example**
~~~js
// request
curl -X POST --data '{"jsonrpc": "2.0", "method": "web4b_getTime", "params": [], "id": 32}'

// response
{
  "jsonrpc": "2.0",
  "id": 32,
  "result": [
    "0x016f6fe9fdc8"    // 1,578,130,603,464 (2020년 January 4일 Saturday AM 9:36:43.464)
  ]
}
~~~
***

### web4b_sha3
Returns `Keccak-256` (*not* the standardized `SHA3-256`) of the given data.

**Parameters**
1. `DATA` - Hex string of the data to convert into a SHA3 hash.

**Example Parameters**
~~~js
"params":[
    "0x68656c6c6f20776f726c64"
]
~~~

**Returns**
`DATA` - Hex string of the SHA3 result of the given data.

**Example**
~~~js
// request
curl -X POST --data '{"jsonrpc": "2.0", "method": "web4b_sha3", "params": ["0x68656c6c6f20776f726c64"], "id": 0}'

// response
{
  "jsonrpc": "2.0",
  "id": 0,
  "result": [
    "0x47173285a8d7341e5e972fc677286384f802f8ef42a5ec5f03bbfa254cb01fad"
  ]
}
~~~
***

### web4b_mining
Returns `true` if node is actively mining new blocks.

**Parameters**
None

**Retruns**
`Boolean` - returns `true` of the node is mining, otherwise `false`.

**Example**
~~~js
// request
curl -X POST --data '{"jsonrpc": "2.0", "method": "web4b_mining", "params": [], "id": 0}'

// response
{
  "jsonrpc": "2.0",
  "id": 0,
  "result": [
    true
  ]
}
~~~
***

### web4b_getWork
Returns the hash of the current block, the seedHash, and the boundary condition to be met ("target").

**Parameters**
None

**Retruns**
`Array` - Array with the following properties:

1. `DATA`, 32 Bytes - current block header pow-hash
1. `DATA`, 32 Bytes - the seed hash used for the DAG.
1. `DATA`, 32 Bytes - the boundary condition ("target"), 2^256 / difficulty.

**Example**
~~~js
// request
curl -X POST --data '{"jsonrpc": "2.0", "method": "web4b_getWork", "params": [], "id": 0}'

// response
{
  "jsonrpc": "2.0",
  "id": 0,
  "result": [
    "0x1234567890abcdef1234567890abcdef1234567890abcdef1234567890abcdef",
    "0x5EED00000000000000000000000000005EED0000000000000000000000000000",
    "0xd1ff1c01710000000000000000000000d1ff1c01710000000000000000000000"
  ]
}
~~~
***

### web4b_submitWork
Used for submitting a proof-of-activity solution.

**Parameters**
1. `DATA`, 8 Bytes - The nonce found (64 bits)
1. `DATA`, 32 Bytes - The header's poa-hash (256 bits)
1. `DATA`, 32 Bytes - The mix digest (256 bits)

**Retruns**
`Boolean` - returns true if the provided solution is valid, otherwise false.

**Example**
~~~js
// request
curl -X POST --data '{"jsonrpc": "2.0", "method": "web4b_getWork", "params": [
  "0x0000000000000001", 
  "0x1234567890abcdef1234567890abcdef1234567890abcdef1234567890abcdef",
  "0xD1GE5700000000000000000000000000D1GE5700000000000000000000000000"
  ], "id": 0}'

// response
{
  "jsonrpc": "2.0",
  "id": 0,
  "result": [
    true
  ]
}
~~~
***


### net_listening
Returns `true` if node is listening on `BCP` port.

**Parameters**
None

**Retruns**
`Boolean` - returns `true` of the node is listening on `BCP` port, otherwise `false`.

**Example**
~~~js
// request
curl -X POST --data '{"jsonrpc": "2.0", "method": "net_listening", "params": [], "id": 0}'

// response
{
  "jsonrpc": "2.0",
  "id": 0,
  "result": [
    true
  ]
}
~~~
***

### net_getAuthorizedPeersRootHash
Returns authorized peers root hash.

**Parameters**
None

**Retruns**
`DATA` - Hex string of authorized peers root hash

**Example**
~~~js
// request
curl -X POST --data '{"jsonrpc": "2.0", "method": "net_getAuthorizedPeersRootHash", "params": [], "id": 0}'

// response
{
  "jsonrpc": "2.0",
  "id": 32,
  "result": [
    "0xe235933bcacd7f464c4c4a9a7875dbaefc6d36a233eef318048c8ecb6adecb99"
  ]
}
~~~
***

### net_getAuthorizedPeersCount
Returns authorized peers count.

**Parameters**
None.

**Retruns**
`DATA` - Hex string of authorized peers count.

**Example**
~~~js
// request
curl -X POST --data '{"jsonrpc": "2.0", "method": "net_getAuthorizedPeersCount", "params": [], "id": 0}'

// response
{
  "jsonrpc": "2.0",
  "id": 0,
  "result": [
    "0x03"
  ]
}
~~~
***


### net_getAuthorizedPeers
Returns authorized peers.

**Parameters**
1. `DATA` - Hex string of authorized peers root hash.

**Retruns**
`Array` - Array of authorized peers enode.

**Example**
~~~js
// request
curl -X POST --data '{"jsonrpc": "2.0", "method": "net_getAuthorizedPeers", "params": ["0xe235933bcacd7f464c4c4a9a7875dbaefc6d36a233eef318048c8ecb6adecb99"], "id": 0}'

// response
{
  "jsonrpc": "2.0",
  "id": 0,
  "result": [
    "enode://0x02cae79e5203828cd869aebcf79b6257c687accf06540012a19ddc9549b385ce9e@eu-central.static.bryllite.com:8451/?rpcport=9627&q=static",
    "enode://0x034932efd3d424042945ad10c228d4eb903f9a266049aec69f91598c54e8f047a8@asia-east.static.bryllite.com:8451/?rpcport=9627&q=static",
    "enode://0x0363cfe2d20f2e9faadf0c23beb5b0b1f410f8e8a35293e0d72e6f437a086e7d45@us-east.static.bryllite.com:8451/?rpcport=9627&q=static"
  ]
}
~~~
***

### admin_addPeer
Append peer's enode in authorized peers.

**Parameters**
1. `String` - enode of peer to append.
1. `DATA` - Hex string of timestamp in milliseconds.
1. `DATA` - Hex string of signature signed by node key.

**Example Parameters**
~~~js
params: [
  'enode://0x0363cfe2d20f2e9faadf0c23beb5b0b1f410f8e8a35293e0d72e6f437a086e7d45@us-east.static.bryllite.net:8452/',
    '0x0181e78d9788',
    '0xdad7b6baf280ef8bf98e9a33fc19230820cd0ea4459210ddb8e821eed1df9d62'
]
~~~

**Retruns**
1. `Boolean` - Result of request.
1. `String` - Hex string of authorized peers root hash or error message if request failed.

**Example**
~~~js
// request
curl -X POST --data '{"jsonrpc": "2.0", "method": "admin_addPeer", "params":[{see above}], "id": 0}'

// response
{
    "jsonrpc": "2.0",
    "id": 0,
    "result": [
        true, 
        "0xe235933bcacd7f464c4c4a9a7875dbaefc6d36a233eef318048c8ecb6adecb99"
    ]
}
~~~
***

### admin_removePeer
Remove peer's enode in authorized peers.

**Parameters**
1. `String` - enode of peer to remove.
1. `DATA` - Hex string of timestamp in milliseconds.
1. `DATA` - Hex string of signature signed by node key.

**Example Parameters**
~~~js
params: [
  'enode://0x0363cfe2d20f2e9faadf0c23beb5b0b1f410f8e8a35293e0d72e6f437a086e7d45@us-east.static.bryllite.net:8452/',
    '0x0181e78d9788',
    '0xdad7b6baf280ef8bf98e9a33fc19230820cd0ea4459210ddb8e821eed1df9d62'
]
~~~

**Retruns**
1. `Boolean` - Result of request.
1. `String` - Hex string of authorized peers root hash or error message if request failed.

**Example**
~~~js
// request
curl -X POST --data '{"jsonrpc": "2.0", "method": "admin_removePeer", "params":[{see above}], "id": 0}'

// response
{
    "jsonrpc": "2.0",
    "id": 0,
    "result": [
        true, "0xe235933bcacd7f464c4c4a9a7875dbaefc6d36a233eef318048c8ecb6adecb99"
    ]
}
~~~
***

### be4_coinbase
Returns the node coinbase address.

**Parameters**
none

**Returns**
`DATA` - the current coinbase address.

**Example**
~~~js
// Request
curl -X POST --data '{"jsonrpc": "2.0", "method": "be4_coinbase", "params": [], "id": 64}'

// Result
{
  "jsonrpc": "2.0",
  "id": 64,
  "result": [
    "0x2b8c9ac4d8783e0c16c950e1a6c0b4f73eb7f294"
  ]
}
~~~
***

### be4_blockNumber
Returns the number of most recent block.

**Parameters**
none

**Returns**
`QUANTITY` - integer of the current block number the node is on.

**Example**
~~~js
// Request
curl -X POST --data '{"jsonrpc": "2.0", "method": "be4_blockNumber", "params": [], "id": 1}'

// Result
{
  "jsonrpc": "2.0",
  "id": 1,
  "result": [
    "0x64"  // 100
  ]
}
~~~
***

### be4_getBalance
Returns the balance of the account of given address.

**Parameters**
1. `DATA` - address to check for balance.
1. `QUANTITY|TAG` - integer block number, or the string `"latest"`, `"earliest"` or `"pending"`, see the [default block parameter](#the-default-block-parameter)

**Example Parameters**
~~~js
params:[
    "0x2b8c9ac4d8783e0c16c950e1a6c0b4f73eb7f294", "latest"
]
~~~

**Returns**
`QUANTITY` - integer of the current balance in beryl.

**Example**
~~~js
// Request
curl -X POST --data '{"jsonrpc": "2.0", "method": "be4_getBalance", "params": ["0x2b8c9ac4d8783e0c16c950e1a6c0b4f73eb7f294", "latest"], "id": 32}'

// Result
{
  "jsonrpc": "2.0",
  "id": 32,
  "result": [
    "0xe662cbc700"  // 989500000000 (98.95 BRC)
  ]
}
~~~
***

### be4_getTransactionCount
Returns the number of transactions *sent* from an address. a.k.a `nonce`

**Parameters**
1. `DATA` - address to check for balance.
1. `QUANTITY|TAG` - integer block number, or the string `"latest"`, `"earliest"` or `"pending"`, see the [default block parameter](#the-default-block-parameter)

**Example Parameters**
~~~js
params:[
    "0x2b8c9ac4d8783e0c16c950e1a6c0b4f73eb7f294", "pending"
]
~~~

**Returns**
`QUANTITY` - integer of the number of transactions send from given address.

**Example**
~~~js
// Request
curl -X POST --data '{"jsonrpc": "2.0", "method": "be4_getTransactionCount", "params": ["0x2b8c9ac4d8783e0c16c950e1a6c0b4f73eb7f294", "pending"], "id": 32}'

// Result
{
  "jsonrpc": "2.0",
  "id": 32,
  "result": [
    "0x05"  // 5
  ]
}
~~~
***

### be4_getBlockTransactionCountByHash
Returns the number of transactions in a block matching the given block hash.

**Parameters**
`DATA` - hash of a block.

**Example Parameters**
~~~js
params:[
    "0x89c11a7d8e927431d453f46c0ad1cfbbd50e39aac450d907d1b8768667d01a0c"
]
~~~

**Returns**
`QUANTITY` - integer of the number of transactions in this block.

**Example**
~~~js
// Request
curl -X POST --data '{"jsonrpc": "2.0", "method": "be4_getBlockTransactionCountByHash", "params": ["0x89c11a7d8e927431d453f46c0ad1cfbbd50e39aac450d907d1b8768667d01a0c"], "id": 32}'

// Result
{
  "jsonrpc": "2.0",
  "id": 32,
  "result": [
    "0x01"
  ]
}
~~~
***

### be4_getBlockTransactionCountByNumber
Returns the number of transactions in a block matching the given block number.

**Parameters**
`QUANTITY|TAG` - integer of a block number, or the string `"earliest"`, `"latest"` or `"pending"`, as in the [default block parameter](#the-default-block-parameter)

**Example Parameters**
~~~js
params:[
    "0x63"  // 99
]
~~~

**Returns**
`QUANTITY` - integer of the number of transactions in this block.

**Example**
~~~js
// Request
curl -X POST --data '{"jsonrpc": "2.0", "method": "be4_getBlockTransactionCountByNumber", "params": ["0x63"], "id": 32}'

// Result
{
  "jsonrpc": "2.0",
  "id": 32,
  "result": [
    "0x01"
  ]
}
~~~
***

### be4_sendRawTransaction
Creates new message call transaction or a contract creation for signed transaction.

**Parameters**
`DATA` - The signed transaction data.
See [web4b.cs](#web4b-cs) for transaction data inside.

**Example Parameters**
~~~js
params:[
    "0xf8818400000000840000000080845daae45094e9f5e6d433316e4abfeff8c40ac405b735129501880000000005f5e10084000000008800000000000000008080b841732f528f57e1b0aea54d9bd6a9b250770a2389eeaa202675d9f5845c005bf65d80d37c68d7f04bfd8d9aea8d45b5ee3b504c433dcfc7dd75fe0b11d56867fa3301"
]
~~~

**Returns**
`DATA` - the transaction hash, or json-rpc error object if tx not executable.

Use [be4_getTransactionReceipt](#be4_gettransactionreceipt) to get result of transaction.

**Example**
~~~js
// Request
curl -X POST --data '{"jsonrpc": "2.0", "method": "be4_sendRawTransaction", "params": [{see above}], "id": 32}'

// Result
{
  "jsonrpc": "2.0",
  "id": 32,
  "result": [
    "0x3fc20bc4542b0697990b0558fcda7a1d2aa10e5cacd6154b6dcf7377abd0ba64"
  ]
}
~~~
***

### be4_getBlockByHash
Returns information about a block by hash.

**Parameters**
1. `DATA` - Hash of a block.
1. `NUMBER` - If `0x00(default)` it returns the transaction object(json) with tx hashes, 
if `0x01` it returns the transaction object(json) with tx object,
if `0x02` it returns the raw block data ( block rlp ).

**Example Parameters**
~~~js
params:[
    "0x89c11a7d8e927431d453f46c0ad1cfbbd50e39aac450d907d1b8768667d01a0c", "0x00"
]
~~~

**Returns**
`Object` - A block object or block rlp hex string, or `null` when no block was found:
* `hash`: `DATA` - hash of the block.
* `number`: `QUANTITY` - the block number.
* `chainId`: `DATA` - network id.
* `version`: `DATA` - version.
* `timestamp`: `QUANTITY` - the unix timestamp for when the block was collated.
* `coinbase`: `DATA` - the address of the beneficiary to whom the mining rewards were given.
* `witness`: `DATA` - the address of witness providing share by proof-of-attendance.
* `parentHash`: `DATA` - hash of the parent block.
* `transactionRoot`: `DATA` - the root of the transaction trie of the block.
* `stateRoot`: `DATA` - the root of the final state trie of the block.
* `nonce`: `DATA` - nonce.
* `data`: `DATA` - data.
* `extra`: `DATA` - the "extra data" field of this block.
* `transactions`: `Array` - Array of transaction objects, or array of txid depending on the given parameter.
* `size`: `QUANTITY` - integer the size of this block in bytes.
* `gasUsed`: `QUANTITY` - the total used gas by all transactions in this block.

**Example**
~~~js
// Request
curl -X POST --data '{"jsonrpc": "2.0", "method": "be4_getBlockByHash", "params": ["0xfc205eea221ce9ac3e616f97b048e2a39838b6475fd886922a743b0dbe6ec5a7", "0x00"], "id": 32}'

// Result if 2nd parameter is '0x00'
{
  "jsonrpc": "2.0",
  "id": 32,
  "result": [
    {
      "hash": "0xfc205eea221ce9ac3e616f97b048e2a39838b6475fd886922a743b0dbe6ec5a7",
      "number": "0x1e",
      "chain": "0x00",
      "version": "0x00",
      "timestamp": "0x5daefc26",
      "coinbase": "0x2b8c9ac4d8783e0c16c950e1a6c0b4f73eb7f294",
      "witness": "0x2b8c9ac4d8783e0c16c950e1a6c0b4f73eb7f294",
      "parentHash": "0x2a0b4c347f0d613d0523d710370bd65d8f0a7ac118381dacaef30960a14b8311",
      "transactionRoot": "0x66f87afba13a0eb2629c00cf536c6b4766d5d9eeb6b8e09f4b7e3f645716b0e5",
      "stateRoot": "0xc4b1f8c9c895cce78b4681e42616f5243919bcecf0005e8aec5c68e1119b48bb",
      "nonce": "0x1feb51e443cecf8f",
      "data": "",
      "extra": "",
      "transactions": [
        "0xced8923848413a02869d715943d37b6c085d3b30eb536b5ae7d7db25edad28c3"
      ],
      "size": "0x0165",
      "gasUsed": "0x00"
    }
  ]
}

// Result if 2nd parameter is '0x02'
{
  "jsonrpc": "2.0",
  "id": 32,
  "result": [
    "0xf90162b8b3f8b18400000000840000000080845daefc2688000000000000001e942b8c9ac4d8783e0c16c950e1a6c0b4f73eb7f294942b8c9ac4d8783e0c16c950e1a6c0b4f73eb7f294881feb51e443cecf8fa02a0b4c347f0d613d0523d710370bd65d8f0a7ac118381dacaef30960a14b8311a066f87afba13a0eb2629c00cf536c6b4766d5d9eeb6b8e09f4b7e3f645716b0e5a0c4b1f8c9c895cce78b4681e42616f5243919bcecf0005e8aec5c68e1119b48bb8080b8abf8a9b8a7f8a58400000000840000000080845daefbfc9466012c3c52b40b604081f240b099ad73a8e9abcd880000000005f5e10088000000000000000088000000000000000180a09245cb44760636e824b8835f2295af17f6313572663d62b01cb29e351bb5ea17b84101878ae393e9da43b50ea4cb130159007bb6495af2566fa8f9b58cfa19d66d98be55890390341ce04b8520a31496e338a0bba0a773ea6ae1b0661af612c8c60c01"
  ]
}
~~~
***

### be4_getBlockByNumber
Returns information about a block by number.

**Parameters**
1. `QUANTITY|TAG` - Integer of a block number, or the string `"earliest"`, `"latest"`, or `"pending"`, as in the [default block parameter](#the-default-block-parameter)
1. `NUMBER` - If `0x00(default)` it returns the transaction object(json) with tx hashes, 
if `0x01` it returns the transaction object(json) with tx object,
if `0x02` it returns the raw block data ( block rlp ).

**Example Parameters**
~~~js
params:[
    "0x1e", // 30
    "0x01"
]
~~~

**Returns**
See [be4_getBlockByHash](#be4_getblockbyhash)

**Example**
~~~js
// Request
curl -X POST --data '{"jsonrpc": "2.0", "method": "be4_getBlockByNumber", "params": ["0x1e", "0x01"], "id": 32}'

// Result ( 2nd parameter is '0x01' )
{
  "jsonrpc": "2.0",
  "id": 32,
  "result": [
    {
      "hash": "0xfc205eea221ce9ac3e616f97b048e2a39838b6475fd886922a743b0dbe6ec5a7",
      "number": "0x1e",
      "chain": "0x00",
      "version": "0x00",
      "timestamp": "0x5daefc26",
      "coinbase": "0x2b8c9ac4d8783e0c16c950e1a6c0b4f73eb7f294",
      "witness": "0x2b8c9ac4d8783e0c16c950e1a6c0b4f73eb7f294",
      "parentHash": "0x2a0b4c347f0d613d0523d710370bd65d8f0a7ac118381dacaef30960a14b8311",
      "transactionRoot": "0x66f87afba13a0eb2629c00cf536c6b4766d5d9eeb6b8e09f4b7e3f645716b0e5",
      "stateRoot": "0xc4b1f8c9c895cce78b4681e42616f5243919bcecf0005e8aec5c68e1119b48bb",
      "nonce": "0x1feb51e443cecf8f",
      "data": "",
      "extra": "",
      "transactions": [
        {
          "hash": "0xced8923848413a02869d715943d37b6c085d3b30eb536b5ae7d7db25edad28c3",
          "chain": "0x00",
          "version": "0x00",
          "timestamp": "0x5daefbfc",
          "from": "0x2b8c9ac4d8783e0c16c950e1a6c0b4f73eb7f294",
          "to": "0x66012c3c52b40b604081f240b099ad73a8e9abcd",
          "value": "0x05f5e100",
          "gas": "0x00",
          "nonce": "0x01",
          "input": "",
          "extra": "0x9245cb44760636e824b8835f2295af17f6313572663d62b01cb29e351bb5ea17",
          "v": "0x01",
          "r": "0x986dd619fa8cb5f9a86f56f25a49b67b00590113cba40eb543dae993e38a8701",
          "s": "0x0cc6c812f61a66b0e16aea73a7a0bba038e39614a320854be01c3490038955be",
          "size": "0xa7"
        }
      ],
      "size": "0x0165",
      "gasUsed": "0x00"
    }
  ]
}
~~~
***

### be4_getTransactionByHash
Returns the information about a transaction by transaction hash.

**Parameters**
`DATA` - Hash of a transaction

**Example Parameters**
~~~js
params:[
    "0x3fc20bc4542b0697990b0558fcda7a1d2aa10e5cacd6154b6dcf7377abd0ba64"
]
~~~

**Returns**
`Object` - A Transaction object, or `null` when no transaction was found:
* `blockHash`: `DATA` - hash of the block where this transaction was in. `null` when it is pending.
* `blockNumber`: `QUANTITY` - block number where this transaction was in. `null` when it is pending.
* `transactionIndex`: `QUANTITY` - integer of the transaction's index position in the block. `null` when it is pending.
* `hash`: `DATA` - hash of the transaction. `txid`
* `chain`: `DATA` - network id.
* `version`: `DATA` - version.
* `timestamp`: `QUANTITY` - the unix timestamp for when the transaction was created.
* `from`: `DATA` - address of the sender.
* `to`: `DATA` - address of the receiver.
* `value`: `QUANTITY` - value transferred in beryl.
* `gas`: `QUANTITY` - gas provided by the sender.
* `nonce`: `DATA` - the number of transactions made by the sender prior to this one.
* `input`: `DATA` - the data send along with the transaction.
* `extra`: `DATA` - the "extra data" field of this transaction.
* `v`: `QUANTITY` - ECDSA recovery id.
* `r`: `QUANTITY` - ECDSA signature r.
* `s`: `QUANTITY` - ECDSA signature s.
* `size`' : `QUANTITY` - integer the size of this transaction in bytes.

**Example**
~~~js
// Request
curl -X POST --data '{"jsonrpc": "2.0", "method": "be4_getTransactionByHash", "params": ["0xced8923848413a02869d715943d37b6c085d3b30eb536b5ae7d7db25edad28c3"], "id": 32}'

// Result
{
  "jsonrpc": "2.0",
  "id": 32,
  "result": [
    {
      "blockNumber": "0x1e",
      "blockHash": "0xfc205eea221ce9ac3e616f97b048e2a39838b6475fd886922a743b0dbe6ec5a7",
      "transactionIndex": "0x00",
      "hash": "0xced8923848413a02869d715943d37b6c085d3b30eb536b5ae7d7db25edad28c3",
      "chain": "0x00",
      "version": "0x00",
      "timestamp": "0x5daefbfc",
      "from": "0x2b8c9ac4d8783e0c16c950e1a6c0b4f73eb7f294",
      "to": "0x66012c3c52b40b604081f240b099ad73a8e9abcd",
      "value": "0x05f5e100",
      "gas": "0x00",
      "nonce": "0x01",
      "input": "",
      "extra": "0x9245cb44760636e824b8835f2295af17f6313572663d62b01cb29e351bb5ea17",
      "v": "0x01",
      "r": "0x986dd619fa8cb5f9a86f56f25a49b67b00590113cba40eb543dae993e38a8701",
      "s": "0x0cc6c812f61a66b0e16aea73a7a0bba038e39614a320854be01c3490038955be",
      "size": "0xa7"
    }
  ]
}
~~~
***


### be4_getTransactionByBlockHashAndIndex
Returns the information about a transaction by block hash and transaction index position.

**Parameters**
1. `DATA` - Hash of a block.
1. `QUANTITY` - integer of the transaction index position.

**Example Parameters**
~~~js
params:[
    "0xfc205eea221ce9ac3e616f97b048e2a39838b6475fd886922a743b0dbe6ec5a7",
    "0x00" // 0
]
~~~

**Returns**
See [be4_getTransactionByHash](#be4_gettransactionbyhash)

**Example**
~~~js
// Request
curl -X POST --data '{"jsonrpc": "2.0", "method": "be4_getTransactionByBlockHashAndIndex", "params": ["0xfc205eea221ce9ac3e616f97b048e2a39838b6475fd886922a743b0dbe6ec5a7", "0x00"], "id": 32}'
~~~
Result See [be4_getTransactionByHash](#be4_gettransactionbyhash)
***

### be4_getTransactionByBlockNumberAndIndex
Returns the information about a transaction by block number and transaction index position.

**Parameters**
1. `QUANTITY|TAG` - a block number, or the string `"earliest"`, `"latest"` or `"pending"`, as in the [default block parameter](#the-default-block-parameter).
1. `QUANTITY` - the transaction index position.

**Example Parameters**
~~~js
params:[
    "0x63", // 99
    "0x00" // 0
]
~~~

**Returns**
See [be4_getTransactionByHash](#be4_gettransactionbyhash)

**Example**
~~~js
// Request
curl -X POST --data '{"jsonrpc": "2.0", "method": "be4_getTransactionByBlockNumberAndIndex", "params": ["0x63", "0x00"], "id": 32}'
~~~
Result See [be4_getTransactionByHash](#be4_gettransactionbyhash)
***

### be4_getTransactionReceipt
Returns the receipt of a transaction by transaction hash.
**Note** That the receipt is not available for pending transactions.

**Parameters**
`DATA` - Hash of a transaction

**Example Parameters**
~~~js
params:[
    "0xced8923848413a02869d715943d37b6c085d3b30eb536b5ae7d7db25edad28c3"
]
~~~

**Returns**
`Object` - A Transaction receipt object, or `null` when no transaction was found:
* `blockHash`: `DATA` - hash of the block where this transaction was in.
* `blockNumber`: `QUANTITY` - block number where this transaction was in.
* `transactionIndex`: `QUANTITY` - integer of the transaction's index position in the block.
* `hash`: `DATA` - hash of the transaction.
* `chain`: `DATA` - network id.
* `version`: `DATA` - version.
* `timestamp`: `QUANTITY` - the unix timestamp for when the transaction was created.
* `from`: `DATA` - address of the sender.
* `to`: `DATA` - address of the receiver.
* `value`: `QUANTITY` - value transferred in beryl.
* `gas`: `QUANTITY` - gas provided by the sender.
* `nonce`: `DATA` - the number of transactions made by the sender prior to this one.
* `input`: `DATA` - the data send along with the transaction.
* `extra`: `DATA` - the "extra data" field of this transaction.
* `v`: `QUANTITY` - ECDSA recovery id.
* `r`: `QUANTITY` - ECDSA signature r.
* `s`: `QUANTITY` - ECDSA signature s.
* `size` : `QUANTITY` - integer the size of this transaction in bytes.

**Example**
~~~js
// Request
curl -X POST --data '{"jsonrpc": "2.0", "method": "be4_getTransactionReceipt", "params": ["0xced8923848413a02869d715943d37b6c085d3b30eb536b5ae7d7db25edad28c3"], "id": 32}'

// Result
{
  "jsonrpc": "2.0",
  "id": 32,
  "result": [
    {
      "blockNumber": "0x1e",
      "blockHash": "0xfc205eea221ce9ac3e616f97b048e2a39838b6475fd886922a743b0dbe6ec5a7",
      "transactionIndex": "0x00",
      "hash": "0xced8923848413a02869d715943d37b6c085d3b30eb536b5ae7d7db25edad28c3",
      "chain": "0x00",
      "version": "0x00",
      "timestamp": "0x5daefbfc",
      "from": "0x2b8c9ac4d8783e0c16c950e1a6c0b4f73eb7f294",
      "to": "0x66012c3c52b40b604081f240b099ad73a8e9abcd",
      "value": "0x05f5e100",
      "gas": "0x00",
      "nonce": "0x01",
      "input": "",
      "extra": "0x9245cb44760636e824b8835f2295af17f6313572663d62b01cb29e351bb5ea17",
      "v": "0x01",
      "r": "0x986dd619fa8cb5f9a86f56f25a49b67b00590113cba40eb543dae993e38a8701",
      "s": "0x0cc6c812f61a66b0e16aea73a7a0bba038e39614a320854be01c3490038955be",
      "size": "0xa7"
    }
  ]
}
~~~
***

### be4_pendingTransactions
Returns the pending transactions list.

**Parameters**
none

**Returns**
`Array` - a list of pending transactions.

**Example**
~~~js
// Request
curl -X POST --data '{"jsonrpc": "2.0", "method": "be4_pendingTransactions", "params": [], "id": 1}'

// Result
{
  "jsonrpc": "2.0",
  "id": 32,
  "result": [
    {
      "hash": "0x0459e243e528ff2319218239d11896136dd63b55ddb71877b14593f9b9d42d9c",
      "chain": "0x00",
      "version": "0x00",
      "timestamp": "0x5dafff11",
      "from": "0x2b8c9ac4d8783e0c16c950e1a6c0b4f73eb7f294",
      "to": "0xe9f5e6d433316e4abfeff8c40ac405b735129501",
      "value": "0x4a817c80",  // 12.5 BRC
      "gas": "0x00",
      "nonce": "0x02",  // 2
      "input": "",
      "extra": "",
      "v": "0x00",
      "r": "0x82e9bea18a69d1aafe262729bc1a33e663e957cc05bb23453acb7b72440a0171",
      "s": "0x71b70b9c834e7e64b8f47f6212eef5752fdb3a45f7981db894683ad3b9117eff",
      "size": "0x83"
    }
  ]
}
~~~
***

### be4_getTransactionsByAddress
Returns the transaction history of given address.

**Parameters**
1. `DATA` - address to check history.
1. `DATA` - Hex string of start block number. (or `latest`)
1. `String` - Sort type ( `asc` or `desc` )
1. `DATA` - Hex string of transaction count to retreive.

**Example Parameters**
~~~js
params:[
    "0xe9f5e6d433316e4abfeff8c40ac405b735129501", "latest", "desc", "0x14"
]
~~~

**Returns**
Array of tx information. 
For tx object, See [be4_getTransactionByHash](#be4_gettransactionbyhash)

**Example**
~~~js
// request
curl -X POST --data '{"jsonrpc": "2.0", "method": "be4_getTransactionsByAddress", "params": [{See above}], "id": 32}'

// response
{
  "jsonrpc": "2.0",
  "id": 32,
  "result": [
    {
      "hash": "0x3c48c66329c426380ab945711dcdba4c52ee92ffea64171c0bf197310ad90f5b",
      "chain": "0x00",
      "version": "0x00",
      "timestamp": "0x5db2c4eb",
      "from": "0x2b8c9ac4d8783e0c16c950e1a6c0b4f73eb7f294",
      "to": "0xdb5e402f70b4f9a1b3d09ba9341ffa393823b71b",
      "value": "0x3b9aca00",
      "gas": "0x00",
      "nonce": "0x01",
      "input": "",
      "extra": "",
      "v": "0x01",
      "r": "0x10186e3715a4c93084d619237bb8b0395e4d29c94b093fd1bcba21e1332ba408",
      "s": "0x29556e35b26de22e4181f13690270243701fdb8f074cfcbc8043c713b918442d",
      "size": "0x6b"
    },
    {
      "hash": "0x9d6446ab8fffaea6feb949c97a6f87d84b6f029b049a6da12b6aee2ed7f704a0",
      "chain": "0x00",
      "version": "0x00",
      "timestamp": "0x5db2c40b",
      "from": "0x2b8c9ac4d8783e0c16c950e1a6c0b4f73eb7f294",
      "to": "0xe9f5e6d433316e4abfeff8c40ac405b735129501",
      "value": "0x3b9aca00",
      "gas": "0x00",
      "nonce": "0x00",
      "input": "",
      "extra": "",
      "v": "0x01",
      "r": "0xa092b41525f9082cd36426c28c80070356e11089fb20ebd1561e775ce2507ecc",
      "s": "0x61adca608288af6272dc8e7504f9707e06683aa5678e10516e4941bb03da0191",
      "size": "0x6b"
    }
  ]
}
~~~
***