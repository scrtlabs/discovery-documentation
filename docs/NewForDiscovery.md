
# What's New in Discovery 🎁

The Discovery release of the Enigma protocol provides the following features:

### Secret State
Secret contracts are capable of maintaining a state. Encrypted data persists across separate private computations or different tasks. Stateful contracts enable many more use cases than stateless computations (like secret tokens).
    
### Secret Contracts are seperate from Smart Contracts
The Enigma network performs computation and storage, using Ethereum only for consensus and a shared ledger. This means that secret contracts have their own runtime (modified EVM) and programming language (Rust, instead of Solidity). 
    
### Private Outputs
In addition to private inputs, the network now supports storage of computation outputs. The contract state is stored (encrypted) on-chain and results can be encrypted directly for the dApp user.
    
### Economic Incentives
Each node receives a financial reward for participating in the network.
    
### Gas Cost Optimization
On Ethereum, gas cost is based on computation units associated with each  [opcode](https://docs.google.com/spreadsheets/d/1m89CVujrQe5LAFJ8-YAUCcNK950dUzMQPMJBxRtGCqs/edit#gid=0). The STORAGEADD opcode is the most expensive at 20k gas for each 32 bytes of data. Secret contracts should be significantly cheaper in gas than they were in the previous MVT release due to the following modifications:

1.  Inputs are not stored in the contract, only the taskId (a hash of inputs). The taskId is generated from inputs in the worker’s enclave to ensure their integrity
2.  Outputs are not stored in the contract. On chain verification of the results still takes place but using a hash of the results.
    
### Ethereum Integration
These features maintain interoperability with Ethereum:

1.  _Token Transfers_: Calling a payable secret contract function may involve an arbitrary ERC20 token deposit. Tokens are locked in the contract state until explicitly transferred to an Ethereum address in the secret contract business logic.
2.  _Function Calls_: Secret Contracts may call arbitrary functions of smart contract deployed on Ethereum.
    
### Contract Attributes
Discovery supports the storage of system attributes (metadata) about each contract. To illustrate their utility, these attributes include but may not be limited to:

1.  A list of workers assigned to each contract which is useful for key management.
2. The contract bytecode (or a reference to it)
3. A mapping between each attribute and its position in the state array.
4. Reference to external storage (like s3 or IPFS) if we build support for sealed data stored externally.
