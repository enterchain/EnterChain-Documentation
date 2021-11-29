# API Methods

### `ADMIN` methods

The `ADMIN` API methods provide administrative functionality to manage your node.

Note

The `ADMIN` API methods are not enabled by default for JSON-RPC. To enable the `ADMIN` API methods, use the [`--rpc-http-api`](https://besu.hyperledger.org/en/stable/Reference/CLI/CLI-Syntax/#rpc-http-api) or [`--rpc-ws-api`](https://besu.hyperledger.org/en/stable/Reference/CLI/CLI-Syntax/#rpc-ws-api) options.

#### `admin_addPeer`

Adds a [static node](https://besu.hyperledger.org/en/stable/HowTo/Find-and-Connect/Static-Nodes/).

Caution

If connections are timing out, ensure the node ID in the [enode URL](https://besu.hyperledger.org/en/stable/Concepts/Node-Keys/#enode-url) is correct.

**Parameters**

`enode`: _string_ - [enode URL](https://besu.hyperledger.org/en/stable/Concepts/Node-Keys/#enode-url) of peer to add

**Returns**

`result`: _boolean_ - `true` if peer added or `false` if peer already a [static node](https://besu.hyperledger.org/en/stable/HowTo/Find-and-Connect/Static-Nodes/)

Example

curl HTTP request

```
curl -X POST --data '{"jsonrpc":"2.0","method":"admin_addPeer","params":["enode://f59c0ab603377b6ec88b89d5bb41b98fc385030ab1e4b03752db6f7dab364559d92c757c13116ae6408d2d33f0138e7812eb8b696b2a22fe3332c4b5127b22a3@127.0.0.1:30304"],"id":1}' http://127.0.0.1:8545
```

wscat WS requestJSON result

#### `admin_changeLogLevel`

Changes the log level without restarting Besu. You can change the log level for all logs, or you can change the log level for specific packages or classes.

You can specify only one log level per RPC call.

**Parameters**

* `level`: _string_ - [log level](https://besu.hyperledger.org/en/stable/Reference/CLI/CLI-Syntax/#logging)
* `log_filter`: _array_ - (optional) packages or classes for which to change the log level

**Returns**

`result`: _string_ - `Success` if the log level has changed, otherwise `error`

Example

The following example changes the debug level for specified classes to `DEBUG`.

curl HTTP request

```
curl -X POST --data '{"jsonrpc":"2.0", "method":"admin_changeLogLevel", "params":["DEBUG", ["org.hyperledger.besu.ethereum.eth.manager","org.hyperledger.besu.ethereum.p2p.rlpx.connections.netty.ApiHandler"]], "id":1}' http://127.0.0.1:8545
```

wscat WS requestJSON result

The following example changes the debug level of all logs to `WARN`.

curl HTTP request

```
curl -X POST --data '{"jsonrpc":"2.0","method":"admin_changeLogLevel","params":["WARN"], "id":1}' http://127.0.0.1:8545
```

wscat WS requestJSON result

#### `admin_generateLogBloomCache`

Generates cached log bloom indexes for blocks. APIs such as [`eth_getLogs`](https://besu.hyperledger.org/en/stable/Reference/API-Methods/#eth\_getlogs) and [`eth_getFilterLogs`](https://besu.hyperledger.org/en/stable/Reference/API-Methods/#eth\_getfilterlogs) use the cache for improved performance.

Tip

Manually executing `admin_generateLogBloomCache` is not required unless the [`--auto-log-bloom-caching-enabled`](https://besu.hyperledger.org/en/stable/Reference/CLI/CLI-Syntax/#auto-log-bloom-caching-enabled) command line option is set to false.

Note

Each index file contains 100000 blocks. The last fragment of blocks less than 100000 are not indexed.

**Parameters**

* `startBlock`: _string_ - block to start generating indexes
* `endBlock`: _string_ - block to stop generating indexes

**Returns**

`result`: _object_ - log bloom index details:

* `startBlock`: _string_ - starting block for the last requested cache generation
* `endBlock`: _string_ - ending block for the last requested cache generation
* `currentBlock`: _string_ - most recent block added to the cache
* `indexing`: _boolean_ - indicates if indexing is in progress
* _boolean_ - indicates acceptance of the request from this call to generate the cache

Example

curl HTTP request

```
curl -X POST --data '{jsonrpc":"2.0","method":"admin_generateLogBloomCache", "params":["0x0", "0x10000"], "id":1}' http://127.0.0.1:8545
```

wscat WS requestJSON result

#### `admin_logsRemoveCache`

Removes cache files for the specified range of blocks.

**Parameters**

* `fromBlock`: _string_ - integer representing a block number or one of the string tags `latest`, `earliest`, or `pending`, as described in [Block Parameter](https://besu.hyperledger.org/en/stable/HowTo/Interact/APIs/Using-JSON-RPC-API/#block-parameter)
* `toBlock`: _string_ - integer representing a block number or one of the string tags `latest`, `earliest`, or `pending`, as described in [Block Parameter](https://besu.hyperledger.org/en/stable/HowTo/Interact/APIs/Using-JSON-RPC-API/#block-parameter)

You can skip a parameter by using an empty string, `""`. If you specify:

* No parameters, the call removes cache files for all blocks.
* Only `fromBlock`, the call removes cache files for the specified block.
* Only `toBlock`, the call removes cache files from the genesis block to the specified block.

**Returns**

`result`: _object_ - `Cache Removed` status or `error`.

Example

curl HTTP request

```
curl -X POST --data '{"jsonrpc":"2.0","method":"admin_logsRemoveCache","params":["1", "100"], "id":1}' http://127.0.0.1:8545
```

wscat WS requestJSON result

#### `admin_logsRepairCache`

Repairs cached logs by fixing all segments starting with the specified block number.

**Parameters**

`startBlock`: _string_ - decimal index of the starting block to fix; defaults to the head block

**Returns**

`result`: _object_ - status of the repair request; `Started` or `Already running`

Example

curl HTTP request

```
curl -X POST --data '{"jsonrpc":"2.0","method":"admin_logsRepairCache","params":["1200"], "id":1}' http://127.0.0.1:8545
```

wscat WS requestJSON result

#### `admin_nodeInfo`

Returns networking information about the node. The information includes general information about the node and specific information from each running Ethereum sub-protocol (for example, `eth`).

**Parameters**

None

**Returns**

`result`: _object_ - node object with the following fields:

* `enode`: _string_ - [enode URL](https://besu.hyperledger.org/en/stable/Concepts/Node-Keys/#enode-url) of the node
* `listenAddr`: _string_ - host and port for the node
* `name`: _string_ - client name
* `id`: _string_ - [node public key](https://besu.hyperledger.org/en/stable/Concepts/Node-Keys/#node-public-key)
* `ports`: _object_ - peer discovery and listening [ports](https://besu.hyperledger.org/en/stable/HowTo/Find-and-Connect/Managing-Peers/#port-configuration)
* `protocols`: _object_ - list of objects containing information for each Ethereum sub-protocol

Note

If the node is running locally, the host of the `enode` and `listenAddr` display as `[::]` in the result. When advertising externally, the external address displayed for the `enode` and `listenAddr` is defined by [`--nat-method`](https://besu.hyperledger.org/en/stable/HowTo/Find-and-Connect/Specifying-NAT/).

Example

curl HTTP request

```
curl -X POST --data '{"jsonrpc":"2.0","method":"admin_nodeInfo","params":[],"id":1}' http://127.0.0.1:8545
```

wscat WS requestJSON result

#### `admin_peers`

Returns networking information about connected remote nodes.

**Parameters**

None

**Returns**

`result`: _array_ of _objects_ - list of objects returned for each remote node, with the following fields.

* `version`: _string_ - P2P protocol version
* `name`: _string_ - client name
* `caps`: _array_ of _strings_ - list of Ethereum sub-protocol capabilities
* `network`: _object_ - local and remote addresses established at time of bonding with the peer (the remote address might not match the hex value for `port`; it depends on which node initiated the connection.)
* `port`: _string_ - port on the remote node on which P2P discovery is listening
* `id`: _string_ - node public key (excluding the `0x` prefix, the node public key is the ID in the [enode URL](https://besu.hyperledger.org/en/stable/Concepts/Node-Keys/#enode-url) `enode://<id ex 0x>@<host>:<port>`.)
* `protocols`: _object_ - [current state of peer](https://besu.hyperledger.org/en/stable/HowTo/Find-and-Connect/Managing-Peers/#monitoring-peer-connections) including `difficulty` and `head` (`head` is the hash of the highest known block for the peer.)
* `enode`: _string_ - enode URL of the remote node

Example

curl HTTP request

```
curl -X POST --data '{"jsonrpc":"2.0","method":"admin_peers","params":[],"id":1}' http://127.0.0.1:8545
```

wscat WS requestJSON result

#### `admin_removePeer`

Removes a [static node](https://besu.hyperledger.org/en/stable/HowTo/Find-and-Connect/Static-Nodes/).

**Parameters**

`enode`: _string_ - [enode URL](https://besu.hyperledger.org/en/stable/Concepts/Node-Keys/#enode-url) of peer to remove

**Returns**

`result`: _boolean_ - `true` if peer removed or `false` if peer not a [static node](https://besu.hyperledger.org/en/stable/HowTo/Find-and-Connect/Static-Nodes/)

Example

curl HTTP request

```
curl -X POST --data '{"jsonrpc":"2.0","method":"admin_removePeer","params":["enode://f59c0ab603377b6ec88b89d5bb41b98fc385030ab1e4b03752db6f7dab364559d92c757c13116ae6408d2d33f0138e7812eb8b696b2a22fe3332c4b5127b22a3@127.0.0.1:30304"],"id":1}' http://127.0.0.1:8545
```

wscat WS requestJSON result

### `CLIQUE` methods

The `CLIQUE` API methods provide access to the [Clique](https://besu.hyperledger.org/en/stable/HowTo/Configure/Consensus-Protocols/Clique/) consensus engine.

Note

The `CLIQUE` API methods are not enabled by default for JSON-RPC. To enable the `CLIQUE` API methods use the [`--rpc-http-api`](https://besu.hyperledger.org/en/stable/Reference/CLI/CLI-Syntax/#rpc-http-api) or [`--rpc-ws-api`](https://besu.hyperledger.org/en/stable/Reference/CLI/CLI-Syntax/#rpc-ws-api) options.

#### `clique_discard`

Discards a proposal to [add or remove a signer with the specified address](https://besu.hyperledger.org/en/stable/HowTo/Configure/Consensus-Protocols/Add-Validators/#clique).

**Parameters**

`address`: _string_ - 20-byte address of proposed signer

**Returns**

`result`: _boolean_ - indicates if the proposal is discarded

Example

curl HTTP request

```
curl -X POST --data '{"jsonrpc":"2.0","method":"clique_discard","params":["0xFE3B557E8Fb62b89F4916B721be55cEb828dBd73"], "id":1}' http://127.0.0.1:8545
```

wscat WS requestJSON result

#### `clique_getSigners`

Lists [signers for the specified block](https://besu.hyperledger.org/en/stable/HowTo/Configure/Consensus-Protocols/Clique/#adding-and-removing-signers).

**Parameters**

`blockNumber`: _string_ - integer representing a block number or one of the string tags `latest`, `earliest`, or `pending`, as described in [Block Parameter](https://besu.hyperledger.org/en/stable/HowTo/Interact/APIs/Using-JSON-RPC-API/#block-parameter)

**Returns**

`result`: _array_ of _string_ - list of 20-byte addresses of signers

Example

curl HTTP request

```
curl -X POST --data '{"jsonrpc":"2.0","method":"clique_getSigners","params":["latest"], "id":1}' http://127.0.0.1:8545
```

wscat WS requestJSON result

#### `clique_getSignerMetrics`

Provides the following validator metrics for the specified range:

* Number of blocks from each validator
* Block number of the last block proposed by each validator (if any proposed in the specified range)
* All validators present in the last block

**Parameters**

* `fromBlockNumber`: _string_ - integer representing a block number or the string tag `earliest`, as described in [Block Parameter](https://besu.hyperledger.org/en/stable/HowTo/Interact/APIs/Using-JSON-RPC-API/#block-parameter)
* `toBlockNumber`: _string_ - integer representing a block number or one of the string tags `latest` or `pending`, as described in [Block Parameter](https://besu.hyperledger.org/en/stable/HowTo/Interact/APIs/Using-JSON-RPC-API/#block-parameter)

If you specify:

* No parameters, the call provides metrics for the last 100 blocks, or all blocks if there are less than 100 blocks.
* Only the first parameter, the call provides metrics for all blocks from the block specified to the latest block.

**Returns**

`result`: _array_ of _objects_ - list of validator objects

Note

The proposer of the genesis block has address `0x0000000000000000000000000000000000000000`.

Example

curl HTTP

```
curl -X POST --data '{"jsonrpc":"2.0","method":"clique_getSignerMetrics","params":["1", "100"], "id":1}' http://127.0.0.1:8545
```

wscat WSJSON result

#### `clique_getSignersAtHash`

Lists signers for the specified block.

**Parameters**

`hash`: _string_ - 32-byte block hash

**Returns**

`result`: _array_ of _string_ - list of 20-byte addresses of signers

Example

curl HTTP request

```
curl -X POST --data '{"jsonrpc":"2.0","method":"clique_getSignersAtHash","params":["0x98b2ddb5106b03649d2d337d42154702796438b3c74fd25a5782940e84237a48"], "id":1}' http://127.0.0.1:8545
```

wscat WS requestJSON result

#### `clique_proposals`

Returns [current proposals](https://besu.hyperledger.org/en/stable/HowTo/Configure/Consensus-Protocols/Clique/#adding-and-removing-signers).

**Parameters**

None

**Returns**

`result`: _map_ of _strings_ to _booleans_ - map of account addresses to corresponding boolean values indicating the proposal for each account (if `true`, the proposal is to add a signer; if `false`, the proposal is to remove a signer.)

Example

curl HTTP request

```
curl -X POST --data '{"jsonrpc":"2.0","method":"clique_proposals","params":[], "id":1}' http://127.0.0.1:8545
```

wscat WS requestJSON result

#### `clique_propose`

Proposes to [add or remove a signer with the specified address](https://besu.hyperledger.org/en/stable/HowTo/Configure/Consensus-Protocols/Add-Validators/#clique).

**Parameters**

* `address`: _string_ - 20-byte address
* `proposal`: _boolean_ - `true` to propose adding signer or `false` to propose removing signer

**Returns**

`result`: _boolean_ - `true`

Example

curl HTTP request

```
curl -X POST --data '{"jsonrpc":"2.0","method":"clique_propose","params":["0xFE3B557E8Fb62b89F4916B721be55cEb828dBd73", true], "id":1}' http://127.0.0.1:8545
```

wscat WS requestJSON result

### `DEBUG` methods

The `DEBUG` API methods allow you to inspect and debug the network. The `DEBUG` API is a more verbose alternative to the [`TRACE` API](https://besu.hyperledger.org/en/stable/Reference/API-Methods/#trace-methods), and its main purpose is compatibility with tools such as [Remix](https://remix.ethereum.org). We recommend using the [`TRACE` API](https://besu.hyperledger.org/en/stable/Reference/API-Methods/#trace-methods) for production use over the `DEBUG` API.

Note

The `DEBUG` API methods are not enabled by default for JSON-RPC. To enable the `DEBUG` API methods, use the [`--rpc-http-api`](https://besu.hyperledger.org/en/stable/Reference/CLI/CLI-Syntax/#rpc-http-api) or [`--rpc-ws-api`](https://besu.hyperledger.org/en/stable/Reference/CLI/CLI-Syntax/#rpc-ws-api) options.

#### `debug_accountRange`

[Retesteth](https://github.com/ethereum/retesteth/wiki/Retesteth-Overview) uses `debug_accountRange` to implement debugging.

Returns the accounts for a specified block.

**Parameters**

* `blockHashOrNumber`: _string_ - block hash or number
* `txIndex`: _number_ - transaction index from which to start
* `address`: _string_ - address hash from which to start
* `limit`: _integer_ - maximum number of account entries to return

**Returns**

`result`: _object_ - account details object with the following fields:

* `addressMap`: _map_ of _strings_ to _strings_ - map of address hashes and account addresses
* `nextKey`: _string_ - hash of the next address if any addresses remain in the state, otherwise zero

Example

curl HTTP request

```
curl -X POST --data '{"jsonrpc":"2.0","method":"debug_accountRange","params":["12345", 0, "0", 5],"id":1}' http://127.0.0.1:8545
```

wscat WS requestJSON result

#### `debug_batchSendRawTransaction`

Sends a list of [signed transactions](https://besu.hyperledger.org/en/stable/HowTo/Send-Transactions/Transactions/). This is used to quickly load a network with a lot of transactions. This does the same thing as calling [`eth_sendRawTransaction`](https://besu.hyperledger.org/en/stable/Reference/API-Methods/#eth\_sendRawTransaction) multiple times.

**Parameters**

`data`: _string_ - signed transaction data array

**Returns**

`result`: _array_ of _objects_ - object returned for each transaction, with the following fields:

* `index`: _string_ - index of the transaction in the request parameters array
* `success`: _boolean_ - indicates whether or not the transaction has been added to the transaction pool
* `errorMessage`: _string_ - (optional) error message

Example

curl HTTP

```
curl -X POST --data '{"jsonrpc":"2.0","method":"debug_batchSendRawTransaction","params":["0xf868808203e882520894627306090abab3a6e1400e9345bc60c78a8bef57872386f26fc10000801ba0ac74ecfa0e9b85785f042c143ead4780931234cc9a032fce99fab1f45e0d90faa02fd17e8eb433d4ca47727653232045d4f81322619c0852d3fe8ddcfcedb66a43","0x416","0xf868018203e882520894627306090abab3a6e1400e9345bc60c78a8bef57872386f26fc10000801ca0b24ea1bee8fe36984c36acbf80979a4509f23fc17141851e08d505c0df158aa0a00472a05903d4cd7a811bd4d5c59cc105d93f5943f3393f253e92e65fc36e7ce0","0xf868808203e882520894627306090abab3a6e1400e9345bc60c78a8bef5787470de4df820000801ca0f7936b4de04792e3c65095cfbfd1399d231368f5f05f877588c0c8509f6c98c9a01834004dead527c8da1396eede42e1c60e41f38a77c2fd13a6e495479c729b99"],"id":1}' http://127.0.0.1:8545
```

wscat WSJSON result

#### `debug_getBadBlocks`

Returns a list of invalid blocks. This is used to detect and analyze consensus flaws.

**Parameters**

None

**Returns**

`result`: _array_ of _objects_ - list of [block objects](https://besu.hyperledger.org/en/stable/Reference/API-Objects/#block-object)

Example

curl HTTP

```
curl -X POST --data '{"jsonrpc":"2.0","method":"debug_getBadBlocks","params":[],"id":1}' http://127.0.0.1:8545
```

wscat WSJSON result

#### `debug_standardTraceBlockToFile`

Generates files containing the block trace. A separate file is generated for each transaction in the block.

You can also specify a trace file for a specific transaction in a block.

Use [`debug_standardTraceBadBlockToFile`](https://besu.hyperledger.org/en/stable/Reference/API-Methods/#debug\_standardtracebadblocktofile) to view the trace for an invalid block.

**Parameters**

`blockHash`: _string_ - block hash

`txHash`: _string_ - (optional) transaction hash; if omitted, a trace file is generated for each transaction in the block.

`disableMemory`: _boolean_ - (optional) specifies whether to capture EVM memory during the trace; defaults to `true`

**Returns**

`result`: _string_ - location of the generated trace files

Example

curl HTTP request

```
curl -X POST --data '{"jsonrpc":"2.0","method":"debug_standardTraceBlockToFile","params":["0x2dc0b6c43144e314a86777b4bd4f987c0790a6a0b21560671d221ed81a23f2dc", {
"txHash": "0x4ff04c4aec9517721179c8dd435f47fbbfc2ed26cd4926845ab687420d5580a6", "disableMemory": false}], "id":1}' http://127.0.0.1:8545
```

wscat WS requestJSON result

#### `debug_standardTraceBadBlockToFile`

Generates files containing the block trace of invalid blocks. A separate file is generated for each transaction in the block.

Use [`debug_standardTraceBlockToFile`](https://besu.hyperledger.org/en/stable/Reference/API-Methods/#debug\_standardtraceblocktofile) to view the trace for a valid block.

**Parameters**

`blockHash`: _string_ - block hash

**Returns**

`result`: _string_ - location of the generated trace files

Example

curl HTTP request

```
curl -X POST --data '{"jsonrpc":"2.0","method":"debug_standardTraceBadBlockToFile","params":["0x53741e9e94791466d117c5f9e41a2ed1de3f73d39920c621dfc2f294e7779baa"], "id":1}' http://127.0.0.1:8545
```

wscat WS requestJSON result

#### `debug_storageRangeAt`

[Remix](https://remix.ethereum.org) uses `debug_storageRangeAt` to implement debugging. Use the _Debugger_ tab in Remix instead of calling `debug_storageRangeAt` directly.

Returns the contract storage for the specified range.

**Parameters**

* `blockHash`: _string_ - block hash
* `txIndex`: _number_ - transaction index from which to start
* `address`: _string_ - contract address
* `startKey`: _string_ - start key
* `limit`: _number_ - number of storage entries to return

**Returns**

`result`: _object_ - [range object](https://besu.hyperledger.org/en/stable/Reference/API-Objects/#range-object).

Example

curl HTTP request

```
curl -X POST --data '{"jsonrpc":"2.0","method":"debug_storageRangeAt","params":["0x2b76b3a2fc44c0e21ea183d06c846353279a7acf12abcc6fb9d5e8fb14ae2f8c",0,"0x0e0d2c8f7794e82164f11798276a188147fbd415","0x0000000000000000000000000000000000000000000000000000000000000000",1], "id":1}' http://127.0.0.1:8545
```

wscat WS requestJSON result

#### `debug_metrics`

Returns metrics providing information on the internal operation of Besu.

The available metrics might change over time. The JVM metrics might vary based on the JVM implementation used.

The metric types are:

* Timer
* Counter
* Gauge

**Parameters**

None

**Returns**

`result`: _object_ - metrics object

Example

curl HTTP request

```
curl -X POST --data '{"jsonrpc":"2.0","method":"debug_metrics","params":[],"id":1}' http://127.0.0.1:8545
```

wscat WS requestJSON result

#### `debug_traceTransaction`

[Remix](https://remix.ethereum.org) uses `debug_traceTransaction` to implement debugging. Use the _Debugger_ tab in Remix instead of calling `debug_traceTransaction` directly.

Reruns the transaction with the same state as when the transaction executed.

**Parameters**

* `transactionHash`: _string_ - transaction hash
* `options`: _object_ - request options object with the following fields (all optional and default to `false`):
  * `disableStorage`: _boolean_ - `true` disables storage capture.
  * `disableMemory`: _boolean_ - `true` disables memory capture.
  * `disableStack` : _boolean_ - `true` disables stack capture.

**Returns**

`result`: _object_ - [trace object](https://besu.hyperledger.org/en/stable/Reference/API-Objects/#trace-object)

Example

curl HTTP request

```
curl -X POST --data '{"jsonrpc":"2.0","method":"debug_traceTransaction","params":["0x2cc6c94c21685b7e0f8ddabf277a5ccf98db157c62619cde8baea696a74ed18e",{"disableStorage":true}],"id":1}' http://127.0.0.1:8545
```

wscat WS requestJSON result

#### `debug_traceBlock`

Returns full trace of all invoked opcodes of all transactions included in the block.

**Parameters**

* `block`: _string_ - RLP of the block
* `options`: _object_ - request options object with the following fields (all optional and default to `false`):
  * `disableStorage`: _boolean_ - `true` disables storage capture.
  * `disableMemory`: _boolean_ - `true` disables memory capture.
  * `disableStack` : _boolean_ - `true` disables stack capture.

**Returns**

`result`: _object_ - [trace object](https://besu.hyperledger.org/en/stable/Reference/API-Objects/#trace-object)

Example

curl HTTP request

```
curl -X POST --data '{"jsonrpc":"2.0","method":"debug_traceBlock","params":["0xf90277f90208a05a41d0e66b4120775176c09fcf39e7c0520517a13d2b57b18d33d342df038bfca01dcc4de8dec75d7aab85b567b6ccd41ad312451b948a7413f0a142fd40d4934794e6a7a1d47ff21b6321162aea7c6cb457d5476bcaa00e0df2706b0a4fb8bd08c9246d472abbe850af446405d9eba1db41db18b4a169a04513310fcb9f6f616972a3b948dc5d547f280849a87ebb5af0191f98b87be598a0fe2bf2a941abf41d72637e5b91750332a30283efd40c424dc522b77e6f0ed8c4b9010000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000860153886c1bbd82b44382520b8252088455c426598b657468706f6f6c2e6f7267a0b48c515a9dde8d346c3337ea520aa995a4738bb595495506125449c1149d6cf488ba4f8ecd18aab215f869f86780862d79883d2000825208945df9b87991262f6ba471f09758cde1c0fc1de734827a69801ca088ff6cf0fefd94db46111149ae4bfc179e9b94721fffd821d38d16464b3f71d0a045e0aff800961cfce805daef7016b9b675c137a6a41a548f7b60a3484c06a33ac0"],"id":1}' http://127.0.0.1:8545
```

wscat WS requestJSON result

#### `debug_traceBlockByHash`

Returns full trace of all invoked opcodes of all transactions included in the block.

**Parameters**

* `blockHash`: _string_ - block hash
* `options`: _object_ - request options object with the following fields (all optional and default to `false`):
  * `disableStorage`: _boolean_ - `true` disables storage capture.
  * `disableMemory`: _boolean_ - `true` disables memory capture.
  * `disableStack` : _boolean_ - `true` disables stack capture.

**Returns**

`result`: _array_ of _objects_ - list of [trace objects](https://besu.hyperledger.org/en/stable/Reference/API-Objects/#trace-object)

Example

curl HTTP request

```
curl -X POST --data '{"jsonrpc":"2.0","method":"debug_traceBlockByHash","params":["0xaceb3b2c9b25b0589230873921eb894b28722011b8df63977145517d754875a5"], "id":1}' http://127.0.0.1:8545
```

wscat WS requestJSON result

#### `debug_traceBlockByNumber`

Returns full trace of all invoked opcodes of all transactions included in the block.

**Parameters**

* `blockNumber`: _string_ - integer representing a block number or one of the string tags `latest`, `earliest`, or `pending`, as described in [Block Parameter](https://besu.hyperledger.org/en/stable/HowTo/Interact/APIs/Using-JSON-RPC-API/#block-parameter)
* `options`: _object_ - request options object with the following fields (all optional and default to `false`):
  * `disableStorage`: _boolean_ - `true` disables storage capture.
  * `disableMemory`: _boolean_ - `true` disables memory capture.
  * `disableStack` : _boolean_ - `true` disables stack capture.

**Returns**

`result`: _array_ of _objects_ - list of [trace objects](https://besu.hyperledger.org/en/stable/Reference/API-Objects/#trace-object)

Example

curl HTTP request

```
curl -X POST --data '{"jsonrpc":"2.0","method":"debug_traceBlockByNumber","params":["0x7224",{"disableStorage":true}], "id":1}' http://127.0.0.1:8545
```

wscat WS requestJSON result

### `EEA` methods

The `EEA` API methods provide functionality for [private transactions](https://besu.hyperledger.org/en/stable/Concepts/Privacy/Private-Transactions/) and [privacy groups](https://besu.hyperledger.org/en/stable/Concepts/Privacy/Privacy-Groups/).

Note

The `EEA` API methods are not enabled by default for JSON-RPC. To enable the `EEA` API methods, use the [`--rpc-http-api`](https://besu.hyperledger.org/en/stable/Reference/CLI/CLI-Syntax/#rpc-http-api) or [`--rpc-ws-api`](https://besu.hyperledger.org/en/stable/Reference/CLI/CLI-Syntax/#rpc-ws-api) options.

#### `eea_sendRawTransaction`

Distributes the [private transaction](https://besu.hyperledger.org/en/stable/HowTo/Send-Transactions/Creating-Sending-Private-Transactions/), generates the [privacy marker transaction](https://besu.hyperledger.org/en/stable/Concepts/Privacy/Private-Transaction-Processing/) and submits it to the transaction pool, and returns the transaction hash of the [privacy marker transaction](https://besu.hyperledger.org/en/stable/Concepts/Privacy/Private-Transaction-Processing/).

The signed transaction passed as an input parameter includes the `privateFrom`, [`privateFor` or `privacyGroupId`](https://besu.hyperledger.org/en/stable/HowTo/Send-Transactions/Creating-Sending-Private-Transactions/#eea-compliant-or-besu-extended-privacy), and `restriction` fields.

The `gas` and `gasPrice` are used by the [privacy marker transaction](https://besu.hyperledger.org/en/stable/Concepts/Privacy/Private-Transaction-Processing/) not the private transaction itself.

To avoid exposing your private key, create signed transactions offline and send the signed transaction data using `eea_sendRawTransaction`.

Important

For production systems requiring private transactions, use a network with a consensus mechanism supporting transaction finality to make sure the private state does not become inconsistent with the chain. For example, [IBFT 2.0](https://besu.hyperledger.org/en/stable/HowTo/Configure/Consensus-Protocols/IBFT/) and [QBFT](https://besu.hyperledger.org/en/stable/HowTo/Configure/Consensus-Protocols/QBFT/) provide the required finality.

Using private transactions with [pruning](https://besu.hyperledger.org/en/stable/Concepts/Pruning/) or [fast sync](https://besu.hyperledger.org/en/stable/Reference/CLI/CLI-Syntax/#sync-mode) is not supported.

Besu does not implement [`eea_sendTransaction`](https://besu.hyperledger.org/en/stable/HowTo/Send-Transactions/Account-Management/).

[EthSigner](https://docs.ethsigner.consensys.net/en/latest/) provides transaction signing and implements [`eea_sendTransaction`](https://docs.ethsigner.consensys.net/en/latest/Using-EthSigner/Using-EthSigner/#eea\_sendtransaction).

**Parameters**

`transaction`: _string_ - signed RLP-encoded private transaction

**Returns**

`result`: _string_ - 32-byte transaction hash of the [Privacy Marker Transaction](https://besu.hyperledger.org/en/stable/Concepts/Privacy/Private-Transaction-Processing/)

Tip

If creating a contract, use [priv\_getTransactionReceipt](https://besu.hyperledger.org/en/stable/Reference/API-Methods/#priv\_gettransactionreceipt) to retrieve the contract address after the transaction is finalized.

Example

curl HTTP request

```
curl -X POST --data '{"jsonrpc":"2.0","method":"eea_sendRawTransaction","params": ["0xf869018203e882520894f17f52151ebef6c7334fad080c5704d77216b732881bc16d674ec80000801ba02da1c48b670996dcb1f447ef9ef00b33033c48a4fe938f420bec3e56bfd24071a062e0aa78a81bf0290afbc3a9d8e9a068e6d74caa66c5e0fa8a46deaae96b0833"], "id":1}' http://127.0.0.1:8545
```

wscat WS requestJSON result

### `ETH` methods

The `ETH` API methods allow you to interact with the blockchain.

Note

Methods with an equivalent [GraphQL](https://besu.hyperledger.org/en/stable/HowTo/Interact/APIs/GraphQL/) query include a GraphQL request and result in the method example. The parameter and result descriptions apply to the JSON-RPC requests. The GraphQL specification is defined in the [schema](https://github.com/hyperledger/besu/blob/master/ethereum/api/src/main/resources/schema.graphqls).

#### `eth_accounts`

Returns a list of account addresses a client owns.

Note

This method returns an empty object because Besu [doesn’t support key management](https://besu.hyperledger.org/en/stable/HowTo/Send-Transactions/Account-Management/) inside the client.

To provide access to your key store and and then sign transactions, use [EthSigner](http://docs.ethsigner.consensys.net/en/latest/) with Besu.

**Parameters**

None

**Returns**

`result`: _array_ of _strings_ - list of 20-byte account addresses owned by the client

Example

curl HTTP request

```
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_accounts","params":[],"id":53}' http://127.0.0.1:8545
```

wscat WS requestJSON result

#### `eth_blockNumber`

Returns the index corresponding to the block number of the current chain head.

**Parameters**

None

**Returns**

`result`: _string_ - hexadecimal integer representing the index corresponding to the block number of the current chain head

Example

curl HTTP

```
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_blockNumber","params":[],"id":51}' http://127.0.0.1:8545
```

wscat WSJSON resultcurl GraphQLGraphQLGraphQL result

#### `eth_call`

Invokes a contract function locally and does not change the state of the blockchain.

You can interact with contracts using [`eth_sendRawTransaction`](https://besu.hyperledger.org/en/stable/Reference/API-Methods/#eth\_sendrawtransaction) or `eth_call`.

If revert reason is enabled with [`--revert-reason-enabled`](https://besu.hyperledger.org/en/stable/Reference/CLI/CLI-Syntax/#revert-reason-enabled), the `eth_call` error response includes the [revert reason](https://besu.hyperledger.org/en/stable/HowTo/Send-Transactions/Revert-Reason/).

**Parameters**

`call`: _object_ - [transaction call object](https://besu.hyperledger.org/en/stable/Reference/API-Objects/#transaction-call-object)

`blockNumber`: _string_ - integer representing a block number or one of the string tags `latest`, `earliest`, or `pending`, as described in [Block Parameter](https://besu.hyperledger.org/en/stable/HowTo/Interact/APIs/Using-JSON-RPC-API/#block-parameter)

Note

By default, `eth_call` does not fail if the sender account has an insufficient balance. This is done by setting the balance of the account to a large amount of ether. To enforce balance rules, set the [`strict` parameter](https://besu.hyperledger.org/en/stable/Reference/API-Objects/#transaction-call-object) in the transaction call object to `true`.

**Returns**

`result`: _string_ - return value of the executed contract

Example

curl HTTP

```
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_call","params":[{"to":"0x69498dd54bd25aa0c886cf1f8b8ae0856d55ff13","value":"0x1"}, "latest"],"id":53}' http://127.0.0.1:8545
```

wscat WSJSON resultcurl GraphQLGraphQLGraphQL result

#### `eth_chainId`

Returns the [chain ID](https://besu.hyperledger.org/en/stable/Concepts/NetworkID-And-ChainID/).

**Parameters**

None

**Returns**

`result`: _string_ - chain ID in hexadecimal

Example

curl HTTP request

```
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_chainId","params":[],"id":51}' http://127.0.0.1:8545
```

wscat WS requestJSON result

#### `eth_coinbase`

Returns the client coinbase address. The coinbase address is the account to pay mining rewards to.

To set a coinbase address, start Besu with the `--miner-coinbase` option set to a valid Ethereum account address. You can get the Ethereum account address from a client such as MetaMask or Etherscan. For example:

Example

```
besu --miner-coinbase="0xfe3b557e8fb62b89f4916b721be55ceb828dbd73" --rpc-http-enabled
```

**Parameters**

None

**Returns**

`result`: _string_ - coinbase address

Example

curl HTTP request

```
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_coinbase","params":[],"id":53}' http://127.0.0.1:8545
```

wscat WS requestJSON result

#### `eth_estimateGas`

Returns an estimate of the gas required for a transaction to complete. The estimation process does not use gas and the transaction is not added to the blockchain. The resulting estimate can be greater than the amount of gas the transaction ends up using, for reasons including EVM mechanics and node performance.

The `eth_estimateGas` call does not send a transaction. You must call [`eth_sendRawTransaction`](https://besu.hyperledger.org/en/stable/Reference/API-Methods/#eth\_sendrawtransaction) to execute the transaction.

If revert reason is enabled with [`--revert-reason-enabled`](https://besu.hyperledger.org/en/stable/Reference/CLI/CLI-Syntax/#revert-reason-enabled), the `eth_estimateGas` error response includes the [revert reason](https://besu.hyperledger.org/en/stable/HowTo/Send-Transactions/Revert-Reason/).

**Parameters**

For `eth_estimateGas`, all fields are optional because setting a gas limit is irrelevant to the estimation process (unlike transactions, in which gas limits apply).

`call`: _object_ - [transaction call object](https://besu.hyperledger.org/en/stable/Reference/API-Objects/#transaction-call-object)

**Returns**

`result`: _string_ - amount of gas used

Example of cost estimate of a value transaction

The following example returns an estimate of 21000 wei (`0x5208`) for the transaction.

curl HTTP

```
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_estimateGas","params":[{"from":"0xFE3B557E8Fb62b89F4916B721be55cEb828dBd73","to":"0x44Aa93095D6749A706051658B970b941c72c1D53","value":"0x1"}],"id":53}' http://127.0.0.1:8545
```

wscat WSJSON resultcurl GraphQLGraphQLGraphQL result

Example of cost estimate of deploying a simple storage smart contract

The following example request estimates the cost of deploying a simple storage smart contract to the network. The data field contains the hash of the compiled contract you want to deploy. (You can get the compiled contract hash from your IDE, for example, **Remix > Compile tab > details > WEB3DEPLOY**.) The result is 113355 wei.

curl HTTP request

```
 curl -X POST \
    http://127.0.0.1:8545 \
    -H 'Content-Type: application/json' \
    -d '{
      "jsonrpc": "2.0",
      "method": "eth_estimateGas",
      "params": [{
        "from": "0x8bad598904ec5d93d07e204a366d084a80c7694e",
        "data": "0x608060405234801561001057600080fd5b5060e38061001f6000396000f3fe6080604052600436106043576000357c0100000000000000000000000000000000000000000000000000000000900480633fa4f24514604857806355241077146070575b600080fd5b348015605357600080fd5b50605a60a7565b6040518082815260200191505060405180910390f35b348015607b57600080fd5b5060a560048036036020811015609057600080fd5b810190808035906020019092919050505060ad565b005b60005481565b806000819055505056fea165627a7a7230582020d7ad478b98b85ca751c924ef66bcebbbd8072b93031073ef35270a4c42f0080029"
      }],
      "id": 1
    }'
```

JSON result

#### `eth_feeHistory`

Returns base fee per gas and transaction effective priority fee per gas history for the requested block range, allowing you to track trends over time.

**Parameters**

* `blockCount`: _integer_ - Number of blocks in the requested range. Between 1 and 1024 blocks can be requested in a single query. If blocks in the specified block range are not available, then only the fee history for available blocks is returned.
* `newestBlock`: _string_ - Integer representing the highest number block of the requested range or one of the string tags `latest`, `earliest`, or `pending`, as described in [Block parameter](https://besu.hyperledger.org/en/stable/HowTo/Interact/APIs/Using-JSON-RPC-API/#block-parameter).

**Returns**

`result`: _object_ - [Fee history results object](https://besu.hyperledger.org/en/stable/Reference/API-Objects/#fee-history-results-object).

Example

curl HTTP

```
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_feeHistory","params":[2, "latest"],"id":28}' http://127.0.0.1:8545
```

wscat WSJSON result

#### `eth_gasPrice`

Returns a percentile gas unit price for the most recent blocks, in Wei. By default, the last 100 blocks are examined and the 50th percentile gas unit price (that is, the median value) is returned.

If there are no blocks, the value for [`--min-gas-price`](https://besu.hyperledger.org/en/stable/Reference/CLI/CLI-Syntax/#min-gas-price) is returned. The value returned is restricted to values between [`--min-gas-price`](https://besu.hyperledger.org/en/stable/Reference/CLI/CLI-Syntax/#min-gas-price) and [`--api-gas-price-max`](https://besu.hyperledger.org/en/stable/Reference/CLI/CLI-Syntax/#api-gas-price-max). By default, 1000 Wei and 500GWei.

Use the [`--api-gas-price-blocks`](https://besu.hyperledger.org/en/stable/Reference/CLI/CLI-Syntax/#api-gas-price-blocks), [`--api-gas-price-percentile`](https://besu.hyperledger.org/en/stable/Reference/CLI/CLI-Syntax/#api-gas-price-percentile) , and [`--api-gas-price-max`](https://besu.hyperledger.org/en/stable/Reference/CLI/CLI-Syntax/#api-gas-price-max) command line options to configure the `eth_gasPrice` default values.

**Parameters**

None

**Returns**

`result`: _string_ - percentile gas unit price for the most recent blocks, in Wei, as a hexadecimal value

Example

curl HTTP

```
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_gasPrice","params":[],"id":53}' http://127.0.0.1:8545
```

wscat WSJSON resultcurl GraphQLGraphQLGraphQL result

#### `eth_getBalance`

Returns the account balance of the specified address.

**Parameters**

* `address`: _string_ - 20-byte account address from which to retrieve the balance
* `blockNumber`: _string_ - integer representing a block number or one of the string tags `latest`, `earliest`, or `pending`, as described in [Block Parameter](https://besu.hyperledger.org/en/stable/HowTo/Interact/APIs/Using-JSON-RPC-API/#block-parameter)

**Returns**

`result`: _string_ - current balance, in Wei, as a hexadecimal value

Example

curl HTTP

```
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_getBalance","params":["0xfe3b557e8fb62b89f4916b721be55ceb828dbd73", "latest"],"id":53}' http://127.0.0.1:8545
```

wscat WSJSON resultcurl GraphQLGraphQLGraphQL result

#### `eth_getBlockByHash`

Returns information about the block matching the specified block hash.

**Parameters**

* `hash`: _string_ - 32-byte hash of a block
* `verbose`: _boolean_ - if `true`, returns the full [transaction objects](https://besu.hyperledger.org/en/stable/Reference/API-Objects/#transaction-object); if `false`, returns the transaction hashes

**Returns**

`result`: _object_ - [block object](https://besu.hyperledger.org/en/stable/Reference/API-Objects/#block-object), or `null` when there is no block

Example

curl HTTP

```
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_getBlockByHash","params":["0xd5f1812548be429cbdc6376b29611fc49e06f1359758c4ceaaa3b393e2239f9c", false],"id":53}' http://127.0.0.1:8545
```

wscat WSJSON resultcurl GraphQLGraphQLGraphQL result

#### `eth_getBlockByNumber`

Returns information about the block matching the specified block number.

**Parameters**

* `blockNumber`: _string_ - integer representing a block number or one of the string tags `latest`, `earliest`, or `pending`, as described in [Block Parameter](https://besu.hyperledger.org/en/stable/HowTo/Interact/APIs/Using-JSON-RPC-API/#block-parameter)
* `verbose`: _boolean_ - if `true`, returns the full [transaction objects](https://besu.hyperledger.org/en/stable/Reference/API-Objects/#transaction-object); if `false`, returns only the hashes of the transactions.

**Returns**

`result`: _object_ - [block object](https://besu.hyperledger.org/en/stable/Reference/API-Objects/#block-object), or `null` when there is no block.

Example

curl HTTP

```
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_getBlockByNumber","params":["0x68B3", true],"id":1}' http://127.0.0.1:8545
```

wscat WSJSON resultcurl GraphQLGraphQLGraphQL result

#### `eth_getBlockTransactionCountByHash`

Returns the number of transactions in the block matching the specified block hash.

**Parameters**

`hash`: _string_ - 32-byte block hash

**Returns**

`result`: _number_ - integer representing the number of transactions in the specified block, or `null` if no matching block hash is found

Example

curl HTTP

```
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_getBlockTransactionCountByHash","params":["0xb903239f8543d04b5dc1ba6579132b143087c68db1b2168786408fcbce568238"],"id":53}' http://127.0.0.1:8545
```

wscat WSJSON resultcurl GraphQLGraphQLGraphQL result

#### `eth_getBlockTransactionCountByNumber`

Returns the number of transactions in a block matching the specified block number.

**Parameters**

`blockNumber`: _string_ - integer representing a block number or one of the string tags `latest`, `earliest`, or `pending`, as described in [Block Parameter](https://besu.hyperledger.org/en/stable/HowTo/Interact/APIs/Using-JSON-RPC-API/#block-parameter)

**Returns**

`result`: _string_ - integer representing the number of transactions in the specified block, or `null` if no matching block number is found

Example

curl HTTP

```
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_getBlockTransactionCountByNumber","params":["0xe8"],"id":51}' http://127.0.0.1:8545
```

wscat WSJSON resultcurl GraphQLGraphQLGraphQL result

#### `eth_getCode`

Returns the code of the smart contract at the specified address. Besu stores compiled smart contract code as a hexadecimal value.

**Parameters**

`address`: _string_ - 20-byte contract address

`blockNumber`: _string_ - integer representing a block number or one of the string tags `latest`, `earliest`, or `pending`, as described in [Block Parameter](https://besu.hyperledger.org/en/stable/HowTo/Interact/APIs/Using-JSON-RPC-API/#block-parameter)

**Returns**

`result`: _data_ - code stored at the specified address

Example

curl HTTP

```
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_getCode","params":["0xa50a51c09a5c451c52bb714527e1974b686d8e77", "latest"],"id":53}' http://127.0.0.1:8545
```

wscat WSJSON resultcurl GraphQLGraphQLGraphQL result

#### `eth_getFilterChanges`

Polls the specified filter and returns an array of changes that have occurred since the last poll.

**Parameters**

`filterId`: _string_ - filter ID

**Returns**

`result`: _array_ of _strings_ or _objects_ - if nothing changed since the last poll, an empty list; otherwise:

* For filters created with `eth_newBlockFilter`, returns block hashes.
* For filters created with `eth_newPendingTransactionFilter`, returns transaction hashes.
* For filters created with `eth_newFilter`, returns [log objects](https://besu.hyperledger.org/en/stable/Reference/API-Objects/#log-object).

Example

curl HTTP request

```
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_getFilterChanges","params":["0xf8bf5598d9e04fbe84523d42640b9b0e"],"id":1}' http://127.0.0.1:8545
```

wscat WS requestJSON result

#### `eth_getFilterLogs`

Returns an array of [logs](https://besu.hyperledger.org/en/stable/Concepts/Events-and-Logs/) for the specified filter.

Leave the [`--auto-log-bloom-caching-enabled`](https://besu.hyperledger.org/en/stable/Reference/CLI/CLI-Syntax/#auto-log-bloom-caching-enabled) command line option at the default value of `true` to improve log retrieval performance.

Note

`eth_getFilterLogs` is only used for filters created with `eth_newFilter`. To specify a filter object and get logs without creating a filter, use `eth_getLogs` .

**Parameters**

`filterId`: _string_ - filter ID

**Returns**

`result`: _array_ of _objects_ - list of [log objects](https://besu.hyperledger.org/en/stable/Reference/API-Objects/#log-object)

Example

curl HTTP request

```
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_getFilterLogs","params":["0x5ace5de3985749b6a1b2b0d3f3e1fb69"],"id":1}' http://127.0.0.1:8545
```

wscat WS requestJSON result

#### `eth_getLogs`

Returns an array of [logs](https://besu.hyperledger.org/en/stable/Concepts/Events-and-Logs/) matching a specified filter object.

Leave the [`--auto-log-bloom-caching-enabled`](https://besu.hyperledger.org/en/stable/Reference/CLI/CLI-Syntax/#auto-log-bloom-caching-enabled) command line option at the default value of `true` to improve log retrieval performance.

Attention

Using `eth_getLogs` to get the logs from a large range of blocks, especially an entire chain from its genesis block, can cause Besu to hang and never return a response. We recommend splitting one large query into multiple ones for better performance.

**Parameters**

`filterOptions`: _object_ - [filter options object](https://besu.hyperledger.org/en/stable/Reference/API-Objects/#filter-options-object)

**Returns**

`result`: _array_ of _objects_ - list of [log objects](https://besu.hyperledger.org/en/stable/Reference/API-Objects/#log-object)

Example

curl HTTP

```
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_getLogs","params":[{"fromBlock":"earliest", "toBlock":"latest", "address": "0x2e1f232a9439c3d459fceca0beef13acc8259dd8", "topics":[]}], "id":1}' http://127.0.0.1:8545
```

wscat WSJSON resultcurl GraphQLGraphQLGraphQL result

#### `eth_getMinerDataByBlockHash`

Returns miner data for the specified block.

**Parameters**

`hash`: _string_ - 32-byte block hash

**Returns**

`result`: _object_ - [miner data object](https://besu.hyperledger.org/en/stable/Reference/API-Objects/#miner-data-object)

Example

curl HTTP

```
curl -X POST --data '{"jsonrpc":"2.0","method": "eth_getMinerDataByBlockHash","params": ["0xbf137c3a7a1ebdfac21252765e5d7f40d115c2757e4a4abee929be88c624fdb7"],"id": 1}' http://127.0.0.1:8545
```

wscat WSJSON result

#### `eth_getMinerDataByBlockNumber`

Returns miner data for the specified block.

**Parameters**

`blockNumber`: _string_ - integer representing a block number or one of the string tags `latest`, `earliest`, or `pending`, as described in [Block Parameter](https://besu.hyperledger.org/en/stable/HowTo/Interact/APIs/Using-JSON-RPC-API/#block-parameter)

**Returns**

`result`: _object_ - [miner data object](https://besu.hyperledger.org/en/stable/Reference/API-Objects/#miner-data-object)

Example

curl HTTP

```
curl -X POST --data '{"jsonrpc":"2.0","method": "eth_getMinerDataByBlockNumber","params": ["0x7689D2"],"id": 1}' http://127.0.0.1:8545
```

wscat WSJSON result

#### `eth_getProof`

Returns the account and storage values of the specified account, including the Merkle proof.

The API allows IoT devices or mobile apps which are unable to run light clients to verify responses from untrusted sources, by using a trusted block hash.

**Parameters**

`address`: _string_ - 20-byte address of the account or contract

`keys`: _array_ of _strings_ - list of 32-byte storage keys to generate proofs for

`blockNumber`: _string_ - integer representing a block number or one of the string tags `latest`, `earliest`, or `pending`, as described in [Block Parameter](https://besu.hyperledger.org/en/stable/HowTo/Interact/APIs/Using-JSON-RPC-API/#block-parameter)

**Returns**

`result`: _object_ - account details object with the following fields:

* `balance`: _string_ - account balance
* `codeHash`: _string_ - 32-byte hash of the account code
* `nonce`: _string_ - number of transactions sent from the account
* `storageHash`: _string_ - 32-byte SHA3 of the `storageRoot`
* `accountProof`: _array_ of _strings_ - list of RLP-encoded Merkle tree nodes, starting with the `stateRoot`
* `storageProof`: _array_ of _objects_ - list of storage entry objects with the following fields:
  * `key`: _string_ - storage key
  * `value`: _string_ - storage value
  * `proof`: _array_ of _strings_ - list of RLP-encoded Merkle tree nodes, starting with the `storageHash`

Example

curl HTTP

```
curl -X POST --data '{"jsonrpc":"2.0","method": "eth_getProof","params": [
"0a8156e7ee392d885d10eaa86afd0e323afdcd95", ["0x0000000000000000000000000000000000000000000000000000000000000347"], "latest"],"id": 1}' http://127.0.0.1:8545
```

wscat WSJSON result

#### `eth_getQuorumPayload`

When using [GoQuorum-compatible privacy](https://besu.hyperledger.org/en/stable/HowTo/Use-Privacy/Use-GoQuorum-compatible-privacy/), returns the [unencrypted payload from Tessera](https://docs.tessera.consensys.net/Concepts/Transaction-manager/#private-transaction-flow).

**Parameters**

`id`: _string_ - the generated SHA3-512 hash of the encrypted payload from Tessera, in hex (the `input` value in the transaction)

**Returns**

`result`: _string_ - unencrypted transaction payload in hex

Example

curl HTTP

```
curl -X POST http://127.0.0.1:22000 --data '{"jsonrpc":"2.0","method":"eth_getQuorumPayload","params":["0x5e902fa2af51b186468df6ffc21fd2c26235f4959bf900fc48c17dc1774d86d046c0e466230225845ddf2cf98f23ede5221c935aac27476e77b16604024bade0"],"id":67}'
```

wscat WSJSON result

#### `eth_getStorageAt`

Returns the value of a storage position at a specified address.

**Parameters**

`address`: _string_ - 20-byte storage address

`index`: _string_ - integer index of the storage position

`blockNumber`: _string_ - integer representing a block number or one of the string tags `latest`, `earliest`, or `pending`, as described in [Block Parameter](https://besu.hyperledger.org/en/stable/HowTo/Interact/APIs/Using-JSON-RPC-API/#block-parameter)

**Returns**

`result` : _string_ - value at the specified storage position

Example

Calculating the correct position depends on the storage you want to retrieve.

curl HTTP

```
curl -X POST --data '{"jsonrpc":"2.0","method": "eth_getStorageAt","params": ["0x‭3B3F3E‬","0x0","latest"],"id": 53}' http://127.0.0.1:8545
```

wscat WSJSON resultcurl GraphQLGraphQLGraphQL result

#### `eth_getTransactionByBlockHashAndIndex`

Returns transaction information for the specified block hash and transaction index position.

**Parameters**

`block`: _string_ - 32-byte hash of a block

`index`: _string_ - integer representing the transaction index position

**Returns**

`result`: _object_ - [transaction object](https://besu.hyperledger.org/en/stable/Reference/API-Objects/#transaction-object), or `null` when there is no transaction

Example

curl HTTP

```
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_getTransactionByBlockHashAndIndex","params":["0xbf137c3a7a1ebdfac21252765e5d7f40d115c2757e4a4abee929be88c624fdb7", "0x2"], "id":1}' http://127.0.0.1:8545
```

wscat WSJSON resultcurl GraphQLGraphQLGraphQL result

#### `eth_getTransactionByBlockNumberAndIndex`

Returns transaction information for the specified block number and transaction index position.

**Parameters**

`blockNumber`: _string_ - integer representing a block number or one of the string tags `latest`, `earliest`, or `pending`, as described in [Block Parameter](https://besu.hyperledger.org/en/stable/HowTo/Interact/APIs/Using-JSON-RPC-API/#block-parameter)

`index`: _string_ - transaction index position

**Returns**

`result`: _object_ - [transaction object](https://besu.hyperledger.org/en/stable/Reference/API-Objects/#transaction-object), or `null` when there is no transaction

Example

This request returns the third transaction in the 82990 block on the Ropsten testnet. You can also view this [block](https://ropsten.etherscan.io/txs?block=82990) and [transaction](https://ropsten.etherscan.io/tx/0xfc766a71c406950d4a4955a340a092626c35083c64c7be907060368a5e6811d6) on Etherscan.

curl HTTP

```
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_getTransactionByBlockNumberAndIndex","params":["82990", "0x2"], "id":1}' http://127.0.0.1:8545
```

wscat WSJSON resultcurl GraphQLGraphQLGraphQL result

#### `eth_getTransactionByHash`

Returns transaction information for the specified transaction hash.

**Parameters**

`transaction`: _string_ - 32-byte transaction hash

**Returns**

`result`: _object_ - [transaction object](https://besu.hyperledger.org/en/stable/Reference/API-Objects/#transaction-object), or `null` when there is no transaction

Example

curl HTTP

```
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_getTransactionByHash","params":["0xa52be92809541220ee0aaaede6047d9a6c5d0cd96a517c854d944ee70a0ebb44"],"id":53}' http://127.0.0.1:8545
```

wscat WSJSON resultcurl GraphQLGraphQLGraphQL result

#### `eth_getTransactionCount`

Returns the number of transactions sent from a specified address. Use the `pending` tag to get the next account nonce not used by any pending transactions.

**Parameters**

`address`: _string_ - 20-byte account address

`blockNumber`: _string_ - integer representing a block number or one of the string tags `latest`, `earliest`, or `pending`, as described in [Block Parameter](https://besu.hyperledger.org/en/stable/HowTo/Interact/APIs/Using-JSON-RPC-API/#block-parameter)

**Returns**

`result`: _string_ - integer representing the number of transactions sent from the specified address

Example

curl HTTP

```
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_getTransactionCount","params":["0xc94770007dda54cF92009BFF0dE90c06F603a09f","latest"],"id":1}' http://127.0.0.1:8545
```

wscat WSJSON resultcurl GraphQLGraphQLGraphQL result

#### `eth_getTransactionReceipt`

Returns the receipt of a transaction by transaction hash. Receipts for pending transactions are not available.

If you enabled [revert reason](https://besu.hyperledger.org/en/stable/HowTo/Send-Transactions/Revert-Reason/), the receipt includes available revert reasons in the response.

**Parameters**

`transaction`: _string_ - 32-byte hash of a transaction

**Returns**

`result`: _object_ - [transaction receipt object](https://besu.hyperledger.org/en/stable/Reference/API-Objects/#transaction-receipt-object), or `null` when there is no receipt

Example

curl HTTP

```
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_getTransactionReceipt","params":["0x504ce587a65bdbdb6414a0c6c16d86a04dd79bfcc4f2950eec9634b30ce5370f"],"id":53}' http://127.0.0.1:8545
```

wscat WSJSON resultcurl GraphQLGraphQLGraphQL result

#### `eth_getUncleByBlockHashAndIndex`

Returns uncle specified by block hash and index.

**Parameters**

`block`: _string_ - 32-byte block hash

`uncleIndex`: _string_ - index of the uncle

**Returns**

`result`: _object_ - [block object](https://besu.hyperledger.org/en/stable/Reference/API-Objects/#block-object)

Note

Uncles do not contain individual transactions.

Example

curl HTTP

```
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_getUncleByBlockHashAndIndex","params":["0xc48fb64230a82f65a08e7280bd8745e7fea87bc7c206309dee32209fe9a985f7", "0x0"],"id":1}' http://127.0.0.1:8545
```

wscat WSJSON resultcurl GraphQLGraphQLGraphQL result

#### `eth_getUncleByBlockNumberAndIndex`

Returns uncle specified by block number and index.

**Parameters**

* `blockNumber`: _string_ - integer representing a block number or one of the string tags `latest`, `earliest`, or `pending`, as described in [Block Parameter](https://besu.hyperledger.org/en/stable/HowTo/Interact/APIs/Using-JSON-RPC-API/#block-parameter)
* `uncleIndex`: _string_ - index of the uncle

**Returns**

`result`: _object_ - [block object](https://besu.hyperledger.org/en/stable/Reference/API-Objects/#block-object)

Note

Uncles do not contain individual transactions.

Example

curl HTTP

```
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_getUncleByBlockNumberAndIndex","params":["0x7689D2", "0x0"],"id":1}' http://127.0.0.1:8545
```

wscat WSJSON resultcurl GraphQLGraphQLGraphQL result

#### `eth_getUncleCountByBlockHash`

Returns the number of uncles in a block from a block matching the given block hash.

**Parameters**

`block`: _string_ - 32-byte block hash

**Returns**

`result`: _string_ - integer representing the number of uncles in the specified block

Example

curl HTTP

```
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_getUncleCountByBlockHash","params":["0xb903239f8543d04b5dc1ba6579132b143087c68db1b2168786408fcbce568238"],"id":1}' http://127.0.0.1:8545
```

wscat WSJSON resultcurl GraphQLGraphQLGraphQL result

#### `eth_getUncleCountByBlockNumber`

Returns the number of uncles in a block matching the specified block number.

**Parameters**

`blockNumber`: _string_ - integer representing a block number or one of the string tags `latest`, `earliest`, or `pending`, as described in [Block Parameter](https://besu.hyperledger.org/en/stable/HowTo/Interact/APIs/Using-JSON-RPC-API/#block-parameter)

**Returns**

`result`: _string_ - integer representing the number of uncles in the specified block

Example

curl HTTP

```
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_getUncleCountByBlockNumber","params":["0xe8"],"id":1}' http://127.0.0.1:8545
```

wscat WSJSON resultcurl GraphQLGraphQLGraphQL result

#### `eth_getWork`

Returns the hash of the current block, the seed hash, and the required target boundary condition.

**Parameters**

None

**Returns**

`result`: _array_ of _strings_ - array with the following items:

* `header`: _string_ - 32-byte hash of the current block header (PoW-hash)
* `seed`: _string_ - 32-byte seed hash used for the DAG
* `target`: _string_ - 32-byte required target boundary condition: 2^256 / difficulty
* `blockNumber`: _string_ - hexadecimal integer representing the current block number

Example

curl HTTP request

```
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_getWork","params":[],"id":1}' http://127.0.0.1:8545
```

wscat WS requestJSON result

#### `eth_hashrate`

Returns the number of hashes per second with which the node is mining.

When the stratum server is enabled, this method returns the cumulative hashrate of all sealers reporting their hashrate.

**Parameters**

None

**Returns**

`result`: _string_ - number of hashes per second

Example

curl HTTP request

```
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_hashrate","params":[],"id":1}' http://127.0.0.1:8545
```

wscat WS requestJSON result

#### `eth_mining`

Whether the client is actively mining new blocks. Besu pauses mining while the client synchronizes with the network regardless of command settings or methods called.

**Parameters**

None

**Returns**

`result`: _boolean_ - indicates if the client is actively mining new blocks

Example

curl HTTP request

```
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_mining","params":[],"id":53}' http://127.0.0.1:8545
```

wscat WS requestJSON result

#### `eth_newBlockFilter`

Creates a filter to retrieve new block hashes. To poll for new blocks, use [`eth_getFilterChanges`](https://besu.hyperledger.org/en/stable/Reference/API-Methods/#eth\_getfilterchanges).

**Parameters**

None

**Returns**

`result`: _string_ - filter ID

Example

curl HTTP request

```
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_newBlockFilter","params":[],"id":1}' http://127.0.0.1:8545
```

wscat WS requestJSON result

#### `eth_newFilter`

Creates a [log filter](https://besu.hyperledger.org/en/stable/Concepts/Events-and-Logs/). To poll for logs associated with the created filter, use [`eth_getFilterChanges`](https://besu.hyperledger.org/en/stable/Reference/API-Methods/#eth\_getfilterchanges). To get all logs associated with the filter, use [`eth_getFilterLogs`](https://besu.hyperledger.org/en/stable/Reference/API-Methods/#eth\_getfilterlogs).

**Parameters**

`filterOptions`: _object_ - [filter options object](https://besu.hyperledger.org/en/stable/Reference/API-Objects/#filter-options-object)

Note

`fromBlock` and `toBlock` in the filter options object default to `latest`.

**Returns**

`result`: _string_ - filter ID

Example

curl HTTP request

```
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_newFilter","params":[{"fromBlock":"earliest", "toBlock":"latest", "topics":[]}],"id":1}' http://127.0.0.1:8545
```

wscat WS requestJSON result

#### `eth_newPendingTransactionFilter`

Creates a filter to retrieve new pending transactions hashes. To poll for new pending transactions, use [`eth_getFilterChanges`](https://besu.hyperledger.org/en/stable/Reference/API-Methods/#eth\_getfilterchanges).

**Parameters**

None

**Returns**

`result`: _string_ - filter ID

Example

curl HTTP request

```
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_newPendingTransactionFilter","params":[],"id":1}' http://127.0.0.1:8545
```

wscat WS requestJSON result

#### `eth_protocolVersion`

Returns current Ethereum protocol version.

**Parameters**

None

**Returns**

`result`: _string_ - Ethereum protocol version

Example

curl HTTP

```
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_protocolVersion","params":[],"id":1}' http://127.0.0.1:8545
```

wscat WSJSON resultcurl GraphQLGraphQLGraphQL result

#### `eth_sendRawTransaction`

Sends a [signed transaction](https://besu.hyperledger.org/en/stable/HowTo/Send-Transactions/Transactions/). A transaction can send ether, deploy a contract, or interact with a contract. Set the maximum transaction fee for transactions using the [`--rpc-tx-feecap`](https://besu.hyperledger.org/en/stable/Reference/CLI/CLI-Syntax/#rpc-tx-feecap) CLI option.

You can interact with contracts using `eth_sendRawTransaction` or [`eth_call`](https://besu.hyperledger.org/en/stable/Reference/API-Methods/#eth\_call).

To avoid exposing your private key, create signed transactions offline and send the signed transaction data using `eth_sendRawTransaction`.

Important

Besu does not implement [`eth_sendTransaction`](https://besu.hyperledger.org/en/stable/HowTo/Send-Transactions/Account-Management/).

[EthSigner](https://docs.ethsigner.consensys.net) provides transaction signing and implements [`eth_sendTransaction`](https://docs.ethsigner.consensys.net/Using-EthSigner/Using-EthSigner/#eth\_sendtransaction).

**Parameters**

Note

[Creating and Sending Transactions](https://besu.hyperledger.org/en/stable/HowTo/Send-Transactions/Transactions/) includes examples of creating signed transactions using the [web3.js](https://github.com/ethereum/web3.js/) library.

**Returns**

`result`: _string_ - 32-byte transaction hash

Example

curl HTTP

```
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_sendRawTransaction","params":["0xf869018203e882520894f17f52151ebef6c7334fad080c5704d77216b732881bc16d674ec80000801ba02da1c48b670996dcb1f447ef9ef00b33033c48a4fe938f420bec3e56bfd24071a062e0aa78a81bf0290afbc3a9d8e9a068e6d74caa66c5e0fa8a46deaae96b0833"],"id":1}' http://127.0.0.1:8545
```

wscat WSJSON resultcurl GraphQLGraphQLGraphQL result

#### `eth_submitHashrate`

Submits the mining hashrate. This is used by mining software such as [Ethminer](https://github.com/ethereum-mining/ethminer).

**Parameters**

* `hashrate`: _string_ - 32-byte hexadecimal string representation of the hashrate
* `id`: _string_ - 32-byte random hexadecimal ID identifying the client

**Returns**

`result`: _boolean_ - indicates if submission is successful

Example

curl HTTP request

```
curl -X POST --data '{"jsonrpc":"2.0", "method":"eth_submitHashrate", "params":["0x0000000000000000000000000000000000000000000000000000000000500000", "0x59daa26581d0acd1fce254fb7e85952f4c09d0915afd33d3886cd914bc7d283c"],"id":1}' http://127.0.0.1:8545
```

wscat WS requestJSON result

#### `eth_submitWork`

Submits a proof of work (Ethash) solution. This is used by mining software such as [Ethminer](https://github.com/ethereum-mining/ethminer).

**Parameters**

* `nonce`: _string_ - retrieved 8-byte nonce
* `header`: _string_ - 32-byte hash of the block header (PoW-hash)
* `digest`: _string_ - 32-bytes mix digest

**Returns**

`result`: _boolean_ - indicates if the provided solution is valid

Example

curl HTTP request

```
curl -X POST --data '{"jsonrpc":"2.0", "method":"eth_submitWork", "params":["0x0000000000000001", "0x1234567890abcdef1234567890abcdef1234567890abcdef1234567890abcdef", "0xD1GE5700000000000000000000000000D1GE5700000000000000000000000000"],"id":1}' http://127.0.0.1:8545
```

wscat WS requestJSON result

#### `eth_syncing`

Returns an object with data about the synchronization status, or `false` if not synchronizing.

Note

Once the node reaches the head of the chain, `eth_syncing` returns false, indicating that there is no active syncing target.

**Parameters**

None

**Returns**

`result`: _object_ or _boolean_ - synchronization status data object with the following fields, or `false` if not synchronizing:

* `startingBlock`: _string_ - index of the highest block on the blockchain when the network synchronization starts
* `currentBlock`: _string_ - index of the latest block (also known as the best block) for the current node (this is the same index that [`eth_blockNumber`](https://besu.hyperledger.org/en/stable/Reference/API-Methods/#eth\_blocknumber) returns.)
* `highestBlock`: _string_ - index of the highest known block in the peer network (that is, the highest block so far discovered among peer nodes. This is the same value as `currentBlock` if the current node has no peers.)
* `pulledStates`: _string_ - if fast synchronizing, the number of state entries fetched so far, or `null` if this is not known or not relevant (if full synchronizing or fully synchronized, this field is not returned.)
* `knownStates`: _string_ - if fast synchronizing, the number of states the node knows of so far, or `null` if this is not known or not relevant (if full synchronizing or fully synchronized, this field is not returned.)

Example

curl HTTP

```
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_syncing","params":[],"id":51}' http://127.0.0.1:8545
```

wscat WSJSON resultcurl GraphQLGraphQLGraphQL result

#### `eth_uninstallFilter`

Uninstalls a filter with the specified ID. When a filter is no longer required, call this method.

Filters time out when not requested by [`eth_getFilterChanges`](https://besu.hyperledger.org/en/stable/Reference/API-Methods/#eth\_getfilterchanges) or [`eth_getFilterLogs`](https://besu.hyperledger.org/en/stable/Reference/API-Methods/#eth\_getfilterlogs) for 10 minutes.

**Parameters**

`filterId`: _string_ - filter ID

**Returns**

`result`: _boolean_ - indicates if the filter is successfully uninstalled

Example

curl HTTP request

```
curl -X POST --data '{"jsonrpc":"2.0","method":"eth_uninstallFilter","params":["0x70355a0b574b437eaa19fe95adfedc0a"],"id":1}' http://127.0.0.1:8545
```

wscat WS requestJSON result

### `IBFT` 2.0 methods

The `IBFT` API methods provide access to the [IBFT 2.0](https://besu.hyperledger.org/en/stable/HowTo/Configure/Consensus-Protocols/IBFT/) consensus engine.

Note

The `IBFT` API methods are not enabled by default for JSON-RPC. To enable the `IBFT` API methods, use the [`--rpc-http-api`](https://besu.hyperledger.org/en/stable/Reference/CLI/CLI-Syntax/#rpc-http-api) or [`--rpc-ws-api`](https://besu.hyperledger.org/en/stable/Reference/CLI/CLI-Syntax/#rpc-ws-api) options.

#### `ibft_discardValidatorVote`

Discards a proposal to [add or remove a validator](https://besu.hyperledger.org/en/stable/HowTo/Configure/Consensus-Protocols/Add-Validators/#ibft-20) with the specified address.

**Parameters**

`address`: _string_ - 20-byte address of proposed validator

**Returns**

`result`: _boolean_ - indicates if the proposal is discarded

Example

curl HTTP request

```
curl -X POST --data '{"jsonrpc":"2.0","method":"ibft_discardValidatorVote","params":["0xef1bfb6a12794615c9b0b5a21e6741f01e570185"], "id":1}' http://127.0.0.1:8545
```

wscat WS requestJSON result

#### `ibft_getPendingVotes`

Returns [votes](https://besu.hyperledger.org/en/stable/HowTo/Configure/Consensus-Protocols/IBFT/#adding-and-removing-validators) cast in the current [epoch](https://besu.hyperledger.org/en/stable/HowTo/Configure/Consensus-Protocols/IBFT/#genesis-file).

**Parameters**

None

**Returns**

`result`: _map_ of _strings_ to _booleans_ - map of account addresses to corresponding boolean values indicating the vote for each account; if `true`, the vote is to add a validator. If `false`, the proposal is to remove a validator.

Example

curl HTTP request

```
curl -X POST --data '{"jsonrpc":"2.0","method":"ibft_getPendingVotes","params":[], "id":1}' http://127.0.0.1:8545
```

wscat WS requestJSON result

#### `ibft_getSignerMetrics`

Provides the following validator metrics for the specified range:

* Number of blocks from each validator
* Block number of the last block proposed by each validator (if any proposed in the specified range)
* All validators present in the last block of the range

**Parameters**

* `fromBlockNumber`: _string_ - integer representing a block number or the string tag `earliest` as described in [Block Parameter](https://besu.hyperledger.org/en/stable/HowTo/Interact/APIs/Using-JSON-RPC-API/#block-parameter)
* `toBlockNumber`: _string_ - integer representing a block number or one of the string tags `latest` or `pending`, as described in [Block Parameter](https://besu.hyperledger.org/en/stable/HowTo/Interact/APIs/Using-JSON-RPC-API/#block-parameter)

If you specify:

* No parameters, the call provides metrics for the last 100 blocks, or all blocks if there are less than 100 blocks.
* Only the first parameter, the call provides metrics for all blocks from the block specified to the latest block.

**Returns**

`result`: _array_ of _objects_ - list of validator objects

Note

The proposer of the genesis block has address `0x0000000000000000000000000000000000000000`.

Example

curl HTTP

```
curl -X POST --data '{"jsonrpc":"2.0","method":"ibft_getSignerMetrics","params":["1", "100"], "id":1}' http://127.0.0.1:8545
```

wscat WSJSON result

#### `ibft_getValidatorsByBlockHash`

Lists the validators defined in the specified block.

**Parameters**

`block`: _string_ - 32-byte block hash

**Returns**

`result`: _array_ of _strings_ - list of validator addresses

Example

curl HTTP request

```
curl -X POST --data '{"jsonrpc":"2.0","method":"ibft_getValidatorsByBlockHash","params":["0xbae7d3feafd743343b9a4c578cab5e5d65eb735f6855fb845c00cab356331256"], "id":1}' http://127.0.0.1:8545
```

wscat WS requestJSON result

#### `ibft_getValidatorsByBlockNumber`

Lists the validators defined in the specified block.

**Parameters**

* `blockNumber`: _string_ - integer representing a block number or one of the string tags `latest`, `earliest`, or `pending`, as described in [Block Parameter](https://besu.hyperledger.org/en/stable/HowTo/Interact/APIs/Using-JSON-RPC-API/#block-parameter)

**Returns**

`result`: _array_ of _strings_ - list of validator addresses

Example

curl HTTP request

```
curl -X POST --data '{"jsonrpc":"2.0","method":"ibft_getValidatorsByBlockNumber","params":["latest"], "id":1}' http://127.0.0.1:8545
```

wscat WS requestJSON result

#### `ibft_proposeValidatorVote`

Proposes to [add or remove a validator](https://besu.hyperledger.org/en/stable/HowTo/Configure/Consensus-Protocols/Add-Validators/#ibft-20) with the specified address.

**Parameters**

* `address`: _string_ - account address
* `proposal`: _boolean_ - `true` to propose adding validator or `false` to propose removing validator

**Returns**

`result`: _boolean_ - `true`

Example

curl HTTP request

```
curl -X POST --data '{"jsonrpc":"2.0","method":"ibft_proposeValidatorVote","params":["42d4287eac8078828cf5f3486cfe601a275a49a5",true], "id":1}' http://127.0.0.1:8545
```

wscat WS requestJSON result

### `MINER` methods

The `MINER` API methods allow you to control the node’s mining operation.

Note

The `MINER` API methods are not enabled by default for JSON-RPC. To enable the `MINER` API methods, use the [`--rpc-http-api`](https://besu.hyperledger.org/en/stable/Reference/CLI/CLI-Syntax/#rpc-http-api) or [`--rpc-ws-api`](https://besu.hyperledger.org/en/stable/Reference/CLI/CLI-Syntax/#rpc-ws-api) options.

#### `miner_changeTargetGasLimit`

Updates the target gas limit set using the [`--target-gas-limit`](https://besu.hyperledger.org/en/stable/Reference/CLI/CLI-Syntax/#target-gas-limit) command line option.

**Parameters**

`gasPrice`: _number_ - target gas price in Wei

**Returns**

`result`: _string_ - `Success` or `error`

Example

curl HTTP request

```
curl -X POST --data '{"jsonrpc":"2.0","method":"miner_changeTargetGasLimit","params":[800000], "id":1}' http://127.0.0.1:8545
```

wscat WS requestJSON result

#### `miner_start`

Starts the mining process. To start mining, you must first specify a miner coinbase using the [`--miner-coinbase`](https://besu.hyperledger.org/en/stable/Reference/CLI/CLI-Syntax/#miner-coinbase) command line option.

**Parameters**

None

**Returns**

`result`: _boolean_ - `true` if mining starts, or if the node is already mining

Example

curl HTTP request

```
curl -X POST --data '{"jsonrpc":"2.0","method":"miner_start","params":[],"id":1}' http://127.0.0.1:8545
```

wscat WS requestJSON result

#### `miner_stop`

Stops the mining process on the client.

**Parameters**

None

**Returns**

`result`: _boolean_ - `true` if mining stops, or if the node is not mining

Example

curl HTTP request

```
curl -X POST --data '{"jsonrpc":"2.0","method":"miner_stop","params":[],"id":1}' http://127.0.0.1:8545
```

wscat WS requestJSON result

### `NET` methods

The `NET` API methods provide network-related information.

#### `net_enode`

Returns the [enode URL](https://besu.hyperledger.org/en/stable/Concepts/Node-Keys/#enode-url).

**Parameters**

None

**Returns**

`result`: _string_ - [enode URL](https://besu.hyperledger.org/en/stable/Concepts/Node-Keys/#enode-url) of the node

Example

curl HTTP request

```
curl -X POST --data '{"jsonrpc":"2.0","method":"net_enode","params":[],"id":1}' http://127.0.0.1:8545
```

wscat WS requestJSON result

#### `net_listening`

Whether the client is actively listening for network connections.

**Parameters**

None

**Returns**

`result`: _boolean_ - indicates if the client is actively listening for network connections

Example

curl HTTP request

```
curl -X POST --data '{"jsonrpc":"2.0","method":"net_listening","params":[],"id":53}' http://127.0.0.1:8545
```

wscat WS requestJSON result

#### `net_peerCount`

Returns the number of peers currently connected to the client.

**Parameters**

None

**Returns**

`result`: _string_ - number of connected peers in hexadecimal

Example

curl HTTP request

```
curl -X POST --data '{"jsonrpc":"2.0","method":"net_peerCount","params":[],"id":53}' http://127.0.0.1:8545
```

wscat WS requestJSON result

#### `net_services`

Returns enabled services (for example, `jsonrpc`) and the host and port for each service.

Note

The [`--nat-method`](https://besu.hyperledger.org/en/stable/Reference/CLI/CLI-Syntax/#nat-method) setting affects the JSON-RPC and P2P host and port values, but not the metrics host and port values.

**Parameters**

None

**Returns**

`result`: _object_ - enabled services

Example

curl HTTP request

```
curl -X POST --data '{"jsonrpc":"2.0","method":"net_services","params":[],"id":1}' http://127.0.0.1:8545
```

wscat WS requestJSON result

#### `net_version`

Returns the [network ID](https://besu.hyperledger.org/en/stable/Concepts/NetworkID-And-ChainID/).

**Parameters**

None

**Returns**

`result`: _string_ - current network ID

| Network ID | Chain | Network | Description                   |
| ---------- | ----- | ------- | ----------------------------- |
| `1`        | ETH   | Mainnet | Main Ethereum network         |
| `3`        | ETH   | Ropsten | PoW test network              |
| `4`        | ETH   | Rinkeby | PoA test network using Clique |
| `5`        | ETH   | Goerli  | PoA test network using Clique |
| `2018`     | ETH   | Dev     | PoW development network       |
| `1`        | ETC   | Classic | Main Ethereum Classic network |
| `7`        | ETC   | Mordor  | PoW test network              |
| `6`        | ETC   | Kotti   | PoA test network using Clique |
| `212`      | ETC   | Astor   | PoW test network              |

Note

For almost all networks network ID and chain ID are the same.

The only networks in the table above with different network and chain IDs are Classic with a chain ID of `61` and Mordor with a chain ID of `63`.

Example

curl HTTP request

```
curl -X POST --data '{"jsonrpc":"2.0","method":"net_version","params":[],"id":53}' http://127.0.0.1:8545
```

wscat WS requestJSON result for MainnetJSON result for Ropsten

### `PERM` (Permissioning) methods

The `PERM` API methods provide permissioning functionality. Use these methods for [local permissioning](https://besu.hyperledger.org/en/stable/HowTo/Limit-Access/Local-Permissioning/) only.

Important

The `PERM` API methods are not enabled by default for JSON-RPC. To enable the `PERM` API methods, use the [`--rpc-http-api`](https://besu.hyperledger.org/en/stable/Reference/CLI/CLI-Syntax/#rpc-http-api) or [`--rpc-ws-api`](https://besu.hyperledger.org/en/stable/Reference/CLI/CLI-Syntax/#rpc-ws-api) CLI options.

#### `perm_addAccountsToAllowlist`

Adds accounts (participants) to the [accounts permission list](https://besu.hyperledger.org/en/stable/HowTo/Limit-Access/Local-Permissioning/#account-permissioning).

**Parameters**

`addresses`: _array_ of _strings_ - list of account addresses

Note

The parameters list contains a list which is why the account addresses are enclosed by double square brackets.

**Returns**

`result`: _string_ - `Success` or `error` (errors include attempting to add accounts already on the allowlist and including invalid account addresses.)

Example

curl HTTP request

```
curl -X POST --data '{"jsonrpc":"2.0","method":"perm_addAccountsToAllowlist","params":[["0xb9b81ee349c3807e46bc71aa2632203c5b462032", "0xb9b81ee349c3807e46bc71aa2632203c5b462034"]], "id":1}' http://127.0.0.1:8545
```

wscat WS requestJSON result

#### `perm_addNodesToAllowlist`

Adds nodes to the [nodes allowlist](https://besu.hyperledger.org/en/stable/HowTo/Limit-Access/Local-Permissioning/#node-allowlisting).

To use domain names in enode URLs, ensure you [enable DNS support](https://besu.hyperledger.org/en/stable/Concepts/Node-Keys/#domain-name-support) to avoid receiving a `request contains an invalid node` error.

Warning

Enode URL domain name support is an experimental feature.

**Parameters**

`enodes`: _array_ of _strings_ - list of [enode URLs](https://besu.hyperledger.org/en/stable/Concepts/Node-Keys/#enode-url)

Note

The parameters list contains a list which is why the enode URLs are enclosed by double square brackets.

**Returns**

`result`: _string_ - `Success` or `error`; errors include attempting to add nodes already on the allowlist or including invalid enode URLs.

Example

curl HTTP request

```
curl -X POST --data '{"jsonrpc":"2.0","method":"perm_addNodesToAllowlist","params":[["enode://7e4ef30e9ec683f26ad76ffca5b5148fa7a6575f4cfad4eb0f52f9c3d8335f4a9b6f9e66fcc73ef95ed7a2a52784d4f372e7750ac8ae0b544309a5b391a23dd7@127.0.0.1:30303","enode://2feb33b3c6c4a8f77d84a5ce44954e83e5f163e7a65f7f7a7fec499ceb0ddd76a46ef635408c513d64c076470eac86b7f2c8ae4fcd112cb28ce82c0d64ec2c94@127.0.0.1:30304"]], "id":1}' http://127.0.0.1:8545
```

wscat WS requestJSON result

#### `perm_getAccountsAllowlist`

Lists accounts (participants) in the [accounts permissions list](https://besu.hyperledger.org/en/stable/HowTo/Limit-Access/Local-Permissioning/#account-permissioning).

**Parameters**

None

**Returns**

`result`: _array_ of _strings_ - list of accounts (participants) in the accounts allowlist

Example

curl HTTP request

```
curl -X POST --data '{"jsonrpc":"2.0","method":"perm_getAccountsAllowlist","params":[], "id":1}' http://127.0.0.1:8545
```

wscat WS requestJSON result

#### `perm_getNodesAllowlist`

Lists nodes in the [nodes allowlist](https://besu.hyperledger.org/en/stable/HowTo/Limit-Access/Local-Permissioning/#node-allowlisting).

**Parameters**

None

**Returns**

`result`: _array_ of _strings_ - [enode URLs](https://besu.hyperledger.org/en/stable/Concepts/Node-Keys/#enode-url) of nodes in the nodes allowlist

Example

curl HTTP request

```
curl -X POST --data '{"jsonrpc":"2.0","method":"perm_getNodesAllowlist","params":[], "id":1}' http://127.0.0.1:8545
```

wscat WS requestJSON result

#### `perm_reloadPermissionsFromFile`

Reloads the accounts and nodes allowlists from the [permissions configuration file](https://besu.hyperledger.org/en/stable/HowTo/Limit-Access/Local-Permissioning/#permissions-configuration-file).

**Parameters**

None

**Returns**

`result`: _string_ - `Success`, or `error` if the permissions configuration file is not valid

Example

curl HTTP request

```
curl -X POST --data '{"jsonrpc":"2.0","method":"perm_reloadPermissionsFromFile","params":[], "id":1}' http://127.0.0.1:8545
```

wscat WS requestJSON result

#### `perm_removeAccountsFromAllowlist`

Removes accounts (participants) from the [accounts permissions list](https://besu.hyperledger.org/en/stable/HowTo/Limit-Access/Local-Permissioning/#account-permissioning).

**Parameters**

`addresses`: _array_ of _strings_ - list of account addresses

Note

The parameters list contains a list which is why the account addresses are enclosed by double square brackets.

**Returns**

`result`: _string_ - `Success` or `error` (errors include attempting to remove accounts not on the allowlist and including invalid account addresses.)

Example

curl HTTP request

```
curl -X POST --data '{"jsonrpc":"2.0","method":"perm_removeAccountsFromAllowlist","params":[["0xb9b81ee349c3807e46bc71aa2632203c5b462032", "0xb9b81ee349c3807e46bc71aa2632203c5b462034"]], "id":1}' http://127.0.0.1:8545
```

wscat WS requestJSON result

#### `perm_removeNodesFromAllowlist`

Removes nodes from the [nodes allowlist](https://besu.hyperledger.org/en/stable/HowTo/Limit-Access/Local-Permissioning/#node-allowlisting).

**Parameters**

`enodes`: _array_ of _strings_ - list of [enode URLs](https://besu.hyperledger.org/en/stable/Concepts/Node-Keys/#enode-url)

Note

The parameters list contains a list which is why the enode URLs are enclosed by double square brackets.

**Returns**

`result`: _string_ - `Success` or `error` (errors include attempting to remove nodes not on the allowlist and including invalid enode URLs.)

Example

curl HTTP request

```
curl -X POST --data '{"jsonrpc":"2.0","method":"perm_removeNodesFromAllowlist","params":[["enode://7e4ef30e9ec683f26ad76ffca5b5148fa7a6575f4cfad4eb0f52f9c3d8335f4a9b6f9e66fcc73ef95ed7a2a52784d4f372e7750ac8ae0b544309a5b391a23dd7@127.0.0.1:30303","enode://2feb33b3c6c4a8f77d84a5ce44954e83e5f163e7a65f7f7a7fec499ceb0ddd76a46ef635408c513d64c076470eac86b7f2c8ae4fcd112cb28ce82c0d64ec2c94@127.0.0.1:30304"]], "id":1}' http://127.0.0.1:8545
```

wscat WS requestJSON result

### `PLUGINS` methods

The `PLUGINS` API methods provide plugin-related functionality.

Note

The `PLUGINS` API methods are not enabled by default for JSON-RPC. To enable the `PLUGINS` API methods, use the [`--rpc-http-api`](https://besu.hyperledger.org/en/stable/Reference/CLI/CLI-Syntax/#rpc-http-api) or [`--rpc-ws-api`](https://besu.hyperledger.org/en/stable/Reference/CLI/CLI-Syntax/#rpc-ws-api) options.

#### `plugins_reloadPluginConfig`

Reloads specified plugin configuration.

**Parameters**

`plugin`: _string_ - plugin

**Returns**

`result`: _string_ - `Success`

Example

curl HTTP request

```
curl -X POST --data '{"jsonrpc":"2.0","method":"plugins_reloadPluginConfig","params":["tech.pegasys.plus.plugin.kafka.KafkaPlugin"],"id":1}' http://127.0.0.1:8545
```

wscat WS requestJSON result

### `PRIV` methods

The `PRIV` API methods provide functionality for [private transactions](https://besu.hyperledger.org/en/stable/Concepts/Privacy/Private-Transactions/) and [privacy groups](https://besu.hyperledger.org/en/stable/Concepts/Privacy/Privacy-Groups/).

Note

The `PRIV` API methods are not enabled by default for JSON-RPC. To enable the `PRIV` API methods, use the [`--rpc-http-api`](https://besu.hyperledger.org/en/stable/Reference/CLI/CLI-Syntax/#rpc-http-api) or [`--rpc-ws-api`](https://besu.hyperledger.org/en/stable/Reference/CLI/CLI-Syntax/#rpc-ws-api) options.

#### `priv_call`

Invokes a private contract function locally and does not change the privacy group state.

For private contracts, `priv_call` is the same as [`eth_call`](https://besu.hyperledger.org/en/stable/Reference/API-Methods/#eth\_call) for public contracts.

**Parameters**

* `privacyGroupId`: _string_ - 32-byte [privacy Group ID](https://besu.hyperledger.org/en/stable/Concepts/Privacy/Privacy-Groups/)
* `call`: _object_ - [transaction call object](https://besu.hyperledger.org/en/stable/Reference/API-Objects/#transaction-call-object)
* `blockNumber`: _string_ - integer representing a block number or one of the string tags `latest`, `earliest`, or `pending`, as described in [Block Parameter](https://besu.hyperledger.org/en/stable/HowTo/Interact/APIs/Using-JSON-RPC-API/#block-parameter)

**Returns**

`result`: _data_ - return value of the executed contract

Example

curl HTTP

```
curl -X POST --data '{"jsonrpc":"2.0","method":"priv_call","params":["tb8NVyQqZnHNegf/3mYsyB+HEud4SPWn90rz3GoskRw=", {"to":"0x69498dd54bd25aa0c886cf1f8b8ae0856d55ff13","data": "0x3fa4f245"}, "latest"],"id":1}' http://127.0.0.1:8545
```

wscat WSJSON resultcurl GraphQLGraphQLGraphQL result

#### `priv_createPrivacyGroup`

Creates a group of nodes, specified by their [Tessera](https://docs.tessera.consensys.net) public key.

**Parameters**

`options`: _object_ - request options object with the following fields:

* `addresses`: _array_ of _strings_ - list of nodes specified by [Tessera](https://docs.tessera.consensys.net) public keys
* `name`: _string_ - (optional) privacy group name
* `description`: _string_ - (optional) privacy group description

**Returns**

`result`: _string_ - privacy group ID

Example

curl HTTP request

```
curl -X POST --data '{"jsonrpc":"2.0","method": "priv_createPrivacyGroup", "params": [{"addresses":["sTZpbQhcOfd9ZaFDnC00e/N2Ofv9p4/ZTBbEeVtXJ3E=","quhb1pQPGN1w8ZSZSyiIfncEAlVY/M/rauSyQ5wVMRE="],"name":"Group A","description":"Description Group A"}],"id":1}' http://127.0.0.1:8545
```

wscat WS requestJSON result

#### `priv_debugGetStateRoot`

Returns the state root of the specified privacy group at the specified block.

**Parameters**

* `privacyGroupId`: _string_ - 32-byte [privacy Group ID](https://besu.hyperledger.org/en/stable/Concepts/Privacy/Privacy-Groups/)
* `blockNumber`: _string_ - integer representing a block number or one of the string tags `latest`, `earliest`, or `pending`, as described in [Block Parameter](https://besu.hyperledger.org/en/stable/HowTo/Interact/APIs/Using-JSON-RPC-API/#block-parameter)

**Returns**

`result`: _string_ - 32-byte state root

Example

curl HTTP

```
curl -X POST --data '{"jsonrpc":"2.0","method":"priv_debugGetStateRoot","params":["xJdxvWOEmrs2MCkKWlgArTzWIXFfU/tmVxI3EKssVTk=","latest"],"id":1}' http://127.0.0.1:8545
```

wscat WSJSON result

#### `priv_deletePrivacyGroup`

Deletes the specified privacy group.

**Parameters**

`privacyGroupId`: _string_ - privacy group ID

**Returns**

`result`: _string_ - deleted privacy group ID

Example

curl HTTP request

```
curl -X POST --data '{"jsonrpc":"2.0","method":"priv_deletePrivacyGroup","params":["ewuTVoc5nlvWMwTFdRRK/wvV0dcyQo/Pauvx5bNEbTk="],"id":1}' http://127.0.0.1:8545
```

wscat WS requestJSON result

#### `priv_distributeRawTransaction`

Distributes a signed, RLP encoded [private transaction](https://besu.hyperledger.org/en/stable/HowTo/Send-Transactions/Creating-Sending-Private-Transactions/).

Tip

If you want to sign the Privacy Marker Transaction outside of Besu, use [`priv_distributeRawTransaction`](https://besu.hyperledger.org/en/stable/HowTo/Send-Transactions/Creating-Sending-Private-Transactions/#priv\_distributerawtransaction) instead of [`eea_sendRawTransaction`](https://besu.hyperledger.org/en/stable/Reference/API-Methods/#eea\_sendrawtransaction).

**Parameters**

`transaction`: _string_ - signed RLP-encoded private transaction

**Returns**

`result`: _string_ - 32-byte enclave key (the enclave key is a pointer to the private transaction in [Tessera](https://docs.tessera.consensys.net).)

Example

curl HTTP request

```
curl -X POST --data '{"jsonrpc":"2.0","method":"priv_distributeRawTransaction","params": ["0xf869018203e882520894f17f52151ebef6c7334fad080c5704d77216b732881bc16d674ec80000801ba02da1c48b670996dcb1f447ef9ef00b33033c48a4fe938f420bec3e56bfd24071a062e0aa78a81bf0290afbc3a9d8e9a068e6d74caa66c5e0fa8a46deaae96b0833"], "id":1}' http://127.0.0.1:8545
```

wscat WS requestJSON result

#### `priv_findPrivacyGroup`

Returns a list of privacy groups containing only the listed members. For example, if the listed members are A and B, a privacy group containing A, B, and C is not returned.

**Parameters**

`members`: _array_ of _strings_ - members specified by [Tessera](https://docs.tessera.consensys.net) public keys

**Returns**

`result`: _array_ of _objects_ - privacy group objects containing only the specified members; privacy groups are [EEA-compliant](https://besu.hyperledger.org/en/stable/Concepts/Privacy/Privacy-Groups/#enterprise-ethereum-alliance-privacy) or [Besu-extended](https://besu.hyperledger.org/en/stable/Concepts/Privacy/Privacy-Groups/#besu-extended-privacy) with types:

* `LEGACY` for EEA-compliant groups.
* `PANTHEON` for Besu-extended groups.

Example

curl HTTP request

```
curl -X POST --data '{"jsonrpc": "2.0","method": "priv_findPrivacyGroup","params": [["negmDcN2P4ODpqn/6WkJ02zT/0w0bjhGpkZ8UP6vARk=", "g59BmTeJIn7HIcnq8VQWgyh/pDbvbt2eyP0Ii60aDDw="]],"id": 1}' http://127.0.0.1:8545
```

wscat WS requestJSON result

#### `priv_getCode`

Returns the code of the private smart contract at the specified address. Compiled smart contract code is stored as a hexadecimal value.

**Parameters**

* `privacyGroupId`: _string_ - 32-byte [privacy Group ID](https://besu.hyperledger.org/en/stable/Concepts/Privacy/Privacy-Groups/)
* `address`: _string_ - 20-byte contract address
* `blockNumber`: _string_ - integer representing a block number or one of the string tags `latest`, `earliest`, or `pending`, as described in [Block Parameter](https://besu.hyperledger.org/en/stable/HowTo/Interact/APIs/Using-JSON-RPC-API/#block-parameter)

**Returns**

`result`: _data_ - code stored at the specified address

Example

curl HTTP

```
curl -X POST --data '{"jsonrpc":"2.0","method":"priv_getCode","params":["1lJxSIP4JOp6uRn9wYsPeWwqoOP1c4nPQjylB4FExUA=", "0xeaf1c1bd00ef0bec5e39fba81740f1c5d05aa201", "latest"],"id":1}' http://127.0.0.1:8545
```

wscat WSJSON result

#### `priv_getEeaTransactionCount`

Returns the private transaction count for the specified account and [group of sender and recipients](https://besu.hyperledger.org/en/stable/Concepts/Privacy/Privacy-Groups/#enterprise-ethereum-alliance-privacy).

Important

If sending more than one transaction to be mined in the same block (that is, you are not waiting for the transaction receipt), you must calculate the private transaction nonce outside Besu instead of using `priv_getEeaTransactionCount`.

**Parameters**

* `address`: _string_ - account address
* `sender`: _string_ - base64-encoded Tessera address of the sender
* `recipients`: _array_ of _strings_ - base64-encoded Tessera addresses of recipients

**Returns**

`result`: _string_ - integer representing the number of private transactions sent from the address to the specified group of sender and recipients

Example

curl HTTP request

```
curl -X POST --data '{"jsonrpc":"2.0","method":"priv_getEeaTransactionCount","params":["0xfe3b557e8fb62b89f4916b721be55ceb828dbd73", "GGilEkXLaQ9yhhtbpBT03Me9iYa7U/mWXxrJhnbl1XY=", ["KkOjNLmCI6r+mICrC6l+XuEDjFEzQllaMQMpWLl4y1s=","eLb69r4K8/9WviwlfDiZ4jf97P9czyS3DkKu0QYGLjg="]], "id":1}' http://127.0.0.1:8545
```

wscat WS requestJSON result

#### `priv_getFilterChanges`

Polls the specified filter for a private contract and returns an array of changes that have occurred since the last poll.

Filters for private contracts can only be created by [`priv_newFilter`](https://besu.hyperledger.org/en/stable/Reference/API-Methods/#priv\_newfilter) so unlike [`eth_getFilterChanges`](https://besu.hyperledger.org/en/stable/Reference/API-Methods/#eth\_getfilterchanges), `priv_getFilterChanges` always returns an array of log objects or an empty list.

**Parameters**

* `privacyGroupId`: _string_ - 32-byte [privacy Group ID](https://besu.hyperledger.org/en/stable/Concepts/Privacy/Privacy-Groups/)
* `filterId`: _string_ - filter ID

**Returns**

`result`: _array_ of _objects_ - list of [log objects](https://besu.hyperledger.org/en/stable/Reference/API-Objects/#log-object), or an empty list if nothing has changed since the last poll

Example

curl HTTP request

```
curl -X POST --data '{"jsonrpc": "2.0","method": "priv_getFilterChanges","params": ["4rFldHM792LeP/e2WPkTXZedjwKuTr/KwCFTt6mBbkI=","0x4a35b92809d73f4f53a2355d62125442"],"id": 1}' http://127.0.0.1:8545
```

wscat WS requestJSON result

#### `priv_getFilterLogs`

Returns an array of [logs](https://besu.hyperledger.org/en/stable/Concepts/Events-and-Logs/) for the specified filter for a private contract.

For private contracts, `priv_getFilterLogs` is the same as [`eth_getFilterLogs`](https://besu.hyperledger.org/en/stable/Reference/API-Methods/#eth\_getfilterlogs) for public contracts except there is no [automatic log bloom caching](https://besu.hyperledger.org/en/stable/Reference/CLI/CLI-Syntax/#auto-log-bloom-caching-enabled) for private contracts.

Note

`priv_getFilterLogs` is only used for filters created with [`priv_newFilter`](https://besu.hyperledger.org/en/stable/Reference/API-Methods/#priv\_newfilter). To specify a filter object and get logs without creating a filter, use [`priv_getLogs`](https://besu.hyperledger.org/en/stable/Reference/API-Methods/#priv\_getlogs).

**Parameters**

* `privacyGroupId`: _string_ - 32-byte [privacy Group ID](https://besu.hyperledger.org/en/stable/Concepts/Privacy/Privacy-Groups/)
* `filterId`: _string_ - filter ID

**Returns**

`result`: _array_ of _objects_ - list of [log objects](https://besu.hyperledger.org/en/stable/Reference/API-Objects/#log-object)

Example

curl HTTP request

```
curl -X POST --data '{"jsonrpc": "2.0","method": "priv_getFilterLogs","params":["4rFldHM792LeP/e2WPkTXZedjwKuTr/KwCFTt6mBbkI=","0x4a35b92809d73f4f53a2355d62125442"],"id": 1}' http://127.0.0.1:8545
```

wscat WS requestJSON result

#### `priv_getLogs`

Returns an array of [logs](https://besu.hyperledger.org/en/stable/Concepts/Events-and-Logs/) matching a specified filter object.

For private contracts, `priv_getLogs` is the same as [`eth_getLogs`](https://besu.hyperledger.org/en/stable/Reference/API-Methods/#eth\_getlogs) for public contracts except there is no [automatic log bloom caching](https://besu.hyperledger.org/en/stable/Reference/CLI/CLI-Syntax/#auto-log-bloom-caching-enabled) for private contracts.

**Parameters**

* `privacyGroupId`: _string_ - 32-byte [privacy Group ID](https://besu.hyperledger.org/en/stable/Concepts/Privacy/Privacy-Groups/)
* `filterOptions`: _object_ - [filter options object](https://besu.hyperledger.org/en/stable/Reference/API-Objects/#filter-options-object)

**Returns**

`result`: _array_ of _objects_ - list of [log objects](https://besu.hyperledger.org/en/stable/Reference/API-Objects/#log-object)

Example

curl HTTP

```
curl -X POST --data '{"jsonrpc": "2.0","method": "priv_getLogs","params":["vGy/TZgO6y8VPMVeJAQ99MF1NaTf5ohA3TFfzoEF71k=",{"fromBlock": "earliest","toBlock": "latest","addresses": ["0x630c507ff633312087dc33c513b66276abcd2fc3"],"topics": ["0x85bea11d86cefb165374e0f727bacf21dc2f4ea816493981ecf72dcfb212a410"]}],"id": 1}' http://127.0.0.1:8545
```

wscat WSJSON result

#### `priv_getPrivacyPrecompileAddress`

Returns the address of the [privacy precompiled contract](https://besu.hyperledger.org/en/stable/Concepts/Privacy/Private-Transaction-Processing/). The address is derived and based on the value of the [`privacy-flexible-groups-enabled`](https://besu.hyperledger.org/en/stable/Reference/CLI/CLI-Syntax/#privacy-flexible-groups-enabled) option.

**Parameters**

None

**Returns**

`result`: _string_ - address of the privacy precompile

Example

curl HTTP request

```
curl -X POST --data '{"jsonrpc":"2.0","method":"priv_getPrivacyPrecompileAddress","params":[], "id":1}' http://127.0.0.1:8545
```

wscat WS requestJSON result

#### `priv_getPrivateTransaction`

Returns the private transaction if you are a participant, otherwise, `null`.

**Parameters**

`transaction`: _string_ - transaction hash returned by [`eea_sendRawTransaction`](https://besu.hyperledger.org/en/stable/Reference/API-Methods/#eea\_sendrawtransaction) or [`eea_sendTransaction`](https://docs.ethsigner.consensys.net/en/latest/Using-EthSigner/Using-EthSigner/#eea\_sendtransaction).

**Returns**

`result`: _object_ - [private transaction object](https://besu.hyperledger.org/en/stable/Reference/API-Objects/#private-transaction-object), or `null` if not a participant in the private transaction

Example

curl HTTP request

```
curl -X POST --data '{"jsonrpc":"2.0","method":"priv_getPrivateTransaction","params":["0x623c4ce5275a87b91f4f1c521012d39ca19311c787bde405490f4c0426a71498"], "id":1}' http://127.0.0.1:8545
```

wscat WS requestJSON result

#### `priv_getTransactionCount`

Returns the private transaction count for specified account and privacy group.

Important

If sending more than one transaction to be mined in the same block (that is, you are not waiting for the transaction receipt), you must calculate the private transaction nonce outside Besu instead of using `priv_getTransactionCount`.

**Parameters**

* `address`: _string_ - account address
* `privacyGroupId`: _string_ - privacy group ID

**Returns**

`result`: _string_ - integer representing the number of private transactions sent from the address to the specified privacy group

Example

curl HTTP request

```
curl -X POST --data '{"jsonrpc":"2.0","method":"priv_getTransactionCount","params":["0xfe3b557e8fb62b89f4916b721be55ceb828dbd73", "kAbelwaVW7okoEn1+okO+AbA4Hhz/7DaCOWVQz9nx5M="], "id":1}' http://127.0.0.1:8545
```

wscat WS requestJSON result

#### `priv_getTransactionReceipt`

Returns information about the private transaction after mining the transaction. Receipts for pending transactions are not available.

**Parameters**

`transaction`: _string_ - 32-byte hash of a transaction

**Returns**

`result`: _object_ - [private Transaction receipt object](https://besu.hyperledger.org/en/stable/Reference/API-Objects/#private-transaction-receipt-object), or `null` if no receipt found

Example

curl HTTP request

```
curl -X POST --data '{"jsonrpc":"2.0","method":"priv_getTransactionReceipt","params":["0xf3ab9693ad92e277bf785e1772f29fb1864904bbbe87b0470455ddb082caab9d"],"id":1}' http://127.0.0.1:8545
```

wscat WS requestJSON result

#### `priv_newFilter`

Creates a [log filter](https://besu.hyperledger.org/en/stable/Concepts/Events-and-Logs/) for a private contract. To poll for logs associated with the created filter, use [`priv_getFilterChanges`](https://besu.hyperledger.org/en/stable/Reference/API-Methods/#priv\_getfilterchanges). To get all logs associated with the filter, use [`priv_getFilterLogs`](https://besu.hyperledger.org/en/stable/Reference/API-Methods/#priv\_getfilterlogs).

For private contracts, `priv_newFilter` is the same as [`eth_newFilter`](https://besu.hyperledger.org/en/stable/Reference/API-Methods/#eth\_newfilter) for public contracts.

**Parameters**

* `privacyGroupId`: _string_ - 32-byte [privacy Group ID](https://besu.hyperledger.org/en/stable/Concepts/Privacy/Privacy-Groups/)
* `filterOptions`: _object_ - [filter options object](https://besu.hyperledger.org/en/stable/Reference/API-Objects/#filter-options-object)

Note

`fromBlock` and `toBlock` in the filter options object default to `latest`.

**Returns**

`result`: _string_ - filter ID

Example

curl HTTP request

```
curl -X POST --data '{"jsonrpc": "2.0","method": "priv_newFilter","params": ["4rFldHM792LeP/e2WPkTXZedjwKuTr/KwCFTt6mBbkI=",{"fromBlock": "earliest","toBlock": "latest","addresses": ["0x991cc548c154b2953cc48c02f782e1314097dfbb"],"topics": ["0x85bea11d86cefb165374e0f727bacf21dc2f4ea816493981ecf72dcfb212a410"]}],"id": 1}' http://127.0.0.1:8545
```

wscat WS requestJSON result

#### `priv_uninstallFilter`

Uninstalls a filter for a private contract with the specified ID. When a filter is no longer required, call this method.

Filters time out when not requested by [`priv_getFilterChanges`](https://besu.hyperledger.org/en/stable/Reference/API-Methods/#priv\_getfilterchanges) or [`priv_getFilterLogs`](https://besu.hyperledger.org/en/stable/Reference/API-Methods/#priv\_getfilterlogs) for 10 minutes.

For private contracts, `priv_uninstallFilter` is the same as [`eth_uninstallFilter`](https://besu.hyperledger.org/en/stable/Reference/API-Methods/#eth\_uninstallfilter) for public contracts.

**Parameters**

* `privacyGroupId`: _string_ - 32-byte [privacy Group ID](https://besu.hyperledger.org/en/stable/Concepts/Privacy/Privacy-Groups/)
* `filterId`: _string_ - filter ID

**Returns**

`result`: _boolean_ - indicates if the filter is successfully uninstalled

Example

curl HTTP request

```
curl -X POST --data '{"jsonrpc": "2.0","method": "priv_uninstallFilter","params":["4rFldHM792LeP/e2WPkTXZedjwKuTr/KwCFTt6mBbkI=","0x4a35b92809d73f4f53a2355d62125442"],"id": 1}' http://127.0.0.1:8545
```

wscat WS requestJSON result

### `QBFT` methods

The `QBFT` API methods provide access to the [QBFT](https://besu.hyperledger.org/en/stable/HowTo/Configure/Consensus-Protocols/QBFT/) consensus engine.

Note

The `QBFT` API methods are not enabled by default for JSON-RPC. To enable the `QBFT` API methods, use the [`--rpc-http-api`](https://besu.hyperledger.org/en/stable/Reference/CLI/CLI-Syntax/#rpc-http-api) or [`--rpc-ws-api`](https://besu.hyperledger.org/en/stable/Reference/CLI/CLI-Syntax/#rpc-ws-api) options.

#### `qbft_discardValidatorVote`

Discards a proposal to [add or remove a validator](https://besu.hyperledger.org/en/stable/HowTo/Configure/Consensus-Protocols/QBFT/#adding-and-removing-validators) with the specified address.

**Parameters**

`address`: _string_ - 20-byte address of proposed validator

**Returns**

`result`: _boolean_ - indicates if the proposal is discarded

Example

curl HTTP request

```
curl -X POST --data '{"jsonrpc":"2.0","method":"qbft_discardValidatorVote","params":["0xef1bfb6a12794615c9b0b5a21e6741f01e570185"], "id":1}' http://127.0.0.1:8545
```

wscat WS requestJSON result

#### `qbft_getPendingVotes`

Returns [votes](https://besu.hyperledger.org/en/stable/HowTo/Configure/Consensus-Protocols/QBFT/#adding-and-removing-validators) cast in the current [epoch](https://besu.hyperledger.org/en/stable/HowTo/Configure/Consensus-Protocols/QBFT/#genesis-file).

**Parameters**

None

**Returns**

`result`: _map_ of _strings_ to _booleans_ - map of account addresses to corresponding boolean values indicating the vote for each account; if `true`, the vote is to add a validator. If `false`, the proposal is to remove a validator.

Example

curl HTTP request

```
curl -X POST --data '{"jsonrpc":"2.0","method":"qbft_getPendingVotes","params":[], "id":1}' http://127.0.0.1:8545
```

wscat WS requestJSON result

#### `qbft_getSignerMetrics`

Provides the following validator metrics for the specified range:

* Number of blocks from each validator
* Block number of the last block proposed by each validator (if any proposed in the specified range)
* All validators present in the last block of the range

**Parameters**

* `fromBlockNumber`: _string_ - integer representing a block number or the string tag `earliest` as described in [Block Parameter](https://besu.hyperledger.org/en/stable/HowTo/Interact/APIs/Using-JSON-RPC-API/#block-parameter)
* `toBlockNumber`: _string_ - integer representing a block number or one of the string tags `latest` or `pending`, as described in [Block Parameter](https://besu.hyperledger.org/en/stable/HowTo/Interact/APIs/Using-JSON-RPC-API/#block-parameter)

If you specify:

* No parameters, the call provides metrics for the last 100 blocks, or all blocks if there are less than 100 blocks.
* Only the first parameter, the call provides metrics for all blocks from the block specified to the latest block.

**Returns**

`result`: _array_ of _objects_ - list of validator objects

Note

The proposer of the genesis block has address `0x0000000000000000000000000000000000000000`.

Example

curl HTTP

```
curl -X POST --data '{"jsonrpc":"2.0","method":"qbft_getSignerMetrics","params":["1", "100"], "id":1}' http://127.0.0.1:8545
```

wscat WSJSON result

#### `qbft_getValidatorsByBlockHash`

Lists the validators defined in the specified block.

**Parameters**

`block`: _string_ - 32-byte block hash

**Returns**

`result`: _array_ of _strings_ - list of validator addresses

Example

curl HTTP request

```
curl -X POST --data '{"jsonrpc":"2.0","method":"qbft_getValidatorsByBlockHash","params":["0xbae7d3feafd743343b9a4c578cab5e5d65eb735f6855fb845c00cab356331256"], "id":1}' http://127.0.0.1:8545
```

wscat WS requestJSON result

#### `qbft_getValidatorsByBlockNumber`

Lists the validators defined in the specified block.

**Parameters**

* `blockNumber`: _string_ - integer representing a block number or one of the string tags `latest`, `earliest`, or `pending`, as described in [Block Parameter](https://besu.hyperledger.org/en/stable/HowTo/Interact/APIs/Using-JSON-RPC-API/#block-parameter)

**Returns**

`result`: _array_ of _strings_ - list of validator addresses

Example

curl HTTP request

```
curl -X POST --data '{"jsonrpc":"2.0","method":"qbft_getValidatorsByBlockNumber","params":["latest"], "id":1}' http://127.0.0.1:8545
```

wscat WS requestJSON result

#### `qbft_proposeValidatorVote`

Proposes to [add or remove a validator](https://besu.hyperledger.org/en/stable/HowTo/Configure/Consensus-Protocols/QBFT/#adding-and-removing-validators) with the specified address.

**Parameters**

* `address`: _string_ - account address
* `proposal`: _boolean_ - `true` to propose adding validator or `false` to propose removing validator

**Returns**

`result`: _boolean_ - `true`

Example

curl HTTP request

```
curl -X POST --data '{"jsonrpc":"2.0","method":"qbft_proposeValidatorVote","params":["42d4287eac8078828cf5f3486cfe601a275a49a5",true], "id":1}' http://127.0.0.1:8545
```

wscat WS requestJSON result

### `TRACE` methods

The `TRACE` API is a more concise alternative to the [`DEBUG` API](https://besu.hyperledger.org/en/stable/Reference/API-Methods/#debug-methods).

Note

The `TRACE` API methods are not enabled by default for JSON-RPC. To enable the `TRACE` API methods, use the [`--rpc-http-api`](https://besu.hyperledger.org/en/stable/Reference/CLI/CLI-Syntax/#rpc-http-api) or [`--rpc-ws-api`](https://besu.hyperledger.org/en/stable/Reference/CLI/CLI-Syntax/#rpc-ws-api) options.

#### `trace_block`

Provides transaction processing of [type `trace`](https://besu.hyperledger.org/en/stable/Reference/Trace-Types/#trace) for the specified block.

Important

Your node must be an archive node (that is, synchronized without pruning or fast sync) or the requested block must be within [the number of pruning blocks retained](https://besu.hyperledger.org/en/stable/Reference/CLI/CLI-Syntax#pruning-blocks-retained) (by default, 1024).

**Parameters**

`blockNumber`: _string_ - integer representing a block number or one of the string tags `latest`, `earliest`, or `pending`, as described in [Block Parameter](https://besu.hyperledger.org/en/stable/HowTo/Interact/APIs/Using-JSON-RPC-API/#block-parameter)

**Returns**

`result`: _array_ of _objects_ - list of [calls to other contracts](https://besu.hyperledger.org/en/stable/Reference/Trace-Types/#trace) containing one object per call, in transaction execution order; if revert reason is enabled with [`--revert-reason-enabled`](https://besu.hyperledger.org/en/stable/Reference/CLI/CLI-Syntax/#revert-reason-enabled), the returned list items include the [revert reason](https://besu.hyperledger.org/en/stable/HowTo/Send-Transactions/Revert-Reason/).

Example

curl HTTP request

```
curl -X POST --data '{"jsonrpc":"2.0","method":"trace_block","params":["0x6"],"id":1}' http://127.0.0.1:8545
```

wscat WS requestJSON result

#### `trace_replayBlockTransactions`

Provides transaction processing tracing per block.

Important

Your node must be an archive node (that is, synchronized without pruning or fast sync) or the requested block must be within [the number of pruning blocks retained](https://besu.hyperledger.org/en/stable/Reference/CLI/CLI-Syntax#pruning-blocks-retained) (by default, 1024).

**Parameters**

* `blockNumber`: _string_ - integer representing a block number or one of the string tags `latest`, `earliest`, or `pending`, as described in [Block Parameter](https://besu.hyperledger.org/en/stable/HowTo/Interact/APIs/Using-JSON-RPC-API/#block-parameter)
* `options`: _array_ of _strings_ - list of tracing options; tracing options are [`trace`, `vmTrace`, and `stateDiff`](https://besu.hyperledger.org/en/stable/Reference/Trace-Types/). Specify any combination of the three options including none of them.

**Returns**

`result`: _array_ of _objects_ - list of [transaction trace objects](https://besu.hyperledger.org/en/stable/Reference/API-Objects/#transaction-trace-object) containing one object per transaction, in transaction execution order; if revert reason is enabled with [`--revert-reason-enabled`](https://besu.hyperledger.org/en/stable/Reference/CLI/CLI-Syntax/#revert-reason-enabled), the [`trace`](https://besu.hyperledger.org/en/stable/Reference/Trace-Types/#trace) list items in the returned transaction trace object include the [revert reason](https://besu.hyperledger.org/en/stable/HowTo/Send-Transactions/Revert-Reason/).

Example

curl HTTP request

```
curl -X POST --data '{"jsonrpc": "2.0", "method": "trace_replayBlockTransactions","params": ["0x12",["trace","vmTrace","stateDiff"]],"id": 1}' http://127.0.0.1:8545
```

wscat WS requestJSON result

#### `trace_transaction`

Provides transaction processing of [type `trace`](https://besu.hyperledger.org/en/stable/Reference/Trace-Types/#trace) for the specified transaction.

Important

Your node must be an archive node (that is, synchronized without pruning or fast sync) or the requested transaction must be contained in a block within [the number of pruning blocks retained](https://besu.hyperledger.org/en/stable/Reference/CLI/CLI-Syntax#pruning-blocks-retained) (by default, 1024).

**Parameters**

`transaction`: _string_ - transaction hash

**Returns**

`result`: _array_ of _objects_ - list of [calls to other contracts](https://besu.hyperledger.org/en/stable/Reference/Trace-Types/#trace) containing one object per call, in the order called by the transaction; if revert reason is enabled with [`--revert-reason-enabled`](https://besu.hyperledger.org/en/stable/Reference/CLI/CLI-Syntax/#revert-reason-enabled), the returned list items include the [revert reason](https://besu.hyperledger.org/en/stable/HowTo/Send-Transactions/Revert-Reason/).

Example

curl HTTP request

```
curl -X POST --data '{"jsonrpc": "2.0", "method": "trace_transaction","params": ["0x4c253746668dca6ac3f7b9bc18248b558a95b5fc881d140872c2dff984d344a7"],"id": 1}' http://127.0.0.1:8545
```

wscat WS requestJSON result

### `TXPOOL` methods

The `TXPOOL` API methods allow you to inspect the contents of the transaction pool.

Note

The `TXPOOL` API methods are not enabled by default for JSON-RPC. To enable the `TXPOOL` API methods, use the [`--rpc-http-api`](https://besu.hyperledger.org/en/stable/Reference/CLI/CLI-Syntax/#rpc-http-api) or [`--rpc-ws-api`](https://besu.hyperledger.org/en/stable/Reference/CLI/CLI-Syntax/#rpc-ws-api) options.

#### `txpool_besuPendingTransactions`

Lists pending transactions that match the supplied filter conditions.

**Parameters**

* `numResults`: _number_ - integer representing the maximum number of results to return
* `fields`: _object_ - object of fields used to create the filter condition

Each field in the object corresponds to a field name containing an operator, and a value for the operator. A field name can only be specified once, and can only contain one operator. For example, you cannot query transactions with a gas price between 8 and 9 Gwei by using both the `gt` and `lt` operator in the same field name instance.

All filters must be satisfied for a transaction to be returned.

| Field name   | Value                                              |    Value type    | Supported operators |
| ------------ | -------------------------------------------------- | :--------------: | ------------------- |
| **from**     | Address of the sender.                             | _Data_, 20 bytes | `eq`                |
| **to**       | Address of the receiver, or `"contract_creation"`. | _Data_, 20 bytes | `eq`, `action`      |
| **gas**      | Gas provided by the sender.                        |    _Quantity_    | `eq`, `gt`, `lt`    |
| **gasPrice** | Gas price, in wei, provided by the sender.         |    _Quantity_    | `eq`, `gt`, `lt`    |
| **value**    | Value transferred, in wei.                         |    _Quantity_    | `eq`, `gt`, `lt`    |
| **nonce**    | Number of transactions made by the sender.         |    _Quantity_    | `eq`, `gt`, `lt`    |
|              |                                                    |                  |                     |

Supported operators:

* `eq` (equal to)
* `lt` (less than)
* `gt` (greater than)
* `action`

Note

The only supported `action` is `"contract_creation"`.

**Returns**

`result`: _array_ of _objects_ - list of objects with [details of the pending transaction](https://besu.hyperledger.org/en/stable/Reference/API-Objects/#pending-transaction-object)

Example

curl HTTP request

```
curl -X POST --data '{"jsonrpc":"2.0","method":"txpool_besuPendingTransactions","params":[2,{"from":{"eq":"0xfe3b557e8fb62b89f4916b721be55ceb828dbd73"},"gas":{"lt":"0x5209"},"nonce":{"gt":"0x1"}}],"id":1}' http://127.0.0.1:8545
```

wscat WS requestJSON result

#### `txpool_besuStatistics`

Lists statistics about the node transaction pool.

**Parameters**

None

**Returns**

`result`: _object_ - transaction pool statistics object with the following fields:

* `maxSize`: _number_ - maximum number of transactions kept in the transaction pool; use the [`--tx-pool-max-size`](https://besu.hyperledger.org/en/stable/Reference/CLI/CLI-Syntax/#tx-pool-max-size) option to configure the maximum size.
* `localCount`: _number_ - number of transactions submitted directly to this node
* `remoteCount`: _number_ - number of transactions received from remote nodes

Example

curl HTTP request

```
curl -X POST --data '{"jsonrpc":"2.0","method":"txpool_besuStatistics","params":[],"id":1}' http://127.0.0.1:8545
```

wscat WS requestJSON result

#### `txpool_besuTransactions`

Lists transactions in the node transaction pool.

**Parameters**

None

**Returns**

`result`: _array_ of _objects_ - list of transactions

Example

curl HTTP request

```
curl -X POST --data '{"jsonrpc":"2.0","method":"txpool_besuTransactions","params":[],"id":1}' http://127.0.0.1:8545
```

wscat WS requestJSON result

### `WEB3` methods

The `WEB3` API methods provide functionality for the Ethereum ecosystem.

#### `web3_clientVersion`

Returns the current client version.

**Parameters**

None

**Returns**

`result`: _string_ - current client version

Example

curl HTTP request

```
curl -X POST --data '{"jsonrpc":"2.0","method":"web3_clientVersion","params":[],"id":1}' http://127.0.0.1:8545
```

wscat WS requestJSON result

#### `web3_sha3`

Returns a [SHA3](https://en.wikipedia.org/wiki/SHA-3) hash of the specified data. The result value is a [Keccak-256](https://keccak.team/keccak.html) hash, not the standardized SHA3-256.

**Parameters**

`data`: _string_ - data to convert to a SHA3 hash

**Returns**

`result`: _string_ - SHA3 result of the input data

Example

curl HTTP request

```
curl -X POST --data '{"jsonrpc":"2.0","method":"web3_sha3","params":["0x68656c6c6f20776f726c00"],"id":53}' http://127.0.0.1:8545
```

wscat WS requestJSON result

### Miscellaneous methods

#### `rpc_modules`

Lists [enabled APIs](https://besu.hyperledger.org/en/stable/HowTo/Interact/APIs/Using-JSON-RPC-API/#api-methods-enabled-by-default) and the version of each.

**Parameters**

None

**Returns**

`result`: _map_ of _strings_ to _strings_ - enabled APIs and their versions
