---
sidebar_position: 3
---

# Wraptoken

Reference documentation for the `wraptoken` contract.

## Overview

This contract implements the `eosio.token` interface, since it is the actual token contract for all wrapped tokens issued on the token destination chain. 

This contract publishes user and administration interfaces for managing the issuance and retirement of wrapped tokens on the destination chain of the token bridge:

* User actions (token bridge): `issuea`, `issueb`, `cancela`, `cancelb`, `retire`
* User actions (`eosio.token` interface): `transfer`, `open`, `close`
* Administrative actions: `init`, `enable`, `disable`
* Inline action (for IBC Bridge to prove): `emitxfer`

## Structs

### STRUCT `xfer`

Data structure used as an argument to the `emitxfer` action.

**Fields**

* `{name} owner` - sender of the transfer
* `{extended_asset} quantity` - object of the transfer
* `{name} beneficiary` - receiver of the transfer

## Tables

### TABLE `accounts`

Standard `eosio.token` account balance table, used for keeping user balances. Scoped by user.

**Fields**

- `{asset} balance` - a balance of an issued wrapped token (scoped by the user account that has this balance entry)

### TABLE `global`

This table contains a singleton entry with the global configuration parameters of this contract instance (see [init](#action-init)).

**Fields**

- `{checksum256} chain_id` - the id of the chain running this contract
- `{name} bridge_contract` - the bridge contract on this chain
- `{checksum256} paired_chain_id` - the id of the chain hosting the native tokens
- `{name} paired_wraplock_contract` - the wraplock contract on the native token chain
- `{name} paired_token_contract` - the token contract on the native chain being enabled for interchain transfers
- `{bool} enabled` - set to `false` if this contract is currently set to refuse all incoming requests

### TABLE `processed`

This table is used to hold action receipt digests of accepted proven actions, to prevent replay attacks.

**Fields**

- `{uint64_t} id` - key for this action receipt digest
- `{checksum256} receipt_digest` - hash of the action receipt that has already been accepted

### TABLE `stat`

Standard `eosio.token` token stats table.

**Fields**

- `{asset} supply - current supply of the token
- `{asset} max_supply - maximum supply of the token
- `{name} issuer - account that acts as the issuer authority for the token

## Actions

### ACTION `init`

Allows contract account to set which chains and associated contracts are used for all interchain transfers.

**Authority**

- `get_self()`

**Parameters**

- `{checksum256} chain_id` - the id of the chain running this contract
- `{name} bridge_contract` - the bridge contract on this chain
- `{checksum256} paired_chain_id` - the id of the chain hosting the native tokens
- `{name} paired_wraplock_contract` - the wraplock contract on the native token chain
- `{name} paired_token_contract` - the token contract on the native chain being enabled for interchain transfers

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

### ACTION `issuea`

Allows `prover` account to issue wrapped tokens and send them to the beneficiary indentified in the `actionproof`.

**Authority**

* `prover`

**Parameters**

- `{name} prover` - the calling account whose ram is used for storing the action receipt digest to prevent replay attacks
- `{bridge::heavyproof} blockproof` - the heavy proof data structure
- `{bridge::actionproof} actionproof` - the proof structure for the `emitxfer` action associated with the locking transfer action on the native chain

### ACTION `issueb`

Allows `prover` account to issue wrapped tokens and send them to the beneficiary indentified in the `actionproof`.

**Authority**

* `prover`

**Parameters**

- `{name} prover` - the calling account whose ram is used for storing the action receipt digest to prevent replay attacks
- `{bridge::lightproof} blockproof` - the light proof data structure
- `{bridge::actionproof} actionproof` - the proof structure for the `emitxfer` action associated with the locking transfer action on the native chain

### ACTION `cancela`

Allows `prover` account to cancel a token transfer and return them to the beneficiary indentified in the `actionproof`.

**Authority**

* `prover`

**Parameters**

- `{name} prover` - the calling account whose ram is used for storing the action receipt digest to prevent replay attacks
- `{bridge::heavyproof} blockproof` - the heavy proof data structure
- `{bridge::actionproof} actionproof` - the proof structure for the `emitxfer` action associated with the locking transfer action on the native chain

### ACTION `cancelb`

Allows `prover` account to cancel a token transfer and return them to the beneficiary indentified in the `actionproof`.

**Authority**

* `prover`

**Parameters**

- `{name} prover` - the calling account whose ram is used for storing the action receipt digest to prevent replay attacks
- `{bridge::lightproof} blockproof` - the light proof data structure
- `{bridge::actionproof} actionproof` - the proof structure for the `emitxfer` action associated with the locking transfer action on the native chain

### ACTION `retire`

Allows `owner` account to retire the `quantity` of wrapped tokens and calls the `emitxfer` action inline so that can be used as the basis for a proof of locking for the withdraw actions on the native chain.

**Authority**

* `owner`

**Parameters**

- `{name} owner` - account retiring wrapped tokens
- `{asset} quantity` - quantity of a wrapped token being retired
- `{name} beneficiary` - account on the native chain that will be able to unlock a corresponding amount of native tokens

### ACTION `transfer`

The standard `eosio.token` action to transfer tokens. Transfers wrapped tokens between accounts on this blockchain.

**Authority**

* `from`

**Parameters**

- `{name} from` - account with wrapped tokens to send on this blockchain
- `{name} to` - account to receive the wrapped tokens on this blockchain
- `{quantity} quantity` - the wrapped token and amount to be sent 
- `{string}  memo` - memo field of the transfer (can be anything)

### ACTION `open`

The standard `eosio.token` action to open a balance of wrapped tokens for an account.

**Authority**

* `owner`

**Parameters**

- `{name} owner` - account opening a token balance
- `{symbol} symbol` - symbol for the token the `owner` account will open a balance 
- `{name} ram_payer` - account that will pay for the RAM cost of opening a balance, if any

### ACTION `close`

The standard `eosio.token` action to close a balance of wrapped tokens for an account.

**Authority**

* `owner`

**Parameters**

- `{name} owner` - account closing a token balance
- `{symbol} symbol` - symbol for the token the `owner` account will close the balance 

### ACTION `emitxfer`

This is an action that is called inline by this contract when tokens are retired (by the `retire` action). Proof of this action is then used on the native token chain.

**Authority**

- `get_self()`

**Parameters**

* `{`[`wraptoken::xfer`](#struct-xfer)`} xfer` - struct that contains the parameters of the token retirement
