# TACo Authorization & Operator Registration

The following demonstrates how to authorize the TACo application and register your operator address via the Threshold Dashboard. These are required for running a TACo node. The TACo application only needs to be authorized once.

{% hint style="info" %}
Authorizing an application assumes that you already have a T stake.
{% endhint %}

{% hint style="danger" %}
**Please note:** by authorizing the TACo application, a minimum unbonding period of 6 months will go into effect on the T you stake. This cool-down period will begin the moment you submit an unstake request.
{% endhint %}

## TACo Application Authorization

To get started, visit the [Threshold Dashboard](https://dashboard.threshold.network/staking) and connect your wallet.

<figure><img src="../../../.gitbook/assets/Screenshot 2023-12-11 at 9.20.37 AM.png" alt=""><figcaption><p>Staking Dashboard</p></figcaption></figure>

Click on `Configure Apps`

<figure><img src="../../../.gitbook/assets/Screenshot 2023-12-11 at 9.19.46 AM.png" alt=""><figcaption><p>Configure Applications</p></figcaption></figure>

Select `TACO APP` and enter your desired amount of T to stake. Note that the minimum is 40,000 T.

<figure><img src="../../../.gitbook/assets/Screenshot 2023-12-11 at 9.04.59 AM.png" alt=""><figcaption><p>Authorize TACo</p></figcaption></figure>

Click `Authorize TACo`

<figure><img src="../../../.gitbook/assets/Screenshot 2023-12-11 at 9.05.21 AM.png" alt=""><figcaption><p>TACo Authorization Confirmation</p></figcaption></figure>

Click `Authorize` and confirm the transaction.

<figure><img src="../../../.gitbook/assets/Screenshot 2023-12-11 at 9.06.00 AM.png" alt=""><figcaption><p>TACo Authorization Confirmed</p></figcaption></figure>

Exit out of the modal dialogue

<figure><img src="../../../.gitbook/assets/Screenshot 2023-12-11 at 9.06.42 AM.png" alt=""><figcaption><p>TACo Authorized</p></figcaption></figure>

## Operator Account Registration

The operator account is the Ethereum account used/to be used by the TACo node. In order for the network to associate your T stake with your node, you must register your _Operator Address_. Once the TACo application has been authorized, you can begin the operator registration process.

Navigate to the [Staking Page](https://dashboard.threshold.network/staking) on the Threshold Dashboard, and click on _Start Mapping_&#x20;

<figure><img src="../../../.gitbook/assets/Screenshot 2023-12-12 at 9.21.52 AM.png" alt=""><figcaption><p>Start Mapping</p></figcaption></figure>

and an _Operator Address Mapping_ pop-up should be displayed.

<figure><img src="../../../.gitbook/assets/Screenshot 2023-12-11 at 4.05.00 PM.png" alt=""><figcaption><p>Operator Address Mapping Popup</p></figcaption></figure>

Scroll down to the `TACO` section.

<figure><img src="../../../.gitbook/assets/Screenshot 2023-12-11 at 4.05.08 PM.png" alt=""><figcaption><p>TACo Operator Mapping</p></figcaption></figure>

Enter your operator address, click `Map Address`, and confirm the transaction.

{% hint style="info" %}
Once the map operator address transaction executes on Ethereum Mainnet, it can take \~25 minutes to sync to Polygon Mainnet, which TACo nodes check on startup. The TACo node will wait for this sync to occur before starting up.
{% endhint %}

