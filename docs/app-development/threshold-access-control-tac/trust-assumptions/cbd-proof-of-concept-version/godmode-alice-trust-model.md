# Godmode Alice Trust Model

{% hint style="warning" %}
Godmode Alice only applies to adopting developers who trialled TACo proof-of-concepts before October 2023. It is scheduled to be deprecated when TACo v7.1.0 is released.&#x20;
{% endhint %}

In addition to the testnet [trust assumptions](./), "Godmode Alice" comes with the following limitations:&#x20;

* In the absence of a DKG ceremony, the encryptor (or 'Alice') retains permanent decryption power, regardless of their condition fulfillment later – e.g. transferring the corresponding NFT out of their wallet.&#x20;
* Conditions can technically be 'stripped' from ciphertexts by a sophisticated user, which would enable them to gain decryption rights by presenting a ciphertext with no attached access conditionality.&#x20;
* Upgrading from Godmode to more recent iterations of TACo involved entirely replacing the underlying cryptosystem, which means all data encrypted via Godmode will have to be (downloaded and) encrypted again from plaintext utilizing the new Ferveo scheme.
