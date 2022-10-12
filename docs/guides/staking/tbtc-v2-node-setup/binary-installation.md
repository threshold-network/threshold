---
description: This page will guide you through Binary setup steps.
---

# Binary Installation

{% hint style="warning" %}
Choose either Docker installation OR Binary installation.
{% endhint %}

### Download the Binary

Download the client binary file, make sure you are in the correct directory.

{% code overflow="wrap" %}
```bash
cd /home/keep
wget https://github.com/keep-network/keep-core/releases/download/v2.0.0-m1/keep-client-mainnet-v2.0.0-m1-linux-amd64.tar.gz
```
{% endcode %}

After the download completes, use the command below to display the contents of the folder:

```bash
ls -la
```

Extract the compressed file:

```bash
tar xzvf downloaded-file-name-here.tar.gz
```

{% hint style="info" %}
You can save some time and prevent misspelling a file name by typing the first few letter of the file name and pressing the Tab key. This will autocomplete the file name. Be sure to verify the file name prior to pressing the enter key.
{% endhint %}

### Configure tBTC v2 Client

To launch the tBTC v2 client, several configuration flags and environmental values need to be set. For simplicity, a bash script can be used rather than typing or pasting all the flags into the console.&#x20;

The following flags must be set at minimum:

```bash
--ethereum.url "wss://mainnet-ETH-enpoint-here"
--mainnet
--storage.dir "/home/keep/storage"
--ethereum.keyFile "/home/keep/config/UTC--Your-Operator-Key-Name"
# to configure your node to use your machine's public IP
--network.announcedAddresses "/ip4/your.ipv4.address.here/tcp/3919"
# to configure your node to use DNS, provide your DNS in the following
# format
# /dns4/bootstrap-1.test.keep.network/tcp/3919

```

For a complete list of client commands and flags, see [CLI Options](advanced-options/cli-options.md).

To launch the client, execute the following:

{% code overflow="wrap" %}
```bash
./keep-client start --ethereum.url "wss://mainnet-ETH-enpoint-here" --mainnet --storage.dir "/home/keep/storage" --ethereum.keyFile "/home/keep/config/UTC--Your-Operator-Key-Name" --network.announcedAddresses "/ip4/your.ipv4.address.here/tcp/3919"
```
{% endcode %}

If everything is configured correctly, the client will request the password for the Operator Account. Supply the password and press Enter.&#x20;

You should see the following shortly:

```

▓▓▌ ▓▓ ▐▓▓ ▓▓▓▓▓▓▓▓▓▓▌▐▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓ ▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓ ▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▄
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
```

Congratulations, your node is up and running.&#x20;
