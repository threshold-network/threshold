---
description: This page will guide you through Docker setup steps.
---

# Docker Installation

### Install Docker

Install or update Docker to the latest version. Visit the [Official Docker website](https://docs.docker.com/engine/install/ubuntu/) for detailed instructions. Use the command below to find  your installed version if needed:

```bash
docker --version
```

### Docker and Security Best Practices

General best practices recommend against running the tBTC v2 client as the `root` user as a security precaution. One security-minded approach is to [Run the Docker daemon as a non-root user](https://docs.docker.com/engine/security/rootless/) (Rootless mode).&#x20;

Next, choose ONE of the following options. The **tBTC v2 Service** option configures the client to run as a service in order to ensure that the client is restarted automatically, should your machine reboot. The **Docker Launch Script** is faster to setup, but won't start the client if your machine reboots.

{% tabs %}
{% tab title="Systemd Service" %}
Create the tbtcv2.service file:

```bash
cd /etc/systemd/system/
nano tbtcv2.service
```

Paste the following in the tbtcv2.service file:

<pre class="language-bash"><code class="lang-bash"><strong>[Unit]
</strong>Description=tBTC v2 client
After=network.target
Wants=network.target

[Service]
Environment="ETHEREUM_WS_URL=&#x3C;Ethereum API WS URL>"
Environment="OPERATOR_KEY_FILE_NAME=&#x3C;Operator Account keyfile name>"
Environment="OPERATOR_KEY_FILE_PASSWORD=&#x3C;Operator Account keyfile password>"
Environment="PUBLIC_IP=/dns4/&#x3C;PUBLIC_IP_OF_MACHINE>/tcp/3919"
Environment="CONFIG_DIR=/home/&#x3C;user name>/keep/config"
Environment="STORAGE_DIR=/home/&#x3C;user name>/keep/storage"

# These items only apply if you setup rootless-mode.
# Do not enable unless using rootless mode. Don't forget to adjust user UID.
#Environment="XDG_RUNTIME_DIR=/run/&#x3C;user name>/&#x3C;user UID>/"
#Environment="DOCKER_HOST=unix:///run/&#x3C;user name>/&#x3C;user UID>/docker.sock"

Type=simple
WorkingDirectory=/home/&#x3C;user>

ExecStart=/usr/bin/docker run \
    --volume ${CONFIG_DIR}:/mnt/keep/config \
    --volume ${STORAGE_DIR}:/mnt/keep/storage \
    --env KEEP_ETHEREUM_PASSWORD=${OPERATOR_KEY_FILE_PASSWORD} \
    --env LOG_LEVEL=info \
    --log-opt max-size=100m \
    --log-opt max-file=3 \
    -p 3919:3919 \
    -p 9601:9601 \
    keepnetwork/keep-client:latest \
    start \
    --ethereum.url ${ETHEREUM_WS_URL} \
    --ethereum.keyFile /mnt/keep/config/${OPERATOR_KEY_FILE_NAME} \
    --storage.dir /mnt/keep/storage \
    --network.announcedAddresses $PUBLIC_IP

Restart=always
RestartSec=15s

[Install]
WantedBy=default.target

</code></pre>

Replace the placeholders inside the <> brackets, remove the <> brackets but be sure to not edit anything further.

When done, save and close the file.

Next, test to make sure your configuration works:

```bash
sudo systemctl start tbtcv2
```

There will be no console output because it will be running in the background. Use systemctl to get the status of the service:

```bash
sudo systemctl status tbtcv2
```

If the service failed, go back and double check your configuration.&#x20;

Another option is to see if the Docker container is running:

```bash
docker ps
```

If everything is running as intended, enable the service:

```bash
systemctl enable tbtcv2
```

Now, with the service running, you should make sure that your configuration will tolerate a reboot and start up again automatically.

```bash
reboot now
```

Log back in to your machine and check that the service is running. If it is, you're done. If not, go back and review your work.
{% endtab %}

{% tab title="Docker Container" %}
### Docker Launch Script

To launch the tBTC v2 client, several configuration flags and environmental values need to be set. For simplicity, a bash script can be used rather than typing or pasting all the flags into the console.

Create the launch script:

```
nano keep.sh
```

And paste the following:

```bash
# Keep tBTC v2 Client
#
# Ethereum endpoint WebSocket URL
# This can be a provider such as Infura, Alchemy, Ankr, etc or your own Geth Nodeq
# ETHEREUM_WS_URL="wss://mainnet.infura.io/ws/v3/redacted_credentials"
# note: only replace characters inside the " ". The Quotation marks must be retained
ETHEREUM_WS_URL="<Ethereum API WS URL>"

# copied to home/keep/config earlier
OPERATOR_KEY_FILE_NAME="<Operator Account keyfile name>"

# password set during Operator Account Address creation
OPERATOR_KEY_FILE_PASSWORD="<Operator Account keyfile password>"

# To configure your node with a Public IP, enter it below.
PUBLIC_IP="<PUBLIC_IP_OF_MACHINE>"
# Alternatively, you can use DNS.
# To configure DNS, modify the last line of the script
# and add your DNS in the following format:
# /dns4/bootstrap-1.test.keep.network/tcp/3919

# Setup configuration and storage directories
# THESE MUST BE PERSISTENT STORAGE
CONFIG_DIR="/home/keep/config"
STORAGE_DIR="/home/keep/storage"

docker run \
    --detach \
    --restart on-failure \
    --volume $CONFIG_DIR:/mnt/keep/config \
    --volume $STORAGE_DIR:/mnt/keep/storage \
    --env KEEP_ETHEREUM_PASSWORD=$OPERATOR_KEY_FILE_PASSWORD \
    --env LOG_LEVEL=info \
    --log-opt max-size=100m \
    --log-opt max-file=3 \
    -p 3919:3919 \
    -p 9601:9601 \
    keepnetwork/keep-client:latest \
    start \
    --ethereum.url $ETHEREUM_WS_URL \
    --ethereum.keyFile /mnt/keep/config/$OPERATOR_KEY_FILE_NAME \
    --storage.dir /mnt/keep/storage \
    --network.announcedAddresses /ip4/$PUBLIC_IP/tcp/3919

```

Save and close the file, and make it executable:

```bash
sudo chmod +x keep.sh
```

To launch the tBTC v2 client, execute:

<pre class="language-bash"><code class="lang-bash"><strong>sudo bash keep.sh
</strong></code></pre>

{% hint style="info" %}
The `--detach` property will prevent the status messages from the client to be printed to the console. Review the Docker logs for detailed status information.
{% endhint %}
{% endtab %}
{% endtabs %}

{% hint style="danger" %}
The path shown in the example configuration will differ from yours. Make sure it is configured correctly.
{% endhint %}

## Client Startup

Unless the `--detach` flag was removed from the startup script, there will be no console output. In order to check your node, retrieve the Docker logs.

First, find your Docker instance identification, it'll be a random combination of words, e.g. `stinky_brownie`:

```bash
sudo docker ps
```

Use your specific identification and substitute:

```
sudo docker logs stinky_brownie >& /path/to/output/file
```

Scroll down about half a page, and you should see the following:

<pre><code><strong>
</strong>▓▓▌ ▓▓ ▐▓▓ ▓▓▓▓▓▓▓▓▓▓▌▐▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓ ▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓ ▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▄
▓▓▓▓▓▓▓▓▓▓ ▓▓▓▓▓▓▓▓▓▓▌▐▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓ ▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓ ▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓
  ▓▓▓▓▓▓    ▓▓▓▓▓▓▓▀    ▐▓▓▓▓▓▓    ▐▓▓▓▓▓   ▓▓▓▓▓▓     ▓▓▓▓▓   ▐▓▓▓▓▓▌   ▐▓▓▓▓▓▓
  ▓▓▓▓▓▓▄▄▓▓▓▓▓▓▓▀      ▐▓▓▓▓▓▓▄▄▄▄         ▓▓▓▓▓▓▄▄▄▄         ▐▓▓▓▓▓▌   ▐▓▓▓▓▓▓
  ▓▓▓▓▓▓▓▓▓▓▓▓▓▀        ▐▓▓▓▓▓▓▓▓▓▓         ▓▓▓▓▓▓▓▓▓▓▌        ▐▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓
  ▓▓▓▓▓▓▀▀▓▓▓▓▓▓▄       ▐▓▓▓▓▓▓▀▀▀▀         ▓▓▓▓▓▓▀▀▀▀         ▐▓▓▓▓▓▓▓▓▓▓▓▓▓▓▀
  ▓▓▓▓▓▓   ▀▓▓▓▓▓▓▄     ▐▓▓▓▓▓▓     ▓▓▓▓▓   ▓▓▓▓▓▓     ▓▓▓▓▓   ▐▓▓▓▓▓▌
▓▓▓▓▓▓▓▓▓▓ █▓▓▓▓▓▓▓▓▓ ▐▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓ ▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓  ▓▓▓▓▓▓▓▓▓▓
▓▓▓▓▓▓▓▓▓▓ ▓▓▓▓▓▓▓▓▓▓ ▐▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓ ▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓  ▓▓▓▓▓▓▓▓▓▓

Trust math, not hardware.
	
-----------------------------------------------------------------------------------
| Keep Client Node                                                                |
|                                                                                 |
| Version: Version: v2.0.0-m1 (4d745f6d0)                                         |
|                                                                                 |
| Operator: 0x_your_operator_address                                              |
|                                                                                 |
| Port: 3919                                                                      |
| IPs : /ip4/111.222.333.444/tcp/3919/ipfs/redacted                               |
|                                                                                 |
| Contracts:                                                                      |
| RandomBeacon   : 0x5499f54b4A1CB4816eefCf78962040461be3D80b                     |
| WalletRegistry : 0x46d52E41C2F300BC82217Ce22b920c34995204eb                     |
| TokenStaking   : 0x01B67b1194C75264d06F808A921228a95C765dd7                     |
-----------------------------------------------------------------------------------
</code></pre>

Congratulations, your node is up and running.&#x20;
