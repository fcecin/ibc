---
sidebar_position: 4
---

# Wraptoken API

This section discusses the Wraptoken API.

**FIXME/WIP**

The Wraptoken contract exposes the following user APIs:

* issuea: accepts a block proof (using the heavy scheme) and an action proof of an emitxfer action from the paired chain, saves this to RAM and calls checkproofb on the bridge contract for verification. On success, issues and transfers wrapped tokens from the contract to the beneficiary named in the emitxfer action.

* issueb: accepts a block proof (using the light scheme) and an action proof of an emitxfer action from the paired chain, saves this to RAM and calls checkproofc on the bridge contract for verification. On success, issues and transfers wrapped tokens from the contract to the beneficiary named in the emitxfer action.

* cancela: accepts a block proof (using the heavy scheme) and an action proof of an emitxfer action from the paired chain, saves this to RAM and calls checkproofb on the bridge contract for verification. On success, triggers an inline emitxfer action to return the tokens.

* cancelb: accepts a block proof (using the light scheme) and an action proof of an emitxfer action from the paired chain, saves this to RAM and calls checkproofc on the bridge contract for verification. On success, triggers an inline emitxfer action to return the tokens.

* retire: removes the wrapped tokens from supply and triggers an inline emitxfer action

**FIXME/TODO discussion**

Inline actions:

* emitxfer: an inline action generated when wrapped tokens are retired or a cross-chain transfer to this chain is cancelled. Specifies the token, quantity and beneficiary, and forms part of the proof. Requires contract authority.

**FIXME/TODO discussion**

Admin APIs:

* init: initializes a new pairing between two chains, specifies the bridge contract which handles proof verification and specifies the native token contract on the source chain. Requires contract authority.

**FIXME/TODO discussion**
