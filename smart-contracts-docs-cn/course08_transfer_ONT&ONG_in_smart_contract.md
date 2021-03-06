<h1 align="center">How to Transfer ONT/ONG in Smart Contract</h1>
<p align="center" class="version">Version 0.1</p>

## 1. Introduction

| API                          | Return Value  | Description                                       |
| ---------------------------- | ---- | ---------------------------------------- |
| Invoke                 | byte[] |   Invoke a native contract         |
| state | string |      Build transfer structure             |

For more details, you can view the [API-doc](http://dev-docs.ont.io/#/docs-en/DeveloperGuide/smartcontract/05-sc-api) and the [source code](https://github.com/ontio/ontology-python-compiler) here.

### Native contract

|Contract name | Contract Address | 
---|---|
|ONT Token | 0000000000000000000000000000000000000001| 
|ONG Token | 0000000000000000000000000000000000000002 | 
|ONT ID | 0000000000000000000000000000000000000003 | 
|Global Params | 0000000000000000000000000000000000000004 | 
|Oracle | 0000000000000000000000000000000000000005 | 
|Authorization Manager(Auth) | 0000000000000000000000000000000000000006 | 
|Governance | 0000000000000000000000000000000000000007 | 
|DDXF(Decentralized Exchange) | 0000000000000000000000000000000000000008 | 



## 2. Transfer ONT/ONG

`state` is used for building transfer structure.

`Invoke` is used for invoke native contract.

```
from ontology.interop.System.Runtime import Notify, CheckWitness
from ontology.interop.Ontology.Runtime import Base58ToAddress
from ontology.interop.Ontology.Native import Invoke
from ontology.builtins import state

# contract address 
contract_address_ONT = bytearray(b'\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x01')
contract_address_ONG = bytearray(b'\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x02')

def Main(operation, args):
    if operation == 'transfer':
        from_acct = args[0]
        to_acct = args[1]
        ont_amount = args[2]
        ong_amount = args[3]
        return transfer(from_acct,to_acct,ont_amount,ong_amount)
    
    return False


def transfer(from_acct, to_acct, ont_amount, ong_amount):
    # convert base58 address to address in the form of byte array 
    from_acct=Base58ToAddress(from_acct)
    to_acct=Base58ToAddress(to_acct)
    # check whether the sender is the payer
    if CheckWitness(from_acct):
        # transfer ONT
        if ont_amount > 0:
            param = state(from_acct, to_acct, ont_amount)
            res = Invoke(1, contract_address_ONT, 'transfer', [param])
            if res and res == b'\x01':
                Notify('transfer succeed')
            else:
                Notify('transfer failed')
        # transfer ONG
        if ong_amount > 0:
            param = state(from_acct, to_acct, ong_amount)
            res = Invoke(1, contract_address_ONG, 'transfer', [param])
            if res and res == b'\x01':
                Notify('transfer succeed')
            else:
                Notify('transfer failed')
    else:
        Notify('CheckWitness failed')
```

## 3. Transfer ONG to self contract 

```
from ontology.interop.System.Runtime import Notify, CheckWitness
from ontology.interop.Ontology.Runtime import Base58ToAddress
from ontology.interop.Ontology.Native import Invoke
from ontology.builtins import state, bytearray
from ontology.interop.System.ExecutionEngine import GetExecutingScriptHash

# contract address 
contract_address_ONT = bytearray(b'\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x01')
contract_address_ONG = bytearray(b'\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x02')
self_contract_address = GetExecutingScriptHash()

def Main(operation, args):
    if operation == 'transfer_ONG_to_contract':
        from_acct = args[0]
        ong_amount = args[1]
        return transfer_ONG_to_contract(from_acct, ong_amount)
    if operation == 'check_self_contract_ONG_amount':
        return check_self_contract_ONG_amount()
    return False


def transfer_ONG_to_contract(from_acct, ong_amount):
    # convert base58 address to address in the form of byte array 
    from_acct=Base58ToAddress(from_acct)
    param = state(from_acct, self_contract_address, ong_amount)
    res = Invoke(0, contract_address_ONG, 'transfer', [param])
    if res and res == b'\x01':
        Notify('transfer ONG to contract successfully')
    else:
        Notify('transfer ONG failed')
        
        
def check_self_contract_ONG_amount():
    param = state(self_contract_address)
    # do not use [param]
    res = Invoke(0, contract_address_ONG, 'balanceOf', param)
    return res
```

## 4. Contributing 

```
Please feel free to give any suggestion and help us make video better!
Contact: Yue Zhao 
Wechat: 16621171248
Email: messixaviinsta0303@163.com
```
