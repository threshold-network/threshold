# Fee Model

### Sponsor & Authority Roles

To initiate a DKG ritual and generate a cohort of nodes, a payment into the TACo _Coordinator_ contract is required first. When initiating a DKG ritual, two addresses are required:

* _Sponsor:_ This address sends the initiation transaction, triggering a DKG ritual, along with paying the initiation fee in DAI. Note that this address does not have any special privileges or power over the cohort.
* _Cohort Authority:_ This address has unilateral control over the parameters governing the cohort of nodes associated with a given DKG public key, and controls the encryptor allow list (i.e. which addresses can encrypt using the DKG public key) or allow logic (i.e. what rules determine who can encrypt using the DKG public key). Note that this address does not have to participate during the initiation process.

In most cases, sponsor and cohort authority can be the same address, but it's also possible that in some scenarios, adopters may prefer to use different addresses. For example, the cohort authority could be a cold wallet address, while the sponsor might simply be a one-off software address. External developers may also prefer to set a DAO, a Multisig, or any kind of smart contract as the Cohort Authority, which would reduce the trust burden on their end-users.&#x20;

After the beta program ends, any address can act as the Sponsor and trigger DKG rituals. In the genesis version (v7.0) of TACo, i.e. during the beta program, generating a cohort of nodes is not permissionless, and so adopting developers must follow the instructions on the [Mainnet Integration](integration-guides/mainnet-taco-beta-program.md) page.

### Duration-based API Fee

The API fee is an upfront payment for cohort availability. To create a cohort of nodes – associated with a unique DKG public key and `ritual ID` - the Sponsor is required to pay upfront such that the cohort’s nodes are compensated for future availability.&#x20;

The base availability horizon is set to **6 months** (182.5 days), with default ‘top-up’ payments scheduled to recur monthly (30 days). In other words, all TACo adopters must pay for six months of service before their application/users can begin encrypting and decrypting data.

If a Sponsor fails to pay, the cohort of nodes will continue providing the service (key material management, condition checking, and decryption fragment provision) until the availability horizon is reached.&#x20;

Given that the Sponsor is often a single point-of-failure, the relatively long availability horizon reduces the severity of discontinued sponsorship by providing as much time as possible for another sponsor to step in and/or for end-users to retrieve their data.&#x20;

### Price Points

Adopters will be charged in DAI on Polygon, proportional to:\
(i) the number of nodes they rent for the cohort \
_Minimum: 15 nodes, Default: 30 nodes_\
(ii) the duration of the cohort’s existence \
_Minimum: 6 months, Default: 6 months_&#x20;

### Future models&#x20;

In future versions, adopters can generate multiple cohorts to service different components or security levels within their applications, increasing the base fee revenue per adopter.

TACo will also eventually have two fee models working in tandem:

(1) Duration-based API-fee&#x20;

(2) Action-based, contract-enforced fee

The _action-based_ fee mechanisms will be developed alongside early adopters and involve the following user action-driven payment gates, combined and tuned to fit specific use cases or domains: \
(i) Charging per authorization of allowed encrypting device \
(ii) Charging per decryption request and/or condition verification\
(iii) Charging per condition update

