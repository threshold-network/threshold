---
description: Mechanisms to keep thUSD solvent under changing collateral price
---

# Stability Pool and Liquidations

## What is the Stability Pool?

The Stability Pool is the first line of defense in maintaining system solvency. It achieves that by acting as the source of liquidity to repay debt from liquidated Vaults—ensuring that the total thUSD supply always remains backed.

When any Vault is liquidated, an amount of thUSD corresponding to the remaining debt of the Vault is burned from the Stability Pool’s balance to repay its debt. In exchange, the entire collateral from the Vault is transferred to the Stability Pool.

The Stability Pool is funded by users transferring thUSD into it (called Stability Providers). Over time Stability Providers lose a pro-rata share of their thUSD deposits, while gaining a pro-rata share of the liquidated collateral. However, because Vaults are likely to be liquidated at just below 110% collateral ratios, it is expected that Stability Providers will receive a greater dollar-value of collateral relative to the debt they pay off.

## Why should I deposit thUSD to the Stability Pool?

Stability Pool Providers will make liquidation gains.

## What are liquidations?

To ensure that the entire Stablecoin supply remains fully backed by collateral, Vaults that fall under the minimum collateral ratio of 110% will be closed (liquidated).

The debt of the Vault is canceled and absorbed by the Stability Pool and its collateral distributed among Stability Providers.

The owner of the Vault still keeps the full amount of thUSD borrowed but loses \~10% value overall hence it is critical to always keep the ratio above 110%, ideally above 150%.

## Who can liquidate Vaults?

Anybody can liquidate a Vault as soon as it drops below the Minimum Collateral Ratio of 110%. The initiator receives a gas compensation (200 thUSD + 0.5% of the Vaults collateral) as reward for this service.

## How am I compensated for liquidating a Vault?

The liquidation of Vaults is connected with certain gas costs which the initiator has to cover. The cost per Vault was reduced by implementing batch liquidations of up to 160 - 185 Vaults but with the aim of ensuring that liquidations remain profitable even in times of soaring gas prices the protocol offers a gas compensation given by the following formula:

gas compensation = 200 thUSD + 0.5% of Vault's collateral (ETH)

The 200 thUSD is funded by a Liquidation Reserve while the variable 0.5% part (in tBTC) comes from the liquidated collateral, slightly reducing the liquidation gain for Stability Providers.

## How do I benefit as a Stability Provider from liquidations?

As liquidations happen just below a collateral ratio of 110%, you will most likely experience a net gain whenever a Vault is liquidated.

Let’s say there is a total of 1,000,000 thUSD in the Stability Pool and your deposit is 100,000 thUSD.

Now, a Vault with debt of 200,000 thUSD and collateral of 10.9 tBTCis liquidated at a tBTC price of $20,000, and thus at a collateral ratio of 109% (= 100% \* (10.9 \* 20,000) / 200,000). Given that your pool share is 10%, your deposit will go down by 10% of the liquidated debt (20,000 thUSD), i.e. from 100,000 to 80,000 thUSD. In return, you will gain 10% of the liquidated collateral, i.e. 1.09 tBTC, which is currently worth $21,800. Your net gain from the liquidation is $1,800.

Note that depositors can immediately withdraw the collateral received from liquidations and sell it to reduce their exposure to tBTC, if the USD value of tBTC is expected to decrease (for an exception see the next section).

## Can I withdraw my deposit whenever I want?

As a general rule, you can withdraw the deposit made to the Stability Pool at any time. There is no minimum lockup duration. However, withdrawals are temporarily suspended whenever there are liquidatable Vault with a collateral ratio below 110% that have not been liquidated yet.

## What Oracle are you using to determine the price of tBTC?

The protocol uses[ Chainlink’s tBTC:USD](https://feeds.chain.link/eth-usd) price feed, falling back to the[ Tellor](https://www.tellor.io/) tBTC:USD oracle under the following (extreme) conditions:

* Chainlink price has not been updated for more than 4 hours
* Chainlink response call reverts, returns an invalid price or an invalid timestamp
* The price change between two consecutive Chainlink price updates is >50%.

## Can I lose money by depositing funds to the Stability Pool?

While liquidations will occur at a collateral ratio well above 100% most of the time, it is theoretically possible that a Vault gets liquidated below 100% in a flash crash or due to an oracle failure. In such a case, you may experience a loss since the collateral gain will be smaller than the reduction of your deposit.

If thUSD is trading above $1, liquidations may become unprofitable for Stability Providers even at collateral ratios higher than 100%. However, this loss is hypothetical since thUSD is expected to return to the peg, so the “loss” only materializes if you had withdrawn your deposit and sold the thUSD at a price above $1.

Please note that although the system is diligently audited, a hack or a bug that results in losses for the users can never be fully excluded.

## What happens if the Stability Pool is empty when liquidations occur?

If the Stability Pool is empty, the system uses a secondary liquidation mechanism called redistribution. In such a case, the system redistributes the debt and collateral from liquidated Vaults to all other existing Vaults. The redistribution of debt and collateral is done in proportion to the recipient Vault's collateral amount.&#x20;

\
