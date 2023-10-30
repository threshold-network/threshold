# Godmode Alice Trust Model

{% hint style="warning" %}
Godmode Alice will be deprecated shortly after TACo v7.0.0 is released.&#x20;
{% endhint %}

In addition to the trust assumptions mentioned for TACo's testnet versions, Godmode Alice has the following limitations:&#x20;

* In the absence of a DKG component, the encryptor (or 'Alice') retains permanent decryption power, regardless of their condition fulfillment later – e.g. transferring the corresponding NFT out of their wallet.&#x20;
* Upgrading from Godmode to more recent iterations of TACo involved entirely replacing the underlying cryptosystem, which means all data encrypted via Godmode will have to be (downloaded and) encrypted again from plaintext utilizing the new Ferveo scheme.
* Conditions can technically be 'stripped' from ciphertexts by a sophisticated user, which would enable them to maliciously gain decryption rights by presenting a ciphertext with no attached access conditionality.&#x20;
