# Sweeping

### What is Sweeping?&#x20;

Sweeping is an accounting optimization for tracking bitcoin deposits in tBTC contracts. It involves periodically consolidating recent bitcoin deposits into a single Unspent Transaction Output (UTXO) for a more optimal commitment cadence to Ethereum. This process simplifies tracking associated public keys and refund time locks while also helping prevent supply peg disruption. Typically, Sweeping runs every 8 hours depending on the volume and size of deposits.&#x20;

### How it works

When a user makes a deposit, they're locking up the funds using our specific [locking script](https://blog.threshold.network/tbtcv2-deposits-explained/). This creates an Unspent Transaction Output ([UTXO](https://learnmeabitcoin.com/technical/utxo)).&#x20;

Typically, the job of a bitcoin "wallet" (wallets don't exist in the same way that they do on ethereum) is to scan the blockchain for particular locking script patterns that it thinks it can unlock (like a [P2PKH script](https://learnmeabitcoin.com/technical/p2pkh) associated to your public key) and sum up the relevant funds. When you want to send someone else bitcoin, the wallet software figures out which UTXOs to unlock to send to them so that you don't have to.

If we wanted to be able to redeem bitcoin deposits, we would need to track which deposits are associated to which public keys the same way that wallet software does; this is a headache especially from a bitcoin mining fee perspective.

Further, our [locking script](https://blog.threshold.network/tbtcv2-deposits-explained/) includes this idea of a time-locked refund. If the funds are not touched by a certain amount of time (9 months at the time of writing; `<locktime>` in the script), we allow the depositor to reclaim their deposit. We do this as an effective escape hatch for if something goes wrong with the protocol or deposit.

As a result of this, we give ourselves the same time limit for collecting the deposit. If we don't move the funds by that time, a user could deposit 5 BTC, receive 5 TBTC, and then 9 months later reclaim their original 5 BTC without redeeming the 5 BTC. This would break the TBTC supply peg.

Sweeping aims to solve both of these problems simultaneously as well as serves as a performance optimization. Every 8 hours, we take all of the recent deposits, unlock them, and then send the funds using a normal [P2PKH script](https://learnmeabitcoin.com/technical/p2pkh) back to the wallet itself, consolidating the UTXOs into a single UTXO.

The result is that all of the scattered deposits are "swept" into a single "pile" represented by one UTXO that we can commit to on ethereum.

### Example

Here's a deposit for [10.3 BTC](https://mempool.space/tx/a4dc393dc48a82d1bb2d5dc30437fdb83e61565c47274dbe1c064117541ea97d). That deposit is swept in transaction [0db1929c2fa41649264b95b9162bc7a12f43cb455a91bae7177667a53290d7e8](https://mempool.space/tx/0db1929c2fa41649264b95b9162bc7a12f43cb455a91bae7177667a53290d7e8) (along with many other deposits). That was one of three sweeps performed by that wallet (as of writing).

![https://mempool.space/tx/1ddffd9e5d4c84b49c9f912bfc3b97d8f5aba0620e35ec6a50e7043c9141d373](<../.gitbook/assets/image (8).png>)

That transaction created a UTXO worth 1.51614471 BTC. The next transaction

![https://mempool.space/tx/40240aaa602f534ae7e1654546e7ee5f49a32d6d64215d90acbb9a720f63098e](<../.gitbook/assets/image (9).png>)

Used the old UTXO (the first input) as well as all of the recent deposits to create a new UTXO (the output on the right) worth 15.42475511 BTC. The final transaction

![https://mempool.space/tx/0db1929c2fa41649264b95b9162bc7a12f43cb455a91bae7177667a53290d7e8](<../.gitbook/assets/image (10).png>)

Follows the same pattern. It uses the old UTXO (the first input) as well as the recent deposits to create a new UTXO worth ‎59.52818486 BTC.

