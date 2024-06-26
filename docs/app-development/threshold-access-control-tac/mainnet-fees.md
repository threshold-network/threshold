# Mainnet Fees

### Fee structure&#x20;

Adopting developers pay for the TACo service via a dual fee model, which covers:&#x20;

1. Availability of the service, via a **duration-based fee**\
   _Currently 0.5 DAI per node per day_
2. Usage of the service via a **fee** **based on the number of unique data producer identities encrypting data at any one time**\
   _Currently 2.5 DAI per encryptor slot per year_\
   \
   Note that encrypting privileges can be added and removed from identites/addresses at will, without charge or limit, provided the sponsor has pre-paid for sufficient credits and there are slots available.

There is no charge, payment gate or limit on:

* encryptions.
* types or combinations of conditions specified.
* unique requestor identities.
* throughput/number of requests.
* decryptions.
* condition validations/invalidations.
* additions/removals of addresses to/from the authAdmin list.
* additions/removals of addresses to/from the encryptor allowlist.
* any other communication with the network or API.

### `sponsor` & `cohortAdmin` Roles

Adopting developers pay in advance for use of TACo mainnet by transacting with the [`Coordinator`](https://github.com/nucypher/nucypher-contracts/blob/main/contracts/contracts/coordination/Coordinator.sol) contract, triggering the DKG initialization ritual and generating a cohort of TACo nodes under their exclusive control. There are two key roles associated with a given DKG ritual:&#x20;

* `sponsor`_:_ This address sends the initial transaction, triggering the DKG ritual, and also paying the upfront fees. This address does not have any special privileges or power over the cohort of nodes or who can encrypt using the DKG public key. Indeed, any EOA can create a new DKG ritual or sponsor an existing ritual.&#x20;
* `cohortAdmin`_:_ This address has unilateral control over the parameters governing the cohort of nodes associated with a given DKG public key. This address also controls the encryptor allow list – i.e. which identities can encrypt using the DKG public key, and/or allow logic – i.e. what rules determine who can encrypt using the DKG public key. In certain cases, the `cohortAdmin` can also assign other addresses to be an `authAdmin` , who in turn can bestow encrypting privileges to end-users within pre-specified limits. Note that the `cohortAdmin` address does not have to participate during the initiation process.

The `sponsor` and `cohortAdmin` roles can use the same address or use different addresses. For example, the `cohortAdmin` could be a cold wallet address, while the `sponsor` might simply be a one-off software address. External developers may also prefer to set a DAO, a Multisig, or any kind of smart contract as the `cohortAdmin`, which would reduce the trust burden on their end-users with respect to control over encryptors and the TACo cohort.

Currently, initializing a DKG ritual is not permissionless and must be pre-approved in the [`Coordinator`](https://github.com/nucypher/nucypher-contracts/blob/main/contracts/contracts/coordination/Coordinator.sol) contract by the NuCypher team. Prospective `sponsor`s should follow the instructions on the [Mainnet Integration](integration-guide/mainnet-beta-program.md) page.







