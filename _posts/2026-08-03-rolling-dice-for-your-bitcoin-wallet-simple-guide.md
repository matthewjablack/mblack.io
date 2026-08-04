---
title: "Rolling Dice for Your Bitcoin Wallet: The Simple Guide"
description: >-
  No maths, no jargon. Buy dice, roll them 100 times, write the numbers down
  correctly, and you have a Bitcoin wallet nobody can guess. Here's exactly how.
date: 2026-08-03
categories: [Bitcoin, Security]
tags: [Hardware Wallets, Beginners]
---

*A bug in a popular hardware wallet just cost people a lot of money because the wallet picked bad random numbers. You can sidestep that whole problem by picking the numbers yourself, with dice. It takes about fifteen minutes and costs about ten dollars. This guide assumes you know nothing.*

---

## 🎲 Why dice?

Your Bitcoin wallet is protected by one very large random number. Everything else comes from it: your seed words, your addresses.

Normally your wallet device picks that number for you, in secret, and you simply have to trust that it did a good job. In July 2026 a lot of people found out the hard way that their device had not done a good job. It had been picking predictable numbers for five years, and thieves worked them out and emptied the wallets. Nobody had to touch the physical devices.

Dice fix this completely. When you roll dice, **you** make the random number. The device just does arithmetic on what you gave it. There is no secret step left to trust.

> This isn't a workaround or a hack. It's a built-in feature of most serious hardware wallets, and it's the one part of your setup you can check yourself.
{: .prompt-tip }

## 🛒 What you need

- **One or more six-sided dice.** Casino dice (the sharp-edged translucent ones) are about $10 for a set and are made to be fair. A cheap board-game die is *probably* fine but is slightly weighted by its scooped-out dots. If you're securing real money, buy the good ones.
- **Pen and paper.** Not your phone. Not a notes app.
- **Something that turns dice into words.** SeedSigner and Keystone both do this on the device, and so does an offline copy of a tool called iancoleman's BIP39 page. A Coldcard can too, if you already have one. It does **not** have to be the wallet you plan to keep your Bitcoin in (see below).
- **A hard, flat surface**, ideally with something to bounce the dice off.

## 🎯 Step 1: Roll 100 times

That's the whole rule. **Roll 100 times.** Not 20, not 50 if you want to keep it simple. 100 is enough for every wallet and every setting, so you never have to think about it again.

You can roll one die 100 times, or roll 20 dice five times. Both work.

Use this to keep count so you don't lose your place. Tap once per roll:

<div class="roll-counter" id="roll-counter">
  <div class="rc-display">
    <span class="rc-num" id="rc-num">0</span><span class="rc-of">/ 100</span>
  </div>
  <div class="rc-bar"><span class="rc-fill" id="rc-fill"></span></div>
  <p class="rc-status" id="rc-status">Tap the button once for each roll.</p>
  <button type="button" class="rc-tap" id="rc-tap">TAP TO COUNT</button>
  <div class="rc-controls">
    <button type="button" class="rc-small" id="rc-undo">Undo last</button>
    <button type="button" class="rc-small" id="rc-reset">Start over</button>
  </div>
  <p class="rc-note">This only counts <em>how many times</em> you've rolled. It never asks what you rolled, and nothing leaves your device.</p>
</div>

