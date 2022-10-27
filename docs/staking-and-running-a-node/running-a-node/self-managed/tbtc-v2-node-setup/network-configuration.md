---
description: Required network configuration for the tBTC v2 staking client.
---

# Network configuration

### Required Ports

The node has to be accessible publicly to establish and maintain connections with bootstrap nodes and discovered peers. The node exposes metrics and diagnostics services for network health monitoring purposes.

{% hint style="info" %}
Update firewall rules as necessary, including application level firewalls.
{% endhint %}

| Purpose | Config Property | Protocol | Default |
| ------- | --------------- | -------- | ------- |
| Network | network.port    | TCP      | 3919    |
| Status  | clientInfo.port | TCP      | 9601    |

{% hint style="info" %}
A Network Port has to be exposed publicly, so the peers can connect to your node.
{% endhint %}

{% hint style="info" %}
A Status Port has to be exposed publicly, for the rewards allocation.
{% endhint %}

### **Announced Addresses**

An Announced Address is a layered addressing information (`multiaddress`/`multiaddr`) announced to the Threshold Network that is used by peers to connect with your node, e.g.: `/dns4/bootstrap-0.test.keep.network/tcp/3919` or `/ip4/104.154.61.116/tcp/3919`.

If the machine you’re running your node is not exposing a public IP (e.g. it is behind NAT) you should set the `network.AnnouncedAddresses` (flag: `--network.announcedAddresses`) configuration property to addresses (`ip4` or `dns4`) under which your node is reachable for the public.

To read more about `multiaddress` see the [libp2p documentation](https://docs.libp2p.io/reference/glossary/#multiaddr).
