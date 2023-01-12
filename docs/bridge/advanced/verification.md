---
sidebar_position: 7
---

# Proof verification

This section explains how the proofs are verified.

## Heavy proof verification

When a heavy proof is submitted to the bridge contract, its components are tested against the block producer schedule held in the contract RAM.

First, the block to prove is verified:

* The block header is serialized and hashed to produce the header digest;
* The ID of the block to proveis calculated by incrementing the first 8 bytes of the ```previous``` field of the header by 1, and combining this value with the header digest;
* The schedule version in the block to prove header must match the schedule version held in the contract RAM;
* The header digest is concatenated and hashed with the ```previous_bmroot```, and the resulting hash is concatenated and hashed with the schedule hash from the contract to obtain the digest to sign;
* The contract then verifies that the signature supplied for the block to prove matches the digest to sign and the public key of the block producer from the schedule held in RAM.

These steps allow us to verify the integrity of the block header, the authenticity of the block producer signature for that block, as well as presence of that block producer in the elected producer schedule for that block. 

Then, the contract verifies the BFT proofs. For each BFT proof:

* The schedule version in the BFT proof header must match the schedule version held in the contract RAM;
* The header of the block to prove is hashed, and is combined with the ```previous_bmroot``` and with the schedule hash to obtain the digest to sign;
* The contract then verifies that the signature supplied for the BFT proof matches the digest to sign and the public key of the block producer from the schedule held in RAM.

In addition, the contract also verifies: 

* That the ```bmproofpath``` of each BFT proof is correct. The ```bmproofpath``` is a list of merkle branches that cryptographically connects the block to prove's ID with the first BFT proof merkle root, the first BFT proof ID with the second BFT proof merkle root, the second BFT proof ID with the third BFT proof merkle root, etc., ensuring that the BFT proofs are all part of the same chain, which includes the block to prove (see [Proof of inclusion](#proof-of-inclusion) below),
* That the number of BFT proofs is equal to 2/3 block producers in the schedule times 2 rounds (that yields 28 for a chain with 21 block producers),
* That the BFT proofs are in sequential block number order,
* And that each of the two rounds of proofs references the correct number of blocks, each signed by different block producers.

These steps allow us to verify that the block to prove has achieved irreversibility and that it is included in the chain, validated by a super majority of block producers.

## Light proof verification

The light proof verification process first verifies that the merkle root provided in the proof matches the root that is stored in the contract state (from a previous, successful heavy proof verification).

It then computes the block ID by combining the header digest and by incrementing by 1 the first 8 bytes of the ```previous``` field of the header, which represents a merkle leaf.

It then verifies the proof of inclusion of the merkle leaf in the merkle tree described by the root and by the merkle branches (see [Proof of inclusion](#proof-of-inclusion) below).
 
## Action proof verification

When an action proof is supplied alongside a block heavy proof or a block light proof, the smart contract attempts to verify its inclusion into the action merkle tree for the block.

This is done by first calculating the action digest, and making sure it matches the ```act_digest``` field of the action receipt.

Then, the action receipt is hashed, and we verify its inclusion in the merkle tree represented by the merkle root (block header ```action_mroot``` field) and merkle branches (```amproofpath```).

## Proof of inclusion

The proof of inclusion algorithm is a standard merkle proof algorithm, with the difference that it uses Antelope’s canonical bit masking, where left nodes have their first bit flipped to zero and right nodes have their first bit flipped to one.

A merkle leaf is supplied, as well as a merkle root and a list of merkle branches. The algorithm will then attempt to hash the leaf with the first branch, and then will hash that result with the next branch, and so on and so forth, until all the branches have been processed.

It will then compare the resulting hash with the merkle root. If the resulting hash matches the root, the proof is valid. If the hash doesn’t match the root, the proof is invalid and the contract will throw an exception.
