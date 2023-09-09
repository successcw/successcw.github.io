---
layout: post
title: Arbitrum introduction
categories: Blockchain
tags: [Blockchain]
---

Arbitrum is the mainstream Layer2 solution in Optimistic Ethereum ecosystem. Its airdrop earlier this year created a sensation and it currently holds over 50% of the entire Layer2 market share.

## Working principle
<img src="{{site.baseurl}}/assets/img/arbitrum.png">
### Transactions
* Transactions are sequenced,compressed and updated to L1 every few minutes by centralized sequencer. Then transactions are confirmed without finalized by L1, this is so-called 'Optimistic'. We also gain low gas fees and high throughput from this. The 'Optimistic' is introduced by centralized sequencer, but in the world of web3, we need decentralization to archive 'consensus', which will be discussed in the upcoming section on Validators.
* Transactions are submitted to L1 delayed box, which is used for extracting ETH or Tokens through the bridge. This serves as a security mechanism to prevent malicious behavior by the sequencer.

### Validators
Anyone can run an validator(currently, arbitrum only allows whitelist members to run them). Their primary role is to operate and submit L2's chain state(RBlock) to L1, ensuring the consistency(consensus) of the L2 state. In cases where multiple validators have inconsistent states, Arbitrum contracts have corresponding conflict resoltuion mechanism. Here, a binary search is used off-chain to quickly locate the disputed portion, which is then resolved on-chain to ultimately settle the dispute. The party providing false information will face corresponding penalties(deduction of staked tokens). It takes one week for RBlocks to be finalized on L1.

## Public information
* [Transactions updated to L1](https://arbiscan.io/batches)
* [Validator list](https://docs.arbitrum.foundation/state-of-progressive-decentralization#allowlisted-validators)

## Conclusion
Currently, Arbitrum is leading the way in Layer2, both in terms of technical maturity and ecosystem. The team has also proposed plans for decentralizing the sequencer and removing the validator whitelist mechanism. We look forward to further development of the Arbitrum ecosystem.


