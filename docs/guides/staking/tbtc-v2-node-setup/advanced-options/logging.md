---
description: Optional logging configuration for the tBTC v2 staking client.
---

# Logging

### Configuration <a href="#_configuration" id="_configuration"></a>

Logging can be configured with environment variables. Please see sample settings:

```bash
LOG_LEVEL=DEBUG
IPFS_LOGGING_FMT=nocolor
GOLOG_FILE=/var/log/keep/keep.log
GOLOG_TRACING_FILE=/var/log/keep/trace.json
```

{% hint style="success" %}
LOG\_LEVEL option DEBUG will generate extensive output. Consider INFO instead.
{% endhint %}

{% hint style="info" %}
If you want to share your LibP2P address with others you can get it from the startup log. When sharing remember to substitute the `/ipv4/` address with the public facing IP of your client if you’re running on a private machine, or replace the entire `/ipv4/` segment with a DNS entry if you’re using a hostname.
{% endhint %}
