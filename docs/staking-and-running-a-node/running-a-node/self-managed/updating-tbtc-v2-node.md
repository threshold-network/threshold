---
description: Update procedure for the tBTC v2 client depends on installation method
---

# Updating tBTC v2 Node

## Docker Installation

The following instructions assume your docker install followed [these](tbtc-v2-node-setup/installation/docker-installation.md) installation instructions.&#x20;

These steps are specific to the Client: v2.0.0-m2 (Mainnet) release and may change.

The latest release is available on [GitHub](https://github.com/keep-network/keep-core/releases/tag/v2.0.0-m2).

####

{% tabs %}
{% tab title="Systemd Service" %}
Open the systemd service file created during the initial install.

```bash
~/etc/systemd/system/
```

The install instructions named the service `tbtcv2.service`. Locate your service file and open it in your prefered editor. For simplicity we use nano here. You may need `sudo`.

```bash
sudo nano tbtcv2.service
```

The service should look like this:

```bash
[Unit]
Description=tBTC v2 client
After=network.target
Wants=network.target

[Service]
Environment="ETHEREUM_WS_URL=<Ethereum API WS URL>"
Environment="OPERATOR_KEY_FILE_NAME=<Operator Account keyfile name>"
Environment="OPERATOR_KEY_FILE_PASSWORD=<Operator Account keyfile password>"
Environment="PUBLIC_IP=/dns4/<PUBLIC_IP_OF_MACHINE>/tcp/3919"
Environment="CONFIG_DIR=/home/<user name>/keep/config"
Environment="STORAGE_DIR=/home/<user name>/keep/storage"

# These items only apply if you setup rootless-mode.
# Do not enable unless using rootless mode. Don't forget to adjust user UID.
#Environment="XDG_RUNTIME_DIR=/run/<user name>/<user UID>/"
#Environment="DOCKER_HOST=unix:///run/<user name>/<user UID>/docker.sock"

Type=simple
WorkingDirectory=/home/<user>

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

```

Locate the following line:

```
keepnetwork/keep-client:latest \
```

Make the following change to this line only:

```
keepnetwork/keep-client:v2.0.0-m2 \
```

Save and exit the service file.

Reload the daemon and restart the service:

```bash
 sudo systemctl daemon-reload
 sudo systemctl restart tbtcv2
```

Verify your client updated by visiting your status page:

```
http://111.222.333.444:9601/metrics
```

Look for :

```
client_info{version="v2.0.0-m2"}
```


{% endtab %}

{% tab title="Docker Container" %}
If you simply used the docker container launch script, the following change needs to be made to the script prior to relaunching the script.

{% hint style="warning" %}
Please be aware that this method does NOT restart your node in case of a crash.
{% endhint %}

Open the launch script from your storage location

```bash
nano keep.sh
```

Locate the following line:

```
keepnetwork/keep-client:latest \
```

Make the following change to this line only:

```
keepnetwork/keep-client:v2.0.0-m2 \
```

Save and exit the service file.

Use the following to determine your docker container's name

```bash
sudo docker ps
```

The ID of your tBTC v2 container will be shown, copy the container ID and paste it into the folowing:

```bash
sudo docker kill CONTAINER_ID
```

To launch the tBTC v2 client again, execute:

<pre class="language-bash"><code class="lang-bash"><strong>sudo bash keep.sh
</strong></code></pre>
{% endtab %}

{% tab title="Binary Install" %}
This install method requires downloading the latest version of the binary. Follow the installation steps provided [here](tbtc-v2-node-setup/installation/binary-installation.md).
{% endtab %}
{% endtabs %}
