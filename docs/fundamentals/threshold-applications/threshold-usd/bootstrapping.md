# Bootstrapping

A certain network effect is required for a stablecoin to function well. Bootstrapping is the process of ensuring the protocol has gained enough traction to become self-sufficient.&#x20;

In the past this was usually accomplished by being an early adopter of coins, such as buying Bitcoin early and benefiting from the lower price. DeFi revolutionized the bootstrapping concept by enabling the bootstrapping of capital through yield-farming (aka rented liquidity).&#x20;

Liquity Protocol bootstrapped by issuing LQTY tokens to depositors in the stability pool. This incentivized people to take out loans and deposit to the stability pool.&#x20;

For normal, healthy operations of the protocol, it's vital that there is sufficient funds in the stability pool to cover all liquidations. However the LQTY incentive resulted in far more deposits than is required and this is essentially wasted capital. Furthermore there are concerns on how sustainable Liquity will be after the initial 100 million LQTY has been issued and the pool is only sustained through liquidations rewards.&#x20;

In Threshold USD we take a different approach. A newer concept in DeFi known as Protocol Controlled Value (PCV) is the idea that the protocol itself can own liquidity and use that to improve the protocol. This is a new alternative to renting it elsewhere (aka yield-farming) and has the benefit of long-term sustainability.&#x20;

But the PCV has to come from somewhere. In thUSD we resolve this by issuing an [Initial Protocol Loan](initial-protocol-loan.md) to the PCV which then deposit these funds directly to the stability pool.&#x20;

That lets us bootstrap the stability pool at zero cost to the protocol.

But not only that, by eliminating the LQTY token, **all profits** (from interest, etc) will go directly into the PCV. Compared to Liquity, where profits are distributed among LQTY holders, we have eliminated a massive drain on the system, at no cost.&#x20;

Having the stability pool funded on its own is enough for the protocol to function, and we can expect some users that want to borrow against their tBTC to take part, but it's not going to create major adoption, and for thUSD to function well it needs to be stable at \~$1, that requires people to put up liquidity at decentralized exchanges.

In order to draw in more users and create liquidity for thUSD, we will issue rewards to pools on other platforms (such as a thUSD stablecoin pool on curve). This will incentivize users to take debt and deposit into curve, with the added benefit of improved liquidity and resiliency.&#x20;

How fast we want to grow can be almost entirely adjusted by how much reward is sent to the pool.

This part is indeed rented liquidity with all its drawbacks, but remember that Threshold USD profits are sent to the PCV instead of LQTY holders: As the protocol grows in popularity, profits from the protocol itself can be used to fund these rewards, thus creating a self-sustaining feedback loop.

On top of that, Threshold DAO will store some of its reserves in stablecoins, so the Threshold DAO can initiate a PCV with deposits on curve. That will result in more stability for thUSD which is important for adoption.&#x20;
