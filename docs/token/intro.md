---
sidebar_position: 1
---

# Introduction

This section provides a summary of the IBC Token solution.

**FIXME/WIP**

IBC Token is implemented as a pair of smart contracts:

* Wraplock: enables a user to lock native tokens on chain A
* Wraptoken: allows locked tokens on chain A to be issued as wrapped tokens on chain B.

The wraplock and wraptoken contracts work together to facilitate interchain token transfers (also called wrapping and unwrapping). They require and utilise an IBC Bridge contract deployed on the blockchains at both ends of the tokens to be transferred. The bridge handles all the cryptographic proof verification that ensure native tokens are locked before wrapped tokens are minted, and that wrapped tokens are retired before native tokens are released.
