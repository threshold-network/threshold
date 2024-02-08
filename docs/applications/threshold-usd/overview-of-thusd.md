---
description: Background of Threshold USD, Main Parameters and Key Characteristics
---

# Overview of thUSD

## Introduction

[Threshold Protocol](https://threshold.network/earn/btc) launched in 2023 the second generation of [tBTC](https://dashboard.threshold.network/tBTC/mint), a decentralized and scalable bridge between Bitcoin and Ethereum. It provides Bitcoin holders secure and open access to the broader DeFi ecosystem.&#x20;

An immediate question that comes up is providing utility for the bridged tBTC for DeFi applications. Threshold USD provides an alternative utility to tBTC by allowing to mint thUSD, which then can be used for multiple purposes in a wider ecosystem.

## What is Threshold USD?

Threshold USD is a decentralized borrowing protocol that allows you to draw loans using tBTC and ETH as collateral. Loans are paid out in thUSD (a USD pegged stablecoin) and need to maintain a minimum collateral ratio of 110%.

In addition to the collateral, the loans are secured by a Stability Pool containing thUSD and by fellow borrowers collectively acting as guarantors of last resort. Learn more about these mechanisms under [Liquidation](stability-pool-and-liquidations.md#what-are-liquidations).

Threshold USD as a protocol is based on a fork of Liquity ([https://www.liquity.org/](https://www.liquity.org/)), which has established itself as a successful protocol since its launch on May 2021, going through several severe stress tests successfully.

Liquity is totally governance free and immutable; although that is a tremendous strength for a DeFi Protocol as it makes it completely censorship resistant, it also exposes the protocol to existential oracle risk and limits its ability to scale and respond to market dynamics.&#x20;

With that in mind some flexibilitiy is embeded into Threshold USD. It has a few parameters that can be changed through governance:

* **Collateral Type:** in addition to tBTC other collateral types may be added (ETH will be an additional collateral to tBTC, as it is the most decentralized and abundant asset in Ethereum) and also removed by revoking mint and burn capabilities.
* **Price Feed Contract:** Oracles are critical for the health of the protocol (defining liquidations) and may need to be updated due to poor performance of existing oracles
* **PCV Contract:** Withdraw from BAMM to PCV, pay the debt and withdraw funds from PCV after repaying all the debt.
* **BAMM Contract:** Dao has veto capability for change submission to the "A" parameter and B.Protocol fees.

This documentation is based on the Liquity documents ([https://docs.liquity.org/](https://docs.liquity.org/)) with the appropriate changes that are relevant to Threshold USD.

## What’s the motivation behind Threshold USD?

Stable-value assets are an essential building block for Ethereum applications and have grown to represent tens of billions of dollars in value.&#x20;

However, the vast majority of this value is in the form of fiat-collateralized stablecoins like USDT and USDC. Decentralized stablecoins like DAI and sUSD make up only a small portion of the total stablecoin supply, meaning the vast majority of stablecoins are centralized.

Threshold USD addresses this by creating a more capital efficient and user-friendly way to borrow stablecoins.

## What are the key benefits of Threshold USD?

Threshold USD’s key benefits include:

* Low cost for tBTC loan (0.5% issuance fee and no interest rate at launch).
* Minimum collateral ratio of 110% — more efficient usage of deposited tBTC
* Governance Minimized— most operations are algorithmic and fully automated, and most protocol parameters are set at time of contract deployment.&#x20;
  * Note: See section above for parameters that can be changed through governance
* Directly redeemable —  thUSD can be redeemed at face value for the underlying collateral at any time

## Can Threshold USD be upgraded or changed?

Yes. The Threshold USD token contracts mint function will be able to add additional contracts; this is how we keep the option to upgrade while leaving all the contracts immutable with the exception of the price feed contract.

## How can I use Threshold USD?

You can use thUSD via a web interface (aka frontend) or directly via the smart contracts.&#x20;

## What are the main use cases of Threshold USD?

* Borrow thUSD against tBTC by opening a Vault and use directly in DeFi applications or exchange for other coins
* Secure Threshold USD by providing thUSD to the Stability Pool in exchange for Liquidation rewards
* Providing liquidity to the thUSD Pool in Curve ([https://curve.fi/](https://curve.fi/)) to earn swapping fees and maybe incentive fees (these may be time dependent and could be turned on or off at will)
* Redeem 1 thUSD for 1 USD worth of tBTC when the thUSD peg falls below $1 (i.e. arbitrage the system)

## What is thUSD?

thUSD is the USD-pegged stablecoin used to pay out loans on the Threshold USD protocol. At any time it can be redeemed against the underlying collateral at face value.&#x20;

## What do I need in order to use Threshold USD?

To borrow thUSD, all you need is a wallet (e.g. MetaMask or [TaHo](https://taho.xyz/)) and sufficient tBTC or ETH to open a Vault and pay the gas fees.&#x20;

To become a Stability Pool depositor you need to have thUSD, which can be borrowed by opening a Vault. You can also use Curve or another (decentralized) exchange to buy the tokens on the open market.

## Does Threshold USD charge any fees?

These are the fees for using Threshold USD Protocol:

* A one-time fee whenever thUSD is borrowed, as a percentage of the drawn amount (in thUSD)
* A fee when thUSD is redeemed (see Section “Redemptions and thUSD Price Stability” for details on the Redemption Mechanism)
  * For redeemers, there is a redemption fee on the amount paid to users by the system (in tBTC) when exchanging thUSD for tBTC. Note that redemption is separate from repaying your loan as a borrower, which is free of charge
* Both fees depend on the redemption volumes, i.e. they increase upon every redemption in function of the redeemed amount, and decay over time as long as no redemptions take place. The intent is to throttle large redemptions with higher fees, and to throttle borrowing directly after large redemption volumes. The fee decay over time ensures that the fee for both borrowers and redeemers will “cool down”, while redemptions volumes are low.
* The fees cannot become smaller than 0.5% (except in Recovery Mode), which protects the redemption facility from being misused by arbitrageurs front-running the price feed. The borrowing fee is capped at 5%, keeping the system (somewhat) attractive for borrowers even in phases where the monetary is contracting due to redemptions. Other than that, the two fees are identical.
*   **Interest rate:** at launch no ongoing interest rate is charged when using ETH and tBTC as collateral, only the initial borrowing fee.

    The decision to incorporate an ongoing interest rate that will accrue on the loan amount in thUSD, which is calculated based on a predetermined interest rate and the amount of time that has elapsed, will be made by the DAO in the near future for these and any other future collaterals.

## How can I earn money using Threshold USD?

There are two main ways to generate revenue using Threshold USD:

* Providing liquidity to the thUSD Pool in Curve ([https://curve.fi/](https://curve.fi/)) to earn swapping fees and maybe incentive fees (these may be time dependent and could be turned on or off at will)
* Deposit thUSD to the Stability Pool and earn liquidation gains (in tBTC).&#x20;

Other opportunities may arise in time in the DeFi Ecosystem to use thUSD and earn rewards or yield.

## Can I lose my funds?

As a non-custodial system, all the tokens sent to the protocol will be held and managed algorithmically without the interference of any person or legal entity. That means your funds will only be subject to the rules set forth in the smart contract code.

You may lose all or part of your funds in a Liquidation scenario:

* You are a borrower (Vault owner) and your collateral in tBTC is liquidated. You will still keep your borrowed thUSD, but your Vault will be closed and your collateral will be used to compensate Stability Pool depositors.
  * If the liquidation happens under Normal Mode (i.e. your Vault is at 110% Collateral Ratio), all your Collateral will be used the Liquidation and fees
  * If the liquidation happens under Recovery Mode (i.e. Total Protocol Collateral Ratio is less than 150%), you may recover a part of the Collateral if your Vault is between the 110% and 150% Collateral Ratio)

In addition, the following Scenarios may happen that may lead to loss of funds:

* **Bugs/Hacks:** Please note that although the system is diligently audited, a hack or a bug that results in losses for the users can never be fully excluded. This includes potential bugs/hacks on tBTC as it is the collateral for thUSD.
* **Price Oracle Risk:** Liquidations are triggered by the Collateral Ratio, which is a function of the Collateral Price. There is a risk of malfunction of manipulation with the Price Feed. We are minimizing the exposure to those risks, but they cannot be completely ruled out
* If the Threshold USD is not accepted and all users want to leave, the last fraction of collateral cannot be recovered as there won’t be enough thUSD available (due to Loan fees accrued). This would only affect a very minor fraction of capital
* For redemptions, in case of an acceleration of redemptions, the redemption fee increases and can lead to losses. If the Total Collateral Ratio is less than 110%, redemptions are blocked
