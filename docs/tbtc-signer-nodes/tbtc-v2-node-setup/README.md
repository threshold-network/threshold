---
description: >-
  This document explains the basic installation and configuration for the tBTC
  v2 client.
---

# Node Setup

{% hint style="info" %}
Please be aware that running a node is not an easy task and requires technical skill and commitment to maintaining node uptime and availability.
{% endhint %}

Please review this document in its entirety prior to beginning setup of your node to familiarize yourself with the general setup process.&#x20;

### Important Considerations

The Threshold Network expects certain capabilities for each node running on the network. To help attain these capabilities consider the following criteria:

* It is paramount that tBTC v2 nodes remain available to the Network. We strongly encourage a stable and redundant internet connection.
* Equally important is machine uptime and reliability. A VPS is strongly recommended.
* A connection to a production grade self-hosted or third party Ethereum node deployment.
* Persistent and redundant storage that will survive a VM or container rotation, and a disk failure.
* Each node running on the network requires a unique Ethereum Operator Account. The account has to maintain a positive Ether balance at all times.
* Each node running on the network requires a unique IP address or a unique application port running under the same IP.

### Recommended Machine Types <a href="#recommended_machine_types" id="recommended_machine_types"></a>

{% hint style="info" %}
While it is possible to run the client on a local machine, this is not recommended.
{% endhint %}

Your operating environment will ultimately dictate what machine type to go with. This is particularly relevant if you’re running a containerized solution where multiple applications are sharing VM resources. The below types are sufficient for running one instance of the tBTC v2 Node.

The preferred OS is Ubuntu.

| VPS Provider | VPS Type                                     |
| ------------ | -------------------------------------------- |
| AWS          | c5.large                                     |
| Azure        | F2s v2                                       |
| Google Cloud | n2-highcpu-2                                 |
| Self-hosted  | 2 vCPU / 2 GB RAM / 1 GiB Persistent Storage |

### **Ethereum API**

A Keep Node requires a connection to a WebSocket Ethereum API. You should obtain a WS API URL from a service provider (e.g. [Alchemy](https://www.alchemy.com/), [Infura](https://www.infura.io/), [Ankr](https://www.ankr.com/rpc-service/)) or run your own Ethereum node (e.g. [Geth](https://geth.ethereum.org/)).

### Bitcoin API

Starting from version `v2.0.0-m3` , the Keep Node interacts with the Bitcoin network through an Electrum protocol server. You can use one of the publicly available servers or run your own. The URL of the chosen server should be passed under the `bitcoin.electrum` section of the configuration. If no Electrum server is set explicitly in the `bitcoin.electrum` configuration section, the Keep Node will randomly pick one server from its embedded server list, appropriate for the Bitcoin network the Keep Node is currently running against.

### Configuration

The client expects configuration options to be passed as CLI flags or specified in a [config file](advanced-options/config-file.md). If you specify an option by using a parameter on the command line, it will override the value read from the configuration file.
