# Conditions

This section focuses on `Condition` types and composition. There are three distinct, high-level categories of TACo access conditions:&#x20;

* `TimeCondition` – time-based conditions using block height and other blockchain based timestamps. E.g. allow access for one week after the first request is made.&#x20;
* `RpcCondition` –  based on RPC calls as defined in Ethereum's Official API [documentation](https://ethereum.org/en/developers/docs/apis/json-rpc/#json-rpc-methods). E.g. allow access if the address held ETH before 2020.&#x20;
* `ContractCondition` – based on on-chain state, and can be any arbitrary contract function call. E.g. allow access if this requestor holds a special-purpose NFT.&#x20;

Each access condition defines a `returnValueTest` used to compare the obtained value with the expected value for the condition.

Access conditions can also be combined via a `CompoundCondition` which provides the `or`, `and` & `not` logical operators.

[Context variables](conditioncontext-and-context-variables.md) provide the ability for placeholder values to be defined for conditions at encryption time, which will be dynamically populated at decryption time e.g. current user wallet address.

{% hint style="info" %}
Multiple helper objects are provided to streamline the creation of conditions. An expressive API allows for granular control of conditions and examples of methods wherever possible.
{% endhint %}
