---
description: Instructions for setting up a TACo node on a server.
---

# Run a TACo Node with Docker

{% hint style="warning" %}
Attention PRE Node Operators:\
\
It is strongly recommended not to use the same server for both PRE and TACo Node functions. This practice can lead to unnecessary complexity and a departure from the standard operating procedures outlined in our documentation.
{% endhint %}

## Before you begin&#x20;

{% hint style="info" %}
Please be aware that running a node is not an easy task and requires technical skill and commitment to maintaining node uptime and availability.
{% endhint %}

* Running a TACo node requires maintenance and comes with certain constraints. Please review the [duties](../taco-node-setup/duties-and-compensation.md) expected of a node operator, and make sure you are comfortable with the minimum deauthorization delay of 6 months.&#x20;
* Your operator account will need to be funded with about $10 worth of MATIC to connect to the Threshold network. You can do this after setting up the node.
* Once TACo is running smoothly on your machine or VPS, the [next step](../taco-node-setup/taco-authorization-and-operator-registration.md) is to authorize your stake to the TACo app and bond the node to that provider address.

## Technical Overview

The overall technical procedure for running a TACo Node is as follows (excluding staking steps):\
\
1\. Download docker image\
2\. Create an Ethereum wallet to be used by the node ("operator")\
3\. Secure and export two passwords\
4\. Initialize the node\
5\. Launch the node

## 1. Get Docker Image&#x20;

