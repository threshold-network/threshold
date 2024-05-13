---
description: Explains how to enable encryption for your rituals
---

# Encryptor allowlist

## Introduction

Encryptor allowlist is a simple access control mechanism that only allows specific encryptors to access your ritual. In practice, it means that you can limit who can use `taco` to encrypt the data with your ritual's public key.

So in order to encrypt with `taco`, we need to allow-list our wallet address (the encrypter). The entity that performs this action is called `authority`. Each ritual has it's `authority`, which corresponds to the address of the wallet that initiated the ritual.

## Allowlist in the early release

During the beta program of `taco`, the process of initiating the ritual and managing the `allowlist` is simplified into the following steps:

1. User shares their `authority` address with the testnet operator
2. The testnet operator initiates the ritual for the user, marking them as the `authority` for that ritual
3. Now, the user can manage their encrypter allowlist using their `authority`. So the user adds a new encrypter to the allowlist
4. The user can now use the encrypter in `taco` to encrypt their data

## Allowlist on the testnet

In the early release, it is possible to use one of the premade rituals on the testnet without any extra steps outlined in the setup above. You can simply configure `taco` to use one of those rituals and use any encryptor (wallet address) to perform the encryption

You can find the current ritual configurations in the [testnet integration section](../integration-guides/get-started-with-tac.md#testnet-configuration).&#x20;
