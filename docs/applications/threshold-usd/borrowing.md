---
description: Key aspects and parameters of borrowing thUSD
---

# Borrowing

## Why would I use Threshold USD for borrowing?

Threshold USD protocol offers low interest loans and is more capital efficient than other borrowing systems (i.e. less collateral is needed for the same loan). Instead of selling tBTC to have liquid funds, you can use the protocol to lock up your tBTC, borrow against the collateral to withdraw thUSD, and then repay your loan at a future date.

For example: Borrowers speculating on future tBTC price increases can use the protocol to leverage their tBTC positions up to 11 times, increasing their exposure to price changes. This is possible because thUSD can be borrowed against tBTC, sold on the open market to purchase more tBTC — rinse and repeat.\*

\*Note: This is NOT a recommendation for how to use Threshold USD. Leverage can be very risky as you can lose all your funds and should be used only by those with experience and for speculative amounts.

## What do you mean by collateral?

Collateral is any asset which a borrower must provide to take out a loan, acting as a security for the debt.&#x20;

## Is tBTC the only collateral accepted by Threshold USD?

Currently, Threshold USD supports tBTC and ETH as collateral but additional collaterals could be added as voted by governance, as the code allows for it. The reasoning behind having these two collaterals is that they represent the two most decentralized assets in the Ethereum Ecosystem.

## How can I borrow with Threshold USD?

To borrow you must open a Vault and deposit a certain amount of collateral (tBTC) to it. Then you can draw thUSD up to a collateral ratio of 110%. A minimum debt of 2,000 thUSD is required. This minimum debt threshold prevents an attacker from creating a large number of Vaults that are then unprofitable to liquidate.

## What is a Vaults?

A Vault is where you take out and maintain your loan. Each Vault is linked to an Ethereum address and each address can have just one Vault. If you are familiar with Troves or CDPs from other platforms, Vault are similar in concept.

Vaults maintain two balances: one is an asset (tBTC) acting as collateral and the other is a debt denominated in thUSD. You can change the amount of each by adding collateral or repaying debt. As you make these balance changes, your Vault’s collateral ratio changes accordingly.

You can close your Vault at any time by fully paying off your debt.

## Do I have to pay fees as a borrower?

For the base situation, see the section “Does Threshold USD charge any fees?”

