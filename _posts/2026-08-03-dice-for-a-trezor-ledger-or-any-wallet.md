---
title: How to Use Dice With a Trezor, a Ledger, or Any Other Wallet
description: >-
  Your hardware wallet does not need a dice mode. BIP-39 is a standard, so you can
  generate the words anywhere you trust and import them anywhere. Plus why the popular
  "enter the same rolls into two devices" check sometimes lies.
date: 2026-08-03
categories: [Bitcoin, Security]
tags: [Hardware Wallets, Trezor, Ledger, BIP39]
image:
  path: https://mblack.io/assets/img/posts/dice-any-wallet.jpg
  lqip: data:image/jpeg;base64,/9j/4AAQSkZJRgABAQAASABIAAD/4QBMRXhpZgAATU0AKgAAAAgAAYdpAAQAAAABAAAAGgAAAAAAA6ABAAMAAAABAAEAAKACAAQAAAABAAAADKADAAQAAAABAAAACAAAAAD/7QA4UGhvdG9zaG9wIDMuMAA4QklNBAQAAAAAAAA4QklNBCUAAAAAABDUHYzZjwCyBOmACZjs+EJ+/8AAEQgACAAMAwEiAAIRAQMRAf/EAB8AAAEFAQEBAQEBAAAAAAAAAAABAgMEBQYHCAkKC//EALUQAAIBAwMCBAMFBQQEAAABfQECAwAEEQUSITFBBhNRYQcicRQygZGhCCNCscEVUtHwJDNicoIJChYXGBkaJSYnKCkqNDU2Nzg5OkNERUZHSElKU1RVVldYWVpjZGVmZ2hpanN0dXZ3eHl6g4SFhoeIiYqSk5SVlpeYmZqio6Slpqeoqaqys7S1tre4ubrCw8TFxsfIycrS09TV1tfY2drh4uPk5ebn6Onq8fLz9PX29/j5+v/EAB8BAAMBAQEBAQEBAQEAAAAAAAABAgMEBQYHCAkKC//EALURAAIBAgQEAwQHBQQEAAECdwABAgMRBAUhMQYSQVEHYXETIjKBCBRCkaGxwQkjM1LwFWJy0QoWJDThJfEXGBkaJicoKSo1Njc4OTpDREVGR0hJSlNUVVZXWFlaY2RlZmdoaWpzdHV2d3h5eoKDhIWGh4iJipKTlJWWl5iZmqKjpKWmp6ipqrKztLW2t7i5usLDxMXGx8jJytLT1NXW19jZ2uLj5OXm5+jp6vLz9PX29/j5+v/bAEMACQkJCQkJEAkJEBYQEBAWHhYWFhYeJh4eHh4eJi4mJiYmJiYuLi4uLi4uLjc3Nzc3N0BAQEBASEhISEhISEhISP/bAEMBCwwMEhESHxERH0szKjNLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS0tLS//dAAQAAf/aAAwDAQACEQMRAD8A850p9EWBxf8AmbuwUKePqawbgo0zFMbc8dBx+FIn9KgoGf/Z
  alt: Dice, a handwritten seed word list, and hardware wallets that have no dice mode
---

*The most common reaction to the Coldcard entropy bug has been "great, but I own a Trezor and it has no dice mode, so this isn't for me." That is the wrong conclusion, and it's the fault of every dice guide including my own two. Your wallet does not need to support dice.*

---

## 🔑 Your wallet does not need a dice mode

Rolling dice produces a number. That number gets turned into 12 or 24 English words. Those words are your wallet.

The middle step, number into words, is **BIP-39**: a public standard that every serious wallet implements identically. It is arithmetic. It does not care what hardware it runs on.

So the job splits cleanly in two, and only the first half involves dice:

1. **Generate** the words from your dice, on any device or tool you trust.
2. **Import** those words into whatever wallet you actually want to use, via its normal "recover wallet" flow.

A Trezor cannot take your dice rolls. It can absolutely take the words those rolls produced. Same for a Ledger, a Passport, a BitBox, or a phone wallet.

> Your hardware wallet doesn't need to support dice. It needs to support importing a seed phrase. They all do, because that's the same feature that lets you restore a backup.
{: .prompt-tip }

There's an irony here worth naming. Right now the best tools for the dice half of this job are Coldcard and SeedSigner. If you've sworn off Coldcard after the bug, a SeedSigner will do the same work for about $50. The device that generates your seed and the device that holds it do not have to be the same device.

## 🧰 Where to actually generate the words

Ranked by how much I'd trust them, and be honest with yourself about the tradeoff: an "offline laptop" is a general purpose computer with a hard drive, a USB stack, firmware, and a history of being online. That is a much bigger attack surface than a device built to do one thing.

