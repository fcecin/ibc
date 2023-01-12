---
sidebar_position: 2
---

# Bridge API

This section enumerates the Bridge APIs.

## User APIs

As discussed in the previous section, the bridge contract allows a user to prove blocks and actions using either a heavy proof or a light proof scheme. For that, there are six different actions, which are all public APIs that anyone can call:

* ```checkproofa(name contract)```: verify a block proof held in the RAM of another contract (```contract```) using the heavy scheme.

* ```checkproofb(name contract, actionproof actionproof)```: verify a block proof held in the RAM of another contract (```contract```) using the heavy scheme, as well as an action proof (```actionproof```).
 
* ```checkproofc(name contract, actionproof actionproof)```: verify a block proof held in the RAM of another contract (```contract```) using the light scheme, as well as an action proof (```actionproof```).

* ```checkproofd(heavyproof blockproof)```: verify a block proof (```blockproof```) directly using the heavy scheme.

* ```checkproofe(heavyproof blockproof, actionproof actionproof)```: verify a block proof (```blockproof```) directly using the heavy scheme, as well as an action proof (```actionproof```).

* ```checkprooff(lightproof blockproof, actionproof actionproof)```: verify a block proof (```blockproof```) directly using the light scheme, as well as an action proof (```actionproof```).

The first three actions (```checkproofa```, ```checkproofb``` and ```checkproofc```) are intended for use with inline actions. The contract name given by the ```contract``` parameter names a contract that has a light proof and a heavy proof table in it, which are both intented to be holding a singleton object (a light proof and a heavy proof object, respectively) that is read by the corresponding proof-checking action of the bridge contract. The **IBC Token** contracts provide a working example of using this call mechanism. 

The last three actions (```checkproofd```, ```checkproofe``` and ```checkprooff```) mirror the functionality of the first three actions, but they take the full proof objects directly as parameters.

So, in essence, bridging consensus between two Antelope blockchains is encapsulated in the acts of checking the finality of blocks on a foreign blockchain, and of checking that a specific action invocation with specific parameters is present in a given final block on that foreign blockchain. That is because proving that a specific action invocation, with specific parameters, happened with finality in a foreign blockchain, can be coupled with the knowledge of the (deterministic, and possibly immutable) code of that action, and of the expected (also deterministic, and possibly immutable) side-effects of executing that action with specific parameter values.

> **_NOTE_**: The ```heavyproof```, ```lightproof``` and ```actionproof```data structures are unpacked in later subsections.

## Admin APIs

The contract also has four administrative functions that require contract authority, which means some multi-signature governance structure that manages the connection of the deployed bridge contract to other blockchains:

* ```inita(name chain_name, checksum256 chain_id, uint32_t return_value_activated, producer_schedule initial_schedule )```: initializes support for blockchain (```chain_name```, ```chain_id```) on the bridge, informing that the Antelope action-return-value extension was enabled for that blockchain on the block height given by ```return_value_activated``` (if that parameter is greater than zero), and with the given version 1 intial producer schedule (```initial_schedule```). 

* ```initb(name chain_name, checksum256 chain_id, uint32_t return_value_activated, bridge::schedulev2 initial_schedule)```: same as ```ìnita```, but takes version 2 of the producer schedule data structure.

* ```disable(name chain_name)```: prevent previously-initialized blockchain ```chain_name``` from advancing its state.

* ```enable(name chain_name)```: re-enable previously disabled blockchain ```chain_name``` to advance its state.
