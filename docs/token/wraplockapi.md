---
sidebar_position: 3
---

# Wraplock API

This section discusses the Wraplock API.

The `wraplock` contract exposes the following entry points that are callable by users:

* `transfer` notification: transferring native tokens to the `wraplock` contract will lock them and trigger an inline `wraplock::emitxfer` action which records the token transfer in the action parameters. A proof of execution of a `wraplock::emitxfer` action with specific parameters on the source chain, provided to the `wraptoken` contract on the destination chain, allows the `wraptoken` endpoint to issue a quantity of wrapped tokens, to the beneficiary named in the (now proven) `wraplock::emitxfer` action invocation, that is equivalent to the quantity of tokens locked by this token-locking transfer; it also allows the `wraptoken` contract on the destination chain to decide to cancel the token transfer from the source chain (see the [Wraptoken API](/token/wraptokenapi.md) in the next section).

* `withdrawa`: attempts to verify a provided block proof using the heavy proof scheme and an action proof of a `wraptoken::emitxfer` action from the paired chain that proves that some wrapped tokens on the paired chain have been retired. On verification success, transfers native tokens from this `wraplock` contract to the beneficiary account, in this chain, that is named in the parameters to the `wraptoken::emitxfer` action.

* `withdrawb`: same as `withdrawa`, but verifies a block proof using the light proof scheme.

* `cancela`: attempts to verify a provided block proof using the heavy scheme and an action proof of a `wraptoken::emitxfer` action from the paired chain that proves that some wrapped tokens on the paired chain have been retired. On verification success, triggers an inline `emitxfer` action to reissue and return the wrapped tokens to the account on the paired chain that was trying to send the wrapped tokens back to the source chain (where this `wraplock` endpoint resides).

* cancelb: same as `cancela`, but verifies a block proof using the light proof scheme.

In essence, the `transfer` notification on the `wraplock` contract produces an inline action that records the information needed to issue and transfer the equivalent wrapped tokens over at the destination chain, using the block and action proving methods of the IBC Bridge. The `memo` field of the `eosio.token` transfer into the `wraplock` contract specifies the name of the account, on the destination chain, that is the beneficiary, that is, the account that is going to receive the equivalent amount of wrapped tokens. 

On the other hand, the `withdrawa`, `withdrawb`, `cancela` and `cancelb` actions work on the opposite direction: they act upon a proof of a `wraptoken::emitxfer` inline action being triggered on the _destination_ chain (see the [Wraptoken API](/token/wraptokenapi.md) in the next section). Possessing a proof that some account on the destination chain has retired (destroyed) some wrapped tokens allows the `wraplock` contract to release the equivalent amount of locked native tokens to some beneficiary (that is named in the `wraptoken::emitxfer` action invocation's parameters) using the `withdraw` actions, or cancel that transfer of wrapped tokens back into native tokens, using the `cancel` actions.

These are internal inline actions that are triggered by user action:

* `emitxfer`: As mentioned above, this is an internal inline action that is triggered when native tokens are locked in this contract (as a side-effect of a `transfer` of tokens to it) or a cross-chain transfer to this chain is cancelled (via the `cancela` or `cancelb` actions listed above). Specifies the token, quantity and beneficiary, and forms part of the proof.

Admin APIs:

* `init`: initializes the token bridge, between this chain and another specific chain.

* `addcontract`: adds support for a native token contract and its corresponding `wraptoken` contract on the paired chain.

* `delcontract`: removes support for a native token contract.

For more information, refer to the [Wraplock reference documentation](/contracts/wraplock.md).