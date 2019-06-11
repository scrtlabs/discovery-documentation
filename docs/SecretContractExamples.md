# Examples ⛷️

## Millionaire's Problem

```
#![no_std]
#![allow(unused_attributes)] // TODO: Remove on future nightly https://github.com/rust-lang/rust/issues/60050




extern crate eng_wasm;
extern crate eng_wasm_derive;
extern crate rustc_hex as hex;
#[macro_use]
extern crate serde_derive;
extern crate serde;

use eng_wasm::*;
use eng_wasm_derive::pub_interface;
use serde::{Serialize, Deserialize};

// State key name "millionaires" holding a vector of Millionaire structs
static MILLIONAIRES: &str = "millionaires";

// Struct representing a Millionaire
#[derive(Serialize, Deserialize)]
pub struct Millionaire {
    address: H256, // field containing 32 byte hash type for millionaire's address
    net_worth: U256, // field containing 32 byte uint for millionaire's net worth
}

// Public-facing secret contract function declarations
#[pub_interface]
pub trait ContractInterface{
    fn add_millionaire(address: H256, net_worth: U256);
    fn compute_richest() -> H256;
}

pub struct Contract;

// Private functions accessible only by the secret contract
impl Contract {
    // Read secret contract state to obtain vector of Millionaires (or new vector if uninitialized)
    fn get_millionaires() -> Vec<Millionaire> {
        match read_state!(MILLIONAIRES) {
            Some(vec) => vec,
            None => Vec::new(),
        }
    }
}

impl ContractInterface for Contract {
    // Add millionaire with 32-byte hash type for address and 32-byte uint for net worth
    #[no_mangle]
    fn add_millionaire(address: H256, net_worth: U256) {
        // Read state to get vector of Millionaires
        let mut millionaires = Self::get_millionaires();
        // Append a new Millionaire struct to this vector
        millionaires.push(Millionaire {
            address,
            net_worth,
        });
        // Write the updated vector to contract's state
        write_state!(MILLIONAIRES => millionaires);
    }

    // Compute the richest millionaire by returning the 32-byte hash type for the address
    #[no_mangle]
    fn compute_richest() -> H256 {
        // Read state to get vector of Millionaires and obtain the struct corresponding to the
        // richest millionaire by net worth
        match Self::get_millionaires().iter().max_by_key(|m| m.net_worth) {
            // Return millionaire's address
            Some(millionaire) => {
                millionaire.address
            },
            // Return empty address
            None => U256::from(0).into(),
        }
    }
}
```

## Calculator

```
#![no_std]
#![allow(unused_attributes)] // TODO: Remove on future nightly https://github.com/rust-lang/rust/issues/60050



extern crate eng_wasm;
extern crate eng_wasm_derive;

use eng_wasm::*;
use eng_wasm_derive::pub_interface;

#[pub_interface]
pub trait ContractInterface{
    fn add(a: U256, b: U256) -> U256;
    fn sub(a: U256, b: U256) -> U256;
    fn mul(a: U256, b: U256) -> U256;
    fn div(a: U256, b: U256) -> U256;
}

pub struct Contract;
impl ContractInterface for Contract {
    #[no_mangle]
    fn add(a: U256, b: U256) -> U256 {
        let res = a.checked_add(b);
        match res {
            Some(r) => r,
            None => panic!("addition overflow"),
        }
    }

    #[no_mangle]
    fn sub(a: U256, b: U256) -> U256 {
        let res = a.checked_sub(b);
        match res {
            Some(r) => r,
            None => panic!("subtraction overflow"),
        }
    }

    #[no_mangle]
    fn mul(a: U256, b: U256) -> U256 {
        let res = a.checked_mul(b);
        match res {
            Some(r) => r,
            None => panic!("multiple overflow"),
        }
    }

    #[no_mangle]
    fn div(a: U256, b: U256) -> U256 {
        let res = a.checked_div(b);
        match res {
            Some(r) => r,
            None => panic!("division by zero"),
        }
    }
}
```

## Voting

