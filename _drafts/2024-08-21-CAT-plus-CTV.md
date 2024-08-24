---
title: CAT + CTV
description: >-
  Get started with Chirpy basics in this comprehensive overview.
  You will learn how to install, configure, and use your first Chirpy-based website, as well as deploy it to a web server.
date: 2024-08-14
categories: [Soft Forks]
tags: [OP_CAT, OP_CTV, Scaling]
image:
  path: /4L7lL2D.png
  lqip: data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAAoAAAAGCAYAAAD68A/GAAAAAklEQVR4AewaftIAAADnSURBVAXBQUvCYACA4ffbFpumOQ8fIc0EF0EYYVBChw52CjrWqV/Tv6qf4EUPUkR4qJSl6SbYvs1pbvU84vK6/Wf+xtSqJkqk3LQrHFUd+r0vhtGSl94Mf5Fh5FYhe61jrDSg4ZZYvK/xhc7T4xRp6ySiiHRzGHJfEk0CRNlC3xTQyxFvrx+0zlxGqeLKFgQJaE25obajyB/c4Rn35OsujeYu3c4zi1HIwMtI5t9oP35ElgpKhqIgl6hwRfdzzentOZW65OLEIp5G6EFceKjaGpkac5gf4og5znZKPAsYexP6nQHFLZN/bM1co24e5OUAAAAASUVORK5CYII=
  alt: CTV mascot facing off against the menancing CAT in the Bitcoin forest
---

## What's the next Bitcoin Soft Fork?

### History

It's been almost three years since [Taproot](https://www.coindesk.com/tech/2021/11/13/taproot-bitcoins-long-anticipated-upgrade-activates-this-weekend/) was activated, and almost seven years since [Segwit](https://calendar.bitbo.io/segwit-activation/) was activated.

