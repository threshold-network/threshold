---
description: Instructions for setting up a TACo node on a server.
---

# Run a TACo Node with Docker

## Before you begin&#x20;

* Running a TACo node requires maintenance and comes with certain constraints. Please review the [duties](duties-and-compensation.md) expected of a node operator, and make sure you are comfortable with the minimum deauthorization delay of 6 months.&#x20;
* Your operator account will need to be funded with about $10 worth of MATIC to connect to the Threshold network. You can do this after setting up the node.
* Once TACo is running smoothly on your machine or VPS, the [next step](taco-authorization-and-operator-registration.md) is to authorize your stake to the TACo app and bond the node to that provider address.

## 1. Install Docker

Docker installation [instructions](https://docs.docker.com/engine/install/ubuntu/).

It is recommended to check out Docker [post-install configurations](https://docs.docker.com/engine/install/linux-postinstall/). For example, managing docker as non-root user.

## 2. Get Docker Image&#x20;

Pull the latest Docker image from NuCypher's primary repo (NuCypher is a contributing team to the Threshold Network & the primary developers of TACo):&#x20;

```bash
docker pull nucypher/nucypher:latest
```

{% hint style="info" %}
For testnets use the name of the testnet instead of `latest` \
(e.g. `nucypher/nucypher:lynx`)
{% endhint %}

## 3. Create Operator Ethereum Wallet

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

## 4. Export Node Environment Variables

Run the following commands:&#x20;

```bash
# Set a password to create and update the nucypher keystore
export NUCYPHER_KEYSTORE_PASSWORD=<YOUR NUCYPHER KEYSTORE PASSWORD>
```

```bash
# Re-enter your ethereum operator account password (e.g. set on creation of a new Geth account)
export NUCYPHER_OPERATOR_ETH_PASSWORD=<YOUR OPERATOR ETH ACCOUNT PASSWORD>
```

## 5. Initialize Node Configuration

Create and store  the node configuration (this only needs to be executed once):&#x20;

<pre class="language-bash"><code class="lang-bash">docker run -it --rm                        \
--name ursula                              \
<strong>-v ~/.local/share/nucypher:/root/.local/share/nucypher \
</strong>-v ~/.ethereum/:/root/.ethereum            \
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

{% hint style="info" %}
The configuration files will be stored in `~/.local/share/nucypher` on the host machine.
{% endhint %}

## 6. Launch the TACo Node

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
nucypher ursula run               \
--teacher https://mainnet.nucypher.network:9151  
```

The docker command above is for illustrative purposes and can be modified as necessary.&#x20;

### View Logs

Verify your node is running correctly by viewing the logs:

```bash
docker logs -f ursula
```

{% hint style="info" %}
Once the node is running, you can view its public status page at `https://<node_ip>:9151/status/?json=true`
{% endhint %}

## 7. (Optional) Automatic Updates

You can optionally configure your server to automatically update any running docker containers using watchtower.  This will automatically relaunch your node with the same commands and environment when an update to nucypher is published:

```bash
docker run --detach \
--name watchtower   \
--volume /var/run/docker.sock:/var/run/docker.sock \
containrrr/watchtower
```

For more information see the official watchtower documentation here:

&#x20;[https://containrrr.dev/watchtower/](https://containrrr.dev/watchtower/)

## Node Management

### Manually Upgrade Node

If you run into errors, note that you have to stop the node from running before attempting to troubleshoot or try a different configuration.

```bash
# Stop container
$ docker stop ursula && docker rm ursula

# Get latest image
$ docker pull nucypher/nucypher:latest

# Start node (same aforementioned run command)
$ docker run -d ... \
    [...]
    nucypher ursula run
```

### Update Node Configuration

Configuration settings will be stored in an Ursula configuration file, `ursula.json`, stored in `/home/<user>/.local/share/nucypher` by default.

{% hint style="info" %}
After making configuration changes, the node must be restarted for those changes to take effect.
{% endhint %}
