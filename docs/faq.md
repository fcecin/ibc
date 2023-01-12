---
sidebar_position: 5
---

# FAQ

**FIXME/WIP**

## Does it take long to verify?

Gnome, [11/01/2023 10:50]
it depends on the hardware BPs are running of course. On hardware equivalent to EOS mainnet, it was typically less than 5ms

Gnome, [11/01/2023 10:50]
and less than 1ms with OC enabled

Gnome, [11/01/2023 10:51]
also it doesn't only prove one transfer, it proves the block the action was contained in

Gnome, [11/01/2023 10:51]
then, the light proof verification scheme can verify any block / transaction from genesis to the last proven block

the light proof scheme takes about 300-400 us to verify on EOS mainnet equivalent hardware

Gnome, [11/01/2023 10:52]
block size doesn't really affect the proof size or verification time much

Gnome, [11/01/2023 10:52]
scales log base 2 with the number of actions

## IBC Token seems too slow

It is slow because the current block finalization algorithm of Antelope essentially requires all block producers to perform a round of block production twice in front of a block for that block to be considered final. This is being addressed by the Instant Finality upgrade to Antelope, which will reduce the time to finality on Antelope blockchains from a few minutes to a few seconds.

## The IBC Bridge mechanism seems a little too bare-bones for application use

The IBC Bridge provides a fundamental, primitive function for Antelope blockchains. It is intentionally low-level and simple, to reduce the probability of bugs and exploits.

It is possible to develop higher-level functionality on top of it, such as some sort of RPC (Remote Procedure Call) mechanism for smart contracts. If such functionality is added to the Antelope IBC suite in the future, it will be documented here.

Also, if such a module, or any other useful mechanisms, are developed on top of the Antelope IBC primitives by third-parties (outside of the Antelope IBC working group), they will eventually be listed here in this FAQ.