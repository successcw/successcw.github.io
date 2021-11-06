---
layout: post
title: What's NFT?
categories: Blockchain
tags: [Blockchain]
---

## NFT
None Fungible Token, an NFT is a unit of data stored on a digital ledger, called a blockchain, which can be sold and traded. The lack of interchangeablility(fungibility) distinguishes NFTs from other blockchain tokens, such as ETH, is fungible, 1 ETH has same value with another ETH. The NFT is none fungible as it is associated with a particular digital or physical asset(such as a file or a physical object) and a license to use the asset for a specified purpose. Copies of the original file are not restricted to the owner of the NFT, and can be copied and shared like any file.

## Popular NFTs
1. [Bored Ape Yacht Club](https://boredapeyachtclub.com/)  
The Bored Ape Yacht Club is a collection of 10,000 unique Bored Ape NFTs - unique digital collectibles living on the Ethereum blockchain.
Your Bored Ape doubles as your Yacht Club membership card, and grants access to members.

2. [Meebits](https://opensea.io/collection/meebits)   
The Meebits are 20,000 unique 3D voxel characters, created by a custom generative algorithm, then registered on the Ethereum blockchain. 

3. [CryptoKitties](https://www.cryptokitties.co/)  
World's first blockchain game. It makes the blockchain approachable for the everyday consumer and brings us but a small step close to widespread
adoption of cryptocurrencies and blockchain technologies.

## Value
Alike physical collectibles, the value of NFT depends on how much people would pay for it.^^  
In my opinion, functions behind the NFT are important, e.g, BAYC, NFT is used as membership card, only members can access some funny places. So the
value depends on:
* How funny the game or community is?
* How many people use it?
* How scarce is an special NFT?

## How to develop NFT?
Developing NFT includes many parts, I will take CrazyMinotaur - a small but complete NFT project for example and show it one by one.  
* Smart contract
* Storage of Metadata, IPFS here.
* Website, especially mint page
* Crypto Wallet, Metamask here.
* NFT market, OpenSea here.

### Smart contract
There are two standards for NFT. They both define interfaces, the only thing we need to do is implement them and add our own APIs. After deployed, you can check it on etherscan.io  

<img src="{{site.baseurl}}/assets/img/crazyminotaur_smartcontract.png">
#### ERC721
A standard interface for non-fungible tokens, also known as deeds.  
Every ERC721 compliant contract must implement the ERC721 and ERC165 interfaces

ERC165  
A standard method to publish and detect what interfaces a smart contract implements.

#### ERC1155
A standard interface for contracts that manage multiple token types. A single deployed contract may include any combination of fungible tokens, non-fungible tokens or other configurations (e.g. semi-fungible tokens).

### Storage of Metadata
Metadata is optional for ERC721 smart contract. This allow your smart contract to be interrogated for its name and for details about the assets which your NFTs represent.  
In detail, we need to implement interface 'tokenURI' which returns an URL that return a JSON blob of data with the metadata for your token. please refer to [metadata-standards](https://docs.opensea.io/docs/metadata-standards) for details.  
In CrazyMinotaur, I implement three NFTs, their metadata is stored on IPFS  
1. [Asterion](https://gateway.pinata.cloud/ipfs/QmeCZ4XEfdHWKuFp5JtpEYa6jpEJdQoFcSjVqTGztZTgcN)
2. [Erinyes](https://gateway.pinata.cloud/ipfs/QmfAnVfCVucy6ubawqRpX5txZTLCQBqnTF4fsQLEUAB1CA)
3. [Ferdinand](https://gateway.pinata.cloud/ipfs/QmU8JjDLMidjUBdsbxVK8UUjBbG9fV1msyUFeUNYLjUQo4)

### Website
After smart contract has been deployed on the blockchain, we need a website to interact with it and end user. It's time costly depends on how complicated your game or community is and how delicate UI is. Here in CrazyMinotaur, I just implement a simple mint page with metamask to create tokens.  
It's based on [Next.js](https://nextjs.org/).  

<img src="{{site.baseurl}}/assets/img/crazyminotaur_website.png">

### Crypto Wallet
There are many crypto wallets. Metamask is what we used here. It's a chrome extension with 'window' object embedded, we can use it and web3.js to interact with blockchain. It works like a gateway connect off-chain and on-chain.  
It's not enough if we want interact with our smart contact. We also need smart contract's ABI, as I compiled it with truffle, it can be found in './build/contracts/' folder, the json file is what we need. Please refer to pages/index.js for details.

### NFT market
Opensea is one of biggest NFT market that you can create and trade your NFTs. It's compliant with ERC721 and ERC1155. which means, after smart contract is deployed and NFTs are minted. you can find them on opensea.
Click your account icon -> My Collections, see, three crazy minotaurs are all there and attributes are listed too, now you can trade it, amazing!

<img src="{{site.baseurl}}/assets/img/crazyminotaur_opensea_1.png">

<img src="{{site.baseurl}}/assets/img/Ferdinand.png">

## Reference

1. [ERC165][1]
2. [ERC721][2]
3. [ERC1155][3]
4. [call vs transaction][4]
5. [solidity ABI][5]
6. [BoringBananasCompany][6]
7. [NodeJs][7]
8. [OpenSea Doc][8]
9. [Image dataset generator][9]

    [1]: https://eips.ethereum.org/EIPS/eip-165
    [2]: https://eips.ethereum.org/EIPS/eip-721
    [3]: https://eips.ethereum.org/EIPS/eip-1155
    [4]: https://ethereum.stackexchange.com/questions/765/what-is-the-difference-between-a-transaction-and-a-call
    [5]: https://docs.soliditylang.org/en/latest/abi-spec.html
    [6]: https://github.com/vortextemporum/BoringBananasCompany
    [7]: https://github.com/nodesource/distributions/blob/master/README.md
    [8]: https://docs.opensea.io/docs
    [9]: https://github.com/albertsl/image-dataset-generator

