---
sidebar_position: 1
---

# Bridge

Reference documentation for the `bridge` contract.

## Overview

This contract provides user and administration interfaces for the Antelope IBC Bridge contract. It publishes the following actions:

* Core IBC actions, parameters by reference: `checkproofa`, `checkproofb`, `checkproofc`
* Core IBC actions, parameters by value: `checkproofd`, `checkproofe`, `checkprooff`
* Administrative actions: `inita`, `initb`, `enable`, `disable`

## Structs

### STRUCT `actionproof`

Action invocation proof.

**Fields**

- `{action} action` - the action invocation to prove
- `{bridge::actreceipt} receipt` - the receipt of the action invocation
- `{std::vector<char>} returnvalue` - the value returned by the acton
- `{std::vector<checksum256> amproofpath` - the main action proof object

### STRUCT `actreceipt`

**Fields**

- `{name} receiver`
- `{checksum256} act_digest`
- `{uint64_t} global_sequence`
- `{uint64_t} recv_sequence`
- `{std::vector<bridge::authseq>} auth_sequence`
- `{unsigned_int} code_sequence`
- `{unsigned_int} abi_sequence`

### STRUCT `anchorblock`

**Fields**

- `{bridge::sblockheader} block`
- `{std::vector<uint16_t>} active_nodes`
- `{uint64_t} node_count`

### STRUCT `authseq`

**Fields**

- `{name} account`
- `{uint64_t} sequence`

### STRUCT `blockheader`

**Fields**

- `{block_timestamp} timestamp`
- `{name} producer`
- `{uint16_t} confirmed`
- `{checksum256} previous`
- `{checksum256} transaction_mroot`
- `{checksum256} action_mroot`
- `{uint32_t} schedule_version`
- `{std::optional<producer_schedule>} new_producers`
- `{std::vector<std::pair<uint16_t,std::vector<char>>>} header_extensions`

### STRUCT `heavyproof`

Heavy block proof.

**Fields**

- `{checksum256} chain_id` - remote chain id where this heavy block proof originates from 
- `{bridge::anchorblock} blocktoprove` - the block to prove
- `{std::vector<checksum256>} hashes` - array of hashes in the proof
- `{std::vector<bridge::sblockheader>} bftproof` - the main heavy block proof object

### STRUCT `lightproof`

Light block proof.

**Fields**

- `{checksum256} chain_id` - remote chain id where this heavy block proof originates from
- `{bridge::blockheader} header` - header of the block being proven
- `{checksum256} - root` - merkle root
- `{std::vector<checksum256> bmproofpath` - the main light block proof object

### STRUCT `sblockheader`

**Fields**

- `{bridge::blockheader} header`
- `{std::vector<signature>} producer_signatures`
- `{checksum256} previous_bmroot`
- `{std::vector<uint16_t>} bmproofpath`

### STRUCT `schedulev2`

- `{uint32_t} version`
- `{std::vector<producer_authority>} producers`

**Fields**

## Tables

### TABLE `chains`

This table contains the list of blockchains, and their bridge endpoint contracts, that this bridge is connected to.

**Fields**

- `{name} name` - readable chain name of the remote chain
- `{checksum256} chain_id` - chain id of the remote chain
- `{uint32_t} return_value_activated` - if the `RETURN_VALUE_ACTIVATED` feature is not set for the chain, this is set to zero. Otherwise, it is set to the block height the feature was activated
- `{bool} enabled` - set to `false` if bridging to this chain has been disabled

### TABLE `lastproofs`

This table contains the last saved heavy proof for each connected chain, to be used for light proofs for that chain moving forward. Scoped by the readable chain name.

**Fields**

- `{uint64_t} id` - proof id
- `{uint32_t} block_height` - block height of the proof
- `{checksum256} block_merkle_root` - merkle root of the block
- `{time_point} expiry` - proof expiry

### TABLE `schedules`

This table contains the currently saved schedule objects for a chain. Scoped by that chain's readable name.

