---
description: A Bitcoin-backed Stablecoin
---

# Threshold USD

Threshold USD (thUSD) is a stablecoin pegged 1:1 against USD.

With either BTC or ETH as your collateral, you'll be able to borrow USD at low interest rate.

Threshold USD is a modified fork of [Liquity Protocol](https://www.liquity.org/) built to be self-sustained through a PCV ("Protocol Controlled Value"). There is no equivalent of LQTY token in Threshold USD. Instead all profits accrue into the PCV. Since there is no token, [Bootstrapping](threshold-usd/) is completed through an [Initial Protocol Loan](threshold-usd/initial-protocol-loan.md).&#x20;

The result of protocol owning its own liquidity ("PCV") is a more predictable trajectory and sustainability long-term. The stability pool is funded by the PCV instead of user deposits. Thanks to this, no funds are wasted on rewards to passive LQTY stakers (rented liquidity) and those funds can instead by re-injected into the stability pool. As the protocol grows and accrue fees, the stability pool will be consistently topped up.&#x20;

### Multi-collateral

Liquity is limited to ETH as the only type of collateral. Threshold USD expand to support BTC as collateral for loans through the decentralized BTC wrapper tBTC. In the future other types of collateral may be considered for on-boarding if they are sufficiently decentralized.&#x20;

Each collateral will be deployed with its own contracts and each new collateral will require a DAO vote with a lengthy time delay before deployment. By having each collateral be its own contract any collateral can easily be deprecated by disallowing minting on that specific contract.&#x20;

