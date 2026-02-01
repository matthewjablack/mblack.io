---
layout: post
title: "Discreet Log Contracts Part 4: Security and Trust Model"
date: 2019-09-26
categories: [Bitcoin, DLCs, Smart Contracts]
---

*This post is a recreation of the original article ["Discreet Log Contracts Part 4: Security and Trust Model"](https://web.archive.org/web/20240224203038/https://suredbits.com/discreet-log-contracts-part-4-security-and-trust-model/) by Nadav Kohen, originally published on Suredbits.com on September 26, 2019. The original company has since shut down, and this content is preserved via the Internet Archive.*

*Original author: Nadav Kohen*
*Original publication: Suredbits.com*
*Archive URL: [https://web.archive.org/web/20240224203038/https://suredbits.com/discreet-log-contracts-part-4-security-and-trust-model/](https://web.archive.org/web/20240224203038/https://suredbits.com/discreet-log-contracts-part-4-security-and-trust-model/)*

---

So far in our Discrete Log Contracts series, we've covered what they are, how they work, and why they're great. In this final post, we will enter into a practical discussion of the security and trust considerations involved when dealing with DLCs.

## Discreet Log Contracts Series:

- [Part 1 – What is a Discreet Log Contract?](/posts/discreet-log-contracts-part-1-what-is-a-discreet-log-contract/)
- [Part 2 – How Discreet Log Contracts Work](/posts/discreet-log-contracts-part-2-how-they-work/)
- [Part 3 – Why Discreet Log Contracts are Great](/posts/discreet-log-contracts-part-3-why-they-are-great/)
- Part 4 – Security and Trust Model (this post)

## Security

The DLC (user) security model is quite simple and there are only two keys involved. The first key owns your half of the multi-signature funding transaction, let's call this the funding key. The second key gets paid to – should you receive any money out of this DLC – through a CET spend, let's call this the payout key. Both keys can be generated for this DLC and never used again.

The funding key is an initially hot key that can be made cold. It needs to be available during setup because it is used to sign all of the CETs. After this, it has generated all necessary signatures that could ever be needed meaning that the funding key can be kept in cold storage for the entire life of the contract, which is very feasible for long-term contracts. In theory it can even be deleted but this is not recommended.

The payout key can be indefinitely cold! The payout key is never used since it is not spent as part of the DLC and only receives funds.

And that's it as far as keys are concerned. All DLC keys can be kept in cold storage pretty much all of the time! It is important to remember that if your DLC private keys be compromised, all funds tied to those keys are vulnerable.

A slightly more subtle yet important implementation detail is that you should never non-adaptor sign your own CETs except for the case where you sign exactly one which you plan on publishing as a unilateral contract execution. This is important because if an adversary gets a hold of a fully signed CET for any event that has not occurred, they can publish it to the blockchain which could constitute a loss of funds.

One last security consideration to keep in mind is that you should be using a secure channel when communicating with your counter-party. Note that you do not need a secure channel with the oracle since they are publicly broadcasting their signatures – which self-authenticate.

## Trust

As the entire point of DLCs is to execute contracts based on "real-world" (i.e. not internal to the blockchain) information, trust is required and unavoidable. What is great about DLCs when it comes to trust is that they provide what I believe to be a pretty idyllic trust model given that we must put trust somewhere in order to have this functionality.

To start, you are not required to place any trust in your counter-party! At no point does contract execution depend on your counter-party once the DLC is setup (which also requires no counter-party trust).

Like any oracle scheme, we are going to need some trust in our oracle. However, the DLC scheme provides us with a lot of nice, often built-in, trust mitigation. Firstly, the oracle does not know that you or your contract exists. Second, you can (and should) add a time-locked refund CET in case the oracle becomes unresponsive.

Third, every oracle lie will come in the form of a signature tying the oracle's public key to a false message. This acts as a disincentive to lying as an oracle's business model is partially based on reputation. A lie provides extremely compact cryptographic proof of misbehavior. Taking this a step further, I believe that oracles of the future will stake their funds using trusted escrow services in order to gain trustworthiness and cryptographic proof of misbehavior is all that is needed to enforce retribution (and possibly user compensation).

Fourth, users can (and should) use multiple DLC oracles at once to protect themselves from a disappearing and/or lying minority. Not only this, but it also multiplies the cost of oracle bribery n-fold (where n is the number of oracles required for unilateral execution). Note that DLC's provide for really natural oracle aggregation via [schnorr key aggregation](https://web.archive.org/web/20240224203038/https://suredbits.com/schnorr-applications-key-aggregation/).

Lastly, perhaps the most powerful property of the DLC scheme is that oracles cannot equivocate. This means that they cannot tell different users different things have happened. If an oracle signs two different messages with the same keys (which are pre-committed as part of the DLC scheme), then anyone who sees both signatures can compute the oracle's private keys! Should this happen, any user can now sign for the oracle in any situation, as well as take any bitcoin tied to the key. And similar to what I mentioned with escrows earlier, I believe that DLC oracles will publicly stake funds by holding bitcoin with their signing key so that they lose these funds to users should they equivocate.

Thus, we can see that in its fully mature form, a DLC involves distributing some soft version of trust among many oracles, all of whom remain oblivious to your contract and have a lot to lose should they attempt lying.

Not only is lying punishable, but it should be noted that telling the truth is rewarded since oracle's can be [anonymously paid](https://web.archive.org/web/20240224203038/https://suredbits.com/paid-apis/) for their services, making them sustainable to run without sacrificing privacy or trust.

One last practical consideration that DLC users should have in mind is that you must take care when setting fees on CETs, since a fee that is too low can delay or even erase your ability to turstlessly (unilaterally) execute the DLC.

This wraps up our discussion of Discreet Log Contracts. I hope that you now understand how they work, why they're great, and how they provide us with a solid solution to the oracle problem by allowing users to privately distribute minimal trust among trustworthy oracles!

Contact us [@Suredbits](https://twitter.com/Suredbits)

Contact Nadav [@Nadav_Kohen](https://twitter.com/Nadav_Kohen)

All of our API services, for both [Cryptocurrency APIs](https://web.archive.org/web/20240224203038/https://suredbits.com/api/crypto/) as well as [Sports APIs](https://web.archive.org/web/20240224203038/https://suredbits.com/api/sports/), are built using [Lightning technology](https://web.archive.org/web/20240224203038/https://suredbits.com/lightning-technology/) and the Lightning Network. All API services are live on Bitcoin's [mainnet](https://web.archive.org/web/20240224203038/https://suredbits.com/mainnet/). Our fully customizable data service allows customers to stream as much or as little data as they wish and [pay using bitcoin](https://web.archive.org/web/20240224203038/https://suredbits.com/pay-using-bitcoin/).

You can connect to our Lightning node at the url:

```
038bdb5538a4e415c42f8fb09750729752c1a1800d321f4bb056a9f582569fbf8e@ln.suredbits.com
```

To learn more about how our Lightning APIs work please visit our [API documentation](https://web.archive.org/web/20240224203038/https://suredbits.com/api-documentation/) or checkout our [Websocket Playground](https://web.archive.org/web/20240224203038/https://suredbits.com/websocket-playground/) to start exploring fully customized data feeds.

If you are a company or cryptocurrency exchange interested in learning more about how Lightning can help grow your business, contact us at [sales@suredbits.com](mailto:sales@suredbits.com). We would love to talk to you.

---

*Article credit: Nadav Kohen, originally published on Suredbits.com*
*Archive: [https://web.archive.org/web/20240224203038/https://suredbits.com/discreet-log-contracts-part-4-security-and-trust-model/](https://web.archive.org/web/20240224203038/https://suredbits.com/discreet-log-contracts-part-4-security-and-trust-model/)*
