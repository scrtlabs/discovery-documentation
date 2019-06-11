

# Network Setup
In this tutorial, you will install and set up the Enigma developer testnet in software mode (appropriate for computers without SGX hardware support)

## Set up developer environment

1.  `npm i -g @enigmampc/discovery-cli` 
Uses npm to globally install the  `discovery-cli` , a command line interface (CLI) for the Enigma protocol’s Discovery release dev environment
2.  Create and navigate to a directory in which we will write our project code:  
    `mkdir <path>/millionaires_problem_demo  
    cd <path>/millionaires_problem_demo`
3. Initialize this directory as an Enigma project by running  `discovery init`. 
This command pulls the necessary pre-built docker images to 	run the Enigma testnet locally. 

4. When asked whether to set up the environment in the current folder, choose  `Y`  for yes. 
5. When asked whether to use hardware mode (HW) or simulation mode (SW), we will select  `sw`. 
6. Choose  `Y`  to allow the docker images to be pulled to your machine — if this is your first time creating an Enigma project and/or pulling these docker images, this may take ~15–20 minutes.
This process has auto-generated scaffolding that should be familiar to users with experience working with Truffle).
-   `build`: All of our compiled build files are written here and organized by type depending on whether they are Enigma protocol contracts, Ethereum smart contracts, or Enigma secret contracts (often abbreviated ESC as a heads up)
-   `migrations`: Our migrations files, much like you would see in a Truffle environment
-   `smart_contracts`: Any Ethereum smart contracts central to your dApp logic, with an existing example contract,  `Sample.sol`
-   `secret_contracts`: Any Enigma secret contracts central to your dApp logic, with an existing example Rust library  `simple_addition`. We have included a  `Cargo.toml.template`  file which you can use as a foundation for your custom library’s  `Cargo.toml`  specification.
-   `test`: Any unit tests for your contracts

Within our Discovery-initialized project, you are able to:

-   Start the network — `discovery start`
-   Stop the network — `discovery stop`
