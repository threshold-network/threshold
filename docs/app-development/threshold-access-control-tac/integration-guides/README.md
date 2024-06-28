# Integration Guides

This section provides guides on how to integrate TACo into your application. Note that the underlying trust assumptions vary between versions, technologies, networks, and compatible third-party infrastructure. These nuances are explored in the [trust assumptions](../trust-assumptions/) section.&#x20;

## TACo networks

TACo is available on three networks; Lynx, Tapir and Mainnet, each of which has contracts deployed on an L1 (Ethereum) and L2 (Polygon). You can request support for a new chain or L2 in the Threshold Discord [#taco](https://discord.com/channels/866378471868727316/870383642751430666) channel.&#x20;

<table><thead><tr><th width="127">Domain</th><th width="96">Network</th><th width="161">L1 (chain id)</th><th width="146">L2 (chain id)</th><th width="131">Expiration</th></tr></thead><tbody><tr><td>DEV</td><td>lynx</td><td>Sepolia (11155111)</td><td>Amoy (80002)</td><td>2024-09-28</td></tr><tr><td>TESTNET</td><td>tapir</td><td>Sepolia (11155111)</td><td>Amoy (80002)</td><td>2024-10-06</td></tr><tr><td>MAINNET</td><td>mainnet</td><td>Ethereum (1)</td><td>Polygon (137)</td><td>Depends on subscription</td></tr></tbody></table>

Get started with Tapir and the testnet integration [guide](get-started-with-tac.md).&#x20;

## Example applications

The following samples showcase integrations with React-based web apps, and serve as an 'end-to-end' reference for creating conditions-based encryption & decryption:

* [`nucypher-ts/demos`](https://github.com/nucypher/nucypher-ts/tree/main/demos)
* [`nucypher-ts/examples/taco`](https://github.com/nucypher/nucypher-ts/tree/main/examples/taco)
* [`nucypher/alpha-leaks-demo`](https://github.com/nucypher/alpha-leaks-demo)

