# Fee Model

### Initiation Fee

For running a DKG ritual where a cohort of nodes is created, payment into the TACo _Coordinator_ contract is required first. This fee, denominated in DAI, is proportional to the initial duration of the cohort and its number of participants (see "Price Points" section below).

When initiating a DKG ritual, there are two addresses required:

* _Sponsor:_ This address sends the initiation transaction, triggering a DKG ritual, along with paying the initiation fee in DAI. Note that this address does not have any special privileges over the cohort.
* _Cohort Authority:_ This address has unilateral control over the parameters governing the cohort of nodes associated with a given DKG public key, and controls the encryptor allow list (i.e. which addresses can encrypt using the DKG public key) or allow logic (i.e. what rules determine who can encrypt using the DKG public key). Note that this address does not have to participate during the initiation process.

In most cases, sponsor and cohort authority can be the same address, but it's also possible that in some scenarios, adopters may prefer to use different addresses. For example, the cohort authority can be a cold wallet address, while the sponsor can simply be a one-off software address; another example, perhaps more interesting, is that the adopter may prefer to set a DAO, a multisig, or in general a smart contract, as authority.

After the beta program ends, any address can act as sponsor and trigger DKG rituals. In the genesis version (v7.0) of TACo, i.e. during the beta program, generating a cohort of nodes is not permissionless, and so adopting developers must:

(1) Ask to join the beta program. You may make this request in the Threshold Discord [#taco](https://discord.com/channels/866378471868727316/870383642751430666) channel.&#x20;

(2) Provide the Polygon wallet address they wish to designate as Cohort Authority for a unique DKG ritual and cohort of nodes.\
\
(3) Complete the payment into the [Threshold Integrations Guild multisig](https://app.safe.global/home?safe=safe%3Dmatic:0x5bD70E385414C8dCC25305AeB7E542d8FC70e667), which will act as the Sponsor, while your designated address will be the Cohort Authority&#x20;

### Duration-based API Fee

The API fee is an upfront payment for cohort availability. To create a unique cohort of nodes – associated with a unique DKG public key and `ritual ID` - the Sponsor is required to pay upfront such that the cohort’s nodes are compensated for future availability.&#x20;

The base availability horizon is set to **6 months** (182.5 days), with default ‘top-up’ payments scheduled to recur monthly (30 days). In other words, all TACo adopters must pay for six months of service before their application/users can begin encrypting and decrypting data.

If a Sponsor fails to pay, the cohort of nodes will continue providing the service (key material management, condition checking, and decryption fragment provision) until the availability horizon is reached.&#x20;

Given that the Sponsor is often a single point-of-failure, the relatively long availability horizon reduces the severity of discontinued sponsorship by providing as much time as possible for another sponsor to step in and/or for end-users to retrieve their data.&#x20;

### Price Points

Adopters will be charged in DAI on Polygon, proportional to:\
(i) the number of nodes they rent for the cohort \
_Minimum: 15 nodes, Default: 30 nodes_\
(ii) the duration of the cohort’s existence \
_Minimum: 6 months, Default: 6 months_&#x20;

The base fee is set to **0.35 DAI per node per day**. For the recommended cohort size of 30, this costs **3,832.50 DAI per adopter per year**. There is no free tier of service other than on the limited `Tapir` testnet.

### Future models&#x20;

In future versions, adopters can generate multiple cohorts to service different components or security levels within their applications, increasing the base fee revenue per adopter.

TACo will also eventually have two fee models working in tandem:

(1) Duration-based API-fee&#x20;

(2) Action-based, contract-enforced fee

The _action-based_ fee mechanisms will be developed alongside early adopters and involve the following user action-driven payment gates, combined and tuned to fit specific use cases or domains: \
(i) Charging per authorization of allowed encrypting device \
(ii) Charging per decryption request and/or condition verification\
(ii) Charging per condition creation\
(iv) Charging per condition update

