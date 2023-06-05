---
description: Update procedure for the tBTC v2 client depends on installation method
---

# Updating tBTC v2 Node

## Docker Installation

The following instructions assume your docker install followed [these](tbtc-v2-node-setup/installation/docker-installation.md) installation instructions.&#x20;

{% tabs %}
{% tab title="Docker Install" %}
To update a tBTC node:

Pull the new image

```bash
docker pull keepnetwork/keep-client:latest
```

Restart the tBTC service

```bash
sudo systemctl restart tbtcv2
```

To free system resources, run

<pre class="language-bash"><code class="lang-bash">sudo docker container prune

<strong>sudo docker image prune
</strong></code></pre>

Examine logs to ensure the node started correctly. Find Docker instance identification; it'll be a random combination of words, e.g. `stinky_brownie`:

```bash
sudo docker ps
```

Use specific identification and substitute accordingly; specify a path and file name for the log file:

```bash
sudo docker logs stinky_brownie >& /path/to/output/file
```

Display the log file&#x20;

```bash
cat /path/to/output/file
```

Look for the following and take note of the version:

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
| Version: vX.X.X-XX (4d745f6d0)                                         |
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



Alternatively, verify your client updated by visiting your status page:

```
http://111.222.333.444:9601/metrics
```

Compare the displayed version number to the version number you are expecting, i.e.:

```
client_info{version="vX.X.X-XX"}
```
{% endtab %}

{% tab title="Binary Install" %}
This install method requires downloading the latest version of the binary. Follow the installation steps provided [here](tbtc-v2-node-setup/installation/binary-installation.md).
{% endtab %}
{% endtabs %}

