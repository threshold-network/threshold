# Testnet Integration

## Testnet domains

To run `taco` on testnet, we need to configure it to use one of the two available domains:

```typescript
import { domains } from '@nucypher/taco';

domains.DEV     // "lynx" network
domains.TESTNET // "tapir" network
```

* `DEV` domain, or `lynx`, is a bleeding-edge developer network that supports upcoming `taco` release
* `TESTNET` domain, or `tapir`, is a stable testnet environment that supports the current `taco` release

We encourage you to use the `TESTNET` domain for developing `taco` based apps, and to use `DEV` to test compatibility with upcoming `taco` release and new, experimental features.

{% hint style="warning" %}
Both `DEV` and `TESTNET` domains are unsuitable for use in a production setting.\
\
Testnet domains have no trust minimization or stability guarantees, which makes them unfit and unsuitable for production or real-world data payloads. Learn more about the testnet [trust assumptions](../trust-assumptions/testnet-trust-assumptions/).
{% endhint %}

## Testnet Configuration

Each `domain` corresponds to a test network with contracts deployed on an L1&#x20;

<table><thead><tr><th width="122">Domain</th><th width="143">Domain Name</th><th width="132">L1 (chain id)</th><th width="126">L2 (chain id)</th><th data-type="number">*Open ID</th><th>Expiration</th><th>Threshold configuration</th></tr></thead><tbody><tr><td>DEV</td><td>lynx</td><td>Sepolia<br>(11155111)</td><td>Amoy (80002)</td><td>0</td><td>Sep 28, 2024</td><td>2-of-3</td></tr><tr><td>TESTNET</td><td>tapir</td><td>Sepolia<br>(11155111)</td><td>Mumbai (80001)</td><td>0</td><td>July 10, 2024</td><td>3-of-5</td></tr></tbody></table>

`* Open id refers to rituals that have permisionless encryptor allowlists. See` [`encryptor allowlist`](../user-authentication/encryptor-allowlist.md) `section to learn more.`

## Contracts

The source code for contracts used on testnet can be found in [`nucypher/nucypher-contracts`](https://github.com/nucypher/nucypher-contracts) repository.&#x20;

Contract addresses for testnets can be found in their respective contract registries:

* [`lynx.json`](https://github.com/nucypher/nucypher-contracts/blob/main/deployment/artifacts/lynx.json)
* [`tapir.json`](https://github.com/nucypher/nucypher-contracts/blob/main/deployment/artifacts/tapir.json)
