---
title: "DLCs vs Oracle Multisig: A Cryptographic Analysis"
description: >-
  A deep technical analysis of the cryptographic primitives, transaction flows, and security models in DLC-based vs oracle multisig Bitcoin lending systems
date: 2025-04-08
categories: [Bitcoin, DLCs, Lending, Cryptography]
mermaid: true
image:
  path: /JoZSA2N.jpeg
  alt: DLCs vs Oracle Multisig - A Cryptographic Analysis
---

*This post provides a detailed technical comparison of two approaches to non-custodial Bitcoin-backed lending: DLC-based systems using adaptor signatures and oracle multisig systems using standard ECDSA/Schnorr multi-party signing. We examine the cryptographic primitives, transaction flows, security models, and liveness requirements of each approach.*

---

## Cryptographic Primitives

### DLC Adaptor Signatures

DLCs rely on adaptor signatures, which are "incomplete" signatures that can be completed (adapted) given knowledge of a discrete logarithm.

#### Construction

Given:
- Oracle public key `O = oG`
- Oracle nonce `R = kG`
- Message `m` to sign
- Signer secret key `x`

The adaptor signature protocol:

1. **Oracle Announcement**: Oracle publishes `(R, m_event)` committing to attest to outcome of `m_event`

2. **Adaptor Signature Creation**: Signer creates adaptor signature `s'` where:
   ```
   s' = k_signer + H(P || R + R_signer || m) * x
   ```
   This signature is incomplete—it requires knowledge of `k` (the oracle's nonce secret) to validate.

3. **Oracle Attestation**: When event occurs, oracle publishes `s_oracle = k + H(R || outcome) * o`

4. **Signature Adaptation**: Given `s_oracle`, anyone can compute:
   ```
   s = s' + s_oracle
   ```
   This produces a valid Schnorr signature.

```mermaid
sequenceDiagram
    participant O as Oracle
    participant S as Signer
    participant V as Verifier

    Note over O,V: Setup Phase
    O->>O: Generate nonce k, R = kG
    O->>S: Publish (R, event_description)

    Note over O,V: Adaptor Creation
    S->>S: Create adaptor sig s'
    S->>V: Send (s', R_signer)
    V->>V: Verify adaptor (incomplete sig)

    Note over O,V: Attestation Phase
    O->>O: Event occurs, compute s_oracle
    O->>S: Publish s_oracle

    Note over O,V: Adaptation
    S->>S: Compute s = s' + s_oracle
    S->>V: Broadcast transaction with s
    V->>V: Verify complete signature
```

#### Security Properties

1. **Binding**: The adaptor signature commits to a specific oracle attestation
2. **Hiding**: Without the oracle attestation, the adaptor signature is computationally indistinguishable from random
3. **Extractability**: Given adapted signature and adaptor, oracle secret can be extracted (accountability)

### Oracle Multisig: Standard Multi-Party ECDSA/Schnorr

Firefish uses a 3-of-3 multisig where all parties must sign each transaction.

#### Construction (MuSig2 style for Schnorr)

Given three signers with keys `(P_1, P_2, P_3)`:

1. **Key Aggregation**: Compute aggregate key `P_agg = a_1*P_1 + a_2*P_2 + a_3*P_3` where `a_i` are challenge coefficients

2. **Nonce Exchange**: Each signer generates nonces and exchanges commitments

3. **Partial Signatures**: Each signer produces `s_i = k_i + c * a_i * x_i`

4. **Aggregation**: Final signature `s = s_1 + s_2 + s_3`

```mermaid
sequenceDiagram
    participant B as Borrower (B-EPH)
    participant PO as Price Oracle
    participant PyO as Payment Oracle

    Note over B,PyO: Setup Phase
    B->>B: Generate ephemeral key B-EPH
    B->>PO: Exchange pubkeys
    PO->>PyO: Exchange pubkeys

    Note over B,PyO: Pre-signing (all 5 closing txs)
    B->>B: Sign all closing transactions
    PO->>PO: Sign all closing transactions (except responsible outcome)
    PyO->>PyO: Sign all closing transactions (except responsible outcome)

    Note over B,PyO: Key Destruction
    B->>B: Discard B-EPH private key

    Note over B,PyO: Execution (e.g., liquidation)
    PO->>PO: Add final signature to liquidation tx
    PyO->>PyO: Add final signature to liquidation tx
    PO->>PyO: Broadcast transaction
```

#### Security Properties

1. **Threshold security**: All 3 parties must cooperate (no 2-of-3 spending)
2. **Non-repudiation**: Signed transactions are valid Bitcoin transactions
3. **Key deletion binding**: Once B-EPH is deleted, borrower cannot sign new transactions

---

## Transaction Flow Analysis

### DLC Transaction Structure

```mermaid
flowchart TB
    subgraph Funding["Funding Layer"]
        F1[Borrower UTXO] --> F2[Funding TX]
        F2 --> F3[2-of-2 Multisig Output<br/>Borrower + Lender]
    end

    subgraph CETs["Contract Execution Transactions"]
        C1[CET: Not Funded<br/>Adaptor: not-funded attestation]
        C2[CET: Repaid<br/>Adaptor: repaid attestation]
        C3[CET: Liquidated Price<br/>Adaptor: price-liquidation attestation]
        C4[CET: Liquidated Maturity<br/>Adaptor: maturity-liquidation attestation]
    end

    subgraph Refund["Refund Layer"]
        R1[Refund TX<br/>Timelock: maturity + buffer]
    end

    F3 --> C1
    F3 --> C2
    F3 --> C3
    F3 --> C4
    F3 --> R1

    C1 --> |"Output"| B1[Borrower Address]
    C2 --> |"Output"| B2[Borrower Address]
    C3 --> |"Output"| L1[Lender Address]
    C4 --> |"Output"| L2[Lender Address]
    R1 --> |"Output"| B3[Borrower Address]
```

#### CET Spending Conditions

Each CET has the spending condition:
```
<borrower_sig> <lender_sig>
OP_CHECKMULTISIG
```

Where both signatures are adaptor signatures that require the oracle's attestation to become valid.

#### Adaptor Signature Binding

For the liquidation CET:
```
adaptor_sig_borrower = sign_adaptor(tx_liquidation, oracle_pubkey, "liquidated-by-price")
adaptor_sig_lender = sign_adaptor(tx_liquidation, oracle_pubkey, "liquidated-by-price")
```

These become valid signatures only when oracle publishes `attest("liquidated-by-price")`.

### Oracle Multisig Transaction Structure

```mermaid
flowchart TB
    subgraph Prefund["Prefund Layer"]
        PF1[Borrower UTXO] --> PF2[Prefund TX]
        PF2 --> PF3[Prefund Output<br/>3-of-3 OR Borrower+timelock]
    end

    subgraph Escrow["Escrow Layer"]
        E1[Escrow TX]
        E2[3-of-3 Multisig<br/>B-EPH + Price Oracle + Payment Oracle]
    end

    subgraph Closing["Closing Transactions"]
        CT1[tx_repayment<br/>Pre-signed by B-EPH, Price Oracle]
        CT2[tx_default<br/>Pre-signed by B-EPH, Price Oracle<br/>Timelock: maturity]
        CT3[tx_liquidation<br/>Pre-signed by B-EPH]
        CT4[tx_recover<br/>Pre-signed by B-EPH, Price Oracle, Payment Oracle<br/>Timelock: maturity + 1 month]
    end

    PF3 --> E1
    E1 --> E2
    E2 --> CT1
    E2 --> CT2
    E2 --> CT3
    E2 --> CT4

    CT1 --> |"Missing: Payment Oracle sig"| BO1[Borrower]
    CT2 --> |"Missing: Payment Oracle sig"| LQ1[Liquidator]
    CT3 --> |"Missing: Price + Payment Oracle sigs"| LQ2[Lender/Liquidator]
    CT4 --> |"All sigs present"| BO2[Borrower]
```

#### Escrow Spending Conditions

The escrow output script:
```
OP_3 <B-EPH_pubkey> <price_oracle_pubkey> <payment_oracle_pubkey> OP_3 OP_CHECKMULTISIG
```

All three keys must sign for any spend (until B-EPH is discarded, after which only pre-signed transactions are valid).

#### Pre-signing Matrix

| Transaction | B-EPH | Price Oracle | Payment Oracle | Missing at Execution |
| ----------- | ----- | ------------ | -------------- | -------------------- |
| tx_repayment | ✓ | ✓ | - | Payment Oracle |
| tx_default | ✓ | ✓ | - | Payment Oracle |
| tx_liquidation | ✓ | - | - | Price Oracle, Payment Oracle |
| tx_recover | ✓ | ✓ | ✓ | None (timelock only) |

---

## Security Model Comparison

### Trust Assumptions

#### DLC Model

```mermaid
flowchart TB
    subgraph TrustDLC["DLC Trust Model"]
        T1[Oracle Honesty]
        T2[Oracle Liveness<br/>for attestation only]
        T3[No key escrow]
    end

    subgraph NoTrustDLC["No Trust Required"]
        NT1[Oracle doesn't sign txs]
        NT2[Oracle doesn't see funds]
        NT3[Oracle doesn't know parties]
    end
```

**Trust required:**
- Oracle attests honestly to events
- Oracle is available to publish attestations

**No trust required:**
- Oracle cannot steal funds (doesn't have keys)
- Oracle cannot direct funds (predetermined CETs)
- Oracle cannot selectively withhold (refund exists)

#### Oracle Multisig Model

```mermaid
flowchart TB
    subgraph TrustMultisig["Oracle Multisig Trust Model"]
        T1[Oracle Honesty]
        T2[Oracle Liveness<br/>for signing + broadcasting]
        T3[Correct key discard]
    end

    subgraph NoTrustMultisig["No Trust Required"]
        NT1[Predetermined outputs]
        NT2[Disaster recovery exists]
    end
```

**Trust required:**
- Oracle signs and broadcasts promptly
- Oracle signs the correct transaction
- Borrower correctly discards B-EPH

**No trust required:**
- Funds go to predetermined addresses
- Recovery available if oracles fail

### Attack Surface Analysis

#### DLC Attack Vectors

| Attack | Mitigation |
| ------ | ---------- |
| Oracle attests wrong outcome | Accountability via signature extraction |
| Oracle offline | Timelock refund CET |
| Borrower/Lender key compromise | Funds at risk (2-of-2 property) |
| Oracle key compromise | Oracle cannot move funds directly |

#### Oracle Multisig Attack Vectors

| Attack | Mitigation |
| ------ | ---------- |
| Oracle signs wrong tx | Predetermined outputs limit damage |
| Oracle offline | Disaster recovery (maturity + 1 month) |
| Oracle collusion | 2-of-3 threshold... wait, it's 3-of-3 |
| B-EPH not discarded | Borrower could sign alternative txs |
| B-EPH discarded early | Funds stuck until disaster recovery |

### Collusion Resistance

```mermaid
flowchart LR
    subgraph DLCCollusion["DLC Collusion Scenarios"]
        D1["Oracle + Borrower<br/>Cannot steal from lender<br/>(lender has adaptor sigs)"]
        D2["Oracle + Lender<br/>Cannot steal from borrower<br/>(borrower has adaptor sigs)"]
        D3["Borrower + Lender<br/>Can cooperatively close<br/>(2-of-2 allows this)"]
    end

    subgraph MultisigCollusion["Multisig Collusion Scenarios"]
        M1["2 Oracles + Anyone<br/>Could sign arbitrary tx<br/>(if B-EPH not discarded)"]
        M2["After B-EPH discard<br/>Oracles limited to<br/>pre-signed transactions"]
    end
```

---

## Liveness Requirements

### Who Must Be Online When?

```mermaid
gantt
    title Liveness Requirements Timeline
    dateFormat  YYYY-MM-DD
    section DLC
    Borrower Setup    :a1, 2024-01-01, 1d
    Lender Setup      :a2, 2024-01-01, 1d
    Oracle (attestation only) :a3, 2024-01-15, 1d
    Anyone can execute :a4, 2024-01-15, 1d

    section Oracle Multisig
    Borrower Setup    :b1, 2024-01-01, 1d
    Oracles Setup     :b2, 2024-01-01, 1d
    Oracle must sign  :b3, 2024-01-15, 1d
    Oracle must broadcast :b4, 2024-01-15, 1d
```

#### DLC Liveness Matrix

| Phase | Borrower | Lender | Oracle | Watchtower |
| ----- | -------- | ------ | ------ | ---------- |
| Setup | Online | Online | Announcement only | N/A |
| Funding | Signs tx | Signs tx | N/A | N/A |
| During loan | Offline OK | Offline OK | N/A | N/A |
| Execution | Offline OK | Offline OK | Publish attestation | Can execute |

#### Oracle Multisig Liveness Matrix

| Phase | Borrower | Price Oracle | Payment Oracle |
| ----- | -------- | ------------ | -------------- |
| Setup | Online | Online | Online |
| Escrow creation | Signs, discards key | Signs | Signs |
| During loan | Offline OK | N/A | N/A |
| Execution | N/A (key gone) | Must sign | Must sign |
| Broadcast | N/A | Must broadcast | Must broadcast |

### Watchtower Compatibility

```mermaid
flowchart TB
    subgraph DLCWatchtower["DLC Watchtower"]
        DW1[Watchtower receives<br/>adaptor signatures]
        DW2[Monitors oracle<br/>for attestations]
        DW3[Adapts and broadcasts<br/>on party's behalf]
        DW1 --> DW2 --> DW3
    end

    subgraph MultisigWatchtower["Oracle Multisig 'Watchtower'"]
        MW1[No delegation possible]
        MW2[Oracle IS the<br/>execution layer]
        MW3[Cannot replace oracle<br/>at execution time]
        MW1 --> MW2 --> MW3
    end
```

**DLC watchtower protocol:**
1. Party shares adaptor signatures with watchtower
2. Watchtower monitors oracle feed
3. On relevant attestation, watchtower adapts signatures
4. Watchtower broadcasts transaction
5. Original party doesn't need to be online

**Oracle multisig limitation:**
- Oracle must sign at execution time
- Cannot delegate to third party
- Oracle unavailability = execution delayed

---

## Privacy Analysis

### Information Leaked to Oracle

#### DLC Information Flow

```mermaid
flowchart LR
    subgraph Loan["Loan Data"]
        L1[Amount]
        L2[Liquidation Price]
        L3[Maturity Date]
        L4[Repayment Status]
    end

    subgraph Oracle["Oracle Receives"]
        O1[Event parameters]
        O2[Attestation requests]
    end

    subgraph Hidden["Hidden from Oracle"]
        H1[Contract address]
        H2[Borrower identity]
        H3[Lender identity]
        H4[Payout addresses]
        H5[UTXO information]
    end

    Loan --> Oracle
    Hidden -.->|"NOT shared"| Oracle
```

#### Oracle Multisig Information Flow

```mermaid
flowchart LR
    subgraph Loan["Loan Data"]
        L1[Amount]
        L2[All transaction details]
        L3[Contract addresses]
        L4[Party pubkeys]
    end

    subgraph Oracle["Oracle Receives"]
        O1[Full transaction to sign]
        O2[Input/output addresses]
        O3[UTXO references]
    end

    Loan --> Oracle
```

### On-Chain Fingerprint

| Aspect | DLC | Oracle Multisig |
| ------ | --- | --------------- |
| Funding output | 2-of-2 multisig (common) | 3-of-3 multisig (less common) |
| Distinguishability | Similar to Lightning channels | More unique fingerprint |
| Output count | Single output per CET | Single output per closing tx |

---

## Multi-Oracle Extensibility

### DLC Disjoint Union

DLCs support combining multiple oracle attestations without coordination:

```mermaid
flowchart TB
    subgraph Oracles["Independent Oracles"]
        O1["Oracle A (Price Feed 1)<br/>Announcement: R_a"]
        O2["Oracle B (Price Feed 2)<br/>Announcement: R_b"]
        O3["Oracle C (Payment)<br/>Announcement: R_c"]
    end

    subgraph Combination["Announcement Combination"]
        C1["Combined nonce point<br/>R = R_a + R_b + R_c"]
        C2["Adaptor signatures use<br/>combined R"]
    end

    subgraph Execution["Execution Requirements"]
        E1["Need attestations from<br/>all oracles"]
        E2["Or threshold (2-of-3)<br/>with appropriate construction"]
    end

    Oracles --> Combination --> Execution
```

**Properties:**
- Oracles don't need to communicate
- Oracles don't know about each other
- Oracles don't know if their attestations are used
- Flexible threshold schemes possible

### Oracle Multisig Multi-Party

```mermaid
flowchart TB
    subgraph MultiOracle["Multi-Oracle Multisig"]
        M1["5-of-7 threshold?"]
        M2["All 7 must coordinate<br/>on nonce exchange"]
        M3["PSBT passed between<br/>all parties"]
        M4["Each signer adds<br/>partial signature"]
    end

    subgraph Coordination["Coordination Requirements"]
        C1["All oracles know<br/>about each other"]
        C2["Communication channel<br/>between oracles"]
        C3["Sequential or parallel<br/>signing rounds"]
    end

    MultiOracle --> Coordination
```

**Challenges:**
- All signers must coordinate nonces
- Communication channel required
- Signing protocol complexity increases with n
- Easier for signers to collude (already coordinating)

---

## Failure Mode Analysis

### Complete Failure Recovery

```mermaid
stateDiagram-v2
    [*] --> Active: Loan funded
    Active --> OracleOffline: Oracle stops responding

    state DLC {
        OracleOffline --> WaitTimelock: Wait for refund timelock
        WaitTimelock --> RefundCET: Timelock expires
        RefundCET --> BorrowerRecovery: Broadcast refund
    }

    state OracleMultisig {
        OracleOffline --> WaitMaturity: Wait until maturity + 1 month
        WaitMaturity --> DisasterTx: Timelock expires
        DisasterTx --> BorrowerRecovery: tx_recover valid
    }

    BorrowerRecovery --> [*]: Funds recovered
```

### Partial Failure Scenarios

| Scenario | DLC Outcome | Oracle Multisig Outcome |
| -------- | ----------- | ----------------------- |
| Price Oracle offline | Wait for recovery timelock | Wait for disaster recovery |
| Payment Oracle offline | Wait for recovery timelock | Wait for disaster recovery |
| Borrower key lost | Lender can still execute CETs | No impact (key already discarded) |
| Lender key lost | Borrower can still execute CETs | No impact (not a signer) |
| Attestation never published | Refund CET after timelock | Disaster tx after timelock |

---

## Implementation Complexity

### DLC Implementation Requirements

```
- Adaptor signature library
- Oracle announcement parsing
- CET construction for each outcome
- Signature adaptation logic
- Refund transaction construction
```

### Oracle Multisig Implementation Requirements

```
- Multi-party signing coordination
- Ephemeral key generation and secure deletion
- Pre-signing protocol for all closing txs
- Timelock management
- PSBT handling for oracle signing
```

### Comparative Complexity

| Component | DLC | Oracle Multisig |
| --------- | --- | --------------- |
| Cryptographic primitives | Adaptor signatures | Standard multisig |
| Key management | Standard (no deletion) | Ephemeral key lifecycle |
| Pre-signing | 4 CETs with adaptors | 5 closing txs with partial sigs |
| Execution | Local adaptation | Oracle signing protocol |
| Oracle integration | Announcement + attestation | Signing service |

---

## Summary

### Cryptographic Trade-offs

| Dimension | DLC | Oracle Multisig |
| --------- | --- | --------------- |
| Signature scheme | Adaptor signatures | Standard ECDSA/Schnorr |
| Oracle role | Attestation (passive) | Signing (active) |
| Key management | Standard | Ephemeral + deletion |
| Execution | Anyone can adapt | Oracle must sign |
| Multi-oracle | Disjoint union (simple) | n-of-m multisig (complex) |

### Security Trade-offs

| Dimension | DLC | Oracle Multisig |
| --------- | --- | --------------- |
| Oracle trust | Honesty for attestation | Honesty + availability for signing |
| Key compromise risk | 2-of-2 = both needed | 3-of-3 + key discard |
| Collusion resistance | Adaptor sigs limit damage | Pre-signed txs limit damage |
| Recovery mechanism | Timelock CET | Disaster tx |

### Operational Trade-offs

| Dimension | DLC | Oracle Multisig |
| --------- | --- | --------------- |
| Liveness requirements | Oracle: attestation only | Oracle: sign + broadcast |
| Watchtower support | Yes | No |
| Privacy from oracle | On-chain data hidden | Oracle signs all details |
| Top-up flexibility | Oracle update possible | Requires new contract |

---

*For implementation details, refer to the [DLC Specification](https://github.com/discreetlogcontracts/dlcspecs) and [Firefish Protocol Documentation](https://docs.firefish.io/firefish-protocol).*

*See also: [DLCs are perfect for Lending](/posts/dlc-are-perfect-for-lending/) for Lygos's enumerated outcome approach.*
