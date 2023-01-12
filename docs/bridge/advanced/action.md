---
sidebar_position: 3
---

# Action proof

This section explains the ```actionproof``` object.

The action proof is composed of

* ```action```: an action invocation,
* ```receipt```: the matching action receipt,
* ```amproofpath```: the merkle branches connecting the action receipt digest with the action_mroot field of the header,
* ```returnvalue```: an optional return value, if the ```ACTION_RETURN_VALUE``` feature has been enabled on the blockchain.

Example of an action proof object:

```
{
      "receipt": {
        "abi_sequence": "1",
        "act_digest": "3cd8e44278cfd0ca110df21eecee2fb6608cba2c4c02e3b7529c0a1f0c860516",
        "auth_sequence": [
          {
            "account": "eosmechanics",
            "sequence": "1030721"
          }
        ],
        "code_sequence": "1",
        "global_sequence": "36812442",
        "receiver": "eosmechanics",
        "recv_sequence": "1030715"
      },
      "action": {
        "account": "eosmechanics",
        "name": "cpu",
        "authorization": [
          {
            "actor": "eosmechanics",
            "permission": "benchmark"
          }
        ],
        "data": ""
      },
      "amproofpath": [
        "d924045f9f815ab364a576442d8dfaa6939b18f182832bca5c8d516dbdfcd151",
        "6d032916f48f33ca6df571a5316c96affefd8410e34d2855358cb3c9b52080e2"
      ],
      "returnvalue": ""
}
```

