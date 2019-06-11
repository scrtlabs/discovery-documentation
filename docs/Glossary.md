
# Attestation

A process by which a piece of software can prove that is running in a secure container ([enclave](Glossary.md#enclave)) on trusted hardware. This proof is a cryptographic signature, that certifies the hash of the secure container’s contents.

Attestation can be  _local_, if two enclaves communicate with each other on the same host, or  _remote_  if it is attesting to a remote entity that it is trusted, and thus establishing an authenticated communication channel with that entity.

# Batch
A set of tasks within an  [epoch](Glossary.md#epoch)  that do not modify the state in the Ethereum network.

# dApp User
An end=user of the Enigma network, responsible for initiating a  [task](Glossary.md#task)  on the network. These users must:

> -   Have an Ethereum account
> -   Interface with the Enigma network through the Enigma JS Library

# Enclave

An enclave is a private region of memory protected from processes running at higher privilege levels. Enclaves are only found in specific hardware that implements Trusted Execution Environments (TEE), like Intel Software Guard Extensions (SGX). An enclave is a critical component of a  [worker](Glossary.md#worker)  node. Any Secret Node must meet these hardware requirements.

# Epoch

A unit of time in the Enigma network in which a worker node is assigned to work on a set of secret contracts does not change, and in which the state encryption keys do not change.

# Guarded (Code):

Code that runs inside an [enclave](Glossary.md#Enclave), and executes with privacy and correctness guarantees. Guarded code typically has an [unguarded](Glossary.md#Unguarded) counterpart.


# Secret Contract

Secret contracts are smart contracts that provide confidentiality guarantees. The contract functions are executed over encrypted inputs and outputs.

Whereas smart contracts are defined as  _self-executing contracts with the terms of the agreement between parties being directly written into lines of code_, secret contracts add privacy to the data submitted to these contracts. While the code of secret contracts remains public and auditable, the inputs and outputs to the contract are encrypted and hidden from public view. Secret contracts thus provide data privacy and verifiable computation. 

Secret contracts exist across a distributed, decentralized blockchain network (_Enigma_) and permit trusted and privacy-preserving transactions and agreements to be carried out among disparate, anonymous parties without the need for a central authority, legal system, or external enforcement mechanism.

# Task

A single computation, also defined as a secret contract function call.

# Task Receipt

A publicly verifiable proof that a  [task](Glossary.md#task)  has been computed by an uncompromised enclave with all intended inputs.

# Task Record

The record of a  [task](Glossary.md#task)  stored on the Ethereum network for the purposes of:

1.  Ordering
2.  Fee Payment
3.  Token deposits (for payable functions)

# Unguarded (Code):

Code that executes outside of an  [enclave](Glossary.md#enclave), providing an interface between the code that runs inside the enclave ([guarded](Glossary.md#guarded)) and the outside world. Unguarded code does not provide neither correctness nor privacy guarantees.

# User Node

Node from the Enigma network through which a dApp user interfaces with the network. The user node brokers data flows between the dApp user, the worker node and other network participants using the peer-to-peer network protocol. dApp users can freely choose their user nodes through which they interact with the network. It may happen that the user node becomes the worker node for a given taks, but only if it happened to be randomly selected by the network.

# Worker or Worker Node

Secret Node of the Enigma network selected to execute a  [task](Glossary.md#task)  inside an  [enclave](Glossary.md#enclave), characterized as follows:

-   A task is only assigned one worker insofar as the worker meets the task’s service level agreement
-   A contract is only assigned one worker per epoch insofar as the worker successfully executes the tasks assigned.
