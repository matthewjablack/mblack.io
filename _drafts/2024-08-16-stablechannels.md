---
title: Why Stablechannels won't scale
description: >-
  Test
date: 2024-08-14
categories: [Scaling]
tags: [Stablechannels]
image:
  path: /zjjp0ON.png
  lqip: data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAAoAAAAGCAYAAAD68A/GAAAAAklEQVR4AewaftIAAADVSURBVAXBTU+CcADA4R/jH0oYICQVWpjz1KUuXVutL9YXdF1aWx6suQnpEAYiaLz1PNLT2Go7As56HQQV06mDn0rM9wajx2eWX3PqBsTryx3xJqQtC2xTQ9NPoG+j2g/UpwZBnTPQDcTVzSWON8R1uqhNDJpB8aewqCxms3fuJya31wNkz1Te+mqLN+rR0S2yUuFiPKSULbplBJLg43OFSPyYbZuzbHa4E5fVImLj77A9qA4N6yAhi2JEeqz42cr87lPOQwlZCPJkDd8ZcXokCHOK7MA/ILJX3Gbt5U8AAAAASUVORK5CYII=
  alt: Stablechannels on the lightning network
---

## 🚧 Why Stablechannels Won't Scale

Stablechannels on the Bitcoin Lightning Network have been proposed as a way to provide stability in a volatile market. However, there are significant challenges that make it unlikely for stablechannels to scale effectively and compete with stablecoins.

### 💧 Liquidity Challenges

To have stability providers, you need demand. There’s demand, but not from folks who want to run a lightning node. The fact that a user needs to run dedicated hardware (rpi / laptop) to participate is a non-starter IMO. Most people won't do that. They'll just use USDT.

### ⚠️ Free Option Problem

Stablechannels require the liveliness of both parties. This means that both parties need to be online and responsive to maintain the channel. If one party goes offline, the channel could be at risk, leading to potential losses or forced closures.

### 💸 High Cost of Capital

The other challenge will be attracting market makers (MMs) to provide hedging services. They'll be long, so they'll need to do a delta hedge. However, they won't have access to cross-hedging here, which will affect capital efficiency. So supply will be very difficult to build. This means the cost of capital will be much higher, making it expensive for users to utilize stablechannels.

### 🏦 Centralized Alternatives

If there's not enough supply, they'll end up using centralized finance (CeFi). This defeats the purpose of having a decentralized, permissionless system. Users might prefer stablecoins, which are easier to use and don't require running a node or dealing with the complexities of the Lightning Network.

### 📝 Conclusion

In conclusion, while the idea of stablechannels is intriguing, the practical challenges make it unlikely to scale effectively. The difficulty in finding liquidity, the free option problem, the requirement for both parties to be online, and the high cost of capital are significant barriers. As a result, stablechannels are unlikely to compete with stablecoins, which offer a more straightforward and user-friendly solution.


## Why stablechannels won't scale

To have stability providers, you need demand. There’s demand, but not from folks who want to run a lightning node

The fact that a user needs to run dedicated hardware (rpi / laptop) to participate is a non-starter IMO

Most people won't do that. They'll just use USDT

---

However that means all the risk for hedging is on the mint. So capital efficiency will be paramount (in terms of channel, so they'll likely using direct channels). If there's not enough supply, they'll end up using CeFi.

The other challenge will be attracting MMs to provide hedging services. They'll be long, so they'll need to do a delta hedge. However, they won't have access to cross-hedging here, which will affect capital efficiency. So supply will be very difficult to build.

Yep, but will likely be quite expensive for users to utilize, since it'll need to account for the risk the MM takes on for not having cross-hedging

It'll be like a stable note which value decays over time

I guess that's the cost for custodial but permissionless stability
