# Solidity API

## BitcoinTx

Allows to reference Bitcoin raw transaction in Solidity.

See https://developer.bitcoin.org/reference/transactions.html#raw-transaction-format

Raw Bitcoin transaction data:

| Bytes  |     Name     |        BTC type        |        Description        |
|--------|--------------|------------------------|---------------------------|
| 4      | version      | int32_t (LE)           | TX version number         |
| varies | tx_in_count  | compactSize uint (LE)  | Number of TX inputs       |
| varies | tx_in        | txIn[]                 | TX inputs                 |
| varies | tx_out_count | compactSize uint (LE)  | Number of TX outputs      |
| varies | tx_out       | txOut[]                | TX outputs                |
| 4      | lock_time    | uint32_t (LE)          | Unix time or block number |

Non-coinbase transaction input (txIn):

| Bytes  |       Name       |        BTC type        |                 Description                 |
|--------|------------------|------------------------|---------------------------------------------|
| 36     | previous_output  | outpoint               | The previous outpoint being spent           |
| varies | script_bytes     | compactSize uint (LE)  | The number of bytes in the signature script |
| varies | signature_script | char[]                 | The signature script, empty for P2WSH       |
| 4      | sequence         | uint32_t (LE)          | Sequence number                             |

The reference to transaction being spent (outpoint):

| Bytes | Name  |   BTC type    |               Description                |
|-------|-------|---------------|------------------------------------------|
|    32 | hash  | char[32]      | Hash of the transaction to spend         |
|    4  | index | uint32_t (LE) | Index of the specific output from the TX |

Transaction output (txOut):

| Bytes  |      Name       |     BTC type          |             Description              |
|--------|-----------------|-----------------------|--------------------------------------|
| 8      | value           | int64_t (LE)          | Number of satoshis to spend          |
| 1+     | pk_script_bytes | compactSize uint (LE) | Number of bytes in the pubkey script |
| varies | pk_script       | char[]                | Pubkey script                        |

compactSize uint format:

|                  Value                  | Bytes |                    Format                    |
|-----------------------------------------|-------|----------------------------------------------|
| >= 0 && <= 252                          | 1     | uint8_t                                      |
| >= 253 && <= 0xffff                     | 3     | 0xfd followed by the number as uint16_t (LE) |
| >= 0x10000 && <= 0xffffffff             | 5     | 0xfe followed by the number as uint32_t (LE) |
| >= 0x100000000 && <= 0xffffffffffffffff | 9     | 0xff followed by the number as uint64_t (LE) |

(*) compactSize uint is often references as VarInt)

Coinbase transaction input (txIn):

| Bytes  |       Name       |        BTC type        |                 Description                 |
|--------|------------------|------------------------|---------------------------------------------|
| 32     | hash             | char[32]               | A 32-byte 0x0  null (no previous_outpoint)  |
| 4      | index            | uint32_t (LE)          | 0xffffffff (no previous_outpoint)           |
| varies | script_bytes     | compactSize uint (LE)  | The number of bytes in the coinbase script  |
| varies | height           | char[]                 | The block height of this block (BIP34) (*)  |
| varies | coinbase_script  | none                   |  Arbitrary data, max 100 bytes              |
| 4      | sequence         | uint32_t (LE)          | Sequence number

(*)  Uses script language: starts with a data-pushing opcode that indicates how many bytes to push to
the stack followed by the block height as a little-endian unsigned integer. This script must be as
short as possible, otherwise it may be rejected. The data-pushing opcode will be 0x03 and the total
size four bytes until block 16,777,216 about 300 years from now.

### Info

```solidity
struct Info {
  bytes4 version;
  bytes inputVector;
  bytes outputVector;
  bytes4 locktime;
}
```

### Proof

```solidity
struct Proof {
  bytes merkleProof;
  uint256 txIndexInBlock;
  bytes bitcoinHeaders;
}
```

### UTXO

```solidity
struct UTXO {
  bytes32 txHash;
  uint32 txOutputIndex;
  uint64 txOutputValue;
}
```

### RSVSignature

```solidity
struct RSVSignature {
  bytes32 r;
  bytes32 s;
  uint8 v;
}
```

### validateProof

```solidity
function validateProof(struct BridgeState.Storage self, struct BitcoinTx.Info txInfo, struct BitcoinTx.Proof proof) internal view returns (bytes32 txHash)
```

Validates the SPV proof of the Bitcoin transaction.
Reverts in case the validation or proof verification fail.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeState.Storage |  |
| txInfo | struct BitcoinTx.Info | Bitcoin transaction data. |
| proof | struct BitcoinTx.Proof | Bitcoin proof data. |

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| txHash | bytes32 | Proven 32-byte transaction hash. |

### evaluateProofDifficulty

```solidity
function evaluateProofDifficulty(struct BridgeState.Storage self, bytes bitcoinHeaders) internal view
```

Evaluates the given Bitcoin proof difficulty against the actual
Bitcoin chain difficulty provided by the relay oracle.
Reverts in case the evaluation fails.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| self | struct BridgeState.Storage |  |
| bitcoinHeaders | bytes | Bitcoin headers chain being part of the SPV proof. Used to extract the observed proof difficulty. |

### extractPubKeyHash

```solidity
function extractPubKeyHash(struct BridgeState.Storage, bytes output) internal pure returns (bytes20 pubKeyHash)
```

Extracts public key hash from the provided P2PKH or P2WPKH output.
Reverts if the validation fails.

Requirements:
- The output must be of P2PKH or P2WPKH type and lock the funds
on a 20-byte public key hash.

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
|  | struct BridgeState.Storage |  |
| output | bytes | The transaction output. |

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| pubKeyHash | bytes20 | 20-byte public key hash the output locks funds on. |

### makeP2PKHScript

```solidity
function makeP2PKHScript(bytes20 pubKeyHash) internal pure returns (bytes26)
```

Build the P2PKH script from the given public key hash.

The P2PKH script has the following byte format:
<0x1976a914> <20-byte PKH> <0x88ac>. According to
https://en.bitcoin.it/wiki/Script#Opcodes this translates to:
- 0x19: Byte length of the entire script
- 0x76: OP_DUP
- 0xa9: OP_HASH160
- 0x14: Byte length of the public key hash
- 0x88: OP_EQUALVERIFY
- 0xac: OP_CHECKSIG
which matches the P2PKH structure as per:
https://en.bitcoin.it/wiki/Transaction#Pay-to-PubkeyHash

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| pubKeyHash | bytes20 | The 20-byte public key hash. |

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| [0] | bytes26 | The P2PKH script. |

### makeP2WPKHScript

```solidity
function makeP2WPKHScript(bytes20 pubKeyHash) internal pure returns (bytes23)
```

Build the P2WPKH script from the given public key hash.

The P2WPKH script has the following format:
<0x160014> <20-byte PKH>. According to
https://en.bitcoin.it/wiki/Script#Opcodes this translates to:
- 0x16: Byte length of the entire script
- 0x00: OP_0
- 0x14: Byte length of the public key hash
which matches the P2WPKH structure as per:
https://github.com/bitcoin/bips/blob/master/bip-0141.mediawiki#P2WPKH

#### Parameters

| Name | Type | Description |
| ---- | ---- | ----------- |
| pubKeyHash | bytes20 | The 20-byte public key hash. |

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| [0] | bytes23 | The P2WPKH script. |

