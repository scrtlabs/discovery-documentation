

# An Overview of Enigma 🔍

The Enigma Network offloads **private computation tasks** from Ethereum. These tasks are initiated by the end users of Ethereum dApps. The Enigma protocol provides Enigma-JS, a JavaScript library that gives dApp developers
the tools to:

1. safely encrypt sensitive data in-memory for immediate use or storage
2. create a record of each task on the Ethereum ledger
3. obtain an authoritative cryptographic proof that the target
  worker is securely running trusted hardware (SGX) prior to
   sending data to it and paying the corresponding fees.

A key distinction between the current Discovery release and the last release is that the Enigma Network has its own secret contracts. Instead of writing the business logic of a computation task in a function of an Ethereum smart contract, the dApp author creates a secret contract and deploys it on the Enigma Network. This is more powerful for many reasons; notably, a secret contract may store an encrypted state.

## The Enigma Library
Computation tasks are created by dApp users using the Enigma Library. In the first release of Discovery, only one worker is assigned to a smart contract at any given time. A new worker is randomly assigned to a contract at the beginning of each epoch. Before creating a computation task, the Enigma Library verifies the selected worker using the cryptographic proofs included in its SGX report.

When creating a computation task, the dApp user encrypts all inputs using the
Enigma Library:

1. address of the secret contract
2. function signature
3. input parameters
4. for payable functions, the ERC20 tokens to transfer
5. the computation fee

Then, the Enigma Library creates an Ethereum transaction containing a record of the task. The record simply contains a hash of all inputs and some metadata about the task. It contains as little data as possible to minimize the Ethereum storage fee. The task record is the primary source truth for each task. It will
be used by the Enigma Network to verify inputs and the order of tasks.

## On-Chain Computation

Once the task record transaction is submitted, the Enigma
Library broadcasts the task data to the Enigma Network via its
user node, the Enigma Network node that the dApp user
connects to. The task data message is propagated to each node in the network. Upon receiving the message, the selected worker:

1. verifies the task inputs against the task record
2. computes the task

Since secret contracts are stateful, the selected worker keeps an up-to-date copy of the state in memory. State updates are propagated to all nodes in the form of encrypted state deltas. Each node keeps the state deltas in an ordered list from which the full state can be reconstituted. After each computation, the
results are encrypted and propagated to the network. The Enigma Library watches for the results.

A computation task is only confirmed after verification on Ethereum. After computing a task, the selected worker commits a task receipt linked the task record. Unlike MVT, workers process tasks in batches instead of committing every task to Ethereum immediately. These are the triggers to commit tasks:
1. the current epoch ends
2. the business logic of a task calls an Ethereum smart contract

When either trigger occurs, the worker commits a batch of all
unconfirmed tasks stored locally. Ethereum does not store the tasks results nor the encrypted state deltas, only hashes from which nodes can verify the integrity of their own data.

