---
description: 'Summary: The testnet release of TACo is not fit for production use.'
---

# Testnet Trust Assumptions

The Testnet (Tapir) version exists to help developers familiarize themselves with the SDK, API and other architectural/design choices. **It is not intended to be utilized as a trust-minimized service**. Hence, the trust assumptions are strictly worse than the Mainnet version, particularly with respect to the node array managing decryption fragments and validating condition (non/)fulfillment. Nodes are running on a staker-facing Testnet, are operated primarily by members of the NuCypher commercial entity, and are not subject to a cryptoeconomic protocol or required to deposit collateral.

1. &#x20;In the absence of a DKG component, the encryptor (or 'Alice') retains permanent decryption power, regardless of their condition fulfillment later – e.g. transferring the corresponding NFT out of their wallet.&#x20;
2. Conditions can technically be 'stripped' from ciphertexts by a sophisticated user, which would enable them to maliciously gain decryption rights by presenting a ciphertext with no attached access conditionality.&#x20;
3. The node array managing decryption fragments – and validating condition (non/)fulfillment – are running on testnet, are operated primarily by NuCypher team-members, and are not subject to a cryptoeconomic protocol or a requirement to deposit/risk collateral.

Note that these three limitations will be addressed in the TACo Mainnet version. Upgrading from this version to the Mainnet version involves entirely replacing the underlying cryptosystem, which means all data encrypted via the Proof-of-Concept will have to be (downloaded and) encrypted again from plaintext utilizing the new cryptography.



