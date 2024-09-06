---
description: >-
  This is a mechanism to reduce systemic risk to thUSD by ensuring a total
  collateral ratio across all Vaults remain above 150%
---

# Recovery Mode

## What is Recovery Mode?

Recovery Mode kicks in when the Total Collateral Ratio (TCR) of the system falls below 150%.

During Recovery Mode, Vaults with a collateral ratio below 150% can be liquidated.

Moreover, the system blocks borrower transactions that would further decrease the TCR. New thUSD may only be issued by adjusting existing Vaults in a way that improves their collateral ratio, or by opening a new Vault with a collateral ratio>=150%. In general, if an existing Vault's adjustment reduces its collateral ratio, the transaction is only executed if the resulting TCR is above 150%.

## What is the Total Collateral Ratio?

The Total Collateral Ratio or TCR is the ratio of the Dollar value of the entire system collateral at the current ETH:USD price, to the entire system debt. In other words, it's the sum of the collateral of all Vaults expressed in USD, divided by the debt of all Vaults expressed in thUSD.

## What is the purpose of Recovery Mode?

The goal of Recovery Mode is to incentivize borrowers to behave in ways that promptly raise the TCR back above 150%, and to incentivize thUSD holders to replenish the Stability Pool.

Economically, Recovery Mode is designed to encourage collateral top-ups and debt repayments, and also itself acts as a self-negating deterrent: the possibility of it occurring actually guides the system away from ever reaching it. Recovery Mode is not a desirable state for the system.

## What are the fees during Recovery Mode

While Recovery Mode has no impact on the redemption fee, the borrowing fee is set to 0% to maximally encourage borrowing (within the limits described[ above](https://docs.liquity.org/faq/recovery-mode#what-is-recovery-mode)).

## How can I make my Vault safe in Recovery Mode?

By increasing your collateral ratio to 150% or greater, your Vault will be protected from liquidation. This can be done by adding collateral, repaying debt, or both.

## Can I be liquidated if my collateral ratio is below 150% in Recovery Mode?

Yes, you can be liquidated below 150% if your Vault's collateral ratio is smaller than 150%. In order to avoid liquidation in Normal Mode and Recovery Mode, a user should keep their collateral ratio above 150%.

## How much of a Vault’s collateral can be liquidated in Recovery Mode?

In Recovery Mode, liquidation loss is capped at 110% of a Vault's collateral. Any remainder, i.e. the collateral above 110% (and below the TCR), can be reclaimed by the liquidated borrower using the standard web interface.

This means that a borrower will face the same liquidation “penalty” (10%) in Recovery Mode as in Normal Mode if their Vault gets liquidated.

\
