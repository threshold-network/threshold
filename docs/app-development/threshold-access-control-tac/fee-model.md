# Fee Model

### Sponsor

For a DKG ritual to be initiated and a cohort of nodes created, payment into the TACo _Coordinator_ contract is required first.

In future versions, any address can pay this upfront fee and will automatically become both a _Sponsor_ and _Cohort Authority –_ the former does not have any special privileges, but the latter has unilateral control over the parameters governing the cohort of nodes associated with a given DKG public key, and controls the encryptor allow list (i.e. which addresses can encrypt to the DKG public key) or allow logic (i.e. what rules determine who can encrypt to the DKG public key). \
\
In the genesis (v7.0) version of TACo, generating a Cohort of nodes is not permissionless, and so adopting developers must:

(1) Ask to join the beta program. You may make this request in the Threshold Discord [server](https://discord.com/channels/866378471868727316/870383642751430666).&#x20;

(2) Send the NuCypher team the address they wish to designate as Sponsor and Cohort Authority. \
\
(3) Complete the payment into the Coordinator contract, thereby becoming the official Sponsor and Cohort Authority for a unique and persistent DKG Ritual. \
\
Note that any address can technically be a Sponsor for a given DKG ritual & cohort, by paying into the Coordinator contract. This will extend the Cohort's availability horizon, but will not take over the original Cohort Authority.

### Duration-based API Fee

The API fee is an upfront payment for cohort availability. To create a unique cohort of nodes – associated with a unique DKG public key and `RitualID –`  Sponsor is required to pay up front such that the cohort’s nodes are compensated for future availability.&#x20;

The availability horizon is set to **6 months** (182.5 days), with default ‘top-up’ payments scheduled to recur every 1 month (30 days). In other words, all TACo adopters must pay for 6 months of service before their users can begin encrypting and decrypting data.

If a Sponsor fails to pay, the cohort of nodes will continue providing the service (key material management, condition checking, and decryption fragment provision) until the availability horizon is reached.&#x20;

Given that the Sponsor is often a single point-of-failure, the relatively long availability horizon reduces the severity of discontinued sponsorship, in providing as much time as possible for another sponsor to step in, and/or for end-users to retrieve their data.&#x20;

### Price Points

Adopters will be charged in DAI, proportional to:\
(i) the number of nodes they rent for the cohort \
_Minimum: 15 nodes, Default: 30 nodes_\
(ii) the duration of the cohort’s existence \
_Minimum: 6 months, Default: 6 months_&#x20;

The base fee is set to **0.35 DAI per node per day**. For the recommended cohort size of 30, this generates **3,832.50 DAI per adopter per year**. There is no free tier of service, besides the limited Tapir testnet.

### Future models&#x20;

In future versions, adopters will be able to generate multiple cohorts to service different components or security levels within their applications, increasing the base fee revenue per adopter.

TACo will also eventually have two fee models working in tandem:

(1) Duration-based API-fee&#x20;

(2) Action-based, contract-enforced fee

The _action-based_ fee mechanisms will be developed alongside early adopters and involve the following user action-driven payment gates, combined and tuned to fit specific use cases or domains: \
(i) Charging per authorization of allowed encrypting device \
(ii) Charging per decryption request and/or condition verification\
(ii) Charging per condition creation\
(iv) Charging per condition update

