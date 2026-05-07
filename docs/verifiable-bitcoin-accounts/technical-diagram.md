# Technical Diagram

Verifiable Bitcoin Accounts (VBA) combine three layers: the institution's existing custody, Threshold Network's distributed signer infrastructure, and pre-approved onchain deployment venues. Bitcoin Script enforces all spending conditions at the consensus layer.

<figure><img src="../.gitbook/assets/Verifiable Bitcoin Accounts Technical Diagram by Threshold network.png" alt=""><figcaption></figcaption></figure>

### Layer 1: Custody Compatibility

VBA is custody-agnostic. The institution's existing custody arrangement holds the controlling signing key throughout the account's lifecycle.

```
      ┌─────────────────────────────────────────────────────────────────────────┐
      │                            CUSTODY COMPATIBILITY                        │
      └─────────────────────────────────────────────────────────────────────────┘

      ┌─────────────────────┐    ┌─────────────────────┐  ┌─────────────────────┐
      │ QUALIFIED CUSTODIAN │    │    MPC NETWORK      │  │    SELF-CUSTODY     │
      │                     │    │                     │  │                     │
      │ • Anchorage         │    │ • Fireblocks (MPC)  │  │ • HSM-based signing │
      │ • Fireblocks Trust  │    │ • Copper            │  │ • Multi-sig wallets │
      │ • BitGo             │    │ • Custom MPC setups │  │ • Hardware wallets  │
      │ • BNY Mellon        │    │                     │  │                     │
      └──────────┬──────────┘    └──────────┬──────────┘  └──────────┬──────────┘
                 │                          │                        │
                 │                          │                        │
                 └──────────────────────────┐────────────────────────┘ 
                                            ▼
                              ┌──────────────────────────┐
                              │  PSBT SIGNING INTERFACE  │
                              │  (BIP-174 Compatible)    │
                              └──────────────────────────┘
```

**Key principle:** No asset migration is required. The custody relationship that the institution already maintains continues to govern the asset throughout the VBA's lifecycle.

***

### Layer 2: Multi-Party Control Model

Every VBA enforces a multi-party quorum. No single entity holds unilateral spending authority.

```
      ┌─────────────────────────────────────────────────────────────────────────┐
      │                          MULTI-PARTY CONTROL FLOW                       │
      └─────────────────────────────────────────────────────────────────────────┘
      ┌───────────────────────────────┐         ┌───────────────────────────────┐  
      │          INSTITUTION          │         │          THRESHOLD            │  
      │      (or its custodian)       │         │     DECENTRALIZED NETWORK     │   
      │                               │         │                               │ 
      │         Signing Key A         │         │         Signing Key B         │
      │                               │         │       (Threshold Nodes)       │   
      └──────────────┬────────────────┘         └──────────────┬────────────────┘                      │         │                               │    │                 │                
                     │                                         │                       
                     └─────────────────────┐───────────────────┘ 
                                           │   
                                           ▼
                             ┌──────────────────────────┐
                             │   QUORUM EVALUATION      │
                             │                          │
                             │   e.g., 2-of-3 or 3-of-5 │
                             │   threshold required     │
                             └─────────────┬────────────┘
                                           │
                                           ▼        
                             ┌──────────────────────────┐
                             │     PSBT FULLY SIGNED    │
                             │                          │
                             │   Broadcast to Bitcoin   │
                             └──────────────────────────┘
```

**Enforcement:** The quorum threshold is encoded in the Bitcoin Script governing the UTXO. Any transaction below the threshold is rejected by the Bitcoin network at the consensus layer.

***

### Layer 3: Bitcoin Script Enforcement

Spending conditions are written directly in Bitcoin Script and validated by every Bitcoin full node.

