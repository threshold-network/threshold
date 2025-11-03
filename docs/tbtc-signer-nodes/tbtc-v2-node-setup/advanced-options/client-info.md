---
description: Client information exposed by the tBTC v2 client.
---

# Client Info

The client exposes metrics and diagnostics on a configurable port (default: `9601`) under `/metrics` and `/diagnostics` resources.

The data can be consumed by Prometheus to monitor the state of a node.

### Metrics

The client exposes the following metrics:

* connected peers count,
* connected bootstraps count,
* Ethereum client connectivity status (if a simple read-only CALL can be executed).

Metrics are enabled once the client starts. It is possible to customize the port at which metrics endpoint is exposed as well as the frequency with which the metrics are collected.

Exposed metrics contain the value and timestamp at which they were collected.

Example metrics endpoint call result:

```
$ curl localhost:9601/metrics
# TYPE connected_peers_count gauge
connected_peers_count 108 1623235129569

# TYPE connected_bootstrap_count gauge
connected_bootstrap_count 10 1623235129569

# TYPE eth_connectivity gauge
eth_connectivity 1 1623235129789
```

### Diagnostics <a href="#diagnostics" id="diagnostics"></a>

The client exposes the following diagnostics:

* list of connected peers along with their network id and Ethereum operator address,
* information about the client’s network id and Ethereum operator address.

Diagnostics are enabled once the client starts. It is possible to customize the port at which diagnostics endpoint is exposed.

Example diagnostics endpoint call result:

```
$ curl localhost:9601/diagnostics
{
  "client_info" {
   "ethereum_address":"0xDcd4199e22d09248cA2583cBDD2759b2acD22381",
   "network_id":"16Uiu2HAkzYFHsqbwt64ZztWWK1hyeLntRNqWMYFiZjaKu1PZgikN"
  },
  "connected_peers": [
    {"ethereum_address":"0x3712C6fED51CECA83cA953f6FF3458f2339436b4","network_id":"16Uiu2HAkyYtzNoWuF3ULaA7RMfVAxvfQQ9YRvRT3TK4tXmuZtaWi"},
    {"ethereum_address":"0x4bFa10B1538E8E765E995688D8EEc39C717B6797","network_id":"16Uiu2HAm9d4MG4LNrwkFmugD2pX7frm6ZmA4vE3EFAEjk7yaoeLd"},
    {"ethereum_address":"0x650A9eD18Df873cad98C88dcaC8170531cAD2399","network_id":"16Uiu2HAkvjVWogUk2gq6VTNLQdFoSHXYpobJdZyuAYeoWD66e8BD"},
    ...
  ]
}
```