<style>
.roll-counter{--rc-bg:#f6f8fa;--rc-fg:#1b1b1e;--rc-mut:#6b7280;--rc-line:#dfe3e8;--rc-ok:#03b303;--rc-acc:#0070cb;--rc-btn:#fff;
background:var(--rc-bg);color:var(--rc-fg);border:1px solid var(--rc-line);border-radius:.625rem;padding:1.5rem 1.25rem;margin:1.5rem 0;text-align:center}
@media (prefers-color-scheme:dark){.roll-counter{--rc-bg:#1e1f22;--rc-fg:#e6e6e6;--rc-mut:#9aa0a6;--rc-line:#34363b;--rc-ok:#0fa40f;--rc-acc:#0075d1;--rc-btn:#26272b}}
html[data-mode=light] .roll-counter{--rc-bg:#f6f8fa;--rc-fg:#1b1b1e;--rc-mut:#6b7280;--rc-line:#dfe3e8;--rc-ok:#03b303;--rc-acc:#0070cb;--rc-btn:#fff}
html[data-mode=dark] .roll-counter{--rc-bg:#1e1f22;--rc-fg:#e6e6e6;--rc-mut:#9aa0a6;--rc-line:#34363b;--rc-ok:#0fa40f;--rc-acc:#0075d1;--rc-btn:#26272b}
.roll-counter *{box-sizing:border-box}
.rc-display{line-height:1;margin-bottom:.9rem}
.rc-num{font-size:4rem;font-weight:800;font-variant-numeric:tabular-nums;letter-spacing:-.02em}
.rc-of{font-size:1.4rem;font-weight:600;color:var(--rc-mut);margin-left:.4rem}
.rc-bar{height:.6rem;background:var(--rc-line);border-radius:999px;overflow:hidden;margin-bottom:.9rem}
.rc-fill{display:block;height:100%;width:0;background:var(--rc-acc);border-radius:999px;transition:width .15s ease,background .15s ease}
.rc-status{margin:0 0 1rem;font-weight:600;min-height:1.4em}
.rc-tap{display:block;width:100%;padding:1.5rem 1rem;font-size:1.15rem;font-weight:800;letter-spacing:.06em;font-family:inherit;
color:#fff;background:var(--rc-acc);border:0;border-radius:.625rem;cursor:pointer;user-select:none;-webkit-tap-highlight-color:transparent}
.rc-tap:active{transform:scale(.985);filter:brightness(.92)}
.rc-tap[disabled]{background:var(--rc-ok);cursor:default}
.rc-controls{display:flex;gap:.5rem;justify-content:center;margin-top:.75rem}
.rc-small{background:var(--rc-btn);color:var(--rc-mut);border:1px solid var(--rc-line);border-radius:.5rem;padding:.4rem .9rem;font-size:.85rem;font-family:inherit;cursor:pointer}
.rc-small:hover{color:var(--rc-fg)}
.rc-note{margin:1rem 0 0;font-size:.8rem;color:var(--rc-mut)}
</style>

<script>
(function () {
  var box = document.getElementById('roll-counter');
  if (!box) return;
  var TARGET = 100, KEY = 'mb-dice-rolls';
  var n = 0;
  try { n = Math.min(TARGET, parseInt(localStorage.getItem(KEY), 10) || 0); } catch (e) { n = 0; }

  var numEl = document.getElementById('rc-num'),
      fillEl = document.getElementById('rc-fill'),
      statEl = document.getElementById('rc-status'),
      tapEl = document.getElementById('rc-tap');

  function save() { try { localStorage.setItem(KEY, n); } catch (e) {} }

  function render() {
    numEl.textContent = n;
    fillEl.style.width = (n / TARGET) * 100 + '%';
    var done = n >= TARGET;
    fillEl.style.background = done ? 'var(--rc-ok)' : 'var(--rc-acc)';
    tapEl.disabled = done;
    tapEl.textContent = done ? 'DONE. STOP ROLLING' : 'TAP TO COUNT';
    if (done) {
      statEl.style.color = 'var(--rc-ok)';
      statEl.textContent = 'That is 100 rolls. Stop here. More will not help.';
    } else {
      statEl.style.color = '';
      statEl.textContent = n === 0
        ? 'Tap the button once for each roll.'
        : (TARGET - n) + ' rolls to go.';
    }
  }

  tapEl.addEventListener('click', function () { if (n < TARGET) { n++; save(); render(); } });
  document.getElementById('rc-undo').addEventListener('click', function () { if (n > 0) { n--; save(); render(); } });
  document.getElementById('rc-reset').addEventListener('click', function () { n = 0; save(); render(); });
  document.addEventListener('keydown', function (e) {
    if (e.code === 'Space' && document.activeElement === tapEl) { e.preventDefault(); tapEl.click(); }
  });

  render();
})();
</script>

A few things that matter while you roll:

- **Roll properly.** Shake them, let them bounce and tumble. Don't gently place them down.
- **Take whatever comes up.** If you roll six 6s in a row, that is a perfectly normal result. Write it down. Re-rolling things that "look wrong" is the single most common way people accidentally weaken their wallet.
- **Don't arrange the dice** into a nicer order after they land.
- **Decide before you start** how you'll read multiple dice: left to right, top to bottom. Then stick to it.

## 📝 Step 2: Write them down (people get this wrong)

Write your rolls as **one unbroken line of digits**. No spaces. No dashes. No grouping.

✅ **Right:**

```text
3141592653589793238462643383279502884197169399375105820974944592
```

❌ **Wrong:**

```text
31415 92653 58979 32384 62643 38327 95028 84197 16939 93751
3-1-4-1-5-9-2-6-5-3
314 159 265 358 979
```

This looks like fussiness. It is not.

Your wallet takes everything you type and scrambles it into your secret number. A space is a character, so it gets scrambled in too. **Typing `1 2 3` instead of `123` produces a completely different wallet.**

> Here's how this ruins someone's week: you write your rolls in neat groups of five so they're easy to read. You type them into your wallet without the spaces. Months later you check your backup and type them in *with* the spaces, and get different words. You conclude your hardware wallet has been hacked. It hasn't. You just typed something different.
{: .prompt-danger }

Worse, the wallets don't agree with each other about this. Some quietly ignore your spaces; some don't. If you never use spaces in the first place, you never have to know which is which.

One long line of digits. That's it.

## 📲 Step 3: Type them into your wallet

Look for "dice" in your device's menu when creating a new seed. On a SeedSigner: **Tools → New Seed (the dice icon) → 24 words (99 rolls)**. Keystone has the same option when you create a wallet. If you're using a Coldcard you already own: **New Seed Words → Advanced → 24 Word Dice Roll**. In each case you just press the numbers as you read them off your paper.

Type them all in. Some devices ask for exactly 99 rolls; use your first 99 and ignore the spare, it makes no difference. The device will then show you your seed words.

> **What if my wallet has no dice option?** Trezor and Ledger don't have one, and it doesn't matter. Generate the words on something that does, write them down, and then type those words into your Trezor or Ledger using its normal "restore from recovery phrase" option. The words work in any wallet. Full instructions: [How to Use Dice With a Trezor, a Ledger, or Any Other Wallet]({% post_url 2026-08-03-dice-for-a-trezor-ledger-or-any-wallet %}).
{: .prompt-info }

> Do this on the device itself, or on a computer that is switched off from the internet. **Never type your dice rolls into a website.** Not even a good one. If a page is open in your browser, anything else on that computer can potentially read it.
{: .prompt-danger }

## 🔒 Step 4: Keep the words, destroy the numbers

Your device now shows you 24 words. **Those words are your wallet.**

- **Write the words down** on paper or stamped metal. Store them somewhere safe and dry.
- **Do not** photograph them. Phone photos sync to the cloud.
- **Do not** type them into a computer, a password manager, or a notes app.
- **Keep the paper with your dice rolls, for now.** You'll want it for one last check in Step 5. Once that check passes, destroy it. The rolls are just as dangerous as the words. Anyone with them can recreate your wallet.

> Save the **words**, not the numbers. The numbers were scaffolding; the words are the wallet.
{: .prompt-tip }

## ✅ Step 5: Test it before you put money in

Do not skip this.

First, check the conversion. Enter your dice rolls into a *second* tool that works the same way (any of the ones listed in "What you need") and confirm it shows the exact same words. This is the check that catches a broken or dishonest device, and it's the reason you kept the rolls. Restoring the words alone can never catch that, because it only proves the words make *a* wallet, not that your dice made those words.

Then, test the backup. Wipe the device and restore it from the words you wrote down. Check that it shows the same first receiving address as before.

A backup you have never tested is not a backup. Find out now, with an empty wallet, rather than in five years with your savings in it.

Once both checks pass: destroy the rolls, send a small amount first, confirm it arrives, then move the rest.

## 🚫 Five things that will ruin this

1. **Typing your rolls into a website.** Any website. This is the big one.
2. **Using spaces or grouping** when you write the numbers down.
3. **Re-rolling results you don't like.** Take what the dice give you.
4. **Photographing the dice or the words.** Cloud backup will helpfully copy your wallet to someone else's computer.
5. **Keeping the dice rolls** after you're done. Keep the words instead.

## That's the whole thing

Buy dice. Roll 100 times. Write the numbers in one long line with no spaces. Type them into your wallet. Save the words, burn the numbers, test the backup.

Fifteen minutes, and the most important number in your Bitcoin setup is one you made yourself on a table, instead of one you had to take on faith.

---

*Want to know exactly why 100 rolls, what the wallet does with your numbers, and how to check its work with a hash you can verify yourself? That's all in the technical version: [How to Safely Roll Dice for Your Bitcoin Seed]({% post_url 2026-08-03-how-to-safely-roll-dice-for-your-bitcoin-seed %}).*
