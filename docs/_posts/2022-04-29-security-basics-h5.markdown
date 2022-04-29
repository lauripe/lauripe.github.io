---
layout: post
title:  "BitCoin"
date:   2022-04-29 00:00:00 +0300
categories: ICT4HM103
---
## Bitcoin 

### The idea behind peer-to-peer electronic currency

Bitcoin originates from a [paper released at 2008](https://bitcoin.org/bitcoin.pdf) by Satoshi Nakamoto - allegedly a pseudonym, as the actual person or collective behind the name has never been indistibutably identified. The aim of the proposed approach was to establish non-reversible transactions, which allow two parties to handle trasactions directly between each other without a need of an external arbitrator, namely a bank, to provide trust mechanism against transaction reversal, double-spending and other cheating mechanisms otherwise possible in the digital domain. Effectively the system exchanges the central authority of trust to a distributed system of cryptographic proof.

### Transactions

Transactions in Bitcoin form a chain of digital signatures, and the process is simplified in the Bitcoin paper as following:
- Owner gives the coin to a recipient by signing the hash of previous transaction with personal private key along with the recipients public key
- The recipient can verify the signatures in the chain to get proof of the coins ownership

Multiple [transactions](https://en.bitcoin.it/wiki/Transaction) are collected into a block, and eventually the tree of confirmed blocks form the actual [blockchain](https://en.bitcoin.it/wiki/Block_chain).

The transaction model itself does not protect against double spending, as you could sing the same transaction with different recipients public key by any number of times. As there's no centralized authority deciding on which of these chains is correct, additional consensus mechanism is required to guarantee that every participant in the network agree on the same version of the history.

### Shared immutable ledger

While it's obvious that the first transaction made on top of a previous one is the correct one, even if subsequent forks from the same ancestor would appear later, having a common wall clock in a distributed peer-to-peer network to timestamp the transactions requires some ingenuity. BitCoin uses a [Hashcash](https://en.bitcoin.it/wiki/Hashcash) based method of [proof-of-work](https://en.bitcoin.it/wiki/Proof_of_work) to validate the blocks. The idea is that validating the block requires solving a cryptographic puzzle with a preset difficulty, and whoever succeeds to solve the puzzle first is entitled to start a new block that comes with a incentive fee to keep as a reward. Solving the challenges required in validation is called mining, and it requires a considerable amount of CPU/GPU/ASIC resources, electricity and cooling. The difficulty of generated challenges is constantly adjusted so that regardless of advances in compute power, the network generates a new block in a steadyish rate of around 6 per hour.

As also the hashes of proof-of-work are included in the blocks and hashes of subsequent blocks include the hashes of previous blocks, trying to change a transaction history would require recomputing the challenges for each transaction happened since the tampered block. The network always considers the longest chain being the valid one, it would require investing more work than the rest of the network combined to catch up with the longest chain. This also works as a protective measure, as [holding over 50% of the mining capacity](https://en.bitcoin.it/wiki/Majority_attack) in the network would provide such a considerable amount of incentives, it would be hard to benefit more from a double-spend fraud than from simply supporting the network.

- [Nakamoto 2008: Bitcoin: A Peer-to-Peer Electronic Cash System.](https://bitcoin.org/bitcoin.pdf)
- [Felten et al 2015: Bitcoin and Cryptocurrency Technologies](https://www.coursera.org/learn/cryptocurrency/home/week/1)

---

## Historical BTC course
I used [Yahoo Finance](https://finance.yahoo.com/quote/BTC-USD/) as a source to get answers on possible wins and losses.

- At the time of writing 2022-04-29 the course of BTC-USD was around 38 547
- Lowest in Yahoos recorded history was on 2015-01-14 with the value of 178
- Highest on 2021-11-08 with the value of 67 566

The difference between 2015-01-14 and 2021-11-08 was 379.37%, so there has been an opportunity for decent profits. If the history would span longer to the past the ratio would be immensily higher.
On the other hand, if one would have bought at the top of 2021 and sold at the following dip on January 2022, half of the investment would have vanished into air.

{% gist 0f9377476ffba23361e3b7c94c683531 %}

---

## Finlands legislation on bitcoin

---

## Other uses of Blockchain

---

## AltCoins

---

## Links and References

- [Nakamoto, 2008 - Bitcoin: A Peer-to-Peer Electronic Cash System](https://bitcoin.org/bitcoin.pdf)
- [bitcoin.org](https://bitcoin.org)