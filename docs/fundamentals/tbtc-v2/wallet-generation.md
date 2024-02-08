# Wallet Generation

Wallets are created periodically based on governance. [`updateWalletParameters`](https://etherscan.io/address/0x5e4861a80b55f035d899f66772117f00fa0e8e7b#writeProxyContract#F32) changes the time between wallets and [`walletParameters`](https://etherscan.io/address/0x5e4861a80b55f035d899f66772117f00fa0e8e7b#readProxyContract#F18) reads it. The time between new wallets is held in `walletCreationPeriod` in number of seconds, so the current value of `1209600` represents 14 days.\
\
In order for the wallet to move funds, it produces signatures using a [Threshold Elliptic Curve Digital Signature Algorithm](https://eprint.iacr.org/2019/114.pdf), requiring 51-of-100 Signers to cooperate.

The 100 signers on each wallet are chosen with our [Sortition Pool](https://github.com/keep-network/sortition-pools), and the randomness is provided by the [Random Beacon](https://github.com/keep-network/keep-core/tree/main/pkg/beacon).

The probability that a [Staker](https://dashboard.threshold.network/staking/how-it-works/overview) is chosen to be a Signer is equal to their percentage of the total TBTC Stake. Each Signer is chosen independently. The same Staker can be a signer on the same wallet multiple times. The same Staker can be a Signer on multiple wallets simultaneously.

## Examples

For simplicity, say there are only three Stakers: Alice, Bob, and Carol. Alice has 250M T, Bob has 400M T, and Carol has 350M T staked, so they own 25%, 40% and 35% of the stake respectively. That means, Alice has a 25% chance to be a Signer, Bob has a 40% chance, and Carol has a 35% chance. Each Signer is selected independently.

The Sortition Pool is more complex (because of heavy gas optimization), but reasoning about it could look like this:\
\
For each Signer, we're going to generate a random number between 1 and 100. Alice is the Signer if the number is in \[1, 25]. Bob is the Signer if the number is in \[26, 65]. Carol is the Signer if the number is in \[66, 100]. For example, if our first [random number](https://www.random.org/) is 33, our first Signer would be Bob. We can generate 100 random numbers: `(75, 51, 13, 48, 36, 62, 46, 65, 97, 67...)`, and then use that to determine Signers: `(Carol, Bob, Alice, Bob, Bob, Bob, Bob, Bob, Carol, Carol...)`.\
\
This example illustrates a few properties mentioned earlier:

* Each Signer is selected independently. Whether or not Carol is the first signer has no influence on Carol being the second Signer.
* The chance that you become a particular Signer is equal to your share of the Stake.

### Statistics

If Carol has a 35% chance of being a particular Signer, what are chances that Carol has at least 51 of the 100 seats (and could control the wallet by herself)?

The number of Signers that Carol controls on a wallet is modeled by the [Binomial Distribution](https://en.wikipedia.org/wiki/Binomial\_distribution). From wikipedia:

> A Binomial distribution with parameters `n` and `p` is the [discrete probability distribution](https://en.wikipedia.org/wiki/Discrete\_probability\_distribution) of the number of successes in a sequence of `n` [independent](https://en.wikipedia.org/wiki/Statistical\_independence) [experiments](https://en.wikipedia.org/wiki/Experiment\_\(probability\_theory\)), each asking a [yes–no question](https://en.wikipedia.org/wiki/Yes%E2%80%93no\_question), and each with its own [Boolean](https://en.wikipedia.org/wiki/Boolean-valued\_function)-valued [outcome](https://en.wikipedia.org/wiki/Outcome\_\(probability\)): _success_ (with probability `p`) or _failure_ (with probability `1−p`).

This is exactly our situation! From Carol's perspective, there will be `n=100` independent experiments, each asking a yes-no question (does this Signer belong to Carol), each with a 35% probability.

That means we can use a [Binomial Probability Calculator](https://stattrek.com/online-calculator/binomial) to answer our question:\
\
![](<../../.gitbook/assets/image (7).png>)

It's the last figure: "Cumulative probability: P(X>51)" that's relevant. That says there is a \~0.074% chance that Carol would have a controlling Share of any particular wallet.

The next important question is "The probability that Carol controls a Wallet is low, but it only needs to happen once for things to be bad. What is the probability that she controls _any_ wallet in the next 2 years?"

A wallet is generated every 14 days, so over the next 2 years, the system would generate \~52 wallets. Each wallet has a `1 - 0.00074 = .99926` or 99.926% chance of _not_ being controlled by Carol. That means we can exponentiate:

$$
0.99926^{52} = .9622
$$

Carol would have a \~3.8% chance of getting control of a wallet in 2 years. That's with her owning 35% of the total Stake! Alice has a _much_ lower chance. She has a `0.0002131%` chance of controlling a wallet, which means that over the course of 52 wallets, she has a `0.02131%` chance of controlling _any_ wallet in 2 years.

As of writing, the \*largest\* Staker controls [\~8.83% of the Stake](https://dune.com/queries/2013598).

### Sybil Resistance

The only thing that a Staker accomplishes by splitting up their Stake into multiple identities is making the system appear to be more diverse.

For example, say that Alice split up her Stake equally into 5 accounts: Alice1, Alice2, Alice3, Alice4, and Alice5. Now, rather than Alice having a 25% chance to be a Signer, each account has a 5% chance which collectively add up to 25%.

To use the example from earlier: Alice has 250M T, Bob has 400M T, and Carol has 350M T staked. Alice has her T split into 50M for each identity. Alice1 would own 50M/1000M = 5%.\
\
Keeping with the simplification of assigning numbers from 1 to 100 to identities, Alice1 would get \[1, 5], Alice2 gets \[6, 10], Alice3 gets \[11, 15], Alice4 gets \[16, 20], Alice5 gets \[21, 25], Bob gets \[26, 65], Carol gets \[66, 100]. If we use the same random numbers as before, `(75, 51, 13, 48, 36, 62, 46, 65, 97, 67...)`, then we make the assignments as `(Carol, Bob, Alice3, Bob, Bob, Bob, Bob, Bob, Carol, Carol...)`. Nothing has effectively changed!

