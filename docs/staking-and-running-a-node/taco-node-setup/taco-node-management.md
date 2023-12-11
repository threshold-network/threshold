# TACo Node Management

## Node Management

### Manually Upgrade Node

If you run into errors, note that you have to stop the node from running before attempting to troubleshoot or try a different configuration.

```bash
# Stop container
$ docker stop ursula && docker rm ursula

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
