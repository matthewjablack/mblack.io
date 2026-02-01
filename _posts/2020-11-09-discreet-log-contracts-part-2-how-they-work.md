---
layout: post
title: "Discreet Log Contracts Part 2: How They Work [Adaptor Version]"
date: 2020-11-09
categories: [Bitcoin, DLCs, Smart Contracts]
---

*This post is a recreation of the original article ["Discreet Log Contracts Part 2: How They Work [Adaptor Version]"](https://web.archive.org/web/20231130175441/https://suredbits.com/discreet-log-contracts-part-2-how-they-work-adaptor-version/) by Nadav Kohen, originally published on Suredbits.com on November 9, 2020. The original company has since shut down, and this content is preserved via the Internet Archive.*

*Original author: Nadav Kohen*
*Original publication: Suredbits.com*
*Archive URL: [https://web.archive.org/web/20231130175441/https://suredbits.com/discreet-log-contracts-part-2-how-they-work-adaptor-version/](https://web.archive.org/web/20231130175441/https://suredbits.com/discreet-log-contracts-part-2-how-they-work-adaptor-version/)*

---

In our last Discreet Log Contract [post](/bitcoin/dlcs/smart contracts/2019/09/05/discreet-log-contracts-part-1-what-is-a-discreet-log-contract.html), we explored the problems faced by most existing smart contracts as well as what Discrete Log Contracts (DLCs) are at a high level. In this post we will detail what exactly what it takes to execute a DLC as well as the many benefits we reap for doing so.

## Discreet Log Contracts Series:

- [Part 1 – What Is A Discreet Log Contract](/bitcoin/dlcs/smart contracts/2019/09/05/discreet-log-contracts-part-1-what-is-a-discreet-log-contract.html)
- Part 2 – How They Work [Adaptor Version] (this post)
- [Part 3 – Why Discreet Log Contracts Are Great](https://web.archive.org/web/20231130175441/https://suredbits.com/discreet-log-contracts-part-3-why-discreet-log-contracts-are-great/)
- [Part 4 – Security and Trust Model](https://web.archive.org/web/20231130175441/https://suredbits.com/discreet-log-contracts-part-4-security-and-trust-model/)

As the name suggests, DLCs are based on the Discrete Log Problem (DLP). In this context, we are specifically interested in the Elliptic Curve DLP which is used by the Bitcoin protocol. This means we can be sure we aren't adding to our set of cryptographic assumptions.

See our [Cryptography Appendix](https://web.archive.org/web/20231130175441/https://suredbits.com/cryptography-appendix/), or our Schnorr series for more depth, for details on how we use the DLP to generate public keys from private keys, and how we sign messages with private keys in such a way that only require [public keys for validation](https://web.archive.org/web/20231130175441/https://suredbits.com/schnorr-applications-scriptless-scripts/). Also see our blog post on [adaptor signatures](https://web.archive.org/web/20231130175441/https://suredbits.com/schnorr-applications-adaptor-signatures/) for details on how signatures can be verifiably encrypted.

As discussed in the appendix, a Schnorr signature of a message is just a number which requires a private key, a private secret and the message to compute. However, verifying this signature only requires the public key, the public key associated with the secret (usually denoted as *R*) and the message.

In particular, it is possible to compute a public key *P<sub>m</sub>*, whose private key is the valid signature of *m*, from just the signer's public key and *R* value! Thus, we can create our simple oracle contracts by creating Contract Execution Transactions (CETs) for each of the possible messages an oracle might sign and then having counter-parties exchange adaptor signatures of these CETs using *P<sub>m</sub>* as an adaptor point. In case the reader is unfamiliar with adaptor signatures, they are signatures which have been encrypted using some adaptor point (in this case, *P<sub>m</sub>*) which can still be verified as valid signatures in their encrypted form, but which require the adaptor point's scalar (in this case this is the oracle signature of *m*) to decrypt into an on-chain valid signature.

Suppose that Alice and Bob wish to enter into a DLC using the SuredBits oracle which signs the message "MOON" or "CRASH" every 24 hours if the price of BTC goes up or down at the end of that 24 hours interval.

1. Like any good contract, the first step is for them to construct a **Funding Transaction**. This is a transaction with collateral input and a simple 2-of-2 multisignature output (with one key from Alice and one from Bob). Say that each of Alice and Bob put in 1 BTC and that Alice gets 1.5 BTC while Bob gets 0.5 BTC in the MOON case and Bob wins 1.5 BTC and Alice gets 0.5 BTC in the CRASH case.

2. They now compute the two public keys *P<sub>MOON</sub>* and *P<sub>CRASH</sub>* whose scalars are the Schnorr signatures that the SuredBits oracle might broadcast. They do this using the oracle's public key, *P*, along with the *R-value* for this event, which is broadcast daily.

3. They each create and generate adaptor signatures for two more transactions, one for each possible event, both of which spend the Funding Transaction. We call these **Contract Execution Transactions** (CETs). In Alice's case, the first one will output 1.5 BTC to Alice if she can decrypt Bob's adaptor signatures using the *P<sub>MOON</sub>* key, this requires the oracle's signature of MOON. The second will output 0.5 BTC to Alice she can decrypt Bob's adaptor signatures using the *P<sub>CRASH</sub>* key, this requires the oracle's signature of CRASH.

4. They now sign and publish the Funding Transaction to the blockchain.

5. Now they wait 24 hours. Say that the price goes up so that the SuredBits oracle signs the message "*MOON*". Alice can now publish her CET and she gets 1.5 BTC.

Note that it is very easy to extend this scenario to arbitrarily many outcomes. There happen to be some clever [schemes](https://web.archive.org/web/20231130175441/https://suredbits.com/discreet-log-contracts-part-4-security-and-trust-model/) to do so. It is also relatively straightforward to extend this scheme to more parties.

We now have a more in-depth understanding of what Discreet Log Contracts are and how they work. In the next post, we will discuss why we bother with DLCs, discuss their privacy, composability and scalability benefits.

---

*Article credit: Nadav Kohen, originally published on Suredbits.com*
*Archive: [https://web.archive.org/web/20231130175441/https://suredbits.com/discreet-log-contracts-part-2-how-they-work-adaptor-version/](https://web.archive.org/web/20231130175441/https://suredbits.com/discreet-log-contracts-part-2-how-they-work-adaptor-version/)*