In case your server does not already have docker installed, follow the official docker installation [instructions](https://docs.docker.com/engine/install/ubuntu/). &#x20;

Pull the latest Docker image from NuCypher's primary repo (NuCypher is a contributing team to the Threshold Network & the primary developers of TACo):&#x20;

```bash
docker pull nucypher/nucypher:latest
```

## 2. Create Operator Ethereum Wallet

The "Operator" is a dedicated ethereum wallet address to be used by the TACo node. You will bond (aka "map") this address a staking provider on the threshold dashboard later.   This wallet must be in geth-compatible JSON format and can be generated with a variety of publicly available tools like geth or MyCryptoWallet.

In this guide you will create an ethereum software wallet using geth.   Here are the geth installation [instructions](https://geth.ethereum.org/docs/getting-started/installing-geth). Note that installing Geth on an Ubuntu server can generate errors with newer versions. To avoid this, choose a long term support version – e.g. Ubuntu 20.04 (LTS).

To create a new ethereum wallet using geth run:

```bash
geth account new
```

successful output looks like this:

```bash
$ geth account new
...
Your new account is locked with a password. Please give a password. Do not forget this password.
Password: 
Repeat password: 

Your new key was generated

Public address of the key:   0xdEB634255A534870505D085717898F1A8A0B53d8
Path of the secret key file: /home/user/.ethereum/keystore/UTC--2023-12-08T18-58-13.845048610Z--deb634255a534870505d085717898f1a8a0b53d8
...
```

{% hint style="info" %}
Take note of your new operator address and secret key file, you will need them in the next steps.
{% endhint %}

{% hint style="danger" %}
Secure your password and operator secret key file off-site. Loss of your operator wallet or password will result in disruptions to rewards and necessitate manual intervention.
{% endhint %}

## 3. Export Environment Variables

There are two passwords associated with a TACo node: a "_nucypher keystore password_" and an "_operator password_".

* _nucypher keystore password_ - This password is used to encrypt your network participation keys.  You can create this password now.
* _operator password_ - This is the password used to unlock you operator ethereum wallet.  Use the same password you used when you created your wallet.

```bash
# Set a password to create and update the nucypher keystore
export NUCYPHER_KEYSTORE_PASSWORD=<YOUR NUCYPHER KEYSTORE PASSWORD>
```

```bash
# Re-enter your ethereum operator account password (e.g. set on creation of a new Geth account)
export NUCYPHER_OPERATOR_ETH_PASSWORD=<YOUR OPERATOR ETH ACCOUNT PASSWORD>
```

## 4. Initialize

TACo nodes must be initialized before launching. This is an interactive one-time step that will create network participation keys and an initial JSON configuration file:&#x20;

<pre class="language-bash"><code class="lang-bash">docker run -it --rm                        \
--name ursula                              \
<strong>-v ~/.local/share/nucypher:/root/.local/share/nucypher \
</strong>-v ~/.ethereum/:/root/.ethereum:ro         \
-p 9151:9151                               \
-e NUCYPHER_KEYSTORE_PASSWORD              \
nucypher/nucypher:latest                   \
<strong>nucypher ursula init                       \
</strong>--signer keystore:///root/.ethereum/keystore/&#x3C;WALLET FILENAME> \
--domain mainnet                           \
<strong>--eth-endpoint &#x3C;ETH PROVIDER URI>          \
</strong><strong>--polygon-endpoint &#x3C;POLYGON PROVIDER URI>  \
</strong>--operator-address &#x3C;OPERATOR ADDRESS>      
</code></pre>

Replace the following values with your own:

* `<WALLET FILENAME>`  The filename of your operator software wallet (geth JSON format)
* `<ETH ENDPOINT URI>` \
  The URI of a local or hosted ethereum node RPC endpoint (e.g. `https://infura.io/…`)
* `<POLYGON ENDPOINT URI>` \
  The URI of a local or hosted polygon node RPC endpoint  (e.g.. `https://infura.io/...`)
* `<OPERATOR ADDRESS>` \
  The dedicated ethereum wallet address to be used by the TACo node. In most cases this is the local signer address that was generated when you created an Ethereum operator (e.g. Geth) account. You will bond this address a provider address later.&#x20;

Follow the in-terminal prompts. You will see a public key for your TACo node and be assigned a mnemonic phrase.

{% hint style="danger" %}
The TACo mnemonic is a secret -- do not share it with anyone.  Secure your node's secret mnemonic off-site.  Loss of the mnemonic means you will be unable to recover your node's network keys and will be unable to perform duties resulting in rewards disruption.
{% endhint %}

## 5. Launch

Run the following command to launch the node:&#x20;

```bash
docker run -d                     \
--name ursula                     \
--restart unless-stopped          \
-v ~/.local/share/nucypher:/root/.local/share/nucypher \
-v ~/.ethereum/:/root/.ethereum   \
-p 9151:9151                      \
-e NUCYPHER_KEYSTORE_PASSWORD     \
-e NUCYPHER_OPERATOR_ETH_PASSWORD \
nucypher/nucypher:latest          \
nucypher ursula run 
```

Successful execution will resemble this example:

```bash
$ docker run -d --name ursula ...
5ecaa04eb319da576c3b2fa2b8aee9cc1a7079cd2675e3202047b50174696a84
```

### View Logs

When your node starts up, it will connect to Polygon and Ethereum mainnet to determine if the two qualification criteria are satisfied:&#x20;

1\. Operator account is funded with MATIC (\~30 MATIC is recommended)\
2\. Operator account is bonded (aka "mapped") to a staking provider.

{% hint style="info" %}
Operator bonding must be performed on the Threshold Staking dashboard.  Once complete there is a \~20 minute waiting period for your node's status to be automatically bridged to Polygon.   \
\
If your node is not bonded and synced the following message will be displayed in logs during this waiting period:\
\
`! Bonded staking provider address 0xDB1970...0991D096 on Mainnet not yet synced to child application on Polygon/Mainnet ; waiting for sync`
{% endhint %}

\
Verify your node is running correctly by viewing the logs:

```bash
docker logs -f ursula
```

Here is an example of the expected output for a node that is funded with MATIC and correctly bonded to an operator on the threshold dashboard:

```bash
...
! Bonded staking provider address 0xDB1970...0991D096 on Mainnet not yet synced to child application on Polygon/Mainnet ; waiting for sync
✓ Operator 0x27cd20d513cD3aB1D030e60f3aFb75599A33Bc2D is bonded to staking provider 0xDB1970e65B501f906f0fD220164800a0E824456E
! Checking provider's DKG participation public key for 0xDB1970e65B501f906f0fD220164800a0E824456E on Polygon/Mainnet at Coordinator 0xE74259e3dafe30bAA8700238e324b47aC98FE755
Broadcasting SETPROVIDERPUBLICKEY Legacy Transaction (0.021561263955835524 ETH @ 119.649197331 gwei)
TXHASH 0xa034bc89f8f30980e1222c9a17a71683849119ae7953e7c04d659a057f77f384
Waiting 600 seconds for receipt
✓ Successfully published provider's DKG participation public key for 0xDB1970e65B501f906f0fD220164800a0E824456E on Polygon/Mainnet with txhash 0x40cda7a3120d4555e64802e813f2fd9de2ea5c3616cff24393d332daa92ce2d2)
✓ Start Operator Bonded Tracker
✓ Rest Server https://182.16.254.42:9151
Working ~ Keep Ursula Online!
```

{% hint style="success" %}
Working \~ Keep Ursula Online! Indicates successful launch :tada:
{% endhint %}

## 6. (Optional) Automatic Updates

You can optionally configure your server to automatically update any running docker containers using watchtower.  This will automatically relaunch your node with the same commands and environment when an update to nucypher is published:

```bash
docker run --detach \
--name watchtower   \
--volume /var/run/docker.sock:/var/run/docker.sock \
containrrr/watchtower
```

For more information see the official watchtower documentation here:

&#x20;[https://containrrr.dev/watchtower/](https://containrrr.dev/watchtower/)

