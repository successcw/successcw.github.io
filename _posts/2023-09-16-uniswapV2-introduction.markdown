---
layout: post
title: UniswapV2 introduction
categories: Blockchain
tags: [Blockchain]
---

Uniswap is the largest decentralized exchange in the crypto space and a pioneering force in the DeFi.

## What is Uniswap?
Uniswap is a decentralized exchange that operates on the ethereum blockchain. Unlike traditional centralized exchanges which rely on centralized order book to facilitate trading, uniswap uses an Automated Market Maker(AMM) approach to enable transactions. It is referred to as a decentralized exchange because its core functionality is governed by a series of immutable smart contracts, allowing anyone to interact with them.
Generally, Uniswap consists of four components:
* Uniswap Lab
The company which developed the Uniswap protocol, along with the web interface.
* Uniswap Protocol
A series of smart contract on the ethereum blockchain.
* Uniswap interface
A web interface that allow for easy interaction with the Uniswap protocol.
* Uniswap Governance
A governance system for governing the Uniswap Protocol, enabled by the UNI token.

It's worth mentioning Uniswap's innovation: AMM, represented by a simple formula
x * y = k
x is TokenA's quantity. Y is TokenB's quantity. The product of k stays same after tradings.
TokenA and TokenB constitute a trading part, provided by Liqudity provider.
Uniswap consists of two roles: traders and Liqudity providers, they are counterparties to each other.

## Impermanent loss and LP returns
Unlike centralized exchange, where becoming a LP typically requires a high barrier to entry, in Uniswap, anyone can become an LP by depositing any two arbitrary assets.
Impermanent loss is related to LP, it means that the value of assets deposited by LP is lower when withdrawn compared to when they were deposited. The larger this difference, the greater the impermanent loss. Impermanent loss is unavoidable according to AMM, so why would anyone choose to become an LP and accept the loss? This is because Uniswap charges fees to traders and compensates LP.
Let's reason about impermanent loss and LP returns.
1. Using a and b to represent quantity of TokenA and TokenB.
a * b = k
2. Using Pa and Pb to represent price of TokenA and TokenB.
Pa = b/a
Pb = a/b
3. Through 1 and 2, we can derive the relationship between the quantity of tokens and their prices
a = sqrt(k/Pa)
b = sqrt(k*Pa)
4. Calculate impermanent loss
To simplify the calculations, let's make a few assumptions here.
TokenA: ETH
TokenB: USD
1eth = 1000USD
The user has 2000 USD, let's compare three scenarios:
* Holding 2000 USD, cash postion
* Holding 1ETH and 1000 USD, half-cash position
* LP: 1 ETH and 1000 USD
<img src="{{site.baseurl}}/assets/img/univ2_1.png">
Note: ignore fees
The blue curve represents the LP curve, and the black curve represents the half-cash postion. As shown in the chart,the value of LP is same as half-cash postion's only when initial price point(1000), below at all other times. This difference represents impermanent loss.
5. Calculate LP returns
LP returns = a * Pa + b * Pb = sqrt(k/Pa) * Pa + sqrt(K*Pa)*Pb
The formula can be represented simply as bellow as TokenB's price is USD = 1
LP returns = sqrt(k/Pa) * Pa + sqrt(K*Pa)
<img src="{{site.baseurl}}/assets/img/univ2_2.png">
Let's add fee, assume fee is 10% of initial assests, r = 10%
LP returns with fee = LP returns + r*k*2
<img src="{{site.baseurl}}/assets/img/univ2_3.png">

As indicated by the red cure, after adding fee income, LP returns exceed the half-cash position within a certain range. This explains why some people choose to become LPs.
Let's check the details of impermanent loss.
I = (LP returns - half cash position)/half cash position
Substituting into the formula above, we can derive:
<img src="{{site.baseurl}}/assets/img/univ2_4.png">
This formula can be simply represented by(From a graphical perspective, it appears that the x-axis has been scaled down by a factor of k. It can also be understood that in the first equation, 'x' represents the price of TokenA, while in the second equation, 'x' represents the relative price ratio of TokenA compared to its initial price, which is k)
<img src="{{site.baseurl}}/assets/img/univ2_5.png">
<img src="{{site.baseurl}}/assets/img/univ2_6.png">
As shown in above curve, when x = 1, y is 0, it means impermanent loss is 0 when price is same with initial's. Impermanent loss still exists regardless of whether x increases or decreases.

* 1.25x price change results in a 0.6% loss
* 1.50x price change results in a 2.0% loss
* 1.75x price change results in a 3.8% loss
* 2x price change results in a 5.7% loss
* 3x price change results in a 13.4% loss
* 4x price change results in a 20.0% loss
* 5x price change results in a 25.5% loss

## Conclusion
Impermanent loss exists compareing to half-cash position, it can be compensated by trading fee. Furthermore, investment returns also need to be calculated in detail in relation to U-denominated assets.

## Reference
* [Uniswap](https://docs.uniswap.org/concepts/overview)
* [Pintail](https://pintail.medium.com/uniswap-a-good-deal-for-liquidity-providers-104c0b6816f2)
* [多个维度看世界](https://www.youtube.com/watch?v=0nrvwQRuruw&t=264s)
* [UNISWAP-V2 CONTRACT WALK-THROUGH](https://ethereum.org/en/developers/tutorials/uniswap-v2-annotated-code/#add-liquidity-flow)
* [impermanent-loss-calculator](https://dailydefi.org/tools/impermanent-loss-calculator/)
* [Uniswap V2 visualization](https://www.desmos.com/calculator/j8eppi5vvu?lang=zh-CN)