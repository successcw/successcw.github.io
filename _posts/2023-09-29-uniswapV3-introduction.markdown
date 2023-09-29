---
layout: post
title: UniswapV3 introduction
categories: Blockchain
tags: [Blockchain]
---

We have introduced V2 in last article, let's talk about V3.

## What is UniswapV3?
Let's recap V2, it is represented by a simple formula:  
x * y = k  
x is TokenA's quantity, Y is TokenB's quantity, the product of k stays same after tradings.
It is straightforward to implement, but it also brings about the issue of low capital efficency. Therefore, Uniswap introduced the V3 version with concentrated liquidity.

## Concentrated Liquidity
Liquidity Provider(LP) can specify a range of price(a,b) when providing liquidity, meaning that liquidity is only provided when the price is between a and b. This is when fee income is generated.
<img src="{{site.baseurl}}/assets/img/univ3_1.png">
As shown in above curve, V3 and V2 are same except specifing the price range.
LP provides liquidity at point C, the green curve is same as in V2, called virtual reserves. we can calculate the quantity of x and y, and by subtracting them from the virtual reserves, we can determine Xreal and Yreal when price goes to a and b. These two values represent the actual liquidity that liquidity providers need to provide.
<img src="{{site.baseurl}}/assets/img/univ3_2.png">
We know that the price range is 0 to infinity in V2, As the price of X increases, the quanity of X decreases, the quanity of Y increases.
V3 is same except the quanity of X decreases to 0 at point b. In other words, at point b, all fo the X held by LP have been traded to Y. The opposite happens at point a. The process is represented by the red curve.
Let's take an example to show the difference of V2 and V3, and why V3 can increase capital efficency.
<img src="{{site.baseurl}}/assets/img/univ3_3.png">
This is a table from uniswap.
current price = 1000  
a = 900  
b = 1100  
L = 40  
virtual liquidity  
  Token0 = 1.265 Token1 = 1264.911  
real liquidity  
  Token0 = 0.058 Token1 = 64.911  
Capital efficiency has improved by a factor of 20.4. If you further narrow down the range of a and b, V3 can potentially achieve an efficiency improvement of up to 4000 times.

## Risk
Earning and risk go hand in hand, while V3 can greatly enhance efficiency, it also brings about an equivalent level of risk.
<img src="{{site.baseurl}}/assets/img/univ3_4.png">
a = 900  
b = 1100  
c = 1000  
V3 is steeper than V2, when the price is out of range a and b, there is not only no fee generated but in an uptrend, LPs go entirely unhedged(as seen in the right half of the red curve), and in a downtrend, they bear the full brunt(as show in the left half of the red curve). This results in greater impermanent loss.

## Conclusion
V3 brings higher capital efficiency but also introduces corresponding risks. Compared to V2, LPs have a wider array of choices(strategies) available to them.

## Reference
* [UniswapV3 whitepaper](https://uniswap.org/whitepaper-v3.pdf)
* [Uniswap V3 math](http://atiselsts.github.io/pdfs/uniswap-v3-liquidity-math.pdf)
* [Defi-lab](https://defi-lab.xyz/uniswapv3simulator)