| Where | Why it's good | Watch out for |
| :--- | :--- | :--- |
| **SeedSigner** | Dice are the only entropy source. No persistent storage, so it forgets everything on power off. Reproducible builds since v0.7.0. ~$50 of parts. | You have to build it. Pi Zero supply is annoying. |
| **Coldcard / Keystone** | Purpose built, dice mode is verifiable, both publish how to check the result. | If you don't want to keep using it, generate, write the words down, then wipe it. |
| **Tails, no hard drive** | Amnesiac by design, leaves nothing behind. Run Coldcard's `rolls.py` or an offline copy of iancoleman. | You're trusting a laptop's firmware and the USB stick you booted from. |
| **Your daily laptop, "offline"** | It's what you have. | Genuinely the weakest option. Wi-Fi off is not the same as air gapped. Do this only for small amounts, if at all. |

Whatever you pick, the output is the same: 12 or 24 words on a piece of paper, which you then type into your Trezor or Ledger.

## 🔁 The "two devices" check, and when it lies

There's popular advice going around, and it is *nearly* right:

> "Yeah simple just enter 100 dice roles in an exact sequence twice in two different devices. WHY DOESN'T EVERYONE DO THIS?!"
> [@BitPaine](https://x.com/BitPaine), ~7K views
{: .prompt-info }

The instinct is excellent. Two independent implementations agreeing is exactly the kind of check that would have caught the Coldcard bug. But it only works **if both tools convert dice to entropy the same way**, and they don't all agree.

Here's the same 99 physical dice rolls put through two real implementations:

```text
rolls: 655152231316521321611331544441236164664431121534415633526456254462245546236542364246312613322234612

Coldcard / SeedSigner:  eyebrow obvious such suggest poet seven breeze blame virtual ...
Krux:                   skate announce rain myself cross become taxi swap sun ...
```

Same dice. Different wallets. Neither tool is broken.

The reason is the one this blog already made a fuss about: **separators change the hash**. Coldcard and SeedSigner hash `655152...`; Krux joins your rolls with dashes and hashes `6-5-5-1-5-2...`. One character per roll, completely different seed.[^krux]

So before you use the two device check, know which family your tools are in:

| Tool | How it turns dice into a seed | Matches Coldcard? |
| :--- | :--- | :---: |
| **Coldcard**, **SeedSigner**, **Keystone**, iancoleman (word count mode) | `SHA256` over the digits, no separators | **Yes** |
| **Krux** | `SHA256` over the digits, dash separated | **No** |
| **BitBox02**, **Blockstream Jade** | Printed diceware table: roll 5 dice, look up a word, repeat. Device computes the final checksum word | **Not comparable** |
| **Trezor**, **Ledger**, **OneKey** | No dice input at all | Import the words instead |

I verified the top two rows rather than taking anyone's word for it. Coldcard's `rolls.py` reproduces SeedSigner's own published test vector exactly, and the Krux divergence above is real output, not a hypothetical.[^verified] The BitBox and Jade rows are from vendor documentation.[^diceware]

> **Foundation Passport is genuinely unclear.** Its own community guide says there is no way to add your own entropy yet, while several reviews describe it mixing dice into device randomness. Those can't both be true. If your Passport does mix dice with the chip's output, that is perfectly safe but **not reproducible**, so the two device check is impossible by design. Ask Foundation before relying on either behaviour.
{: .prompt-warning }

That last point generalises into the only question you actually need to ask:

> **Are my dice the only input, or are they mixed with the device's own randomness?**
> Dice only means the result is reproducible and you can check it. Mixed means it's still safe, but nobody, including you, can ever recompute it.
{: .prompt-tip }

## ✅ The two checks people keep conflating

The tweet above blurs two different verifications. Separating them is what answers "but what do I check against?" for a Ledger.

| | What it proves | What it needs |
| :--- | :--- | :--- |
| **Check 1: dice to words** | your rolls were converted correctly | two tools from the **same** family in the table above |
| **Check 2: words to address** | the wallet is really using **your** seed | any two BIP-39 wallets, using the same derivation path |

**Check 1 is the one that needs matching algorithms.** Do it entirely offline, before any hardware wallet is involved.

**Check 2 works between literally any two BIP-39 wallets**, because deriving addresses from words is standardised in a way that dice handling never was. This is the check that covers you on a Trezor or a Ledger, and it's the one most people skip.

Concretely, for a Ledger:

1. Generate your words offline from dice. Verify with a second tool from the same family (Check 1).
2. On the same offline machine, derive the first receiving address at `m/84'/0'/0'/0/0`.
3. Write that address down. It should start with `bc1`.
4. Import the words into the Ledger.
5. In Ledger Live, look at your first receive address.
6. **It must match the address from step 3, character for character.**

If it matches, the Ledger is using your seed. You have not verified Ledger's firmware, and you can't, but you've confirmed the one thing that matters here: your dice, not its chip, produced this wallet.

## 📥 Importing into a Trezor

Trezor has no dice input. There's a long standing feature request and it isn't implemented.

What Trezor does have is an entropy check: the device commits to its own randomness before seeing 32 bytes from your computer, then proves it used both. It's a genuinely good design and it defends against exactly the counterfeit-device problem. It just isn't something you can drive with dice.

To import your own words:

- **Model One:** hold both buttons on boot, choose **Recover wallet**, pick 12/18/24 words. Standard recovery types words in Suite; advanced recovery uses the on-device 9-key layout so nothing sensitive touches the computer.
- **Model T / Safe 3 / Safe 5:** **Recover wallet from seed**, choose the word count, then tap the words on the device screen.

Prefer the on-device entry method where you have the choice. It keeps the words off your keyboard.

## 📥 Importing into a Ledger

Also no dice input.

On first setup, choose **Restore from recovery phrase** instead of "Set up as new device". Set a PIN, choose 12 or 24 words, then type the first few letters of each word and select it from the device's suggestions.

One thing to be aware of that has nothing to do with dice:

> **Ledger Recover applies to seeds you imported too.** The firmware can extract seed material from the secure element, split it, and escrow it with third parties, unlocked by identity verification. That includes the beautiful dice seed you just generated. If your reason for rolling dice is minimising who you have to trust, understand that this capability exists on the device regardless of how the seed got there.
{: .prompt-danger }

## 🚧 Four ways this goes wrong

**Derivation paths.** The same words produce completely different addresses depending on the path. `m/84'/0'/0'/0/0` gives `bc1...` (native SegWit), `m/49'/...` gives `3...`, `m/44'/...` gives `1...`. Import a seed into a wallet defaulting to a different path and you'll see a zero balance and panic. **Your coins are not gone**, they're on addresses the wallet isn't looking at. Always compare addresses using the same path.

**Electrum seeds are not BIP-39.** Electrum uses the same wordlist with a different checksum scheme. An Electrum seed will not import into a Trezor or Ledger. If you generate somewhere unusual, confirm it produced a genuine BIP-39 phrase.

**Passphrases.** If you add a BIP-39 passphrase (the "25th word"), it is part of the wallet. Same seed plus different passphrase equals a different wallet with no error message. Whatever you used at setup, you need at restore.

**Untested backups.** Restore the words onto a second wallet, or wipe and re-restore, and confirm you get the same first address before funding. Then send a small amount and confirm it arrives. A backup you have never restored is a hypothesis, not a backup.

## The short version

Roll 100 dice. Turn them into words on something you trust, ideally a device that does nothing else. Verify those words in a second tool **from the same family**. Type them into your Trezor or Ledger. Confirm the first address matches what you computed offline. Then fund it.

Your wallet never needed to know about the dice.

---

*New to this? Start with [Rolling Dice for Your Bitcoin Wallet: The Simple Guide]({% post_url 2026-08-03-rolling-dice-for-your-bitcoin-wallet-simple-guide %}). Want the entropy maths, the bug that caused all this, and the source code? [How to Safely Roll Dice for Your Bitcoin Seed]({% post_url 2026-08-03-how-to-safely-roll-dice-for-your-bitcoin-seed %}).*

[^krux]: Confirmed in [krux discussion #138](https://github.com/selfcustody/krux/discussions/138): *"Krux will use the sha256 hash of `1-5-6-3...` whereas ColdCard and SeedSigner will use the sha256 hash of `15634...`"*. Remove the dashes and the seeds match.

[^verified]: The 99 roll vector is SeedSigner's own, from [docs/dice_verification.md](https://github.com/SeedSigner/seedsigner/blob/dev/docs/dice_verification.md). I ran it through Coldcard's [rolls.py](https://coldcard.com/docs/rolls.py) and got SeedSigner's published mnemonic back, word for word. SeedSigner separately documents matching iancoleman and bitcoiner.guide/seed. Keystone [documents the same SHA256 construction](https://blog.keyst.one/how-to-verify-the-recovery-phrase-created-by-dice-rolling-af01c16b765e).

[^diceware]: [BitBox02 diceware how-to](https://bitbox.swiss/bitbox02/BitBox_Diceware_HowTo.pdf) and [lookup table](https://bitbox.swiss/bitbox02/BitBox_Diceware_LookupTable.pdf): five dice and a coin select each word from the printed 2048 word table, you enter 23 words and the device offers valid 24th words. [Jade's dice guide](https://help.blockstream.com/blockstream-jade/add-more-security-functionality/create-a-recovery-phrase-using-dice) works the same way, entering the first 11 or 23 words.
