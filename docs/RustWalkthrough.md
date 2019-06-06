# An Introduction to Rust
To help developers who are new to Rust or the Enigma protocol get a firm understanding of how secret contracts are written, this section outlines how to write a standalone Rust program that will be used to solve a classic connundrum - [Yao's Millionaire Problem](https://en.wikipedia.org/wiki/Yao%27s_Millionaires%27_Problem).

To begin, you will need to ensure that you have [Rust installed](https://www.rust-lang.org/tools/install) on your system, as well as a basic understanding of how [Cargo](https://github.com/rust-lang/cargo) operates. Note that there are several Rust features and concepts mentioned in the sections below, some of which only lightly touched on - if you would like to learn more about syntax or the language itself, it is recomended to consult the open-source [Rust Book](https://doc.rust-lang.org/book/ch00-00-introduction.html), which contains a variety of great examples and explanations curated by the community itself.

For the full source code of the program below, see [this repository](#).
## Setting Up
1. In your terminal, issue the command ``cargo new rust_millionaires_problem`` to create a new project.
2. Enter your new project directory with the command ``cd rust_millionaires_problem``.
3. Within this new directory there is a file named ``Cargo.toml``. This is your *project manifest*, and it contains information about the author, versioning and dependencies. This should have been autopopulated from your Rust installation, though ensure the contents are similar to this:
```
[package]
name = "rust_millionaires_demo"
version = "0.1.0"
authors = ["<name> <<email>>"]

[dependencies]
```
## Program Design
As with all programs, we must break down the problem at hand and address each component individually. The intended logic is depicted in the graphic below.

![alt text](https://i.imgur.com/SB6ZGc5.png)

This basic flow can be represented in four steps:

1. Creating a new Millionaires’ Problem (this object should maintain a list of millionaires)
2. Add millionaire 1 (object containing address / net worth) with name Bob and net worth 1,000,000
3. Add millionaire 2 (object containing address / net worth) with name Alice and net worth 2,000,000
4. Compute the richest millionaire, returning the appropriate address/name

## Writing your Program
### Data Structs
[Structs](https://doc.rust-lang.org/book/ch05-00-structs.html) are data types that let you package together multiple related values and reference them with attribute fields and one collective name. To begin writing our program, we will be implementing a struct that will represent the Millionaires Problem itself.

To create this struct, open ``src/lib.rs`` in your text editor and replace the contents with the following:

```rust
pub struct MillionairesProblem {
  millionaires: Vec<Millionaire>,
}
```

This implements a basic *struct* with a single field named ```Millionaire```. 

Next, a second struct must be implemented to represent the individual millionaires. This time two attribute fields will be added, one to track each millionaires address, and the other for net worth.

```rust
pub struct Millionaire {
  address: String,    // <field name>: <field type>
  net_worth: u64,     // <field name>: <field type>
}
``` 

### Function and Method Implementation
We must now implement our programs primary logic - generating the millionaires problem itself. To do this we create a [function](https://doc.rust-lang.org/book/ch03-03-how-functions-work.html), and place it inside a [implementation block](https://doc.rust-lang.org/book/ch05-03-method-syntax.html) like so:

```rust
impl MillionairesProblem {
 pub fn new() -> MillionairesProblem {
  MillionairesProblem {
      millionaires: Vec::new(),
  }
 }
}
```
The last line of this section creates an empty [vector](https://doc.rust-lang.org/book/ch08-01-vectors.html) when the Millionaires Problem is initialised.
Also note that the ``impl`` block has the same name as the ``MillionairesProblem`` struct we defined above, and thus functions and methods we implement here refer to the same context.

### Adding a Millionaire
In order to add a new millionaire, we will define a ``method`` in the ``impl`` block like this:

```rust
pub fn add_millionaire(&mut self, address: String, net_worth: u64) {
  let millionaire = Millionaire {
      address,
      net_worth,
  };
  self.millionaires.push(millionaire);
}
```
NOTE: Methods, unlike traditional functions and associated functions, always start with self as the first parameter, which represents the instance of the struct this method is being called on. In this case, we prefix self with ``&mut``. The ``&`` touches upon the topic of [ownership](https://doc.rust-lang.org/book/ch04-00-understanding-ownership.html), a big contributor to Rusts memory efficiency. It is reccomended to take the time to understand ownership, and how it can improve your development experience down the road.

### Computing the Richest Millionaire
The final method we will write is to compute the richest millionaire.
```rust
pub fn compute_richest(&self) -> &str {
  match self.millionaires.iter().max_by_key(|m| m.net_worth) {
      Some(millionaire) => &millionaire.address[..],
      None => "None"
  }
}
```
## Summary
Congratulations, you've just made your first Rust library! To summarize, we first created two structs: ``MillionairesProblem`` and ``Millionaire``, then created an ``impl`` block to further the functionality of the ``MillionairesProblem``, and two methods to allow us to add a new millionaire and compute the richest millionaire.
