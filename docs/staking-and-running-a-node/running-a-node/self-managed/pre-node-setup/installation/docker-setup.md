---
description: Instructions for setting up a TACo node on a server.
---

# Docker Setup

{% hint style="info" %}
[nucypher-ops](cloud-setup-nucypher-ops.md) is the recommended way to set up a TACo node in the cloud (Digital Ocean, AWS).
{% endhint %}

## 1. Install Docker

Docker installation [instructions](https://docs.docker.com/get-docker/).

It is recommended to check out Docker [post-install configurations](https://docs.docker.com/engine/install/linux-postinstall/). For example, managing docker as non-root user.

## 2. Install Geth

Geth installation [instructions](https://geth.ethereum.org/docs/getting-started/installing-geth).

## 3. Get Docker Image&#x20;

Pull the latest Docker image from NuCypher's primary repo (NuCypher is a contributing team to the Threshold Network & the primary developers of TACo):&#x20;

```bash
docker pull nucypher/nucypher:latest
```

## 4. Export Node Environment Variables

Run the following commands:&#x20;

```bash
# Password used for creation/update of nucypher keystore
$ export NUCYPHER_KEYSTORE_PASSWORD=<YOUR NUCYPHER KEYSTORE PASSWORD>
```

```bash
# Password used to unlock node's ethereum operator account
$ export NUCYPHER_OPERATOR_ETH_PASSWORD=<YOUR OPERATOR ETH ACCOUNT PASSWORD>
```

## 5. Initialize Node Configuration

Create and store  the node configuration (this only needs to be executed once):&#x20;

<pre class="language-bash"><code class="lang-bash">$ docker run -it --rm                             \
    --name ursula                                 \
<strong>    -v ~/.local/share/nucypher:/root/.local/share/nucypher \
</strong>    -v ~/.ethereum/:/root/.ethereum               \
    -p 9151:9151                                  \
    -e NUCYPHER_KEYSTORE_PASSWORD                 \
    nucypher/nucypher:latest                      \
<strong>    nucypher ursula init                          \
</strong>    --signer &#x3C;ETH KEYSTORE URI>                   \
    --network &#x3C;L1 NETWORK NAME>                   \
<strong>    --eth-provider &#x3C;L1 PROVIDER URI>              \
</strong>    --payment-network &#x3C;L2 NETWORK NAME>           \
    --payment-provider &#x3C;L2 PROVIDER URI>          \
    --operator-address &#x3C;OPERATOR ADDRESS>         
</code></pre>

Replace the following values with your own:

* `<ETH KEYSTORE URI>` \
  The local ethereum keystore URI, `keystore://<PATH TO KEYSTORE FILE>` (e.g. `keystore:///root/.ethereum/keystore` for `mainnet`)
* `<L1 NETWORK NAME>` \
  The name of the TACo network e.g. `mainnet` (or `tapir`, `lynx` for testnets)
* `<L1 PROVIDER URI>` \
  The URI of a local or hosted ethereum node (e.g. `https://infura.io/…`)
* `<L2 NETWORK NAME>` \
  The name of a payment network e.g. `polygon` (or `mumbai` for testnet)
* `<L2 PROVIDER URI>` \
  The URI of a local or hosted L2 node (e.g.. `https://infura.io/...`)
* `<OPERATOR ADDRESS>` \
  The Ethereum wallet address to be used by the TACo node (your staker address).&#x20;

The configuration files will be stored in `~/.local/share/nucypher` on the host machine.

## 6. Launch the TACo Node

Run the following command to launch the node:&#x20;

```bash
$ docker run -d --rm \
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

The docker command above is for illustrative purposes and can be modified as necessary.&#x20;

{% hint style="info" %}
Once the node is running, you can view its public status page at `https://<node_ip>:9151/status`.
{% endhint %}

## TACo Node Management

### View Logs

```bash
$ docker logs -f ursula
```

### Upgrade Node Version

```bash
# Stop container
$ docker stop ursula

# Get latest image
$ docker pull nucypher/nucypher:latest

# Start node (same aforementioned run command)
$ docker run -d ... \
    [...]
    nucypher ursula run
```

### Update Node Configuration

Configuration settings will be stored in an Ursula configuration file, `ursula.json`, stored in `/home/<user>/.local/share/nucypher` by default.

All node configuration values can be modified using the `nucypher ursula config` command.

```bash
# General format
$ docker run \
    -v ~/.local/share/nucypher:/root/.local/share/nucypher \
    nucypher/nucypher:latest \
    nucypher ursula config --<OPTION> <NEW VALUE> --<OPTION_2> <OPTION_2 NEW VALUE>

# Usage
$ docker run \
    -v ~/.local/share/nucypher:/root/.local/share/nucypher \
    nucypher/nucypher:latest \
    nucypher ursula config --help

# View the current configuration
$ docker run \
    -v ~/.local/share/nucypher:/root/.local/share/nucypher \
    nucypher/nucypher:latest \
    nucypher ursula config

# Change the Ethereum provider to use
$ docker run \
    -v ~/.local/share/nucypher:/root/.local/share/nucypher \
    nucypher/nucypher:latest \
    nucypher ursula config --eth-provider <ETH PROVIDER URI>

# View the current configuration of a non-default configuration file path
$ docker run \
    -v ~/.local/share/nucypher:/root/.local/share/nucypher \
    nucypher/nucypher:latest \
    nucypher ursula config --config-file <CONFIG PATH>
```

{% hint style="info" %}
After making configuration changes, the node must be restarted for those changes to take effect.
{% endhint %}