Every time you draw thUSD from your Vault, a one-off borrowing fee is charged on the drawn amount and added to your debt. Please note that the borrowing fee is variable (and determined algorithmically) and has a minimum value of 0.5% under normal operation. The fee is 0% during[ Recovery Mode](https://docs.liquity.org/faq/recovery-mode).&#x20;

A 200 thUSD [ Liquidation Reserve](https://docs.liquity.org/faq/borrowing#what-is-the-liquidation-reserve) charge will be applied as well, but returned to you upon repayment of debt.

## How is the borrowing fee calculated?

When a Loan is opened, the borrowing fee is added to the debt of the Vault and is given by a baseRate . The fee rate is confined to a range between 0.5% and 5% and is multiplied by the amount of liquidity drawn by the borrower.

For example: The borrowing fee stands at 0.5% and the borrower wants to receive 4,000 thUSD to their wallet. Being charged a borrowing fee of 20.00 thUSD, the borrower will incur a debt of 4,220 thUSD after the Liquidation Reserve and issuance fee are added.

## When do I need to pay my loan back?

Loans issued by the protocol do not have a repayment schedule. You can leave your Vault open and repay your debt any time, as long as you maintain a collateral ratio of at least 110%.

## What is the collateral ratio?

This is the ratio between the Dollar value of the collateral in your Vault and its debt in thUSD. The collateral ratio of your Vault will fluctuate over time as the price of tBTC changes. You can influence the ratio by adjusting your Vault’s collateral and/or debt — i.e. adding more tBTC collateral or paying off some of your debt.

For example: Let’s say the current price of tBTC is $30,000 and you decide to deposit 1 tBTC. If you borrow 10,000 thUSD, then the collateral ratio for your Vault would be 200%.

(1tBTC \* $30,000/​10,000 thUSD)\*100 = 300%

​If you instead took out 15,000 thUSD that would put your ratio at 200%.

## What is the minimum collateral ratio (MCR) and the "recommended" collateral ratio?

The minimum collateral ratio (or MCR for short) is the lowest ratio of debt to collateral that will not trigger a liquidation under normal operations (aka Normal Mode). This is a protocol parameter that is set to 110%. So if your Vault has a debt of 10,000 thUSD, you would need at least $11,000 worth of tBTC posted as collateral to avoid being liquidated.

To avoid liquidation during[ Recovery Mode](https://docs.liquity.org/faq/recovery-mode), it is recommended to keep the ratio comfortably above 150% (e.g. 200% or better 250%).

## What happens if my Vault is liquidated?

You lose your collateral as your debt is paid off through[ liquidation](https://docs.liquity.org/faq/stability-pool-and-liquidations#what-are-liquidations), i.e. you will no longer be able to retrieve your collateral by repaying your debt. A liquidation thus results in a net loss of 9.09% (= 100% \* 10 / 110) of your collateral’s Dollar value.

## What is the Liquidation Reserve?

When you open a Vault and draw a loan, 200 thUSD is set aside as a way to compensate gas costs for the transaction sender in the event your Vault gets liquidated. The Liquidation Reserve is fully refundable if your Vault is not liquidated, and is given back to you when you close your Vault by repaying your debt. The Liquidation Reserve counts as debt and is taken into account for the calculation of a Vault's collateral ratio, slightly increasing the actual collateral requirements.

## What happens if my Vault is redeemed against?

When thUSD is redeemed, the tBTC provided to the redeemer is allocated from the Vault(s) with the lowest collateral ratio (even if it is above 110%). If at the time of redemption you have the Vault with the lowest ratio, you will give up some of your collateral, but your debt will be reduced accordingly.&#x20;

The USD value by which your tBTC collateral is reduced corresponds to the nominal thUSD amount by which your Vault’s debt is decreased. You can think of redemptions as if somebody else is repaying your debt and retrieving an equivalent amount of your collateral. As a positive side effect, redemptions improve the collateral ratio of the affected Vaults, making them less risky.

Redemptions that do not reduce your debt to 0 are called partial redemptions, while redemptions that fully pay off a Vault’s debt are called full redemptions. In such a case, your Vault is closed, and you can claim your collateral surplus and the Liquidation Reserve at any time.

Let’s say you own a Vault with 2 tBTC collateralized and a debt of 32,000 thUSD. The current price of tBTC is $20,000. This puts your collateral ratio (CR) at 125% (= 100% \* (2 \* 20,000) / 32,000). Let’s imagine this is the lowest CR in the Threshold USD system and look at two examples of a partial redemption and a full redemption:

### Example of a partial redemption

Somebody redeems 12,000 thUSD for 0.6 tBTC and thus repays 12,000 thUSD of your debt, reducing it from 32,000 thUSD to 20,000 thUSD. In return, 0.6 tBTC, worth $12,000, is transferred from your Vault to the redeemer. Your collateral goes down from 2 to 1.4 tBTC, while your collateral ratio goes up from 125% to 140% (= 100% \* (1.4 \* 20,000) / 20,000).

### Example of a full redemption

Somebody redeems 60,000 thUSD for 3 tBTC. Given that the redeemed amount is larger than your debt minus 200 thUSD (set aside as a Liquidation Reserve), your debt of 32,000 thUSD is entirely cleared and your collateral gets reduced by $30,000 of tBTC, leaving you with a collateral of 0.5 tBTC (= 2 - 30,000 / 20,000).

## How can you offer a collateral ratio as low as 110%?

By making liquidation instantaneous and more efficient, Threshold USD needs less collateral to provide the same security level as similar protocols that rely on lengthy auction mechanisms to sell off collateral in liquidations.

## How can I take advantage of leverage?

You can sell the borrowed thUSD on the market for tBTC and use the latter to top up the collateral of your Vault. That allows you to draw and sell more thUSD, and by repeating the process you can reach the desired leverage ratio.

\
**Note:** This is NOT a recommendation for how to use Threshold USD. Leverage can be very risky and should be used only by those with experience and still will most probably end up with a Liquidation, so this should be avoided.

## Why did the collateral and debt of my Vault increase without my intervention?

If Vaults are liquidated and the Stability Pool is empty (or gets emptied due to the liquidation), every borrower will receive a portion of the liquidated collateral and debt as part of a redistribution process.

\
