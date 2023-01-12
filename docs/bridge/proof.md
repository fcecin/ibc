---
sidebar_position: 3
---

# Proof objects

This section introduces proof objects.

## Serialization

The C++ contract declaration of the ```heavyproof```, ```lightproof``` and ```actionproof``` data structures is a bit complex, with multiple composite, nested data structures and lists, as can be examined in the C++ source code for the IBC Bridge contract (i.e. the ```bridge.cpp``` and ```bridge.hpp``` modules).

However, in the Antelope smart contract environment, these proof objects are provided by users as flattened, serialized JSON (i.e. text) objects. These text objects end up inside some signed user transaction that is then sent by user or user-level DApps to the blockchain, to be consumed by an API of the bridge contract. At that point, the JSON text object is transformed by blockchain nodes into the binary proof objects (for use inside their virtual machines), which is the format that the C++ contract code for them describes.

## Getting JSON proof objects

Blockchain applications that need to obtain proof objects to provide to the bridge's proof-checking actions should, ideally, get those JSON objects (i.e. text strings, essentially) by calling a service/API that provides those, or calling a library that is linked to the relevant application module that knows how to assemble these objects given some source of blockchain data.

Currently, applications have the following alternatives to generate or obtain proof objects:

* Consult their specific Antelope blockchain documentation and support channels for available services/APIs that provide IBC proof objects ready for consumption by applications, such as (but not limited to) an open, running instance of the [proof server](proofserver);

* Directly deploy and use the [proof server](proofserver) that is provided as part of the Antelope IBC suite;

* Develop their own libraries or services, using the proof data structure documentation and Antelope IBC contract and samples source code repositories as the reference, as well as the Antelope IBC developer support groups and the support groups of specific Antelope blockchains.

As libraries and services to provide proof objects are developed and deployed, we will list them in this documentation. If you are a developer or operator of these tools, please contact us for its inclusion in this documentation.
