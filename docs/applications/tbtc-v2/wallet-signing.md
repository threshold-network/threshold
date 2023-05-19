# Wallet Signing

The primary wallet responsibility is taking care of Bitcoins deposited by tBTC users. Whenever a wallet needs to move some Bitcoins due to a protocol action (e.g. deposit sweep or redemption), it must assemble an appropriate Bitcoin transaction and sign each transaction's input using the tECDSA signing algorithm. During the tECDSA signing, randomly selected 51-of-100 wallet signers must collaborate to produce a proper signature.

## Anatomy of a signing attempt

A single tECDSA signing attempt consists of several steps. Each step has a maximum duration expressed in the specific number of host chain (e.g. Ethereum) blocks:

1. Signers readiness announcement phase which takes **exactly** 6 blocks
2. Signature production & advertisement phase which takes **up to** 30 blocks
3. Cooldown period that takes **exactly** 5 blocks

That means a single signing attempt can take up to **41 blocks** (\~8 minutes on Ethereum, assuming 12 seconds as the average block time). If the signature is not produced during that time, wallet signers give up the signing attempt and retry.

## Signing retries

A wallet that is asked to sign the given message (e.g. Bitcoin transaction input) does not limit itself to a single tECDSA signing attempt. Due to the distributed nature of the signing algorithm, a single signing attempt may fail due to, for example, some unexpected network problems affecting inter-signer communication. If the first signing attempt fails, another set of randomly selected 51 wallet signers retry signing during the next attempt. Subsequent signing attempts are scheduled on fixed intervals, based on the block the first attempt started at, and the known maximum duration of a single attempt (41 blocks). For example, if the first attempt started at the block **B**, subsequent attempts will be scheduled as follows:

* Attempt 2 -> **B + 41**
* Attempt 3 -> **B + 82**
* Attempt N -> **B + (N-1) \* 41**

The wallet always executes **5 signing attempts at maximum**. Such a count of maximum attempts is an acceptable trade-off between a reasonable signing timeout and a sufficient tolerance for misbehaving wallet signers. Executing 5 signing attempts allows trying 5 different 51-signer subsets. This is enough to produce a signature even for an unlikely scenario of 2 misbehaving wallet signers. In that case, 51 signers must be selected out of the honest 98 signers. That means the probability of selecting a successful subset is:

```
P = (98 choose 51) / (100 choose 51) = ~0.24
```

which means 5 selections are required in the worst case.

Combining the 5 signing attempts at maximum with up to 41 blocks per single attempt means the wallet will try to sign the given message for **5\*41 = 205 blocks** (\~40 minutes on Ethereum, assuming 12 seconds as the average block time). If the signature is not produced during that time, wallet signers give up ultimately.

## Serial signing

Wallets have the ability to sign multiple messages (e.g. multiple inputs of a single Bitcoin transaction) in a serial manner, one message after another. In such a case, the whole signing process is successful only if all messages in the batch obtained their signatures.&#x20;

As mentioned in the previous section, the maximum allowed time for signing a single message is 205 blocks (\~40 minutes on Ethereum). That means the maximum time to sign a batch of **N** messages is **N\*205 blocks**. During that time, the wallet cannot perform any other work as signing is computationally heavy. This factor must be taken into account while building wallet actions that leverage signing.

## Signing as the foundation of wallet actions

The tBTC protocol defines a finite set of wallet actions: &#x20;

* Heartbeat
* Deposit sweep
* Redemption
* Moving funds
* Moved funds sweep

Each action involves wallet signing under the hood. The time necessary to complete the given action depends on how many signatures must be produced during execution. Knowing the worst-case time necessary to produce a single signature (205 blocks) and the number of signatures that may be required by the given action, we can set the right time boundaries for specific actions. This information is crucial for proper wallet coordination.

### Example: Deposit sweep

Let's use the _Deposit sweep_ action to show how signing affects wallet actions_._ This action is initiated by a coordinator that submits a deposit sweep proposal through the `WalletCoordinator` chain contract. An expected outcome of that action is a Bitcoin transaction with multiple inputs (swept deposits) and one output, submitted to the Bitcoin chain. That means the action's execution time strongly depends on the number of deposits suggested as part of the given sweep proposal. The `WalletCoordinator` contract has some governable parameters that impact the _Deposit sweep_ action:

* `depositSweepMaxSize` that sets the maximum number of deposits within a single sweep proposal
* `depositSweepProposalValidity` which determines the proposal's validity time. This is an exclusive time the wallet obtains to execute the given sweep and no other actions can be taken meanwhile

In other words, the wallet can sweep up to `depositSweepMaxSize` deposits and has `depositSweepProposalValidity` time to do so.&#x20;

Signing is the most important and complex step of the _Deposit sweep_ action and the reference client implementation sets the signing timeout to be `depositSweepProposalValidity - 1 hour` for all inputs of the sweep transaction. Producing a signature for a single deposit input may take up to 205 blocks (5 attempts underneath). This is \~40 minutes assuming Ethereum's average block time of 12 seconds. Having that in mind, the aforementioned `WalletCoordinator` governable parameters must be chosen with care as they have a strong impact on signing execution:

* If `depositSweepMaxSize = 5` and `depositSweepProposalValidity = 4 hours`, the wallet will try to sign 5 inputs in 3 hours which gives \~36 minutes per input. As 5 signing attempts take \~40 minutes, this configuration will allow for 4 attempts per input
* If `depositSweepMaxSize = 10` and `depositSweepProposalValidity = 4 hours`, the wallet will try to sign 10 inputs in 3 hours which gives \~18 minutes per input. As 5 signing attempts take \~40 minutes, this configuration will allow for 2 attempts per input
* If `depositSweepMaxSize = 20` and `depositSweepProposalValidity = 4 hours`, the wallet will try to sign 20 inputs in 3 hours which gives \~9 minutes per input. As 5 signing attempts take \~40 minutes, this configuration will allow for 1 attempt per input

