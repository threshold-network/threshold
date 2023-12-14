# Conditions

This section focuses on Condition types and composition. \
\
Several distinct categories of access conditions can be specified:&#x20;

* `TimeCondition` - blockchain time-based conditions
* `RpcCondition` - Ethereum RPC calls as defined in the [Official API](https://ethereum.org/en/developers/docs/apis/json-rpc/#json-rpc-methods)
* `ContractCondition` - on-chain state, e.g. NFT ownership, ETH balance, arbitrary contract function call

Each access condition defines a `returnValueTest` used to compare the obtained value with the expected value for the condition.

Access conditions can also be combined via a `CompoundCondition` which provides the `or`, `and` , `not` logical operators.

[Context variables](condition-context-and-context-variables.md) provide the ability for placeholder values to be defined for conditions at encryption time, which will be dynamically populated at decryption time e.g. current user wallet address.

Many helper objects are provided to streamline the creation of conditions. An expressive API allows much more granular control of conditions and examples of both methods wherever possible.
