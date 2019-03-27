
# Secret Contract Engine 🚂

Secret contracts are written in Rust and compiled to  [WebAssembly](https://webassembly.org/)  (abbreviated Wasm). The contract bytecode is stored unencrypted on Enigma with a hash on Ethereum. The hash of the bytecode will be used as its address. Benefits of this stack include:

1.  Well suited to execute general purpose tasks (e.g. machine learning algorithms) compared to a smart-contract-specific language like Solidity.
2.  Existing Parity pwasm implementation that can be used as a reference or partially reused.
3.  Contracts will be written in Rust which is consistent with our stack and that of other blockchains.
4.  Using a general purpose language and execution environment enables us to extend the smart contract features simply by creating new libraries. For example, we could create a library which executes web queries directly in the secret contract business logic.

### Contract Architecture

-   We use wasmi - Parity WASM interpreter, which allows us to run general-purpose Wasm code. Baidu already did an adaptation of wasmi to SGX.
-   We create Enigma runtime module for handling all state operations, and eng_wasm, which is an implementation of bindings to Enigma runtime.
-   As required by wasmi, we create ImportResolver, which maps all functions that runtime supports to the corresponding contract import entries.
-   We decided to use Solidity ABI format for input/output for the following reasons:
    -   Pwasm implementation uses Solidity ABI as well and may be reused.
    -   Ability to preserve the current functionality with Solidity contracts that use Solidity ABI.
    -   Despite the restrictions of the Solidity ABI types, the new version V2 should support more types, for example structs

### Technical Specification

1.  Structure
    
    > 1.  For usage of runtime predefined module eng_wasm should be imported, which defines all operations on state.
    > 2.  Constructor function to be called upon contract creation
    > 3.  Functions
    
2.  Other requirements:
    
    > 1.  Users should not write a “main” call function in contract (like in pwasm).
    > 2.  Contract-to-contract calls are not allowed except when at the end of the execution we want to call a contract on Ethereum. For this we can re-use what we did for a callback in MVT or what Parity does for intercalls.
    >     
    > 3.  Gas calculation:
    >     
    >     > 1.  We intend to reuse Parity mechanism of injection of the gas calculation into a contract.
    >     > 2.  We prefer to build a mechanism that executes the computation while there is enough gas, and stops the execution otherwise.
    >     > 3.  If this is difficult, maybe it’s ok to do the gas calculation after the computation (although with some reasonable limits - halting problem)
    >     
    
3.  Compatibility: We decided to preserve the current functionality with Solidity contracts
    
    > 1.  The Solidity contract compiled to EVM will be deployed on ethereum and on Enigma.
    > 2.  For stateful functions users will create regular ethereum call transactions.
    > 3.  For stateless functions there will be an additional option: to call the function through Enigma network as it is done in the case of Wasm contract. Since in both cases we use Solidity ABI, the call seems the same from the network perspective.
    > 4.  The challenge: in the case of Solidity contract it is important not only to return results of the call to the user, but also to commit them on ethereum (for example in the case of secret voting).
