---
sidebar_position: 4
---

# Wraptoken API

This section discusses the main Wraptoken APIs.

The `wraptoken` contract exposes the following public actions:

* `retire`: this action allows an account holding some quantity of wrapped tokens to remove these from the wrapped token supply (i.e. burn, destroy them), while specifying a beneficiary account on the paired source chain that will be able to withdraw an equivalent amount of locked native tokens. This action triggers inline `wraptoken::emitxfer` action, which will contain the parameters of the token transfer back into the paired chain.

* `issuea`: attempts to verify a provided block proof using the heavy proof scheme and an action proof of a `wraplock::emitxfer` action from the paired chain that proves that an amount of native tokens has been locked on the paired source chain. On success, On success, issues and transfers wrapped tokens from this contract to the beneficiary account, in this chain, that is named in the parameters to the `wraplock::emitxfer` action.

* `issueb`: same as `issuea`, but verifies a block proof using the light proof scheme.

* `cancela`: attempts to verify a provided block proof using the heavy proof scheme and an action proof of a `wraplock::emitxfer` action from the paired chain that proves that an amount of native tokens has been locked on the paired source chain. On verification success, triggers an inline `emitxfer` action to unlock and return the native tokens to a beneficiary account on the paired chain.

* `cancelb`: same as `cancela`, but verifies a block proof using the light proof scheme.

The `wraptoken` contract is essentially an `eosio.token` contract for the wrapped version of native tokens that reside on another chain. Wrapped tokens are first created at the destination chain (the chain where this `wraptoken` contract is deployed) through calling one of the `issue` actions. These actions take as a parameter a proof that an equivalent amount of native tokens in the paired source chain have been locked. The `cancel` actions, instead of minting wrapped tokens, will cancel the transfer and cause the locked tokens to be returned in the paired chain to a beneficiary account.

Finally, the `retire` action takes wrapped tokens and retires (burns, destroys) them, which allows for the sending of a corresponding amount of native tokens in the paired chain. In a sense, `retire` is similar to `cancel` in that it allows native tokens to be unlocked and transferred in the paired chain, but `cancel` takes a proof of a remote native token transfer (which is being reverted), while `retire` just takes a quantity of the wrapped tokens.

These are internal inline actions that are triggered by user action:

* `emitxfer`: As mentioned above, this is an internal inline action that is triggered when wrapped tokens are retired (via the `retire` action) or a cross-chain transfer to this chain is cancelled (via the `cancela` or `cancelb` actions listed above). Specifies the token, quantity and beneficiary, and forms part of the proof.

Admin APIs:

* `init`: initializes the token bridge, between this chain and another specific chain.

For more information, refer to the [Wraptoken reference documentation](/contracts/wraptoken.md).
