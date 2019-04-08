# Reference Documentation 📖


### Enigma Smart Contract and Library
This repository includes the JavaScript Enigma Library, which provides the interface that secret contract and dApp developers use to interact with the Enigma protocol. The source code is written in ECMAScript 6 (ES6) and comes prebundled with Webpack, and is exported in UMD format to provide universal functionality. The repository also includes more than 100 unit tests that both provide up to 98% code coverage, as well as comprehensively describes the functions provided both by the Enigma Library and the Enigma Contract.

This repository also includes the Enigma smart contract written in Solidity, which implements the consensus layer for the Discovery release of the Enigma Protocol. Due to the limitations of EVM (Etherium Virtual Machine) as well as the complexity of the contract itself, several components have been broken down into modular contracts and libraries that come together into what is referred to as the *Enigma Contract*.  One instance of this contract will be first deployed on the Ethereum testnet, and once more later on mainnet release.

### Enigma Core
The code of this repository, as its name would imply, lies at the core of the Enigma Protocol. Written in Rust, it includes the code that runs both the inside (trusted) of the enclave and the outside (untrusted), and is responsible for interfacing with the rest of the Enigma Network. This repository also includes the Principal Node that runs inside a trusted enclave, and manages the keys to encrypt the state for all contracts in each epoch. It responds to legitimate requests from all other enclaves in the Enigma network for the encryption keys assigned to any such nodes. 

Both the enclaves and the principal node come packaged with support for 'simulation mode'. This mode allows for dApp developers to test secret contracts and interact with the libraries in a non-live environment without Intel SGX enabled hardware, thus allowing for easier cross-platform and legacy support. Please note that once the Enigma network has been deployed on testnet and mainnet, only enclaves running in hardware mode (on hosts with SGX enabled) will be able to join the Enigma network.

### Enigma Peer-to-Peer (P2P)
Written in Javascript and forking off libp2p, this repository provides each of the above described enclaves with the peer-to-peer network functionality, thus forming the decentralised network. While both Enigma-Core and Enigma-P2P typically run on the same host, they are divided into two separate repositories for two reasons: provide very different functionality, and are coded in different languages (Rust and Javascript, respectively). Enigma-P2P runs in Node.js and provides a command line interface (CLI), although it is most commonly initialised automatically from a configuration file.

The Enigma-P2P packages various autonomous runtimes that communicate between each other through a main controller. This communication is facilitated via both bi-directional message-sending tunnels called *channels*, as well as through *actions*, which are predefined messages that have a specific function attached to them. This infrastructure is optimized for asynchronous communication that reacts in real-time to notifications sent between the various components.

This repository organizes its code into various cargo crates, providing several different components: the Enigma implementation of the WebAssembly (WASM) interpreter in Rust, the Enigma Runtime, cryptographic (AES, ECDSA, ECDH) and hashing (Keccak256) libraries, as well as the Remote Attestation client.
