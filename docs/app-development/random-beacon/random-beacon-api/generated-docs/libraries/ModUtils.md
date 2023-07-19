# Solidity API

## ModUtils

{% hint style="warning" %}
This file documents a code which is not yet deployed to Mainnet.
{% endhint %}

### modExp

```solidity
function modExp(uint256 base, uint256 exponent, uint256 p) internal view returns (uint256 o)
```

Wraps the modular exponent pre-compile introduced in Byzantium.
Returns base^exponent mod p.

### modSqrt

```solidity
function modSqrt(uint256 a, uint256 p) internal view returns (uint256)
```

Calculates and returns the square root of a mod p if such a square
root exists. The modulus p must be an odd prime. If a square root
does not exist, function returns 0.

### legendre

```solidity
function legendre(uint256 a, uint256 p) internal view returns (int256)
```

Calculates the Legendre symbol of the given a mod p.

#### Return Values

| Name | Type | Description |
| ---- | ---- | ----------- |
| [0] | int256 | Returns 1 if a is a quadratic residue mod p, -1 if it is a non-quadratic residue, and 0 if a is 0. |

