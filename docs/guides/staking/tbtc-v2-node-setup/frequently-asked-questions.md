---
description: Find answers to some of the most commonly asked questions here.
---

# Frequently Asked Questions

### Errors in Logs or Console

Certain errors may be reported by your node during the early stages of Chaosnet and the tBTC 2 launch. See a sample below:

{% code overflow="wrap" %}
```
2022-10-10T18:30:42.571Z    WARN    keep-beacon    beacon/node.go:78    selecting group not possible: [cannot select group in the sortition pool: [got error [execution reverted: Sortition pool unlocked] while resolving original error [execution reverted: Sortition pool unlocked]]]    {"seed": "0x29c60250c292e108e6abf4dcc76cb161d8ae8e803c4d4419eaff6e97f514b080"}
```
{% endcode %}

{% hint style="info" %}
This warning is normal and may happen on chaosnet. It will disappear and reappear periodically.
{% endhint %}

This is expected during this stage of the chaosnet. When the pool is locked, new operators cannot join but sortition (selecting operators from the pool) is possible. When the pool is unlocked, new operators can join but the sortition is not possible. Once the first set of **beta operators** is registered, the pool will get locked for some time to allow the sortition. After some time, when another set of beta operators are added, the pool will be unlocked again.&#x20;
