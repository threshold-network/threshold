# Solidity API

## AltBn128

Implementations of common elliptic curve operations on Ethereum's
(poorly named) alt_bn128 curve. Whenever possible, use post-Byzantium
pre-compiled contracts to offset gas costs. Note that these
pre-compiles might not be available on all (eg private) chains.

### G1Point

```solidity
struct G1Point {
  uint256 x;
  uint256 y;
}
```

### gfP2

```solidity
struct gfP2 {
  uint256 x;
  uint256 y;
}
```

### G2Point

```solidity
struct G2Point {
  struct AltBn128.gfP2 x;
  struct AltBn128.gfP2 y;
}
```

### p

```solidity
uint256 p
```

### g1x

```solidity
uint256 g1x
```

Gets generator of G1 group.
Taken from go-ethereum/crypto/bn256/cloudflare/curve.go

### g1y

```solidity
uint256 g1y
```

### g2xx

```solidity
uint256 g2xx
```

Gets generator of G2 group.
Taken from go-ethereum/crypto/bn256/cloudflare/twist.go

### g2xy

```solidity
uint256 g2xy
```

### g2yx

```solidity
uint256 g2yx
```

### g2yy

```solidity
uint256 g2yy
```

### twistBx

```solidity
uint256 twistBx
```

Gets twist curve B constant.
Taken from go-ethereum/crypto/bn256/cloudflare/twist.go

### twistBy

```solidity
uint256 twistBy
```

### hexRootX

```solidity
uint256 hexRootX
```

Gets root of the point where x and y are equal.

### hexRootY

```solidity
uint256 hexRootY
```

### g1YFromX

```solidity
function g1YFromX(uint256 x) internal view returns (uint256)
```

g1YFromX computes a Y value for a G1 point based on an X value.
This computation is simply evaluating the curve equation for Y on a
given X, and allows a point on the curve to be represented by just
an X value + a sign bit.

### g1HashToPoint

```solidity
function g1HashToPoint(bytes m) internal view returns (struct AltBn128.G1Point)
```

Hash a byte array message, m, and map it deterministically to a
point on G1. Note that this approach was chosen for its simplicity
and lower gas cost on the EVM, rather than good distribution of
points on G1.

### g1Decompress

```solidity
function g1Decompress(bytes32 m) internal view returns (struct AltBn128.G1Point)
```

Decompress a point on G1 from a single uint256.

### g1Add

```solidity
function g1Add(struct AltBn128.G1Point a, struct AltBn128.G1Point b) internal view returns (struct AltBn128.G1Point c)
```

Wraps the point addition pre-compile introduced in Byzantium.
Returns the sum of two points on G1. Revert if the provided points
are not on the curve.

### isG1PointOnCurve

```solidity
function isG1PointOnCurve(struct AltBn128.G1Point point) internal view returns (bool)
```

Returns true if G1 point is on the curve.

### scalarMultiply

```solidity
function scalarMultiply(struct AltBn128.G1Point p_1, uint256 scalar) internal view returns (struct AltBn128.G1Point p_2)
```

Wraps the scalar point multiplication pre-compile introduced in
Byzantium. The result of a point from G1 multiplied by a scalar
should match the point added to itself the same number of times.
Revert if the provided point isn't on the curve.

### pairing

```solidity
function pairing(struct AltBn128.G1Point p1, struct AltBn128.G2Point p2, struct AltBn128.G1Point p3, struct AltBn128.G2Point p4) internal view returns (bool result)
```

Wraps the pairing check pre-compile introduced in Byzantium.
Returns the result of a pairing check of 2 pairs
(G1 p1, G2 p2) (G1 p3, G2 p4)

### getP

```solidity
function getP() internal pure returns (uint256)
```

### g1

```solidity
function g1() internal pure returns (struct AltBn128.G1Point)
```

### g2

```solidity
function g2() internal pure returns (struct AltBn128.G2Point)
```

### g2YFromX

```solidity
function g2YFromX(struct AltBn128.gfP2 _x) internal pure returns (struct AltBn128.gfP2 y)
```

g2YFromX computes a Y value for a G2 point based on an X value.
This computation is simply evaluating the curve equation for Y on a
given X, and allows a point on the curve to be represented by just
an X value + a sign bit.

### g1Compress

```solidity
function g1Compress(struct AltBn128.G1Point point) internal pure returns (bytes32)
```

Compress a point on G1 to a single uint256 for serialization.

### g2Compress

```solidity
function g2Compress(struct AltBn128.G2Point point) internal pure returns (bytes)
```

Compress a point on G2 to a pair of uint256 for serialization.

### g1Unmarshal

```solidity
function g1Unmarshal(bytes m) internal pure returns (struct AltBn128.G1Point)
```

Unmarshals a point on G1 from bytes in an uncompressed form.

### g1Marshal

```solidity
function g1Marshal(struct AltBn128.G1Point point) internal pure returns (bytes)
```

Marshals a point on G1 to bytes form.

### g2Unmarshal

```solidity
function g2Unmarshal(bytes m) internal pure returns (struct AltBn128.G2Point)
```

Unmarshals a point on G2 from bytes in an uncompressed form.

### g2Decompress

```solidity
function g2Decompress(bytes m) internal pure returns (struct AltBn128.G2Point)
```

Decompress a point on G2 from a pair of uint256.

### gfP2Add

```solidity
function gfP2Add(struct AltBn128.gfP2 a, struct AltBn128.gfP2 b) internal pure returns (struct AltBn128.gfP2)
```

Returns the sum of two gfP2 field elements.

### gfP2Multiply

```solidity
function gfP2Multiply(struct AltBn128.gfP2 a, struct AltBn128.gfP2 b) internal pure returns (struct AltBn128.gfP2)
```

Returns multiplication of two gfP2 field elements.

### gfP2Pow

```solidity
function gfP2Pow(struct AltBn128.gfP2 _a, uint256 _exp) internal pure returns (struct AltBn128.gfP2 result)
```

Returns gfP2 element to the power of the provided exponent.

### gfP2Square

```solidity
function gfP2Square(struct AltBn128.gfP2 a) internal pure returns (struct AltBn128.gfP2)
```

### gfP2Cube

```solidity
function gfP2Cube(struct AltBn128.gfP2 a) internal pure returns (struct AltBn128.gfP2)
```

### gfP2CubeAddTwistB

```solidity
function gfP2CubeAddTwistB(struct AltBn128.gfP2 a) internal pure returns (struct AltBn128.gfP2)
```

### g2X2y

```solidity
function g2X2y(struct AltBn128.gfP2 x, struct AltBn128.gfP2 y) internal pure returns (bool)
```

Returns true if G2 point's y^2 equals x.

### isG2PointOnCurve

```solidity
function isG2PointOnCurve(struct AltBn128.G2Point point) internal pure returns (bool)
```

Returns true if G2 point is on the curve.

