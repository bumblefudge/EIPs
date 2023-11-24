---
title: Standardizing Smart Account Feature Discovery for apps
description: Enable Applications to Seize Smart Account features such as Sponsored Transactions and Batched Transactions
author: Derek Rein (@arein)
discussions-to: <URL> - TBD
status: Draft
type: Standards Track
category: ERC
created: 2024-11-24
requires: ERC-4337, CAIP-25

## Abstract

Enable wallets to advertise support of ERC-4337 and their batched transactions Application Binary Interface (ABI) in a standardized way such that end-user applications can seize these features.

## Motivation

Smart Accounts are on the rise. A Smart Account is a Smart Contract wallet that implements ERC-4337. Most Smart Accounts also support batched transactions albeit via different interfaces.

Discovering an end-users' wallet's support for these features is very interesting to application developers who want to build more delightful applications for their end-users that require less clicks.

As Smart Account adoption is rising it is likely to see increased developer demand to finally improve the end-user UX. It might even convince some developers to start building applications they wouldn't have prior due to the brittle end-user experience.


### Case Study 1: Ethereum Name Service (ENS)

Take ENS, a popular Naming Service, for example. Their current registration experience requires multiple transactions (i.e. clicks) from the end-user. Managing the blockchain state of these transactions in the user-interface is not easy and a scary experience for end-users who are not confident that the process is idempotent and they can refresh the browser.

Batched transactions can turn this into a single click experience.

### Case Study 2: peanut.to

Peanut.to is a popular payment link service allowing end-users to send tokens to other users without knowing them or asking them their preferred address in advance of sending the funds.

Peanut might want to sponsor the end-user's gas fee and subtract that instead from the funds send, as well as batching the currently two transactions into a single click.


## Specification

We will build this on top of [CAIP-25](https://github.com/ChainAgnostic/CAIPs/blob/main/CAIPs/caip-25.md) which most wallets already implement.

Here is an example reponse:

```
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": {
    "sessionId": "0xdeadbeef",
    "sessionScopes": {
      "eip155": {
        "chains": ["eip155:1", "eip155:137"],
        "methods": ["eth_sendTransaction", "eth_signTransaction", "get_balance", "eth_sign", "personal_sign"]
        "notifications": ["accountsChanged", "chainChanged"],
        "accounts": ["eip155:1:0xab16a96d359ec26a11e2c2b3d8f8b8942d5bfcdb", "eip155:137:0xab16a96d359ec26a11e2c2b3d8f8b8942d5bfcdb"]
      },
      "eip155:10": {
        "methods": ["get_balance"],
        "notifications": ["accountsChanged", "chainChanged"],
        "accounts:" []
      },
      "eip155:42161": {
        "methods": ["personal_sign"],
        "notifications": ["accountsChanged", "chainChanged","eth_sendUserOperation"],
        "accounts":["eip155:42161:0x0910e12C68d02B561a34569E1367c9AAb42bd810"],
        "batchAbi": {
            {"internalType":"bytes[]","name":"func","type":"bytes[]"}],"name":"executeBatchCall","outputs":[],"stateMutability":"nonpayable","type":"function"}
        },
        "dummyBytes": "0x0"
      },
      "wallet": {
        "methods": ["wallet_getPermissions", "wallet_switchEthereumChain", "wallet_creds_store", "wallet_creds_verify", "wallet_creds_issue", "wallet_creds_present"],
        "notifications": []
      },
      "cosmos": {
        ...
      }
    }
  }
}
```

The sample response depicts that the wallet advertises its `eth_sendUserOperation` (aka ERC-4337) support as part of the CAIP-25 response without any changes to the standard.

The `batchAbi` and `dummyBytes` attributes are new.

## Rationale

Handling this on an off-chain layer like CAIP-25 is a stop gap solution until a more permanent solution such as [EIP-5792](https://eips.ethereum.org/EIPS/eip-5792) is adopted and end-users migrated their accounts to it.


## Copyright

Copyright and related rights waived via [CC0](../LICENSE.md).