For the first year of both soft forks, there was initially limited any [adoption](https://cryptonews.com/exclusives/taproot-adoption-remains-low-but-devs-say-it-isnt-problem-for-bitcoin.htm). With Segwit, it took years for wallet developers and others to add support for bech32 addresses, not to mention for Lightning clients to be built and slowly adopted. For Taproot, there was barely any adoption, until ordinals, inscriptions, runes, and stamps were introduced, driving usage [sky high](https://bitcoinist.com/bitcoin-taproot-utilization-new-ath-thanks-ordinals/).

![](https://i.imgur.com/spDMDC0.png)

People incorrectly assume that Taproot and/or Segwit enabled inscriptions. This is not true, since **arbitrary data** on Bitcoin has [always been possible](https://www.coindesk.com/tech/2021/11/13/taproot-bitcoins-long-anticipated-upgrade-activates-this-weekend/). This has created a new class of people who believe that Bitcoin should never change again. They believe we should aim for Bitcoin ossification. 

#### Necessary Hard Forks

On one hand having unchanging money is an important property of sound money. We all know the problems introduced when you can freely make changes to the underlying chain, such as having [blockchain rollbacks](https://www.coindesk.com/tech/2016/07/20/ethereum-executes-blockchain-hard-fork-to-return-dao-funds/). On the other hand, Bitcoin is a software project. Software requires updates, bug fixes, and continual changes in order to [stay operational](https://x.com/bitschmidty/status/1808500228321943953). An extreme example of this is the [2106 bug](https://github.com/bitcoin/bitcoin/issues/21356), caused by the fact that bitcoin uses unsigned 32-bit time types, which will overflow in the year 2106, and cause the chain to halt.

#### Current Vulnerabilities

A more immediate concern is the [Great Consensus Cleanup](https://delvingbitcoin.org/t/great-consensus-cleanup-revival/710), which aims to fix some major vulnerabilities in the current implementation of the software. Things like [timewarp](https://delvingbitcoin.org/t/great-consensus-cleanup-revival/710#timewarp-1) issue, which can allow miners to reduce difficulty of new blocks, [worst case block validation time](https://delvingbitcoin.org/t/great-consensus-cleanup-revival/710#worst-case-block-validation-time-5), which could be up to 3 mins for maliciously crafted legacy transactions, or [merkle tree attacks](https://delvingbitcoin.org/t/great-consensus-cleanup-revival/710#merkle-tree-attacks-using-64-bytes-transactions-8), which can trick light clients into accepting invalid transactions.

> Note: these vulnerabilities have been known for more than a decade, and [never exploited](https://x.com/JeremyRubin/status/1811846115425419391). This may or may not be a case of "security through obscurity".
{: .prompt-info }

Therefore, in my opinion, it's natural to be skeptical to change. But with software, you also need to be skeptical to lack of change (updates and maintenance).

With that in mind, let's talk about the proposed soft forks.

## Proposed Soft Forks

Potential Soft Forks
- [Great Consensus Cleanup (GCC)](#current-vulnerabilities)
- CTV
- old CTV (commits to any combination of inputs and outputs)
- CTV + VAULT
- CAT
- Great Script Restoration (includes CAT)
- LNHANCE

## How does Taproot actually work

![](https://i.imgur.com/edWGDav.png)

This has created a new class of maximalist in the Bitcoin community. The **Ossification Maximalists**.

This resulted in a new groups of folks called the ossification maximalists.

Folks who want Bitcoin to never change again. (Even though we all know that Bitcoin NEEDS a [hard fork by 2106](https://www.coindesk.com/tech/2020/08/07/fixing-this-bitcoin-killing-bug-will-eventually-require-a-hard-fork/))

https://x.com/Truthcoin/status/1088934244762640384

https://x.com/reardencode/status/1795106631748882654

utxos.org

CTV enables

Scaling, Soft Fork Bets, Ark V2, Vaults

https://github.com/taproot-wizards/purrfect_vault/

https://github.com/bennyhodl/dlcat

https://delvingbitcoin.org/t/great-consensus-cleanup-revival/710

Show the Bitcoin stack and OP_CAT concatenating two of them together

https://www.wpsoftware.net/andrew/blog/cat-and-schnorr-tricks-i.html

https://x.com/TheVladCostea/status/1776985929661497568

## What is OP_CTV?

CTV stands for `CheckTemplateVerify`.

TLDR it allows you 

## What is OP_CAT?

CAT simply stands for "concatenate" which joins two strings together

Let's say you have two strings on the stack, and `OP_CAT` is encountered as the script is executing:

```bash
script  |  stack
--------+--------
OP_CAT  |      02
        |      01
```
{: .nolineno }

Next we run to the next step of execution, and we can see that both strings have been joined together.

```bash
script  |  stack
--------+--------
        |    0102
```
{: .nolineno }

(If you're curious to run this yourself, you can check out )

## Why was OP_CAT disabled?

OP_CAT used to be part of the original Bitcoin stack, but Satoshi disabled it back in 2010 as part of [CVE-2010-5137](https://en.bitcoin.it/wiki/Common_Vulnerabilities_and_Exposures#CVE-2010-5137).

## Old CTV

Originally named `OP_SECURETHEBAG`


In the next series we'll talk about how OP_CAT can be used to enable rollups on Bitcoin

https://covenants.info/

---

CAT and CTV have garnered the most attention as potential soft forks lately.

CTV previously had very strong support: https://utxos.org/signals/

With CAT gaining momentum recently:
- Officially asigned [BIP 347](https://github.com/bitcoin/bips/blob/master/bip-0347.mediawiki) (and unnoficial [BIP 420](https://github.com/bip420/bip420))
- Starkware commiting to building [rollups on Bitcoin](https://www.theblock.co/post/298398/starkware-plans-to-bring-zk-scaling-to-bitcoin-alongside-ethereum)
  - Open Source [Bitcoin Circle Stark Verifier](https://github.com/Bitcoin-Wildlife-Sanctuary/bitcoin-circle-stark)
  - Covenant Examples including [State carrying UTXO via P2WSH](https://github.com/Bitcoin-Wildlife-Sanctuary/covenants-examples)
- Taproot Wizards indirectly signalling support with their [Quantum Cats](https://www.axios.com/2024/04/24/bitcoin-quantum-cats-nft-taproot-code) inscription collection 
  - [Purrfect Vault Covenant Demo](https://github.com/taproot-wizards/purrfect_vault/) by [Rijndael](https://x.com/rot13maxi)

---

> Note: if Satoshi had decided to keep `OP_CAT` back in 2010, that alone wouldn't have enabled covenants. Covenants are [only](https://x.com/benthecarman/status/1811837279830442104) enabled by Schnorr (Taproot) + `OP_CAT`. This is thanks to the fact that Taproot includes a variant of Schnorr signature called BIP340 signature, where the [public key shows up explicitly in the signature hash](https://www.wpsoftware.net/andrew/blog/cat-and-schnorr-tricks-i.html), allowing for transaction introspection by [rebuilding a txid on the stack](https://github.com/taproot-wizards/purrfect_vault?tab=readme-ov-file#limitations-and-considerations). Therefore, we can assume that if Satoshi hadn't disabled `OP_CAT` back in 2010, then Taproot may have been substantially more contentious when being considered back in 2019.
{: .prompt-info }

> Additionally, covenants are quite inneficient on-chain using `OP_CAT`, with implementations needing to be [extremely careful](https://github.com/taproot-wizards/purrfect_vault/?tab=readme-ov-file#limitations-and-considerations) not to exceed the consensus limit on stack item size (520 bytes) 
{: .prompt-warning }

---

## How does all this apply to real world use cases?

### Scaling

### DeFi

Dramatically improves DLCs. 

### Vaults
