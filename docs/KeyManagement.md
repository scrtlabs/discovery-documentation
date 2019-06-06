# Key Management  🗝

All data (in flight or at rest) must be encrypted at all times except inside of enclaves at computation time. Since any node must be able to work on any encrypted data, all data must be encrypted using keys sharable to all nodes.

### Key Concepts and Definitions

Here is a description of the key management model broken down by role.

User

1.  **Signing Key**  (asymmetric): basically an Ethereum key that can be used to uniquely identify the user submitting the request.
2.  **Encryption key**  (asymmetric): the key used to derive an input/output key using ECDH (Elliptic-curve Diffie–Hellman).

Worker/Enclave

1.  **Signing key**  (asymmetric): the key used to prove computations have happened inside the enclave (which is attached to the quote).
2.  **Encryption key**  - asymmetric, the key used to derive an input/output key using ECDH (Elliptic-curve Diffie–Hellman).
3.  **Master key**  (symmetric): a key used to derive other State keys (this can easily be done for example by setting contract_state_key := H(master_key || contract_id)
4.  **State key**  (symmetric): Shared AES-256 key per contract (right now everyone shares it, later on will be sharded). If it’s generated from the master key, this can be done on the fly and does not need to be stored.

Shared keys (fresh for every task)

1.  **Input key**  (symmetric, derived): Worker has a permanent pubkey which is known. User uses that + its private key to generate a unique symmetric key for encrypting the argument. User can send its pubkey with the tx. We can use the taskid to make it fresh.
2.  **Output key**  (symmetric, derived): If there’s a return argument, then the same as #1 but in reverse happens - the worker can generate a new symmetric key from its private key and the user’s pubkey. We can use the taskid to make it fresh.