```
┌─────────────────────────────────────────────────────────────────┐
│                BITCOIN SCRIPT ENFORCEMENT MODEL                  │
└─────────────────────────────────────────────────────────────────┘

   VBA UTXO (with embedded Bitcoin Script)
   │
   ├─► SPENDING PATH 1: Multi-Party Authorized Movement
   │   │
   │   ├── OP_CHECKMULTISIG  (2-of-3 or 3-of-5 quorum)
   │   ├── OP_CHECKLOCKTIMEVERIFY  (timing constraints)
   │   └── Whitelisted destination address verification
   │
   ├─► SPENDING PATH 2: Liquidation Migration (Automated)
   │   │
   │   ├── OP_CHECKMULTISIG  (Threshold signer quorum)
   │   ├── Pre-defined migration destination only
   │   └── Triggered by onchain liquidation event
   │
   └─► SPENDING PATH 3: Depositor Recovery (Timelocked)
       │
       ├── OP_CHECKLOCKTIMEVERIFY  (timelock expiration)
       ├── Single signature: Institution's key only
       └── Activates if signer network unavailable

   Validated by: Every Bitcoin full node
   Enforced by: Bitcoin consensus mechanism
```

**No external dependencies:** The rules governing each VBA do not rely on application-layer software, smart contracts on other chains, or proprietary policy engines.

***

### Layer 4: Liquidation & Migration Path

When a position is liquidated in an approved venue, Threshold signers automatically execute the predefined migration path.

```
┌─────────────────────────────────────────────────────────────────┐
│              LIQUIDATION & MIGRATION FLOW                       │
└─────────────────────────────────────────────────────────────────┘

  ┌──────────────────────┐
  │  ONCHAIN VENUE       │   Liquidation event triggered
  │  (Aave, Morpho,      │   (e.g., LTV breach, oracle price)
  │   Curve, Yield Basis)│
  └──────────┬───────────┘
             │
             ▼
  ┌──────────────────────┐
  │  THRESHOLD SIGNERS   │   Independent verification
  │                      │   of onchain state
  │  • Direct read of    │
  │    contract state    │
  │  • No external oracle│
  │  • No off-chain      │
  │    coordination      │
  └──────────┬───────────┘
             │
             ▼
  ┌──────────────────────┐
  │  QUORUM EVALUATION   │   Threshold signers reach
  │                      │   quorum agreement
  │  Each signer verifies│
  │  conditions are met  │
  └──────────┬───────────┘
             │
             ▼
  ┌──────────────────────┐
  │  PRE-CONSTRUCTED     │   Migration PSBT signed
  │  MIGRATION PSBT      │   by quorum
  │                      │
  │  Destination:        │
  │  Pre-defined at      │
  │  account setup       │
  └──────────┬───────────┘
             │
             ▼
  ┌──────────────────────┐
  │  BROADCAST TO        │
  │  BITCOIN NETWORK     │
  └──────────┬───────────┘
             │
        ┌────┴────┐
        ▼         ▼
  ┌──────────┐  ┌──────────────────┐
  │ THRESHOLD│  │ PRE-APPROVED     │
  │ BRIDGE   │  │ COUNTERPARTY     │
  │          │  │ ADDRESS          │
  │ (Default)│  │                  │
  │          │  │ (Where venue's   │
  │          │  │  settlement      │
  │          │  │  requires it)    │
  └──────────┘  └──────────────────┘
```

**Critical property:** The migration destination is whitelisted at account setup. Signers do not exercise discretion about where capital goes. They verify a predefined condition and execute a predefined action.

***

### Layer 5: Recovery Architecture

Every VBA includes layered recovery paths that operate without requiring counterparty cooperation.

```
┌─────────────────────────────────────────────────────────────────┐
│                  RECOVERY ARCHITECTURE                           │
└─────────────────────────────────────────────────────────────────┘

   FAILURE SCENARIO 1: Signer network disrupted
   │
   ├── Recovery Path A: Joint depositor + custodian recovery
   │   │
   │   ├── Threshold signers not required
   │   ├── Institution's key + custodian's key sign
   │   ├── Activates immediately upon configured condition
   │   └── No timelock delay
   │
   │
   FAILURE SCENARIO 2: Both signer network AND custodian unavailable
   │
   ├── Recovery Path B: Depositor solo recovery
   │   │
   │   ├── Only the institution's key required
   │   ├── Enforced by OP_CHECKLOCKTIMEVERIFY timelock
   │   ├── Timelock duration set at account creation
   │   │   (e.g., 90 days, 180 days, 1 year)
   │   └── Activates automatically when timelock expires


   ENFORCEMENT MECHANISM:
   ┌─────────────────────────────────────────────────────┐
   │  All recovery paths encoded in Bitcoin Script       │
   │  Enforced by Bitcoin consensus                      │
   │  No third-party intervention required               │
   │  The Bitcoin UTXO is the system of record           │
   └─────────────────────────────────────────────────────┘
```

