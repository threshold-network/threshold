---
description: Instructions for setting up a TACo node on a server.
---

# Run a TACo Node with Docker

## 1. Install Docker

Docker installation [instructions](https://docs.docker.com/get-docker/).

It is recommended to check out Docker [post-install configurations](https://docs.docker.com/engine/install/linux-postinstall/). For example, managing docker as non-root user.

Geth installation [instructions](https://geth.ethereum.org/docs/getting-started/installing-geth).

## 2. Get Docker Image&#x20;

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
</strong>    --signer keystore://&#x3C;WALLET JSON PATH>        \
    --domain &#x3C;DOMAIN_NAME>                        \
<strong>    --eth-endpoint &#x3C;ETH PROVIDER URI>             \
</strong><strong>    --polygon-endpoint &#x3C;POLYGON PROVIDER URI>     \
</strong>    --operator-address &#x3C;OPERATOR ADDRESS>         
</code></pre>

Replace the following values with your own:

* \`\<WALLET JSON PATH>\`  The path to a local software wallet (geth format)
* `<ETH ENDPOINT URI>` \
  The URI of a local or hosted ethereum node RPC endpoint (e.g. `https://infura.io/…`)
* `<POLYGON ENDPOINT URI>` \
  The URI of a local or hosted polygon node RPC endpoint  (e.g.. `https://infura.io/...`)
* `<OPERATOR ADDRESS>` \
  The dedicated ethereum wallet address to be used by the TACo node.&#x20;

The configuration files will be stored in `~/.local/share/nucypher` on the host machine.

## 6. Launch the TACo Node

Run the following command to launch the node:&#x20;

```bash
$ docker run -d \
    --name Ursula                     \
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

### Upgrade Node

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

{% hint style="info" %}
After making configuration changes, the node must be restarted for those changes to take effect.
{% endhint %}
