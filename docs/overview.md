---
sidebar_position: 2
---

# About IBC

This section provides an overview of IBC and of how Antelope has solved it.

## Inter-Blockchain Communication

Inter-Blockchain Communication (IBC) is generally understood as the ability of two smart contracts hosted in two different blockhains to exchange data, and thus achieve cross-blockchain code composability. In other words, two blockchains that support an IBC protocol connection to each other can act as shards of a larger virtual blockchain, as far as developers and users are concerned.

For that to be possible, the _communication_ that occurs between two blockchains must be a special kind of communication. IBC does not mean, for example, using simple oracles to feed some data stream `X` into a smart contract running on some blockchain `A`, where `X` comes from some third-party simply reading data from another, foreign blockchain `B` and then injecting it on the smart contract in question, on blockchain `A`.

The goal of IBC is, really, to achieve _consensus across blockchains_. That is, two smart contracts calling each other _across blockchains_, using IBC, must enjoy the same sort of security guarantees that are present when two smart contracts call each other _within the same blockchain_. Thus, IBC provides not only communication or composability across blockchains, but a _secure_ version of those, derived from the security model of both blockchains involved.

## Antelope IBC overview

The Antelope IBC infrastructure is built entirely at the smart contract level, making it easier to audit and to upgrade it. DApps hosted on a blockchain will use the IBC infrastructure by calling specific actions on the IBC contracts that are relevant to them, which are connected to the other blockchains that they are trying to reach.

Antelope IBC does not introduce additional third parties that users and developers have to trust, other than the trust that they already grant to the consensus mechanism of elected and active Block Producers (BPs) on the source chain and the destination chain. In that sense, we say that the Antelope IBC runtime infrastructure is a mesh of _trustless bridges_ between the various blockchains in the Antelope blockchain community that choose to deploy their end of this bridging infrastructure.

Technically, what users and developers trust is in cryptographic proofs of finality on a remote chain. What the bridge provides, to a smart contract hosted on a local blockchain, is a proof path of the finality of a block that is produced on a foreign blockchain, or of the successful inclusion of a specific action invocation on that proven block. That mathematical proof is then anchored on the assumption that consensus on the destination chain is maintained.

## Antelope IBC contract suite

As mentioned above, the Antelope IBC mechanism is provided as a suite of smart contracts, namely:

* **IBC Bridge**, and
* **IBC Token**.

The core contract in this suite is the **IBC Bridge** contract, which establishes the actual connection between two blockchains. The IBC Brige provides the basic building block of all inter-blockchain interaction: it provides, to a contract on the same blockchain it resides, a final proof (either a heavy or light proof) that some block or action on a foreign blockchain is final.

The central idea of the IBC Bridge is that if your smart contract, on blockchain `A`, can _know for sure_ that a certain action has ocurred and is declared irreversible on a foreign blockchain `B`, by being passed a chain of signed, cryptographic proofs obtained from blockchain `B`, then it can make certain safe decisions when altering memory (state) on blockchain `A`. On Antelope blockchains, this means, ultimately, obtaining a chain of specific cryptographic signatures from the elected Block Producers (BPs) of Blockchain `B` at the _time_ (i.e. block) the action in question was included on Blockchain `B`[^1].

This can allow, for example, the _travel of tokens between blockchains_. That functionality is provided by the second set of contracts provided in the Antelope IBC blockchain suite: the **Wraplock** and **Wraptoken** contracts, which together will be referred to as the **IBC Token** solution in this documentation. The IBC Token documentation explains in detail how IBC Token calls the actions on the IBC Bridge contract to obtain the assurance it needs to guarantee that tokens can be safely transferred between two blockchains, without ever losing or duplicating tokenized value in the process.

IBC Token provides an essential function on top of IBC Bridge, as tokens are a central component of all blockchains and DApps, and thus it was made an integral part of the Antelope IBC solution. It also provides one real-world practical, important and working example and reference of how to leverage the IBC Bridge contract correctly.

[^1]: It's a bit more involved than that, but contract developers and users are, thankfully, not required to understand the nitty-gritty details, e.g. the correctness of the proof chains, and how the corner cases are dealt with, such as producer schedule changes on Blockchain `B`. This documentation is a work-in-progress; more about internal details will be added over time.
