# What is Enigma? 🤔
Enigma is a decentralized network that enables **Secret Contracts**. This means you can create decentralized applications with end-to-end data privacy and strong correctness guarantees. 

 - Data inputs, and outputs are encrypted throughout. Even the nodes running the computation **can't see the data**. 
 - Enigma is **Ethereum compatible**. Secret Contracts can call any function inside a smart contract on Ethereum.

**Ready to dive in?**

- Get started quickly with our Quick Start guide
- Build your first secret contract
- Check out some ideas for what to build with Enigma

## Overview 👁️
[insert diagram here]
This diagram illustrates how data flows in an Enigma-based application.

### Data Flow

 1. A secret contract function call is considered a single private computation or [task](http://enigma.co/protocol-discovery/glossary.html#task). Secret contracts are registered at deployment on the Enigma Contract (on Ethereum), which the Enigma network watches for new tasks. 
 2. These tasks are initiated (and paid for) by the end users of Ethereum dApps and are then offloaded from Ethereum into the Enigma network for computation. 
 3. The Enigma network then selects a [worker node](http://enigma.co/protocol-discovery/glossary.html#worker) to execute the given task in a modified Web Assembly interpreter (WASMI) running inside a Trusted Execution Environment (TEE) using Intel Software Guard Extensions (SGX) technology (also known as an [enclave](http://enigma.co/protocol-discovery/glossary.html#enclave)). 
 4. After computing a task, a cryptographic proof of the completed task is recorded, and eventually verified, on the Ethereum network.

## Enigma and Ethereum

Enigma and Ethereum work together, and separate computation and storage from consensus.

### Enigma: Compute and Storage Layer 

 - **Secret Contract Engine**: Executes computations.
 - **Peer-to-peer Network**: Propagates messages in the Enigma network.
 - **State Management**: Stores and manages the state.
 - **Key Management**: Stores and manages encryption, decryption and signing keys.
 

### Ethereum: Consensus Layer 
 -   **Shared Ledger**: Stores the worker registry, task records and proofs of completed tasks.
 - **Consensus**: Agreement on task order and confirmation status.



**Key Concepts** 🤞

 - Secret Contract 
 - Task 
 - Enigma Contract  
 - Enigma JS Library  
 - Worker Node

## What can I do with Enigma? 🛠️
The **Discovery** release of Enigma has plenty of exciting new features, including **secret state**, Rust-based secret contracts, private outputs, and more. Some of the applications we're excited about include:

 - *Access-control for data* (like price predictions or a pointer to an
   IPFS file)
 - Games where your *"pocket cards"* must stay secret (like Gin
   Rummy or Poker)
 -  *Private voting,* without a commit-reveal scheme
   (actually, any use-case with a commit-reveal pattern can be
   streamlined with Enigma!)
 - *Secret inputs* to a credit-scoring algorithm
 - *Sybil-prevention via social verification*-- without exposing your
   user's data!