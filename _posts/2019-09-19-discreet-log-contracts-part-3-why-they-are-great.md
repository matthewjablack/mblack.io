---
layout: post
title: "Discreet Log Contracts Part 3: Why They Are Great"
date: 2019-09-19
categories: [Bitcoin, DLCs, Smart Contracts]
---

*This post is a recreation of the original article ["Discreet Log Contracts Part 3: Why They Are Great"](https://web.archive.org/web/20231130162659/https://suredbits.com/discreet-log-contracts-part-3-why-they-are-great/) by Nadav Kohen, originally published on Suredbits.com on September 19, 2019. The original company has since shut down, and this content is preserved via the Internet Archive.*

*Original author: Nadav Kohen*
*Original publication: Suredbits.com*
*Archive URL: [https://web.archive.org/web/20231130162659/https://suredbits.com/discreet-log-contracts-part-3-why-they-are-great/](https://web.archive.org/web/20231130162659/https://suredbits.com/discreet-log-contracts-part-3-why-they-are-great/)*

---

In previous posts, we discussed both what Discreet Log Contracts (DLCs) are and how they work in detail. Now, let's talk about the features of DLCs and how they make powerful oracle contracts.

## Discreet Log Contracts Series:

- [Part 1 – What is a Discreet Log Contract?](/posts/discreet-log-contracts-part-1-what-is-a-discreet-log-contract/)
- [Part 2 – How Discreet Log Contracts Work](/posts/discreet-log-contracts-part-2-how-they-work/)
- Part 3 – Why They Are Great (this post)
- [Part 4 – Security and Trust Model](https://web.archive.org/web/20231130162659/https://suredbits.com/discreet-log-contracts-part-4-security-and-trust-model/)

Discreet Log Contracts are private and secure. Not only is the contract only known by the parties involved, but not even the oracle needs to know *anything* about its users. All they need to do is broadcast signatures and can even potentially monetize (see our post on [PAID APIs](https://web.archive.org/web/20231130162659/https://suredbits.com/paid-apis/)).

The public sees nothing that can be distinguished from simple payment activity, no information goes on-chain except for the payout. Not only do oracles stay oblivious of their users but there are additional built in protections in the DLC scheme against oracles lying. We will address the security and trust model of DLCs further in our next post.

DLCs are also very flexible. The DLC scheme can be used for contracts on just about any blockchain (including Bitcoin). All that is needed is multi-signature contracts and timeouts. Users across different blockchains and Layer 2 solutions can simultaneously use the same oracles. Further, there is a natural way to combine DLC oracles. For example, to extend the scheme described [previously](https://web.archive.org/web/20231130162659/https://suredbits.com/discreet-log-contracts-part-2-how-they-work-adaptor-version/) to a 2-oracle DLC, Alice and Bob must simply add the two oracles' *P_MOON* keys to get an aggregate *P_MOON*, whose underlying private key is simply the sum of the two oracles' signatures!

Discreet Log Contracts are scalable. They are natural candidates for execution within payment channels such as those of the [Lightning Network](https://web.archive.org/web/20231130162659/https://suredbits.com/category/lightning-101/). It is possible to execute arbitrary DLCs completely off-chain with absolutely no on-chain footprint, fees, or privacy leaks. Even without going fully off-chain, DLCs are already mostly off-chain so that the only on-chain footprint is two simple value-transfer transactions – funding and closing. This makes DLCs a viable candidate on any chain to "allow extensive complex smart contracts to occur without unduly taxing the [global network](https://web.archive.org/web/20231130162659/https://suredbits.com/discreet-log-contracts-part-3-why-they-are-great/)".

Finally DLCs are composable. Essentially a DLC is an oracle contract that requires nothing but multisig or MuSig to enforce. This means that all smart-contracting platforms out there, such as Ethereum, can replace the oracle subcontracts it currently uses, with DLCs. This replacement allows those involved to enjoy all of the benefits we have highlighted.

All of this from building blocks so simple that they can be expressed in Bitcoin Script!

But what exactly are the security and trust models when it comes to executing DLCs? This will be the topic of our next post.

Contact us [@Suredbits](https://twitter.com/Suredbits)

Contact Nadav [@Nadav_Kohen](https://twitter.com/Nadav_Kohen)

All of our API services, for both [Cryptocurrency APIs](https://web.archive.org/web/20231130162659/https://suredbits.com/api/crypto/) as well as [Sports APIs](https://web.archive.org/web/20231130162659/https://suredbits.com/api/sports/), are built using [Lightning technology](https://web.archive.org/web/20231130162659/https://suredbits.com/lightning-technology/) and the Lightning Network. All API services are live on Bitcoin's [mainnet](https://web.archive.org/web/20231130162659/https://suredbits.com/mainnet/). Our fully customizable data service allows customers to stream as much or as little data as they wish and [pay using bitcoin](https://web.archive.org/web/20231130162659/https://suredbits.com/pay-using-bitcoin/).

You can connect to our Lightning node at the url:

```
038bdb5538a4e415c42f8fb09750729752c1a1800d321f4bb056a9f582569fbf8e@ln.suredbits.com
```

To learn more about how our Lightning APIs work please visit our [API documentation](https://web.archive.org/web/20231130162659/https://suredbits.com/api-documentation/) or checkout our [Websocket Playground](https://web.archive.org/web/20231130162659/https://suredbits.com/websocket-playground/) to start exploring fully customized data feeds.

If you are a company or cryptocurrency exchange interested in learning more about how Lightning can help grow your business, contact us at [sales@suredbits.com](mailto:sales@suredbits.com). We would love to talk to you.

---

*Article credit: Nadav Kohen, originally published on Suredbits.com*
*Archive: [https://web.archive.org/web/20231130162659/https://suredbits.com/discreet-log-contracts-part-3-why-they-are-great/](https://web.archive.org/web/20231130162659/https://suredbits.com/discreet-log-contracts-part-3-why-they-are-great/)*
