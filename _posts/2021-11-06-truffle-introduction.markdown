---
layout: post
title: Truffle introduction
categories: Blockchain
tags: [Blockchain]
---

## Install

### truffle
* npm install truffle -g

### ganache
* sudo npm install ganache-cli --global

## Init project
* mkdir your_project_name
* cd your_project_name
* truffle init

## Add contract file
### Add Faucet.sol in contracts/
```solidity
// SPDX-License-Identifier: GPL-3.0


pragma solidity >=0.7.0 <0.9.0;


/**
* @title Faucet
* @dev receive and send ether
*/
contract Faucet {

    address payable owner;

    constructor() {
        owner = payable(msg.sender);
    }

    modifier onlyOwner {
        require(msg.sender == owner, "Only the contract owner can call this function");
        _;
    }

    function destroy() public onlyOwner {
        selfdestruct(owner);
    }
    receive () external payable {}


    function withdraw(uint withdraw_amount) public {
        require(withdraw_amount <= 0.1 ether);
        require(address(this).balance >= withdraw_amount, "Insufficient balance in faucet for withdrawal request");
        payable(msg.sender).transfer(withdraw_amount);
    }
}

```
### Add 2_deploy_contracts.js in migrations/
```javascript
var Faucet = artifacts.require("./Faucet.sol");


module.exports = function(deployer) {
        deployer.deploy(Faucet);
}

```

## Compile
open truffle-config.js, change following:
```Json
compilers: {
  solc: {
      version: "pragma",
```
truffle compile

## Migration
### Local blockchain:ganache
1. type "ganache-cli" to start
2. open truffle-config.js, replace the content with the following:
```Json
module.exports = {
  networks: {
    development: {
      host: "127.0.0.1",
      port: 8545,
      network_id: "*"
    }
  }
};
```

3. truffle migrate

### ethereum testnet rinkeby
1. type "geth console --rinkeby --rpc --allow-insecure-unlock" to start ethereum testnet rinkeby
2. create account in rinkeby, need do only once
    > personal.newAccount()
3. show account
    > eth.accounts  
    "0x7a3244e8d5a4d0c087992633ddc131270616c76a"
4. send eth to the account just created using metamask
5. show account's balance
    > web3.fromWei(eth.getBalance(eth.coinbase),"ether")
6. unlock account
    > personal.unlockAccount("0x7A3244e8d5A4d0C087992633Ddc131270616c76a")
7. open truffle-config.js, add rinkeby config as following:
```Javascript
    rinkeby: {
        host: "localhost",
        port: 8545,
        from: "0x7a3244e8d5a4d0c087992633ddc131270616c76a",
        network_id: 4,
        gas: 5500000,
        confirmations: 2,
        timeoutBlocks: 200
    },
```
8. truffle migrate --network rinkeby  
Note: add '--reset' if show 'Network up to date'  
Result(contract:BoringBananasCompany)

```Bash
Compiling your contracts...
===========================
> Everything is up to date, there is nothing to compile.



Migrations dry-run (simulation)
===============================
> Network name:    'rinkeby-fork'
> Network id:      4
> Block gas limit: 29970705 (0x1c95111)


1_initial_migration.js
======================

   Deploying 'Migrations'
   ----------------------
   > block number:        9497067
   > block timestamp:     1634715589
   > account:             0x7A3244e8d5A4d0C087992633Ddc131270616c76a
   > balance:             0.999532316
   > gas used:            233842 (0x39172)
   > gas price:           2 gwei
   > value sent:          0 ETH
   > total cost:          0.000467684 ETH

   -------------------------------------
   > Total cost:         0.000467684 ETH


2_deploy_contracts.js
=====================

   Deploying 'BoringBananasCo'
   ---------------------------
   > block number:        9497069
   > block timestamp:     1634715590
   > account:             0x7A3244e8d5A4d0C087992633Ddc131270616c76a
   > balance:             0.990106188
   > gas used:            4685551 (0x477eef)
   > gas price:           2 gwei
   > value sent:          0 ETH
   > total cost:          0.009371102 ETH

   -------------------------------------
   > Total cost:         0.009371102 ETH


Summary
=======
> Total deployments:   2
> Final cost:          0.009838786 ETH





Starting migrations...
======================
> Network name:    'rinkeby'
> Network id:      4
> Block gas limit: 29999972 (0x1c9c364)


1_initial_migration.js
======================

   Deploying 'Migrations'
   ----------------------
   > transaction hash:    0x3a6f95c9da80512573f8a548195942768586ca5b52dad15ac52b924c6b1eec96
   > Blocks: 0            Seconds: 12
   > contract address:    0x2292637521f0D1474eF4330506E1858Bfc2034C6
   > block number:        9497067
   > block timestamp:     1634715604
   > account:             0x7A3244e8d5A4d0C087992633Ddc131270616c76a
   > balance:             0.99937464499749858
   > gas used:            250142 (0x3d11e)
   > gas price:           2.50000001 gwei
   > value sent:          0 ETH
   > total cost:          0.00062535500250142 ETH

   Pausing for 2 confirmations...
   ------------------------------
   > confirmation number: 1 (block: 9497068)
   > confirmation number: 2 (block: 9497069)

   > Saving migration to chain.
   > Saving artifacts
   -------------------------------------
   > Total cost:     0.00062535500250142 ETH


2_deploy_contracts.js
=====================

   Deploying 'BoringBananasCo'
   ---------------------------
   > transaction hash:    0x68dae995de3afe2c46b44fcd2875162d2c9d99f0ea1e14794d7cfef2c7b6527e
   > Blocks: 0            Seconds: 12
   > contract address:    0x4654B5bF6D0F1111E5C5a99287b61A261fbDc265
   > block number:        9497071
   > block timestamp:     1634715664
   > account:             0x7A3244e8d5A4d0C087992633Ddc131270616c76a
   > balance:             0.98709848494839394
   > gas used:            4864551 (0x4a3a27)
   > gas price:           2.50000001 gwei
   > value sent:          0 ETH
   > total cost:          0.01216137754864551 ETH

   Pausing for 2 confirmations...
   ------------------------------
   > confirmation number: 1 (block: 9497072)
   > confirmation number: 2 (block: 9497073)

   > Saving migration to chain.
   > Saving artifacts
   -------------------------------------
   > Total cost:     0.01216137754864551 ETH


Summary
=======
> Total deployments:   2
> Final cost:          0.01278673255114693 ETH
```

