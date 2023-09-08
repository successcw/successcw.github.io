---
layout: post
title: Ethereum layer2 status
categories: Blockchain
tags: [Blockchain]
---

In this article, We will outline the current status of two major ethereum layer2 rollup solutions, Optimistic and ZK(Zero-Knowledge) Rollups.

## Three stage of Layer2
The current development of Ethereum Layer2 is in full swing. Ethereum's founder, Vitalik Buterin has defined three stages of layer2. This is intended to help everyone identify layer2 and also to provide direction for various layer2 solutions.

Stage0: entirely dependent on human governance
* all transactions should be updated to L1(Ethereum), user can directly withdraw asset from L1 without operator.
* update L1 status through mulsig(without optimistic and zk)

Stage1: partially dependent on human governance
* must update L1 through optimistic or zk
* can update L1 through mulsig only when system is buggy.

Stage2: code governance
* further restricting the scope of mulsig: unrecoveralbe bugs.

## Current stage of major layer2 rollups
<img src="{{site.baseurl}}/assets/img/current_stage_layer2.png">

from: l2beat  
As shown in diagram above, arbitrum is in stage1, others are in stage0.

## Conclusion
The approach to increasing throughput for decentralized platforms like Ethereum is through localized centralization solutions.
In summary, centralized sequencer executes transactions instead of L1 and commits them to L1, validators prove the status of L2, mulsign mechanism and the status of L1 protocol are governed by DAO, all of these protect L2 rollups safe.

## Reference

1. [Layer2 stages][1]
2. [l2beat][2]


    [1]: https://ethereum-magicians.org/t/proposed-milestones-for-rollups-taking-off-training-wheels/11571
    [2]: https://l2beat.com/scaling/summary

