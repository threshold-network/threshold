---
description: A backstop for assets secured by the tBTC protocol
---

# Coverage Pool

### Summary

Threshold Coverage Pool serves as a backstop for assets secured by the tBTC protocol. In the event that secured Bitcoin is lost from the protocol, assets from the coverage pool are withdrawn by the risk manager, converted to BTC, and put back into the protocol to achieve the supply peg as closely as possible. The risk manager for the coverage pool is the [Threshold Treasury Guild](https://docs.threshold.network/governance/dao/guilds) multisig.

### Asset Strategy

Currently, the coverage pool contains only T tokens, with deposits sourced exclusively from the DAO treasury. The Threshold DAO is responsible for determining how much T enters the pool and how much to withdraw in the event of lost Bitcoin. The DAO has [determined via governance](https://etherscan.io/tx/0x26806758e05ff3f31d2e85a2875803905fc6c8a68b6c34b4201ebeb44399a27a) to allocate up to **50 million T** in coverage at this time. Threshold Treasury Guild can add T dynamically to the pool in accordance to market conditions up to this total amount allocated. Full rational for this strategy is outlined in Threshold Network Forum in [TIP 51](https://forum.threshold.network/t/tip-51-coverage-pool-allocation/610).&#x20;

### Evolution

Coverage pool in its current state is an iteration of tBTC risk management, and its evolution will be determined by Threshold DAO. In the future, Threshold DAO may decide to diversify pool assets to reduce coverage dependency to a single asset type. In addition, the DAO can propose a rewards mechanism to incentivize deposits into the coverage pool from outside parties in return for T rewards. Such a mechanism could grow TVL in the pool, providing even more coverage for tBTC users.

{% hint style="info" %}
Join the discussion and ask questions about the coverage pool in [Treasury Guild Channel](https://discord.com/channels/866378471868727316/971861255100981277) in Threshold Network Discord
{% endhint %}