## Interact with the contract
* truffle console
* let instance = await Faucet.deployed()
* let withdraw_amount = web3.utils.toWei("0.01", "ether")
* instance.withdraw(withdraw_amount)

### Transaction
We can pass an object as the last parameter to functions that lets us edit specific details about the transaction.
* from
* to
* gas
* gasPrice
* value
* data
* nonce
You can check the example in test part, I set gasPrice to 0 in order to easily calculate the diff of the balance.

### Result
When you make a transaction, you're given a result object that gives you a wealth of information about the transaction.
e.g.
result = await instance.withdraw(...)
console.log(result.receipt)
* result.tx(string) - Transaction hash
* result.logs(array) - Decoded events(logs)
* result.receipt(object) - Transaction receipt (includes the amount of gas used)

## Test
* add faucet.js in test/

```javascript
const Faucet = artifacts.require("Faucet")

contract("Faucet", async accounts => {
    it("Faucet should be payable", async () => {
        const instance = await Faucet.deployed();
        balance = await web3.eth.getBalance(instance.address);
        assert.equal(balance, 0);
        await web3.eth.sendTransaction({
            from: accounts[0],
            to: instance.address,
            value: web3.utils.toWei("0.02", "ether")
        });
        balance = await web3.eth.getBalance(instance.address);
        assert.equal(balance, web3.utils.toWei("0.02", "ether"));
    });

    it("Faucet should withdraw correctly", async () => {
        balance_before = await web3.eth.getBalance(accounts[0]);
        const instance = await Faucet.deployed();
        result = await instance.withdraw(web3.utils.toWei("0.01", "ether"),
            {from: accounts[0], gasPrice: 0});
        //console.log(result.receipt)
        balance_after = await web3.eth.getBalance(accounts[0]);
        balance = balance_after - balance_before;
        //console.log(balance)
        assert.equal(balance, web3.utils.toWei("0.01", "ether"));
    });
});

```
* add package.json in root folder

```Json
{
  "dependencies": {
    "mocha": "^9.1.2"
  },
  "scripts": {
    "test": "mocha"
  }
}

```
* truffle test
(use "truffle test --show-events" to show contracts events)

```Bash
Using network 'development'.


Compiling your contracts...
===========================
> Everything is up to date, there is nothing to compile.



  Contract: Faucet
    ✓ Faucet should be payable (47ms)
    ✓ Faucet should withdraw correctly (69ms)


  2 passing (145ms)

```
We can use "try..catch" to test contract's "require" condition, here is an example:
```javascript
    it("CrazyMinotaur can only mint 1 Minotaur at a time", async () => {
        const instance = await CrazyMinotaur.deployed();
        try {
            await instance.mint(2, {
                from: accounts[0],
                gasPrice: 0,
                value: web3.utils.toWei("0.01", "ether")
            });
        } catch (error) {
            //console.log(error.reason);
            assert.equal(error.reason, "can only mint 1 Minatour at a time");
        }
    });
```
## ABI
Application Binary Interface is used by web3 or other contract to interact with our contract.  
It's generated by 'truffle compile' at build/contract folder.
