# Duration-based fees

The duration-based API-fee is an upfront payment for cohort availability. To create a unique cohort of nodes (associated with a unique DKG public key and `RitualID`), the Sponsor is required to pay up front such that the cohort’s nodes are compensated for future availability.&#x20;

The availability horizon is set to **6 months** (182.5 days), with default ‘top-up’ payments scheduled to recur every 1 month (30 days). In other words, all TACo adopters must pay for 6 months of service before their users can begin encrypting and decrypting data.

If a Sponsor fails to pay, the cohort of nodes will continue providing the service (key material management, condition checking, and decryption fragment provision) until the availability horizon is reached.&#x20;

Given that the Sponsor is often a single point-of-failure, the relatively long availability horizon reduces the severity of discontinued sponsorship, in providing as much time as possible for another sponsor to step in, and/or for end-users to retrieve their data. \
