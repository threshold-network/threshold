# TACo Node Management

## Node Management

### Automatically Upgrade Node

Configure your server to automatically update any running docker containers, via Watchtower.  This will relaunch your node with the same environment and commands whenever a new version of nucypher is published. For more information check out the official Watchtower [documentation](https://containrrr.dev/watchtower/).&#x20;

```bash
docker run --detach \
--name watchtower   \
--restart unless-stopped \
--volume /var/run/docker.sock:/var/run/docker.sock \
containrrr/watchtower ursula --cleanup
```

### Manually Upgrade Node

Alternatively, you can manually update your node. This means checking for new version releases in the Threshold [announcements](https://discord.com/channels/866378471868727316/870384195636199455) channel, then running the following commands to upgrade your node.&#x20;

```bash
# Stop container
$ docker stop ursula && docker rm ursula

# Get latest image
$ docker pull nucypher/nucypher:latest

# Start node 
$ docker run -d                   \
--name ursula                     \
--restart unless-stopped          \
-v ~/.local/share/nucypher:/root/.local/share/nucypher:rw \
-v ~/.cache/nucypher:/root/.cache/nucypher:rw \
-v ~/.ethereum/:/root/.ethereum:ro   \
-p 9151:9151                      \
--env-file nucypher.env           \
nucypher/nucypher:latest          \
nucypher ursula run 
```

{% hint style="info" %}
If you run into errors, note that you have to stop the node from running before attempting to troubleshoot or attempting a different configuration.
{% endhint %}

To confirm that your node is now running the latest version, you can use any browser to check your node's status page. You may need to skip through a warning page.&#x20;

```
https://<NODE_IP_ADDRESS>/status/
```

### Update Node Configuration

Configuration settings will be stored in an Ursula configuration file, `ursula.json`, stored in `/home/<user>/.local/share/nucypher` by default.

{% hint style="info" %}
After making configuration changes, the node must be restarted for those changes to take effect.
{% endhint %}