***

### Complete Lifecycle Summary

A complete view of how a VBA progresses from setup through operation to potential recovery.

```
┌──────────────────────────────────────────────────────┐
│                    VBA LIFECYCLE                     │
└──────────────────────────────────────────────────────┘

  PHASE 1: SETUP
  ┌────────────────────────────────────────────────────┐
  │ • Quorum structure defined                         │
  │ • Whitelisted venues selected                      │
  │ • Recovery paths configured with timelocks         │
  │ • Bitcoin Script encoded                           │
  │ • PSBTs pre-constructed                            │
  │ • Optional ACA executed                            │
  └─────────────────────┬──────────────────────────────┘
                        ▼
  PHASE 2: FUNDING
  ┌────────────────────────────────────────────────────┐
  │ • Institution funds VBA from existing custody      │
  │ • UTXO created with embedded Script conditions     │
  │ • Account verified and operational                 │
  └─────────────────────┬──────────────────────────────┘
                        ▼
  PHASE 3: DEPLOYMENT
  ┌────────────────────────────────────────────────────┐
  │ • Capital deployed into whitelisted venues         │
  │ • Multi-party authorization on every movement      │
  │ • All transactions verifiable on Bitcoin L1        │
  └─────────────────────┬──────────────────────────────┘
                        ▼
  PHASE 4: ACTIVE OPERATION
  ┌────────────────────────────────────────────────────┐
  │ • Threshold signers monitor venue activity         │
  │ • Yield accrues to the account                     │
  │ • Standard reporting generated                     │
  │ • Quarterly reviews with coverage team             │
  └─────────────────────┬──────────────────────────────┘
                        ▼
  PHASE 5: EXIT (one of three paths)
  ┌────────────────────────────────────────────────────┐
  │ Path A: Normal Exit                                │
  │   Multi-party quorum unwinds positions             │
  │   Capital returns to the institution's custod      │
  │                                                    │
  │ Path B: Liquidation-Triggered Migration            │
  │   Pre-defined migration path executes              │
  │   Capital settles on the whitelisted destination   │
  │                                                    │
  │ Path C: Recovery (Counterparty Failure)            │
  │   Recovery path activates per Script conditions    │
  │   The institution recovers capital independently   │
  └────────────────────────────────────────────────────┘
```

***

### Trust Model Summary

| Component                  | Trust Assumption                           | Enforcement Mechanism                               |
| -------------------------- | ------------------------------------------ | --------------------------------------------------- |
| **Spending conditions**    | None required                              | Bitcoin Script + consensus                          |
| **Quorum threshold**       | None required                              | Bitcoin Script (OP\_CHECKMULTISIG)                  |
| **Timelock enforcement**   | None required                              | Bitcoin Script (OP\_CHECKLOCKTIMEVERIFY)            |
| **Liquidation monitoring** | Threshold signer honesty (threshold-bound) | Distributed signer network with quorum requirements |
| **Migration execution**    | Threshold signer honesty (threshold-bound) | Pre-defined paths in Bitcoin Script                 |
| **Recovery activation**    | None required                              | Bitcoin consensus + timelock                        |
| **Legal enforceability**   | Counterparty contractual compliance        | Tripartite Account Control Agreement (optional)     |

**Net trust model:** The institution does not need to trust Threshold Network for the integrity of the rules governing its capital. It uses Threshold's signer network for operational liveness. If that liveness fails, the recovery path activates without requiring Threshold's participation.

***

### Verification

Every condition governing a VBA can be verified independently:

* **Spending conditions:** Inspect the Bitcoin Script attached to the UTXO using any Bitcoin full node or open-source script debugger
* **Quorum requirements:** Validate the multisig structure encoded in Script
* **Recovery paths:** Confirm the timelock parameters and signing requirements
* **Onchain state:** Read the UTXO and transaction history directly from any Bitcoin full node
* **Deployment activity:** Audit transactions on the relevant chain and reconcile against the VBA's signed PSBT history

No proprietary tooling is required for any of this verification.
