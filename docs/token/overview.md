---
sidebar_position: 2
---

# Overview

This section provides an overview of the IBC Token solution.

## What IBC Token does

The token bridging process starts with one blockchain having a predefined token (also called an asset) in it, with its own, preexisting, deployed and working `eosio.token`-compatible contract, which will be referred to as the _native token_ in the context of the IBC Token bridge.

The job of the IBC Token bridge is then to allow users to transfer some quantity of these native tokens from an account that originally holds them on a source chain, to another beneficiary account on a destination chain. Once in the hands of the beneficiary in the destination chain, the tokens can move freely between accounts in the destination chain and, eventually, they should be able to return to their original chain, to any beneficiary accounts named in the return transfers.

The token bridge actually _locks_ quantities of native tokens on the source chain, and mints a corresponting amount of the _wrapped_ version of the tokens on the destination chain, to some specified beneficiary account. Some amount of the wrapped tokens can then later be submitted to the token bridge on the destination chain, which destroys these wrapped tokens, allowing the correspondign amount of locked native tokens to be released on the source chain to some specified beneficiary account.

## Implementation overview

The IBC Token solution is implemented as a pair of smart contracts: `wraplock` and `wraptoken`.

`wraplock` is deployed on what we will refer to in this documentation as the _source chain_, or chain `A`. It is where the _native token_ contract, that is, the original token, resides, and is where the inter-blockchain token transfer begins.

`wraptoken` is deployed on what we will refer to in this documentation as the _destination chain_, or chain `B`. It is where the _wrapped_ incarnation of the locked native token will be minted.

The `wraplock` and `wraptoken` contracts work together to facilitate interchain token transfers (also called _wrapping_ and _unwrapping_). They require and utilise an IBC Bridge contract deployed on the blockchains at both ends of the tokens to be transferred. The bridge handles all the cryptographic proof verification that ensure native tokens are locked before wrapped tokens are minted, and that wrapped tokens are retired before native tokens are released.

Most actions in the `wraplock` and `wraptoken` API are straightforward, but note that the callers will need to supply [IBC Bridge proof objects](/bridge/proof#getting-json-proof-objects) as parameters to the actions that actually operate the token transfers. These proof objects are used by IBC Token to operate the IBC Bridge underlying it. The [proof server](/bridge/proofserver.md) supplied with the Antelope IBC suite is just one method for obtaining these proofs.

## Token bridge setup overview

IBC Token is modeled after and is compatible with the `eosio.token` standard, which allows one deployed token contract to have multiple assets (tokens) defined in it.

One deployed `wraplock` contract on chain `A` may handle interchain token transfers from any number of native token contracts, and any number of different assets issued on each one of these native token contracts. That means one `wraplock` contract on chain `A` may receive multiple transfers (deposits, for locking) of multiple assets (tokens) from multiple `eosio.token`-compatible token contracts on chain `A`.

One deployed `wraptoken` contract on chain `B`, on the other hand, corresponds to _only one native token contract_ on chain `A`, but again any number of assets issued on it. This means that for every supported native _token contract_ (_not_ asset) on chain `A` that will be depositing tokens on a single `wraplock` contract on chain `A`, there will be one, exclusive, deployed instance of `wraptoken` on chain `B` to receive any and all assets from that supported _native token contract_ on chain `A`.

Each `wraplock` and `wraptoken` contract is also restricted to moving tokens between two specific Antelope chains, considered the native and wrapped sides of the token bridge.

To facilitate transfers of both wrapped and native tokens in both directions, a pair of `wraplock` and `wraptoken` contracts needs to be deployed on _both_ chains (this example is considering only one _native token contract_; see discussion above). In other words, if chain `X` is to have a full-duplex IBC Token bridge to chain `Y`, then a `wraplock` contract on chain `X` is conencted to a `wraptoken` contract on chain `Y`, and a `wraplock` contract on chain `Y` is connected to a `wraptoken` contract on chain `X`. Thus, native tokens on chain `X` can be sent to chain `Y` (where they incarnate as the wrapped version of these tokens), and native tokens on chain `Y` can also be sent to chain `X`. 

And, to allow transfers across multiple chains, these (pairs of) contracts need to be deployed multiple times on a given chain: once per remote blockchain to connect to.
