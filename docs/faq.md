---
sidebar_position: 5
---

# FAQ

## Performance

### Q: What is the amount of CPU time consumed by verifying a bridge block heavy proof?

A: It depends on the hardware that block producers are running. On hardware equivalent to the EOS mainnet (2023), it could be seen as being typically less than 5ms CPU time, and less than 1ms CPU time with OC enabled.

It is important to notice that this overhead is for proving an entire block that e.g. contains an action to prove. After one heavy proof of a block is provided, light proof verification can be used to verify any block and action from genesis to that most recent block for which a heavy proof of finality was processed by the bridge. The light proof takes about 300 to 400 microseconds to verify on block production hardware equivalent to that deployed on the EOS mainnet (2023).

### Q: How are the block proof verification costs affected by block size?

A: Block size doesn't really affect the proof size or proof verification time significantly, as it scales log base 2 with the number of actions in the block.

### Q: Why does an IBC Token transfer take two minutes?

A: IBC Token, and any current use of IBC Bridge really, is currently slow because the current block finalization algorithm of Antelope essentially requires all block producers to perform a round of block production twice in front of a block for that block to be considered final. This is being addressed by the Instant Finality upgrade to Antelope, which will reduce the time to finality on Antelope blockchains from a few minutes to a few seconds.

### Q: Why does IBC Token transfers consume RAM?

A: To eliminate the risk of loss to private property (i.e. tokens), which is classed as a security risk, the default implementation of the IBC Token bridge does not perform the garbage collection of processed action receipts, thus opting for the maximally-hardened security choice in its default design. This means that every time there is an IBC Token transfer, a small amount of RAM bytes (to store a hash, essentially) are permanently spent by the involved accounts.

Garbage collection of these receipts is technically easy to do, but the tradeoff is the possibility of token loss after some governance-established timeout (e.g. weeks, months, years without the user finishing an initiated token transfer). Thus, this garbage collection procedure has to be an explicit governance decision of each blockchain that deploys the Antelope IBC suite. This is a simple technical change that can be implemented after deployment of the default solution, and once done it can clear any and all receipts ever processed (i.e. all spent RAM can be recovered).

## Capabilities

### Q: Why doesn't the IBC Bridge look like a Remote Procedure Call (RPC) feature?

A: The IBC Bridge provides the essential IBC primitives for Antelope blockchains that cover all cases of trustless communication between two Antelope smart contracts that are on two different blockchains. It is designed to be as low-level and simple as possible, to reduce the surface for security problems.

It is possible to develop higher-level IBC functionality on top of the block and action proof primitives, such as some sort of IBC RPC service for smart contracts to call each other's actions across different blockchains. If such a module, or any other useful, general-purpose or application-specific higher-level mechanisms are developed on top of the Antelope IBC primitives, they will be referenced in future versions of this documentation.
