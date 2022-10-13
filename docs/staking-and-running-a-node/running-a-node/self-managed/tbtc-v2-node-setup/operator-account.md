---
description: Prepare your machine to install the tBTC v2 staking client
---

# Operator Account

The client requires an Ethereum Key File of an Operator Account to connect to the Ethereum chain. This account is created in a subsequent step using Geth (GoEthereum).

The Ethereum Key File is expected to be encrypted with a password. The password has to be provided in a prompt after the client starts or configured as a `KEEP_ETHEREUM_PASSWORD` environment variable.

The Operator Account has to maintain a positive Ether balance at all times. We strongly advise you monitor the account and top-up when its balance gets below 0,5 Ether.

{% hint style="warning" %}
Please do NOT reuse an operator account that is being used for PRE or other applications.&#x20;
{% endhint %}

### Install Geth (GoEthereum)

To create a new Ethereum account, [install Geth](https://geth.ethereum.org/docs/install-and-build/installing-geth#ubuntu-via-ppas) (GoEthereum) and create a new account using the command below. This account will subsequently be referred to as the Operator Account.

<pre class="language-bash"><code class="lang-bash"><strong>geth account new --keystore ./operator-key</strong></code></pre>

When prompted, provide a password to protect the operator key file.&#x20;

{% hint style="info" %}
Use a password manager to generate a strong password and store it safely. It will be needed again during setup.
{% endhint %}

{% hint style="warning" %}
Avoid passwords that contain the following characters: ', ", \`, $\
These characters may be interpreted as part of the configuration which can lead to undesirable outcomes that may be extremely time intensive to correct.
{% endhint %}

Once the process completes, your public key will be displayed. Take note of your Operator Account public key.

{% hint style="danger" %}
DO NOT LOSE THE PASSWORD TO THE OPERATOR ACCOUNT.
{% endhint %}

### Funding your Operator Account

Your Operator Account will need to maintain a positive ETH balance at all times to ensure proper operation and availability of your tBTC v2 node.
