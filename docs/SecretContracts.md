# Secret Contracts 🤫

[Secret contracts](Glossary.md) are self-executing contracts with encrypted inputs and outputs. Once deployed on the Enigma network, they are called by the end-users of decentralized applications and executed by Enigma nodes.

Enigma Secret Contracts are written in Rust. If you are not familiar with Rust, check out our walkthrough (which will also take you through developing your first Secret Contract). 

**We highly encourage the [walkthrough](SecretContractWalkthrough.md) for first-time users.** 🤞


## Enigma Secret Contract (ESC) Development Environment 🌱

The ESC environment includes `eng_wasm` crate, which implements: 

 1. A bridge to Enigma runtime; 
2. Utilities for generating code that implements calls to the exported ESC’s functions and calls from ESC to Ethereum contracts. 

In the future this crate will be populated through crates.io, and then ESC may be created and built on any client satisfying the following requirements:

-   Nightly Rust language installed
-   `wasm32-unknown-unknown` target installed ([link](https://www.hellorust.com/news/native-wasm-target.html))
    
This means that once you have written your secret contract as per the guidelines below, you will compile it with the following command:
`cargo build --release --target wasm32-unknown-unknown`

Which will put the compiled library under:  
`target/wasm32-unknown-unknown/release/contract_name.wasm`

Today the easiest way to create ESC is to create it as part of enigma-core-internal project. There is a directory called [examples](https://github.com/enigmampc/enigma-core-internal/tree/develop/examples/eng_wasm_contracts) under the root directory. Here you can see the examples of the existing ESCs and here you can add your own examples.

  

ESC should be Rust dynamic library crate:

-   It should contain `src/lib.rs` file as its main file
-   In `Cargo.toml` the following should be defined: `[lib]
crate-type = [“cdylib”]`


[Reference](https://doc.rust-lang.org/reference/linkage.html): This section instructs the compiler to produce a dynamic system library to be loaded from another language. This output type will create `*.so` files on Linux, `*.dylib` files on macOS, and `*.dll` files on Windows.

Additional requirements:
-   At the root level add `.cargo/config` with the following content:
    

`[build]
target = “wasm32-unknown-unknown”`

  

-   `Cargo.toml`:

```
[dependencies] eng_wasm = { path = “../../../eng-wasm”]
eng_wasm_derive = { path = “../../../eng-wasm/derive” ]
[profile.release]
panic = “abort”
lto = true
opt-level = “z”
```
