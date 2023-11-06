# Conditions

This section focuses on Condition types and composition. To understand how Conditions are added and enforced with respect to runtime, check out the [Condition Hierarchies](broken-reference) page. \
\
Several distinct categories of access conditions can be specified and combined:&#x20;

* `ContractCondition` - on-chain state, eg NFT ownership, ETH balance, tx status, contract function call
* `RpcCondition` - Ethereum RPC calls as defined in the [Official API](https://ethereum.org/en/developers/docs/apis/json-rpc/#json-rpc-methods)
* `TimelockCondition` - time-based conditions, eg. `blocktime`
* `CompoundCondition` - a higher-order condition, used to compose conditions with logical operators, such as `or`, `and`, and so on.

We provide many helper objects to streamline the creation of common conditions. An expressive API also allows much more granular control of conditions, and we will provide examples of both methods wherever possible.
