---
sidebar_position: 4
---

# Proof server

The proof server is a sample Node application server that shows one way the finalized block and action proof objects can be generated and served for consumption by the IBC Bridge contract.

The proof server can be downloaded from its [source repository on Github](https://github.com/CryptoMechanics/firehose-webserver-ibc-prod). The repository's README file contains instructions for installation, configuration and operation.

Not that the proof server expects to connect to three other running services:

* A running [lightproof-db](https://github.com/CryptoMechanics/lightproof-db) instance,
* An API node for the Antelope blockchain it is providing proofs for, and
* A [Firehose](https://firehose.streamingfast.io/) service, which provides block data from the Antelope blockchain it is providing proofs for.

