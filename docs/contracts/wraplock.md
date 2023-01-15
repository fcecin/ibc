---
sidebar_position: 2
---

# Wraplock

Reference documentation for the `wraplock` contract.

## Overview

This contract publishes user and administration interfaces for managing the locking and unlocking of native tokens on the source chain of the token bridge:

* User actions: `cancela`, `cancelb`, `withdrawa`, `withdrawb`
* Administrative actions: `init`, `addcontract`, `delcontract`, `enable`, `disable`
* Inline action (for IBC Bridge to prove): `emitxfer`
* Notifications: `transfer`

## Structs

### STRUCT `xfer`

Data structure used as an argument to the `emitxfer` action.

**Fields**

* `{name} owner` - sender of the transfer
* `{extended_asset} quantity` - object of the transfer
* `{name} beneficiary` - receiver of the transfer

## Tables

### TABLE `contractmap`

This table contains the data records used for mapping between native token contracts and wrapped token contracts.

**Fields**

- `{name} native_token_contract` - name of the native token contract
- `{name} paired_wraptoken_contract` - name of the wrapped token contract

### TABLE `global`

This table contains a singleton entry with the global configuration parameters of this contract instance (see [init](#action-init)).

**Fields**

- `{checksum256} chain_id` - the chain ID of this chain
- `{name} bridge_contract` - account where the IBC Bridge contract is deployed on this chain
- `{checksum256} paired_chain_id` - the chain ID of the chain at the other end of this IBC Token bridge
- `{bool} enabled` - set to `false` if this contract is currently set to refuse all incoming requests

### TABLE `processed`

This table is used to hold action receipt digests of accepted proven actions, to prevent replay attacks.

**Fields**

- `{uint64_t} id` - key for this action receipt digest
- `{checksum256} receipt_digest` - hash of the action receipt that has already been accepted

### TABLE `reserves`

This table contains the reserve account balances (i.e. the deposited and locked tokens), scoped by the contract of the deposited token.

**Fields**

- `{asset} balance` - a deposited (locked) token balance

## Actions

### ACTION `init`

Allows contract account to set which chains and associated bridge contracts are used for interchain transfers.

**Authority**

- `get_self()`

**Parameters**

* `{checksum256} chain_id` - the id of the chain running this contract
* `{name} bridge_contract` - the bridge contract on this chain
* `{checksum256} paired_chain_id` - the id of the chain hosting the wrapped tokens

### ACTION `addcontract`

Allows contract account to add support for an asset contract for interchain transfers.

**Authority**

- `get_self()`

**Parameters**

* `{name} native_token_contract` - the token contract being enabled for interchain transfers
* `{name} paired_wraptoken_contract` - the corresponding wraptoken contract which transfers are sent to/from

### ACTION `delcontract`

Allows contract account to disable support for an asset contract for interchain transfers.

**Authority**

- `get_self()`

**Parameters**

* `{name} native_token_contract` - the token contract being disabled for interchain transfers

### ACTION `disable`

Disable all user actions on the contract.

**Authority**

- `get_self()`

**Parameters**

None.

### ACTION `enable`

Enable all user actions on the contract.

**Authority**

- `get_self()`

**Parameters**

None.

### ACTION `withdrawa`

Allows `prover` account to redeem native tokens and send them to the beneficiary indentified in the `actionproof`.

**Authority**

- None.

**Parameters**

* `{name} prover` - the calling account whose ram is used for storing the action receipt digest to prevent replay attacks
* `{bridge::heavyproof} blockproof` - the heavy proof data structure
* `{bridge::actionproof} actionproof` - the proof structure for the `emitxfer` action associated with the `retire` action on the wrapped tokens chain

### ACTION `withdrawb`

Allows `prover` account to redeem native tokens and send them to the beneficiary indentified in the `actionproof`.

**Authority**

- `prover`   `

**Parameters**

* `{name} prover` - the calling account whose ram is used for storing the action receipt digest to prevent replay attacks
* `{bridge::lightproof} blockproof` - the light proof data structure
* `{bridge::actionproof} actionproof` - the proof structure for the `emitxfer` action associated with the `retire` action on the wrapped tokens chain

### ACTION `cancela`

Allows `prover` account to cancel a token transfer and return them to the beneficiary indentified in the `actionproof`.

**Authority**

- `prover`   `

**Parameters**

* `{name} prover` - the calling account whose ram is used for storing the action receipt digest to prevent replay attacks
* `{bridge::heavyproof} blockproof` - the heavy proof data structure
* `{bridge::actionproof} actionproof` - the proof structure for the `emitxfer` action associated with the retiring transfer action on the native chain

### ACTION `cancelb`

Allows `prover` account to cancel a token transfer and return them to the beneficiary indentified in the `actionproof`.

**Authority**

- `prover`   `

**Parameters**

* `{name} prover` - the calling account whose ram is used for storing the action receipt digest to prevent replay attacks
* `{bridge::lightproof} blockproof` - the light proof data structure
* `{bridge::actionproof} actionproof` - the proof structure for the `emitxfer` action associated with the retiring transfer action on the native chain

### ACTION `emitxfer`

This is an action that is called inline by this contract when tokens are locked (by the `transfer` notification). Proof of this action is then used on the wrapped token chain.

**Authority**

- `get_self()`

**Parameters**

* `{`[`wraplock::xfer`](#struct-xfer)`} xfer` - struct that contains the parameters of the token lock

## Notifications

### NOTIFICATION `*::transfer`

On receiving a `transfer` notification from any contract (assuming the source contract conforms to the `eosio.token` interface), makes an internal `deposit(from, to, quantity, memo)` call (with the same, matching arguments from the transfer notification), which locks the `quantity` of tokens sent inside this contract's [`reserves` table](#table-reserve), and calls the [`emitxfer` action](#action-emitxfer) inline so that can be used as the basis for a proof of locking for the issue or cancel actions on the wrapped token chain.
	  
**Parameters**

- `{name} from` - the owner of the tokens to be sent to the wrapped token chain
- `{name} to` - this contract account
- `{asset} quantity` - the asset to be sent to the wrapped token chain
- `{string} memo` - the beneficiary account on the wrapped token chain
