---
sidebar_position: 1
---

# Introduction

This section offers an overview of IBC Bridge.

The **IBC Brige** contract (to simplify, the _bridge contract_, or _bridge_) is, basically, a contract hosted on a specific blockchain that continuously consumes proofs of consensus changes that are occurring on one or more foreign blockchains. In other words, it contains a basic model of the consensus state of one or more foreign blockchains. Since it models multiple blockchains, each blockchain only needs to deploy one instance of the bridge contract to be able to connect to multiple other blockchains.

The bridge's model of other blockchain's state is basically about what is the current situation with  the _producer schedule_ (that is, the list of which BPs are currently authorized to produce which blocks, and in what order) on a foreign blockchain, and a list of recent finalized block merkle roots, which are needed to prove the inclusion of specific actions in specific, finalized blocks.

When the bridge is used to connect to another chain, it is initialized with a block producer schedule obtained from that other chain. This schedule contains the list of elected BPs as well as their respective block signing keys, which is how the bridge gets to ultimately know what is happening on the foreign blockchain over time: because it receives block proofs signed by the expected producers of those blocks, following the given producer schedule. It also allows the bridge to know about changes to the block production schedule, that is, changes to the set of block signing keys itself going forward, which are also signed by the keys in the current producer schedule.

Once the bridge contract has been initialized for a new foreign blockchain, any user can then submit a _heavy proof_ (of a finalized block on the destination chain) to the bridge. When a valid heavy proof is received, the merkle root of the block whose finality is being proven is saved to the contract state, and it then becomes possible to prove actions that occurred prior to that block using a _light proof_ scheme. Calling the bridge with a new heavy proof of finalized block as a parameter effectively advances the bridge's model of the foreign blockchain for all other users of the bridge.

While the light proof is, as the name implies, much smaller and cheaper to verify, the heavy proof scheme is required to advance the local chain’s knowledge of recent irreversible blocks from the foreign chain, which will allow verification of anterior blocks in the local chain using the light proof scheme.

The bridge smart contract can also verify action proofs alongside block proofs. This allows users to submit action invocations that have been executed on the foreign chain to the local chain of the bridge, and for the bridge contract to verify these action invocations were included into a block on the foreign chain that has reached finality.

