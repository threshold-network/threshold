---
description: >-
  The following demonstrates how to authorize applications for tBTC v2 and how
  to register your operator address via the Threshold Dashboard.
---

# Application Authorization & Operator Registration

One important step to get your node operating on the Threshold Network is proper application authorization as well as operator account registration. Applications need only be authorized once.

{% hint style="info" %}
It is **CRITICALLY** important that both the tBTC application as well as the Random Beacon applications are authorized. A node cannot be deployed without both applications being properly authorized.
{% endhint %}

{% hint style="info" %}
**Please note:** by authorizing these applications, an unbonding period of 45 days will go into effect on the T you stake for these applications. This cool-down period begins the day you submit an unstake request.
{% endhint %}

### Application Authorization

To get started, visit the [Threshold Dashboard](https://dashboard.threshold.network/) and connect your wallet.

<figure><img src="../../../.gitbook/assets/Authorize_All_Apps.png" alt=""><figcaption><p>Threshold Dashboard</p></figcaption></figure>

Click on "Configure Apps"

<figure><img src="../../../.gitbook/assets/Documentation_Authorize_Apps.png" alt=""><figcaption><p>Authorizing Threshold Applications</p></figcaption></figure>

Select BOTH tBTC and Random Beacon applications and enter your desired amount of T to stake per application. Note that the minimum is 40,000T.

<figure><img src="../../../.gitbook/assets/Documentation_AuthorizeAppsModal.png" alt=""><figcaption><p>Authorizing Threshold Applications</p></figcaption></figure>

### Operator Registration

The operator account is the Ethereum account created on your node. In order for the network to associate your T stake with your node, you must register your Operator Address.&#x20;

{% hint style="info" %}
An Operator for the Provider registration can be submitted just once. The Operator address assignment cannot be updated.
{% endhint %}

<figure><img src="../../../.gitbook/assets/Operator_Mapping-1.png" alt=""><figcaption><p>Registering Operator Address</p></figcaption></figure>

After both applications have been authorized, click on "Start Mapping" to begin the Operator Registration process.

<figure><img src="../../../.gitbook/assets/Operator_Mapping.png" alt=""><figcaption><p>Registering Operator Address</p></figcaption></figure>

Enter your Operator Address in the field provided and click on "Map Address."

Once the steps above have been successfully completed, you are ready to move on to the next step in the node deployment process.

{% hint style="info" %}
Don't forget: a tBTC v2 node will not be able to be deployed without successfully authorizing both the tBTC and Random Beacon applications, and registering the node's operator address **FIRST**.
{% endhint %}

