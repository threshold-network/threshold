# CBD Proof-of-Concept Version

The Proof-of-Concept version exists to help developers familiarize themselves with the SDK, API and other architectural/design choices, ahead of the Mainnet version release in Q2 2023.. It is not intended to be utilized as a trust-minimized service. Hence, the trust assumptions are strictly worse than the Mainnet version. One can consider the following limitations to be appended on top of the Mainnet trust [model](cbd-mainnet-version.md):&#x20;

1. &#x20;In the absence of a DKG component, the encryptor (or 'Alice') retains permanent decryption power, regardless of their condition fulfillment later – e.g. transferring the corresponding NFT out of their wallet.&#x20;
2. Conditions can technically be 'stripped' from ciphertexts by a sophisticated user, which would enable them to maliciously gain decryption rights by presenting a ciphertext with no attached access conditionality.&#x20;
3. The node array managing decryption fragments – and validating condition (non/)fulfillment – are running on testnet, are operated primarily by NuCypher team-members, and are not subject to a cryptoeconomic protocol or a requirement to deposit/risk collateral.