```
#![no_std]
#![allow(unused_attributes)] // https://github.com/rust-lang/rust/issues/60050

extern crate eng_wasm;
extern crate eng_wasm_derive;
extern crate rustc_hex;
#[macro_use]
extern crate serde_derive;
extern crate serde;

use eng_wasm::*;
use eng_wasm_derive::pub_interface;
use eng_wasm_derive::eth_contract;
use eng_wasm::String;
use std::collections::HashMap;
use rustc_hex::ToHex;

// VotingETH contract abi
#[eth_contract("VotingETH.json")]
struct EthContract;

// State key name "polls" holding a vector of Poll structs
static POLLS: &str = "polls";
// State key name "voting_eth_addr" holding eth address of VotingETH contract
static VOTING_ETH_ADDR: &str = "voting_eth_addr";

// Public-facing secret contract function declarations
#[pub_interface]
pub trait ContractInterface{
    fn construct(voting_eth_addr: H160);
    fn cast_vote(poll_id: U256, voter: H256, vote: U256);
    fn tally_poll(poll_id: U256);
}

pub struct Contract;

// Private functions accessible only by the secret contract
impl Contract {
    // Read secret contract state to obtain vector of Poll structs (or new vector if uninitialized)
    fn get_polls() -> HashMap<u64, HashMap<String, U256>> {
        read_state!(POLLS).unwrap_or_default()
    }

    // Read voting address of VotingETH contract
    fn get_voting_eth_addr() -> String {
        read_state!(VOTING_ETH_ADDR).unwrap_or_default()
    }
}

impl ContractInterface for Contract {
    // Constructor function that takes in VotingETH ethereum contract address
    #[no_mangle]
    fn construct(voting_eth_addr: H160) {
        let voting_eth_addr_str: String = voting_eth_addr.to_hex();
        write_state!(VOTING_ETH_ADDR => voting_eth_addr_str);
    }

    // Cast vote function that takes poll ID, voter address, and vote - calls back to ETH
    #[no_mangle]
    fn cast_vote(poll_id: U256, voter: H256, vote: U256) {
        let mut polls = Self::get_polls();
        {
            let voter_info = polls.entry(poll_id.as_u64()).or_insert_with(HashMap::new);
            let key: String = voter.to_hex();
            assert!(!(*voter_info).contains_key(&key), "user has already voted in poll");
            (*voter_info).insert(key, vote);
        }
        write_state!(POLLS => polls);
        let voting_eth_addr: String = Self::get_voting_eth_addr();
        let c = EthContract::new(&voting_eth_addr);
        c.validateCastVote(poll_id);
    }

    // Tally poll function that takes poll ID - calls back to ETH
    #[no_mangle]
    fn tally_poll(poll_id: U256) {
        let polls = Self::get_polls();
        let mut tallied_quorum: U256 = U256::zero();
        if let Some(voter_info) = polls.get(&poll_id.as_u64()) {
            for val in voter_info.values() {
                tallied_quorum = tallied_quorum.checked_add(*val).unwrap();
            }
            tallied_quorum = tallied_quorum.checked_mul(100.into()).unwrap().checked_div(voter_info.len().into()).unwrap()
        }
        let voting_eth_addr: String = Self::get_voting_eth_addr();
        let c = EthContract::new(&voting_eth_addr);
        c.validateTallyPoll(poll_id, tallied_quorum);
    }
}
```

## Coin Toss
```
#![no_std]



extern crate eng_wasm;
extern crate eng_wasm_derive;

use eng_wasm::*;
use eng_wasm_derive::pub_interface;

#[pub_interface]
pub trait ContractInterface{
    /// Flipping the coin. Uses true sgx randomness.
    fn flip() -> bool;

    /// Player 1 commits to a coin value
    fn commit(bool);

    /// Player 2 guesses the value that player 1 was committed to. The commitment is removed.
    /// True is returned on successful guess.
    fn guess(guess: bool) -> bool;
}

const COMMITMENT_KEY: &'static str = "commitment";

pub struct Contract;

impl ContractInterface for Contract {
    fn flip() -> bool {
        let result: u8 = Rand::gen();
        result % 2 == 1
    }

    fn commit(commitment: bool) {
        let val: Option<Value> = read_state!(COMMITMENT_KEY);
        if val.is_none() {
            write_state!(COMMITMENT_KEY => commitment);
        }
    }

    fn guess(guess: bool) -> bool {
        let val: Option<Value> = remove_from_state!(COMMITMENT_KEY);
        match val {
            Some(commitment) => commitment == guess,
            _ => false
        }
    }
}
```

