# Conditions Schema

This section focuses on Condition types and composition. To understand how Conditions are added and enforced with respect to runtime, check out the [Condition Hierarchies](../advanced-usage/condition-hierarchies.md) page. \
\
Several distinct categories of access conditions can be specified and combined:&#x20;

* EVM - on-chain state, eg NFT ownership, ETH balance, tx status, contract function call
* RPC - ethereum RPC calls as defined in the [Official API](https://ethereum.org/en/developers/docs/apis/json-rpc/#json-rpc-methods)
* Timelock - time-based conditions, eg Block Height

We provide many helper objects to streamline the creation of common conditions. An expressive API also allows much more granular control of conditions, and we will provide examples of both methods wherever possible.
