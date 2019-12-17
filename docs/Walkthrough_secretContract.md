# Writing a secret contract

This walkthrough will take you through the process of initializing a project, creating a secret contract, and writing unit-tests for this contract. 
### Setting Up Project Structure
Start the network with `discovery start`
This should only take 15–20 seconds (unless it is your first time, in which case it may take 3–4 minutes).

Create a brand new Rust library containing our secret contract logic with the following command:
`cargo new secret_contracts/millionaires_problem --lib`

Copy and paste the contents of the `secret_contracts/Cargo.toml.template`file into the `Cargo.toml` file inside the library of `secret_contracts/millionaires_problem` that was just created. 

Under `[dependencies]` add this line: `serde = "1.0.84"`.

The full `Cargo.toml` file should now read:

	
	[package]
	name = "contract"  
	version = "0.1.0"  

	[lib]  
	crate-type = ["cdylib"] 
	
	[dependencies]  
	eng-wasm = { git = "https://github.com/enigmampc/enigma-core.git", branch = "develop" }  
	eng-wasm-derive = { git = "https://github.com/enigmampc/enigma-core.git", branch = "develop" }serde = "1.0.84"

	[profile.release]  
	panic = "abort"  
	lto = true  
	opt-level = "z"
    


### Writing the Secret Contract

Open the `lib.rs` file located at `secret_contracts/millionaires_problem/src/lib.rs`

Delete existing file contents.  Add the following code.

    // Built-In Attributes
    #![no_std]

All of our secret contracts start exactly like this, because the SGX environment does not support all the features of Rust's standard library.  [This attribute](https://medium.com/r/?url=http%3A%2F%2Frust%20import)  removes the standard library from our program’s prelude accordingly.

Now add:

	// Imports
	extern crate eng_wasm;
	extern crate eng_wasm_derive;
	extern crate serde;
	
	use eng_wasm::*;
	use eng_wasm_derive::pub_interface;
	use serde::{Serialize, Deserialize};
	
This brings the following packages into scope:

-   `eng_wasm` — This crate allows us to use the Enigma runtime, offering important functionalities including reading from state, writing to state, and printing.
-   `eng_wasm_derive` — This crate provides functions exposed by the contract that can be called from the Enigma network and the ability to call functions of Ethereum contracts from a secret contract.
-   `eng_wasm_derive::pub_interface` — Gives us the ability to add the  `#[pub_interface]`  trait to any public-facing functions within our secret contract. 
-   `serde::{Serialize, Deserialize}` — Allows us to serialize and deserialize custom struct types.

Next add:

	// Encrypted state keys
	static MILLIONAIRES: &str = "millionaires";
	
Secret contracts can maintain encrypted state. Our contract needs to maintain a list of millionaires in state, which must have an entry `<key, value>` pair that looks something like this: `<"millionaires", <vector of Millionaire structs>>`. As shown, `"millionaires"` is the key into this encrypted state maintaining the list of millionaires.
	
Below the state keys, add:

	// Structs
	#[derive(Serialize, Deserialize)]
	pub struct Millionaire {
	address: H160,
	net_worth: U256,
	}

A millionaire’s metadata consists the following attributes: 
	1. ETH `address` 
	2.  `net_worth`

Note the variable types.  `address` is of type `H160`, which corresponds to a 160-bit/20-byte hash value (recall that ETH addresses are 20 bytes long) and `net_worth` is of type `U256`, which corresponds to a 256-bit/32-byte value. 
	*(For those of you with Solidity backgrounds, you can think of these as `address`and `uint256` types, respectively.)*

Now add:

	// Public struct Contract which will consist of private and public-	facing secret contract functions
	pub struct Contract;

This initializes a public struct called `Contract` which can consist of both private and public-facing functions to be defined later.
	
Add:

	// Private functions accessible only by the secret contract
	impl Contract {
		fn get_millionaires() -> Vec<Millionaire> {
			read_state!(MILLIONAIRES).unwrap_or_default()
		}
	}

This code implements the private methods for the secret contract. `get_millionaires()` returns `Vec<Millionaire>`, a vector of Millionaire structs stored in state. 
*Note how we read from secret contract state with the macro* `read_state!(<key>)`. 

The  `unwrap_or_default()`  [method](https://medium.com/r/?url=https://doc.rust-lang.org/std/option/enum.Option.html#method.unwrap_or_default) returns the contained value, otherwise the default for that type.
	
Next, add:

	// Public trait defining public-facing secret contract functions
	#[pub_interface]
	pub trait ContractInterface{
		fn add_millionaire(address: H160, net_worth: U256);
		fn compute_richest() -> H160;
	}
	
This section declares the public-facing secret contract signatures by creating a public  `trait`  with the  `#[pub_interface]`  macro. 

In this case, there is now a  `trait`  called  `ContractInterface`  and two method definitions:

-   `add_millionaire` — Add/register a new millionaire with an Ethereum address (`H160`) and net worth (`U256`)
-   `compute_richest` — Computes the richest millionaire. This takes no parameters (will only read from state) and returns the richest millionaire’s address, which is of type  `H160`
-   `construct` — This is actually not present in this secret contract, because the contract is not initialized with any data. Therefore, it is left it out entirely, meaning it defaults to the implicit  `construct()`.

Now add:

	// Implementation of the public-facing secret contract functions defined in the ContractInterface
	// trait implementation for the Contract struct above
	impl ContractInterface for Contract {
	    #[no_mangle]
	    fn add_millionaire(address: H160, net_worth: U256) {
	        let mut millionaires = Self::get_millionaires();
        millionaires.push(Millionaire {
	            address,
	            net_worth,
	        });
	        write_state!(MILLIONAIRES => millionaires);
	    }

	    #[no_mangle]
	    fn compute_richest() -> H160 {
	        match Self::get_millionaires().iter().max_by_key(|m| m.net_worth) {
	            Some(millionaire) => {
	                millionaire.address
	            },
	            None => H160::zero(),
	        }
	    }
	}

This section implements the above-defined public-facing secret contract methods. Note the  `#[no_mangle]` [annotation](%28https://medium.com/r/?url=https://doc.rust-lang.org/book/ch19-01-unsafe-rust.html?highlight=no_mangle#calling-an-unsafe-function-or-method%29) above every method definition, which turns off Rust’s automatic method/function name-changing behavior.

-   `add_millionaire` — Read the secret contract state to obtain the list of Millionaire structs (using the private method  `get_millionaires`mentioned above). Push a new  `Millionaire`  struct with the fields initialized to the parameters passed into this method (`address`  and  `net_worth`).  And finally,  update/write to the secret contract state with the macro: `write_state!(<key_1> => <value_1>, <key2 => <value_2>, ..., <key_n> => <value_n>)`.
-   `compute_richest` — Obtain the list of  `Millionaire`  structs stored in contract state. Using a combination of Rust's `match`  and  `max_by_key`  functionalities, return the  `address`  associated with the millionaire with the highest  `net_worth`, otherwise return the empty address  `0x0`.
-   `construct`  —As we do not need our own implementation of a secret contract constructor, this utilizes the default empty  `construct()`  definition, and thus it is left out.

Next, check out our tutorial on compiling, migrating, and unit-testing this contract.