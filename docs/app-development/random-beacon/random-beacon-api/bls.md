# Solidity API

## BLS

Library for verification of 2-pairing-check BLS signatures, including
basic, aggregated, or reconstructed threshold BLS signatures, generated
using the AltBn128 curve.

### sign

```solidity
function sign(bytes message, uint256 secretKey) external view returns (bytes)
```

Creates a signature over message using the provided secret key.

### verifyBytes

```solidity
function verifyBytes(bytes publicKey, bytes message, bytes signature) external view returns (bool)
```

Wraps the functionality of BLS.verify, but hashes a message to
a point on G1 and marshal to bytes first to allow raw bytes
verification.

### verify

```solidity
function verify(bytes publicKey, bytes message, bytes signature) public view returns (bool)
```

Verify performs the pairing operation to check if the signature
is correct for the provided message and the corresponding public
key. Public key must be a valid point on G2 curve in an
uncompressed format. Message must be a valid point on G1 curve in
an uncompressed format. Signature must be a valid point on G1
curve in an uncompressed format.

### _verify

```solidity
function _verify(struct AltBn128.G2Point publicKey, struct AltBn128.G1Point message, struct AltBn128.G1Point signature) public view returns (bool)
```

