---
description: A backstop for assets secured by the tBTC protocol
---

# Coverage Pool

### Summary

Threshold Coverage Pool has been sunset via the [Sunsetting tBTC coverage pools](https://forum.threshold.network/t/sunsetting-tbtc-coverage-pools/714) DAO ratified proposal.

The Threshold Coverage Pool served as a backstop for assets secured by the tBTC protocol. In the event that secured Bitcoin were lost from the protocol, assets from the coverage pool were to be withdrawn by the risk manager, converted to BTC, and put back into the protocol to achieve the supply peg as closely as possible. The risk manager for the coverage pool was the [Threshold Treasury Guild](https://docs.threshold.network/governance/dao/guilds) multisig.

Over time, it became clear that this backstop model was sub-optimal given the relationship between tBTC and T - in the case of a BTC collateral loss event, T's value would also decline, greatly reducing its effectiveness as an insurance backstop. Consequently, the explicit Threshold Coverage Pool has been sunset with the approval of TIP-064.

Under the newly implemented "implicit coverage pool" model, tBTC is backed by the full faith and credit of Threshold DAO - by its treasury assets, which currently include T, ETH, wBTC, tBTC, stablecoins, CVX, veCRV, and associated LP tokens, etc.

{% hint style="info" %}
Join the discussion and ask questions about the coverage pool in the [Treasury Guild Channel](https://discord.com/channels/866378471868727316/971861255100981277) in the [Threshold Network Discord](https://discord.gg/threshold).
{% endhint %}
