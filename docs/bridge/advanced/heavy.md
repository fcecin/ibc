---
sidebar_position: 1
---

# Heavy proofs

This section explains the ```heavyproof``` object.

**WIP/FIXME BELOW**

The ```heavyproof``` structure proves the finality of a block on a foreign blockchain, and it contains four named top-level elements in the JSON object:

* ```chain_id```, which identify the chain this proof originates from.
* ```blocktoprove```, which is the block to be proven final.
* ```hashes```, which is an array of all hashes contained in the incremental merkle tree and merkle branches of the proof. 

## blocktoprove:
”, which contains :
the block header
the block producer signature(s)
the previous block merkle root
the incremental merkle tree structure at the previous block height

block_to_prove example : 



      {
        "block": {
          "header": {
            "timestamp": "2022-08-30T17:57:49.500",
            "producer": "eoseouldotio",
            "previous": "01ed944fd5774b924e89eaaa05c6b5f20257fb12f7d41cc3acb1fa283a52932b",
            "transaction_mroot": "5b0cb5a5ed6e7b25977bb0b823b4e0b2aefb3d8f8bd520caa3b9e0d04f8ef0b5",
            "action_mroot": "d227b326007b2704a2fb716ac3886fcffacfeaaaabfea6e2ef84bf6946dfed26",
            "confirmed": 0,
            "schedule_version": 45,
            "new_producers": null,
            "header_extensions": []
          },
          "producer_signatures": [
            "SIG_K1_KjPPNhS2KEAPg8gpk9XXaS27Zqd3fs9TesuAFswk4QpWUvp9ESYGMZbQvkbFCKfa6JtB98QmhJDXcicHRGQ5y9ZNR2XFBx"
          ],
          "previous_bmroot": "2b3e9633189d107d955701e87258a0de8f931620f7d820d548c1a44aae36e3d6",
          "id": "",
          "bmproofpath": []
        },
        "active_nodes": [
          0,
          1,
          2,
          3,
          4,
          5,
          6,
          7,
          8,
          9,
          10,
          11,
          12,
          13,
          14
        ],
        "node_count": 32347214
      }


## BFT proof

the “BFT proofs” (byzantine fault tolerance proofs), an array of structures composed of the following : 
a block header
the block producer signature(s)
the previous merkle root (previous_bmroot)
a list of merkle branches (bmproofpath)

	
Under the current consensus model, a two-thirds supermajority of producers from the current scheduled set confirm each block twice. The first confirmation stage proposes a last irreversible block (LIB). The second stage confirms the proposed LIB as final. At this point, the block becomes irreversible.

The block producer for a given block implicitly confirms their block as valid, meaning 14 additional blocks signed by different block producers must be added to the block to complete a stage.

As an example, consider the standard Antelope configuration of 21 block producers. A valid BFT proof finality round must contain exactly 15 blocks signed by different block producers.

Since the block_to_prove counts for one in the first round, 14 BFT proofs must be added to seal the first round. Similarly, since the last BFT proof  of the first round counts as the first one for the second round, 14 additional BFT proofs must be added to the array, for a total of 28 BFT proofs.

	


BFT proof example :



      {
          "header": {
            "timestamp": "2022-08-30T17:57:54.000",
            "producer": "eosnationftw",
            "confirmed": 240,
            "previous": "01ed9458124eb33ccca5994c5199389e6ebc4d7dcf3a80219e691fee19879bdc",
            "new_producers": null,
            "header_extensions": [],
            "schedule_version": 45,
            "transaction_mroot": "0000000000000000000000000000000000000000000000000000000000000000",
            "action_mroot": "166e0cbd9e48aeac5502c61c12a507b3b099823f5512ad26e73ffdc413a6b83d"
          },
          "producer_signatures": [
            "SIG_K1_Kh49gyGTZRUjYNJZbKbPwvuKDfr5ViWvoFstEQ85y6bNiXYqaEFLh5AMZMjn2tEFgh9EdmHX1THcMCuBCtpt3iN3A9WsX1"
          ],
          "previous_bmroot": "8d89791346fb91942edd7d63ba9337b7c9623332b0e5b0dda6630b1730a02912",
          "bmproofpath": [
            15,
            16,
            17,
            18,
            19,
            20,
            21,
            22,
            23,
            24,
            25,
            26,
            27,
            28,
            29,
            6,
            7,
            30,
            31,
            32,
            33,
            10,
            34,
            35,
            36
          ]
      }




## Hashes

An array of all hashes contained in the incremental merkle tree (see active_nodes above) and merkle branches of the proof (see bmproofpath above). 

Since many of these hashes are repeated throughout the proof, we map them using uint16_t indices instead, achieving a lossless compression ratio of between 20% and 80% depending on the block height.

hashes array example :



      [
        "f71f3caee388324520ecc34f3e1cc43068127aa1e2c7339db518094e69152490",
        "9c1b482433c921268cc0d35aa44cafa5727eece14b12ee419139e0e6c8c539c8",
        "f81e478eb2e76a7250689b0176d2e6099f20641a793c81278fdf2f9811ac32e6",
        "dec06b5072c38b764f50f0f36d71dfccfd034368c9804b72ce337edcc2e6c690",
        "84bf55aeebb5c05b8b2cbde58492bb3b201fff24501a84f0f71468a39343ae90",
        "d61cdcc9ec20c57acd35e9ccf00806d96ef347b6574fbe20d2a1039cfd52c07e",
        "7dd7116d4809bc6ff3bad85b7d83ac839b98e33f35c29c69a99cd9ecd5fa86cd",
        "6d2ac89b1d64d02dfa927db3c843896ebd4a03bd64cba239ce1386ef0d8a3c4e",
        "ef4daefc75f02402241cf0c4ff1e2005b2bf386c4de97ad138b73a767b087e78",
        "aa453c41aa2f3878c8ad949f76245255f5016b26f2ab3986e409e325eecee51f",
        "521023196e65a05183e7f91a5cefdc2ebb8fa3f265bb7f05792968fb1553fa63",
        "b259b8a5e302122fde20aed648f896ead3ee8cdaa397f7408b494ad5cda30263",
        "90ae8df1f23ab2c80276e7e1c30a32be45913f4c02a8ce3663745d16d12e7507",
        "b2320e40f0ac29475fede29002678a667f39664ff0891c406d64b4c7573dbd20",
        "9db19461c8416b3ad6c0352c09cc3a43d7c04ef6724f201c9268aa59d2e5f0f8",
        [...]
      ]




