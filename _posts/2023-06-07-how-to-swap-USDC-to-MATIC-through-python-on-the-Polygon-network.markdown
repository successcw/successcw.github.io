---
layout: post
title: How to swap USDC to MATIC through Python on the Polygon network
categories: Blockchain
tags: [Blockchain]
---

In this article, We will outline a systematic approach to swapping USDC to MATIC using python on the Polygon network, along with a comprehensive analysis of the process.

## Quickswap
Quickswap is a decentralized exchange native to Polygon that enables permissionless trading and liquidity provision(to earn trading fees). Quickswap V3 is derived from the renowned Uniswap V3.
Therefore, we can leverage Quickswap V3 to implement the desired functionality.

## Swapping through quickswap.exchange
First, we initiate the swap operation through quickswap.exchange and proceed to decode the detailed data associated with the contract interaction.

<img src="{{site.baseurl}}/assets/img/quickswap.jpg">
[quickswap.exchange](https://quickswap.exchange/#/swap?currency0=0x2791Bca1f2de4661ED88A30C99A7a9449Aa84174&currency1=ETH&swapIndex=2)

If everything goes well, we can check transaction hash on polygonscan browser

## polygonsacn browser
Here is a successful transaction https://polygonscan.com/tx/0xe879f1915c09ec3e463b2ec4909d9fca0e7deb22478996f0ccae8067b1b3b4f3
<img src="{{site.baseurl}}/assets/img/contract.jpg">
Upon confirming the transaction, we are presented with the contract involved. By clicking on the contract, we gain access to its source code and ABI on polygonscan.
<img src="{{site.baseurl}}/assets/img/quickswap_source_code.jpg">
<img src="{{site.baseurl}}/assets/img/quickswap_abi.jpg">

Returning to the transaction on Polygonscan, a crucial element to examine is the "Input Data" section.
```
Function: multicall(bytes[] data)

MethodID: 0xac9650d8
[0]:  0000000000000000000000000000000000000000000000000000000000000020
[1]:  0000000000000000000000000000000000000000000000000000000000000002
[2]:  0000000000000000000000000000000000000000000000000000000000000040
[3]:  0000000000000000000000000000000000000000000000000000000000000160
[4]:  00000000000000000000000000000000000000000000000000000000000000e4
[5]:  bc6511880000000000000000000000002791bca1f2de4661ed88a30c99a7a944
[6]:  9aa841740000000000000000000000000d500b1d8e8ef31e21c99d1db9a6444d
[7]:  3adf127000000000000000000000000000000000000000000000000000000000
[8]:  0000000000000000000000000000000000000000000000000000000000000000
[9]:  647ffaa800000000000000000000000000000000000000000000000000000000
[10]: 000f424000000000000000000000000000000000000000000000000000000000
[11]: 0000000000000000000000000000000000000000000000000000000000000000
[12]: 0000000000000000000000000000000000000000000000000000000000000000
[13]: 0000000000000000000000000000000000000000000000000000000000000044
[14]: 69bc35b200000000000000000000000000000000000000000000000000000000
[15]: 00000000000000000000000000000000a03088535df05b625ffce049a504c84f
[16]: 3d803d1300000000000000000000000000000000000000000000000000000000

```
This section contains the content of the "Data" field within the transaction, which is utilized for contract interaction.
This is the content of "Data" filed in the transaction which used to interact with contract.
According to the foregoing, the invoked function is "multicall".

## Decode multicall
[contract source code](https://vscode.blockscan.com/polygon/0xf5b509bB0909a69B1c207E495f687a596C168E12)
```
/// @title Multicall
/// @notice Enables calling multiple methods in a single call to the contract
abstract contract Multicall is IMulticall {
    /// @inheritdoc IMulticall
    function multicall(bytes[] calldata data) external payable override returns (bytes[] memory results) {
        results = new bytes[](data.length);
        for (uint256 i = 0; i < data.length; i++) {
            (bool success, bytes memory result) = address(this).delegatecall(data[i]);

            if (!success) {
                // Next 5 lines from https://ethereum.stackexchange.com/a/83577
                if (result.length < 68) revert();
                assembly {
                    result := add(result, 0x04)
                }
                revert(abi.decode(result, (string)));
            }

            results[i] = result;
        }
    }
}
```
According to the source code, the "multicall" function accecpts bytes[], which represents the ABI of other functions. It enables the calling multiple methods within a single call.
By checking the [ABI spec](https://docs.soliditylang.org/en/latest/abi-spec.html), we know ABI comprises two components, "function selector" and "encoded argument".

Decoding the "function selector" can be accomplished through either of two methods:
1. searching for it on https://www.4byte.directory/signatures/
2. using Keccak-256
<img src="{{site.baseurl}}/assets/img/keccak-256.png">
Go through the foregoing "Input data", we identify two funcitons.
```
[5]:  bc6511880000000000000000000000002791bca1f2de4661ed88a30c99a7a944  <-- exactInputSingle
[14]: 69bc35b200000000000000000000000000000000000000000000000000000000  <-- unwrapWNativeToken
```

Subsequently, we consult the source code to examine and decode the arguments of these functions, one by one, based on their respective lengths.
## python code
Following these steps, we can now proceed with writing our swapping code. For detailed implementation guidance,
please refer to [dex_swap_quickswap_v2.py](https://github.com/successcw/python_utils/blob/main/dex_swap_quickswap_v2.py)

## Reference

1. [quickswap][1]
2. [polygonscan][2]
3. [ABI spec][3]
4. [function selector][4]
5. [Keccak-256][5]
6. [ABI decoder][6]


    [1]: https://quickswap.exchange
    [2]: https://polygonscan.com
    [3]: https://docs.soliditylang.org/en/latest/abi-spec.html
    [4]: https://www.4byte.directory/signatures/
    [5]: https://emn178.github.io/online-tools/keccak_256.html
    [6]: https://bia.is/tools/abi-decoder/


