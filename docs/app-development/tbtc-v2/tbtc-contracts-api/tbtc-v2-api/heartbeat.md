# Heartbeat

## Heartbeat

The library establishes expected format for heartbeat messages signed by wallet ECDSA signing group. Heartbeat messages are constructed in such a way that they can not be used as a Bitcoin transaction preimages.

The smallest Bitcoin non-coinbase transaction is a one spending an OP\_TRUE anyonecanspend output and creating 1 OP\_TRUE anyonecanspend output. Such a transaction has 61 bytes (see `BitcoinTx` documentation): 4 bytes for version 1 byte for tx\_in\_count 36 bytes for tx\_in.previous\_output 1 byte for tx\_in.script\_bytes (value: 0) 0 bytes for tx\_in.signature\_script 4 bytes for tx\_in.sequence 1 byte for tx\_out\_count 8 bytes for tx\_out.value 1 byte for tx\_out.pk\_script\_bytes 1 byte for tx\_out.pk\_script 4 bytes for lock\_time

The smallest Bitcoin coinbase transaction is a one creating 1 OP\_TRUE anyonecanspend output and having an empty coinbase script. Such a transaction has 65 bytes: 4 bytes for version 1 byte for tx\_in\_count 32 bytes for tx\_in.hash (all 0x00) 4 bytes for tx\_in.index (all 0xff) 1 byte for tx\_in.script\_bytes (value: 0) 4 bytes for tx\_in.height 0 byte for tx\_in.coinbase\_script 4 bytes for tx\_in.sequence 1 byte for tx\_out\_count 8 bytes for tx\_out.value 1 byte for tx\_out.pk\_script\_bytes 1 byte for tx\_out.pk\_script 4 bytes for lock\_time

A SIGHASH flag is used to indicate which part of the transaction is signed by the ECDSA signature. There are currently 3 flags: SIGHASH\_ALL, SIGHASH\_NONE, SIGHASH\_SINGLE, and different combinations of these flags.

No matter the SIGHASH flag and no matter the combination, the following fields from the transaction are always included in the constructed preimage: 4 bytes for version 36 bytes for tx\_in.previous\_output (or tx\_in.hash + tx\_in.index for coinbase) 4 bytes for lock\_time

Additionally, the last 4 bytes of the preimage determines the SIGHASH flag.

This is enough to say there is no way the preimage could be shorter than 4 + 36 + 4 + 4 = 48 bytes.

For this reason, we construct the heartbeat message, as a 16-byte message. The first 8 bytes are 0xffffffffffffffff. The last 8 bytes are for an arbitrary uint64, being a signed heartbeat nonce (for example, the last Ethereum block hash).

The message being signed by the wallet when executing the heartbeat protocol should be Bitcoin's hash256 (double SHA-256) of the heartbeat message: heartbeat\_sighash = hash256(heartbeat\_message)

### isValidHeartbeatMessage

```solidity
function isValidHeartbeatMessage(bytes message) internal pure returns (bool)
```

Determines if the signed byte array is a valid, non-fraudulent heartbeat message.

Wallet heartbeat message must be exactly 16 bytes long with the first 8 bytes set to 0xffffffffffffffff.

#### Parameters

| Name    | Type  | Description                                                                                                                    |
| ------- | ----- | ------------------------------------------------------------------------------------------------------------------------------ |
| message | bytes | Message signed by the wallet. It is a potential heartbeat message, Bitcoin transaction preimage, or an arbitrary signed bytes. |

#### Return Values

| Name | Type | Description                                       |
| ---- | ---- | ------------------------------------------------- |
| \[0] | bool | True if valid heartbeat message, false otherwise. |
