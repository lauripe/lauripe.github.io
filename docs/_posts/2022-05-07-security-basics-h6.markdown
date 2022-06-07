---
layout: post
title:  "Can of Worms"
date:   2022-05-07 00:00:00 +0300
categories: ICT4HM103
---
## Decentralization and distributed consensus

When there's no central authority to govern decisions, there must be another mechanism for finding agreement in the network. Without consensus nothing could be held as a fact, as every version of the truth would be as good as any other.

Aspects to consider with decentralized currency
- Who maintains the ledger
- Who's the authority on which transactions are valid
- Who's responsoble of creation of coins
- Who's responsoble of rules of system change
- What determines exchange value


#### Key challenge

How to obtain consensus in distributed environment is a classic computer science problem. The issue is apparent when designing reliability in distributes systems.

Challenges:
- Nodes might crash
- Malicious nodes
- Network problems
    - Not all nodes all connected
    - Faults
    - Latency

The challenges combined with peer-to-peer nature of networking lead into realization, that there's no way to achieve a reliable "wall clock" keeping notion of the global time. 


#### Consensus in Bitcoin

Voting for a correct branch in the blockchain is done by extending the chain
- Nodes accept a new block by adding a new block after it
- Rejection is dony by skipping the proposed block and adding on top of the previous instead
- As all blocks contain the hash of it's predeceeding block, it's easy to track the entire chain regardless of its distributed nature
- Honest nodes will always extend the longest valid branch

This however still leaves an opportunity for double spend attack:
- A pays B the amount of 1
    - Transaction gets added after block X to block Y
- A pays C the amount of 1
    - Transaction gets added after block X to block Z
- The same parent is now forked into two blocks containing a transaction referring the same parent transaction
    - The same coin is paid twice, once to B and once to C

As only one block can be eventually placed in the blockchain, the recipient should wait for sufficient amount of time (=the amount of confirmed blocks) to make sure that the transaction is included in the consensus-chain. 

A common heuristic in Bitcoin is to wait for 6 confirmations before accepting the transaction to be actually received.

### Incentives

The point of incentives is to make it worthwhile for the miners to keep validating the blocks, and also to function as a reward for nodes behaving honestly.

Incentives come in two flavors:
- Coin creation 
    - Creator of a new block gets to include a special coin-creation transaction with freely assignable recipient
- Transaction fee
    - Any creator of a transaction can assign the output being less than input value, the difference is given to the miner as a "tip"

As there's only finite number of bitcoins, and the rate of finding new coins is constantly slower, the importance of transaction fees gets higher all the time.


### Proof of work

A mechanism to enable voting for the blockchain-fork to be continued, in proportion to a resource that cannot (easily) be monopolized
- By computing power: proof-of-work
- By ownership: proof-of-stake


In bitcoin the proof of work is effectively a hash-puzzle
- Solve the nonce from: ```hash(nonce || prev_hash || tx || .. || tx)```
    - where output-hash is smaller than the given target
- Can be solved only by guessing
- Nodes compete on solving the puzzle first (to get the incentive)
- Nodes re-calculate the difficulty automatically every 2 weeks
- Target is to keep difficulty such, that new block is created every ~10 minutes

Attack against voting is infeasible as long as majority of miners (by hash power) follow the protocol.

The validation of the solved puzzle is easy, as the guessed nonce is published along with the block. Anyone can then verify that the output of the hash calculated with the nonce is indeed smaller than the target value given: ```hash(nonce || prev_hash || tx || .. || tx) < target```

([Felten et. al. 2015](https://www.coursera.org/learn/cryptocurrency/lecture/XV5nQ/incentives-and-proof-of-work))

---

## Can of Worms
I tried running whatever was first in the list of "Malicious activity" in Finland.
In this case it was a 32bit Windows executable dubbed aaaa.exe, and tagged with trojan, rat and asyncrat labels. 

The VM booted to a desktop with a login window to something called Rosvo.
![Rosvo 1](/assets/img/rosvo1.png)

On the process tree shown in the any.run I could see that there was a parent process aaaa.exe which had launched RosvoBuilder.exe labeled as suspicios and AsyncClient.exe labeled malicious. The malicious had also a tag classifying it as [asyncrat](https://malpedia.caad.fkie.fraunhofer.de/details/win.asyncrat), a remote adminstration tool that can be used in addition to adminstration also for malicious purposes.

![Rosvo 2](/assets/img/rosvo2.png)

Eventually a chat-window popped up and I got a change to briefly chat with a friendly stranger. Actually this was my second run, as the previous ran out of time, and the strager greeted me like an old friend.

![Rosvo 3](/assets/img/rosvo3.png)

Unfortunately I was already running late with a load of other deadlines and the any.run tool appeared to be deep enough to play hours with, so I decided to leave this to a brief scratch on the surface.


([Any.Run](https://app.any.run/tasks/fbece9c1-6cd0-43f2-88fe-0448bbb4cee6))

---

## Reference Implementation

Out of curiosity, I went to see what's the most highest scored question in entire Stackoverflow and landed into a one regarding better function performance when using a sorted array as compared to unsorted.

__Why is processing a sorted array faster than processing an unsorted array?__

The question has also a small C++ snippet included, and the poster was wondering how come the performance is so much higher with sorted input, even though the function itself is not apparently relient on data order.

The poster had also tried the same functionality with Java with similar, albeit not as dramatic results.

__Because of Branch Misprediction__

The highest rated answers explains the answer in laymans terms using a train and a railroad junction -analogy: if the junction operator doesn't know which way the train is going, the train has to stop and tell him which direction to switch.

The answer also includes a technical part describing processor branch predictor functionality, which in short tries to optimize the CPU:s pipeline processing by guessing what happens next. The response describes modern processors having such long and complicated pipelines, that the price of warm-up and cool-down would be unacceptable in comparison of making few wrong guesses regarding the future state instead. While the branch predictors perform generally well, they are rather powerless on arbitary input without any regonizable pattern.

Also as the function has a if - greater than - condition preceding additional calculation, a sorted input provides the branch predictor the opportunity to skip also the additional calculation until the predicate is met.

([Stackoverflow: why-is-processing-a-sorted-array-faster-than-processing-an-unsorted-array](https://stackoverflow.com/questions/11227809/why-is-processing-a-sorted-array-faster-than-processing-an-unsorted-array))

---

## Cryptopals Challenge 1

{% gist 960a4a70f07db50daf47b6d58238567e %}

[GitHub: lauripe](https://github.com/lauripe/security-basics/blob/main/cryptopals/basics.ipynb)

---
## Links and References
- [Felten et al 2015: Bitcoin and Cryptocurrency Technologies](https://www.coursera.org/learn/cryptocurrency/lecture/XV5nQ/incentives-and-proof-of-work)
- [Stackoverflow](https://stackoverflow.com/questions/11227809/why-is-processing-a-sorted-array-faster-than-processing-an-unsorted-array)
- [Any.Run](https://app.any.run)
- [Cryptopals Challenge](https://cryptopals.com/sets/1)

Assignment: [h6](https://terokarvinen.com/2021/trust-to-blockchain-2022/)