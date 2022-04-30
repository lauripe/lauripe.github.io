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
I used [Yahoo Finance](https://finance.yahoo.com/quote/BTC-USD/) as a source to get answers on possible wins and losses. Yahoos data starts from 2014, so the early years of bitcoin aren't included. At the time of writing 2022-04-29 the course of BTC-USD was around 38 547.

### Hypothetical returns on investing 1000 $

__Best scenario__
- Buy at 2015-01-15 with price of 209,84 $
- Sell at 2021-11-09 with price of 66 971,83 $
- With 1000$ the profit would have been 37 8370 $

__Worst scenario__
- Buy at 2017-12-17 with price of 19 140,80 $
- Sell at 2018-12-16 with price of 3 252,84 $
- With 1000$ the profit would have been -830 $

{% gist 702085f16bf8d8652f398992a2f88b51 %}

---

## Finlands legislation on bitcoin

Bitcoin along with other virtual currencies is considered as a property, but not as a type of currency or security. As it's not seen as a mean of payment in tax legislation, all transactions are subject to capital income tax. This makes the usage of bitcoin as a currency very impractical, as paying anything with bitcoins makes the current value of transferred coins subject to capital income tax.

Due to the pseudonymous nature of bitcoin, all transactions must be reported for taxes by yourself - failing to do so might lead into criminal charges on [tax evasion fraud](https://www.hs.fi/kaupunki/art-2000008342550.html).

- [Finlex](https://www.finlex.fi/fi/laki/ajantasa/2019/20190572)
- [vero.fi](https://www.vero.fi/syventavat-vero-ohjeet/ohje-hakusivu/48411/virtuaalivaluuttojen-verotus2/)

---

## Blockchain
Blockchain is an immutable list of records assembled in blocks, and the blocks are linked to predeccessors using cryptography. Commonly each block contains a hash of all previous blocks in addition to the actual messages, so any modification in the closed blocks would invalidate the chain from that point onwards. 

It's possible for multiple blocks being simultaneously forked from a common parent, but only one can be eventually be accepted as a part of the blockchain. This involves a voting procedure, where the network finds mutual agreement on which block is accepted. Bitcoin uses a consensus mechaninsm called Proof-of-Work, where the voting is done by solving computationally demanding cryptographic challenges. The process of solving the puzzles is called mining, and the miners get coins and/or transaction fees as an incentive when a block is accepted in the chain. Alternative mechanism for Proof-of-Work is Proof-of-Stake, where holding a considerable amount of currency can be used a method of validation, as in Proof-of-Work this is done by spending a considerable amount of compute resources.

---

## AltCoins

Most famous successor of Bitcoin is [Ethereum](https://ethereum.org), which was published as a whitepaper by Vitalik Buterin at 2013. The first Ethers went on sale during 2014, and could be bought with bitcoin. Since then multiple altcoins have surfaced, which many are based on Ethereums smart contract -functionality. A notable trend among the altcoins are so called meme-coins such as [Dogecoin](https://dogecoin.com), which some have managed to reach a [considerable market capitalization](https://finance.yahoo.com/quote/DOGE-USD/) regardless of being launched as a silly joke.


---

## Links and References

- [Nakamoto, 2008 - Bitcoin: A Peer-to-Peer Electronic Cash System](https://bitcoin.org/bitcoin.pdf)
- [Felten et al 2015: Bitcoin and Cryptocurrency Technologies](https://www.coursera.org/learn/cryptocurrency/home/week/1)
- [bitcoin.org](https://bitcoin.org)
- [ethereum.org](https://ethereum.org)
- [Finlex](https://www.finlex.fi/fi/laki/ajantasa/2019/20190572)
- [vero.fi](https://www.vero.fi/syventavat-vero-ohjeet/ohje-hakusivu/48411/virtuaalivaluuttojen-verotus2/)