**Fields**

- `{uint64_t} version` - schedule version
- `{producer_schedule} producer_schedule_v1` - version 1 producer schedule object (can be empty)
- `{bridge::schedulev2} producer_schedule_v2` - version 1 producer schedule object (can be empty)
- `{checksum256} hash` - digest of the schedule
- `{uint32_t} first_block` - first block of the schedule
- `{uint32_t} last_block` - last block of the schedule
- `{time_point} expiry` - schedule expiration time

## Actions

### ACTION `checkproofa`

Prove a block using a heavy block proof.

To be used with inline action. The `heavyproof` object will be fetched from an `eosio::singleton` table named `heavyproof` that is stored on the given `contract`.

**Authority**

- None.

**Parameters**

- `{name} contract` - contract that has the `heavyproof` object stored on a table named `heavyproof` that is an `eosio::singleton`

### ACTION `checkproofb`

Prove a block and an action using a heavy block proof.

To be used with inline action. The `heavyproof` object will be fetched from an `eosio::singleton` table named `heavyproof` that is stored on the given `contract`.

**Authority**

- None.

**Parameters**

- `{name} contract` - contract that has the `heavyproof` object stored on a table named `heavyproof` that is an `eosio::singleton`
- `{actionproof} actionproof` - the action proof

### ACTION `checkproofc`

Using a heavy block proof that has been saved previously in this contract, use a light proof to prove any action that has occured prior to or as part of that block.

To be used with inline action. The `lightproof` object will be fetched from an `eosio::singleton` table named `lightproof` that is stored on the given `contract`.

**Authority**

- None.

**Parameters**

- `{name} contract` - contract that has the `lightproof` object stored on a table named `lightproof` that is an `eosio::singleton`
- `{actionproof} actionproof` - the action proof

### ACTION `checkproofd`

Prove a block using a heavy proof.

**Authority**

- None.

**Parameters**

- `{heavyproof} blockproof` - the heavy block proof

### ACTION `checkproofe`

Prove a block and an action using a heavy block proof.

**Authority**

- None.

**Parameters**

- `{heavyproof} blockproof` - the heavy block proof
- `{actionproof} actionproof` - the action proof

### ACTION `checkprooff`

Using a heavy block proof that has been saved previously in this contract, use a light proof to prove any action that has occured prior to or as part of that block.

**Authority**

- None.

**Parameters**

- `{lightproof} blockproof` - the light block proof
- `{actionproof} actionproof` - the action proof

### ACTION `disable`

Disable communication with a remote chain.

**Authority**

- `get_self()`

**Parameters**

- `{name} chain_name` - readable chain name to disable

### ACTION `enable`

Re-enable communication with a remote chain.

**Authority**

- `get_self()`

**Parameters**

- `{name} chain_name` - readable chain name to enable

### ACTION `inita`

Connects the bridge contract to another instance of the bridge on another blockchain, using a v1 schedule.

**Authority**

- `get_self()`

**Parameters**

- `{name} chain_name` - readable name of the remote chain
- `{checksum256} chain_id` - chain id of the remote chain
- `{uint32_t} return_value_activated` - if the `RETURN_VALUE_ACTIVATED` feature is not set for the chain, this is set to zero. Otherwise, it is set to the block height the feature was activated
- `{producer_schedule} initial_schedule` - old (v1) schedule version to use for initializing IBC with this chain

### ACTION `initb`

Connects the bridge contract to another instance of the bridge on another blockchain, using a v2 schedule.

**Authority**

- `get_self()`

**Parameters**

- `{name} chain_name` - readable name of the remote chain
- `{checksum256} chain_id` - chain id of the remote chain
- `{uint32_t} return_value_activated` - if the `RETURN_VALUE_ACTIVATED` feature is not set for the chain, this is set to zero. Otherwise, it is set to the block height the feature was activated
- `{bridge::schedulev2} initial_schedule` - new (v2) schedule version to use for initializing IBC with this chain
