---
sidebar_position: 3
---

# Wraplock API

This section discusses the Wraplock API.

**FIXME/WIP**

The Wraplock contract exposes the following user APIs:

* deposit (via transfer notification): locks the received native tokens in the contract and triggers an inline emitxfer action.

* withdrawa: accepts a block proof (using the heavy scheme) and an action proof of an emitxfer action from the paired chain, saves this to RAM and calls checkproofb on the bridge contract for verification. On success, transfers native tokens from the contract to the beneficiary named in the emitxfer action.

* withdrawb: accepts a block proof (using the light scheme) and an action proof of an emitxfer action from the paired chain, saves this to RAM and calls checkproofc on the bridge contract for verification. On success, transfers native tokens from the contract to the beneficiary named in the emitxfer action.

* cancela: accepts a block proof (using the heavy scheme) and an action proof of an emitxfer action from the paired chain, saves this to RAM and calls checkproofb on the bridge contract for verification. On success, triggers an inline emitxfer action to return the tokens.

* cancelb: accepts a block proof (using the light scheme) and an action proof of an emitxfer action from the paired chain, saves this to RAM and calls checkproofc on the bridge contract for verification. On success, triggers an inline emitxfer action to return the tokens.

**FIXME/TODO short discussion on how to use these**

These are inline actions that are triggered by other actions:

* emitxfer: an inline action, triggered when native tokens are locked in the contract or a cross-chain transfer to this chain is cancelled. Specifies the token, quantity and beneficiary, and forms part of the proof. Requires contract authority.

**FIXME/TODO short discussion about it here, its relation to the Bridge**

Admin APIs:

* init: initializes a new pairing between two chains and specifies the bridge contract which handles proof verification. Requires contract authority.

* addcontract: adds support for a native token contract and its corresponding wraptoken contract. Requires contract authority.

* delcontract: removes support for a native token contract. Requires contract authority.

**FIXME/TODO short deployment/admin procedure discussion**
