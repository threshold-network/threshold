---
description: Instructions for setting up a TACo node on a server.
---

# Docker Setup

## 1. Install Docker

Docker installation instructions can be found [here](https://docs.docker.com/get-docker/).

## 2. Docker Best Practices (Optional)

There are recommended (but optional) docker post-install configurations [here](https://docs.docker.com/engine/install/linux-postinstall/) e.g. managing docker as non-root user.

## 3. Install Geth

Geth installation instructions can be found [here](https://geth.ethereum.org/docs/getting-started/installing-geth).

## 4. Get the latest Docker Image

```bash
docker pull nucypher/nucypher:latest
```

## 5. Export Node Environment Variables

```bash
# Password used for creation/update of nucypher keystore
$ export NUCYPHER_KEYSTORE_PASSWORD=<YOUR NUCYPHER KEYSTORE PASSWORD>
```

```bash
# Password used to unlock node's ethereum operator account
$ export NUCYPHER_OPERATOR_ETH_PASSWORD=<YOUR OPERATOR ETH ACCOUNT PASSWORD>
```

## 6. Initialize Node Configuration

Creates and stores the node configuration and only needs to be executed once.

```bash
$ docker run -it --rm  \
--name ursula        \
-v ~/.local/share/nucypher:/root/.local/share/nucypher \
-v ~/.ethereum/:/root/.ethereum               \
-p 9151:9151                                  \
-e NUCYPHER_KEYSTORE_PASSWORD                 \
nucypher/nucypher:latest                      \
nucypher ursula init                          \
--signer <ETH KEYSTORE URI>                   \
--network <L1 NETWORK NAME>                   \
--eth-provider <L1 PROVIDER URI>              \
--payment-network <L2 NETWORK NAME>           \
--payment-provider <L2 PROVIDER URI>          \
--operator-address <OPERATOR ADDRESS>         
```

Replace the following values with your own:

* `<ETH KEYSTORE URI>` - The local ethereum keystore URI, `keystore://<PATH TO KEYSTORE FILE>` (e.g. `keystore:///root/.ethereum/keystore` for `mainnet`)
* `<L1 NETWORK NAME>` - The name of the TACo network e.g. `mainnet` (or `tapir`, `lynx` for testnets)
* `<L1 PROVIDER URI>` - The URI of a local or hosted ethereum node (e.g. `https://infura.io/…`)
* `<L2 NETWORK NAME>` - The name of a payment network e.g. `polygon` (or `mumbai` for testnet)
* `<L2 PROVIDER URI>` - The URI of a local or hosted L2 node (e.g.. `https://infura.io/...`)
* `<OPERATOR ADDRESS>` - The Ethereum wallet address to be used by the node

The configuration files will be stored in `~/.local/share/nucypher` on the host machine.

## 7. Launch the Node

```bash
$ docker run -d --rm \
--name ursula      \
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
nucypher ursula run
```

### Update Node Configuration

configuration settings will be stored in an Ursula configuration file, `ursula.json`, stored in `/home/<user>/.local/share/nucypher` by default.

All node configuration values can be modified using the `nucypher ursula config` command.

```bash
# General format
$ docker run \
-v ~/.local/share/nucypher:/root/.local/share/nucypher \
nucypher/nucypher:latest          \
nucypher ursula config --<OPTION> <NEW VALUE> --<OPTION_2> <OPTION_2 NEW VALUE>

# Usage
$ docker run \
-v ~/.local/share/nucypher:/root/.local/share/nucypher \
nucypher/nucypher:latest          \
nucypher ursula config --help

# View the current configuration
$ docker run \
-v ~/.local/share/nucypher:/root/.local/share/nucypher \
nucypher/nucypher:latest          \
nucypher ursula config

# Change the Ethereum provider to use
$ docker run \
-v ~/.local/share/nucypher:/root/.local/share/nucypher \
nucypher/nucypher:latest          \
nucypher ursula config --eth-provider <ETH PROVIDER URI>

# View the current configuration of a non-default configuration file path
$ docker run \
-v ~/.local/share/nucypher:/root/.local/share/nucypher \
nucypher/nucypher:latest          \
nucypher ursula config --config-file <CONFIG PATH>
```

{% hint style="info" %}
After making configuration changes, the node must be restarted for those changes to take effect.
{% endhint %}
