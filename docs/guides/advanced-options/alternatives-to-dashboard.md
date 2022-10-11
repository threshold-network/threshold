---
description: >-
  Alternative application authorization methods to using the Threshold
  Dashboard.
---

# Alternatives to Dashboard

An operator-registering transaction can be submitted with the Keep Client if the staking provider address key file is available.

### Authorizing Random Beacon via the Client (Docker)

{% code overflow="wrap" %}
```bash
export KEEP_CLIENT_CONFIG_DIR=$(pwd)/config

export KEEP_CLIENT_ETHEREUM_WS_URL="<Ethereum API WS URL>"

export STAKING_PROVIDER_KEY_FILE_PASSWORD="<Staking Provider Account Key File Password>"
export STAKING_PROVIDER_KEY_FILE_NAME="<Staking Provider Account Key File Name>"
export OPERATOR_ADDRESS="<Operator Account Address>"

docker run \
    --volume $KEEP_CLIENT_CONFIG_DIR:/mnt/keep-client/config \
    --env KEEP_CLIENT_ETHEREUM_PASSWORD=$STAKING_PROVIDER_KEY_FILE_PASSWORD
    us-docker.pkg.dev/keep-test-f3e0/public/keep-client:latest \
    ethereum \
    --ethereum.url $KEEP_CLIENT_ETHEREUM_WS_URL \
    --ethereum.keyFile /mnt/keep-client/config/$STAKING_PROVIDER_KEY_FILE_NAME \
    beacon random-beacon register-operator --submit \
    $OPERATOR_ADDRESS
```
{% endcode %}



### Authorizing TBTC application via the Client (Docker)

{% code overflow="wrap" %}
```bash
export KEEP_CLIENT_CONFIG_DIR=$(pwd)/config

export KEEP_CLIENT_ETHEREUM_WS_URL="<Ethereum API WS URL>"

export STAKING_PROVIDER_KEY_FILE_PASSWORD="<Staking Provider Account Key File Password>"
export STAKING_PROVIDER_KEY_FILE_NAME="<Staking Provider Account Key File Name>"
export OPERATOR_ADDRESS="<Operator Account Address>"

docker run \
    --volume $KEEP_CLIENT_CONFIG_DIR:/mnt/keep-client/config \
    --env KEEP_CLIENT_ETHEREUM_PASSWORD=$STAKING_PROVIDER_KEY_FILE_PASSWORD
    us-docker.pkg.dev/keep-test-f3e0/public/keep-client:latest \
    ethereum \
    --ethereum.url $KEEP_CLIENT_ETHEREUM_WS_URL \
    --ethereum.keyFile /mnt/keep-client/config/$STAKING_PROVIDER_KEY_FILE_NAME \
    ecdsa wallet-registry register-operator --submit
    $OPERATOR_ADDRESS
```
{% endcode %}



### Via Web Browser <a href="#register-operator-web" id="register-operator-web"></a>

An operator-registering transactions can be submitted with Etherscan.

For each of the `RandomBeacon` and `WalletRegistry` contracts perform the following steps:

1. Find the address of the contract and open it on Etherscan (see below).
2. Go to `Contract` → `Write Contract` tab.
3. Connect your wallet with `Connect to Web3` button.
4. Submit the `registerOperator` function with your Operator address as an argument.
