---
title: Covenants dramatically improve DLCs
description: >-
  Discover how covenants can significantly enhance the efficiency and performance of Discreet Log Contracts (DLCs).
date: 2024-07-17
categories: [Discreet Log Contracts, DeFi]
tags: [OP_CAT, OP_CTV, DLCs]
math: true
image:
  path: /4L7lL2D.png
  lqip: data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAAoAAAAGCAYAAAD68A/GAAAAAklEQVR4AewaftIAAADnSURBVAXBQUvCYACA4ffbFpumOQ8fIc0EF0EYYVBChw52CjrWqV/Tv6qf4EUPUkR4qJSl6SbYvs1pbvU84vK6/Wf+xtSqJkqk3LQrHFUd+r0vhtGSl94Mf5Fh5FYhe61jrDSg4ZZYvK/xhc7T4xRp6ySiiHRzGHJfEk0CRNlC3xTQyxFvrx+0zlxGqeLKFgQJaE25obajyB/c4Rn35OsujeYu3c4zi1HIwMtI5t9oP35ElgpKhqIgl6hwRfdzzentOZW65OLEIp5G6EFceKjaGpkac5gf4og5znZKPAsYexP6nQHFLZN/bM1co24e5OUAAAAASUVORK5CYII=
  alt: CTV mascot facing off against the menancing CAT in the Bitcoin forest
---

## Introduction

[Discreet Log Contracts (DLCs)](https://atomic.finance/blog/discreet-log-contracts/) enable the creation of financial contracts on Bitcoin, such as [bets](https://x.com/atomicfinance/status/1321181338112851975), [perps](https://10101.finance/), [options](https://atomic.finance/), etc. However the current process can be very time-consuming and clunky. This post explores how covenants, specifically [OP_CTV](https://github.com/bitcoin/bips/blob/master/bip-0119.mediawiki) and [OP_CAT](https://github.com/bitcoin/bips/blob/master/bip-0347.mediawiki), can dramatically improve the efficiency and performance of DLCs.

## Current State of DLCs

Currently there are two main computational and implementation complexities that hinder the UX for DLCs:
1. Adaptor Signatures
2. Data Exchange

These issues also lead to problems with granularity!!!

If you round too much this is a problem

### Adaptor Signatures

Adaptor signatures are encrypted signatures used in DLCs to match a specific transaction condition based on the oracle attesting to a particular outcome by creating a signature. The process involves encrypting a transaction signature such that the oracle's released signature acts as the decryption key.

Mathematically, given an anticipated signature \( Y \), an ECDSA signature \( s_a \) is encrypted under \( Y \) such that once the discrete logarithm of \( Y \) is known, the signature can be decrypted. This involves operations like 

$$
k' + H(X, R', m) \cdot x
$$

$$
s_a = k^{-1} (m + r \cdot x)
$$ 

where \( k \) is a nonce, \( m \) is the message hash, and \( r \) is derived from the x-coordinate of \( R = k \cdot Y \).

The computational complexity arises from the need to securely generate nonces, perform elliptic curve multiplications, and verify discrete logarithm equality proofs. Each step involves intensive cryptographic operations, such as computing \( R = k \cdot Y \) and verifying that 

$$
u_1 \cdot G + u_2 \cdot X = R_a
$$ 

where $ u_1 $ and $ u_2 $ are derived from the signature components. These operations are computationally expensive, especially when scaled to multiple transactions, making the process time-consuming and resource-intensive.



Inspired by Lloyd's post on the dlc-dev mailing list, let's first understand the current process of entering a DLC, which typically takes around 2 minutes:

1. Contract Negotiation
2. Offer
3. Accept (25s)
4. Sign (25s)
5. Backup to watchtower (30s)
6. Broadcast

## How Covenants Improve DLCs

Covenants, such as OP_CTV (CheckTemplateVerify) and OP_CAT (Concatenate), offer significant improvements to the DLC process. Here's how:

### OP_CTV

OP_CTV allows for the creation of more efficient and secure DLCs by enabling the commitment to specific transaction templates. This reduces the computational overhead and simplifies the process of verifying and executing contracts.

- **Efficiency**: Replaces complex multiplications with simple hashes, reducing computational time from hundreds of microseconds to around 5 microseconds per party.
- **Scalability**: Handles thousands of outcomes efficiently, making it feasible to bet on detailed events like BTC/USD prices.
- **Security**: Simplifies threshold signatures, reducing the risk of security vulnerabilities.

### OP_CAT

OP_CAT, which stands for "concatenate," joins two strings together on the Bitcoin stack. This can be used to create more complex and flexible contract structures.

- **Flexibility**: Allows for the combination of multiple conditions and outcomes, enabling more sophisticated contract designs.
- **Performance**: Reduces the need for multiple transactions, streamlining the execution process.

## Real-World Use Cases

### Atomic Finance

Atomic Finance has been exploring the use of covenants to improve the performance of their DLCs. Currently, entering a DLC with 5000 CETs involves multiple steps and significant computational effort. With covenants, this process can be dramatically simplified and accelerated.

### Scaling and DeFi

Covenants enable more scalable and efficient DeFi applications on Bitcoin. By reducing the computational and communication complexity, they make it feasible to implement more complex financial instruments and contracts.

### Vaults

Covenants also enhance the security and efficiency of Bitcoin vaults, providing a more robust mechanism for securing funds and executing complex transactions.

## Conclusion

Covenants like OP_CTV and OP_CAT offer a promising path forward for improving the efficiency, scalability, and security of DLCs on Bitcoin. By simplifying the process and reducing computational overhead, they enable more sophisticated and practical applications of smart contracts on the Bitcoin network.

https://github.com/stevenroose/covenants.info/pull/31/files

---

For more detailed technical insights, you can refer to Lloyd's original post on the dlc-dev mailing list and our previous blog post on CAT vs CTV.
