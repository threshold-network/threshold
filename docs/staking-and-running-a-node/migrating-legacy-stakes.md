# Legacy Staking (NU & KEEP)

Legacy Stakes for Threshold can take the following forms:

* T stake using liquid T created on the [Threshold Dashboard](https://dashboard.threshold.network/staking)
* Legacy NU: must be withdrawn, upgraded to T, then staked as liquid T
* Legacy KEEP: must be withdrawn, upgraded to T, then staked as liquid T

Here is a summary of the overall process:

<figure><img src="../.gitbook/assets/staking_initialization_process (1).png" alt=""><figcaption><p>Stake Creation</p></figcaption></figure>

## Stake Liquid T

* Go to [https://dashboard.threshold.network/staking](https://dashboard.threshold.network/staking)
* Connect your wallet
* Your T balance will be shown - click on _"Stake"_
* There will be a pop-up describing the staking process; click the check-box to acknowledge you have read the requirements
* Choose an amount of T to stake and confirm the transaction

## Migrate Legacy NU Stake

* Execute `withdraw` method on legacy [Staking Escrow Contract](https://etherscan.io/address/0xbbD3C0C794F40c4f993B03F65343aCC6fcfCb2e2#writeProxyContract#F7)
* [Upgrade Liquid NU to Liquid T](../threshold-dashboard/upgrade-nu-and-keep-to-t.md)
* [Stake Liquid T](migrating-legacy-stakes.md#stake-liquid-t)

## Migrate Legacy KEEP Stake

* Withdraw Legacy Keep via [legacy KEEP dashboard](https://dashboard.keep.network/overview)
* [Upgrade Liquid KEEP to Liquid T](../threshold-dashboard/upgrade-nu-and-keep-to-t.md)
* [Stake Liquid T](migrating-legacy-stakes.md#stake-liquid-t)
