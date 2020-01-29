# Discovery FAQ

## Data
### What is the size limit for data input to a secret computation?
Currently, the size limit is 1 MB. We expect this number to be larger very soon. 

### Does encrypted data have to be passed through an Ethereum contract before it can be computed on?
The Discovery release uses "Task Records" to track computation tasks on Ethereum. A Task Record is a hash of all the inputs to a secret contract. It takes up significantly less space than raw data would. These hashed Task Records are submitted to Ethereum at the same time as the encrypted Task data is submitted to Enigma. 

### Can encrypted data be stored on the Enigma network?
Yes, Secret Contracts are stateful in the Discovery release. 
  
  
## Nodes

### How does Enigma protect against side-channel attacks like Foreshadow and Spectre?
This is a class of attack deployed against Trusted Execution Environments. In addition to Intel's protections against these attacks, Enigma provides the following features:

1. Randomly selected nodes: A worker is never guaranteed to be selected for a task, increasing the cost and decreasing the potential value of executing a side-channel attack. 

2. Enigma Attestation Process: Enigma has streamlined a version of the Intel Attestation service for nodes. 

3. Local consensus: nodes agree on the result of a computation, ensuring that a worker is not returning an incorrect result. (NOTE: this is not part of the initial Discovery Developer Release)


### How is data shared between nodes?
In the current version of Enigma, state is physically stored in the memory of every node. When the state is updated, EnigmaP2P is responsible for ensuring that update is passed to all participating nodes.

## Ethereum

### Will the Discovery release be on a public Ethereum testnet i.e., Rinkeby/Ropsten/Kovan?
Yes, we will launch Discovery on a public testnet prior to mainnet launch. However, the developer testnet release is only deployed locally. 

### Will we be able to swap tokens for ENG easily to facilitate payment?
Supporting token abstraction is a high-priority goal, but is not included in the Discovery developer release. 
  

## Secret Contracts

### Will Discovery contracts be forward-compatible 
Yes. Future iterations of the network will also support Rust-based contracts, and we are aiming for forward compatibility in all contracts.


### What languages are supported for secret contracts?
Secret contracts are written using Rust.
  

## Oracles

### How do I get oracle data into a computation?
We currently do not support TLS or HTTP requests within a computation. We suggest that developers utilize a relayer model where applicable to obtain and submit this type of data. 
