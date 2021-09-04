# Bloom-2.2
Blockchains
The Arbor Chain includes a few key components:

- Transaction creation
- Transaction validation
- Block creation
    - Transaction selection
    - Proof of Work mining
- Block validation
- Peer to peer messaging
    - Peer discovery

# Specification

## Transactions

Arbor follows a UTXO (unspent transaction output) transaction model, similar to Bitcoin. Each transaction includes a header and a body.

### Header

The header contains the transaction id which is a Blake3 hash of the transaction body.

### Body

The body contains an input, a set of outputs, and a cryptographic signature.

- The input must be a previous output that has not yet been included in a block
    - The previous output used for the input must be cryptographically signed by the recipient of the output using ECDSA over the Secp256k1 elliptic curve
- The output list must include at least one valid output
    - A valid output includes a public key and a value of ARB that the public key will have control over (i.e. be able to spend in a new transaction)
- The value of the input must be greater than or equal to the value of all outputs
    - The difference in value between the input and outputs gets burned such that those ARB coins are removed from circulation and can not be spent

### Seeds

A "seed" transaction is a special type of transaction that mints new ARB. The seed transaction has no input, no signature, and must contain at least one output. The sum of the values associated to the outputs must be equal to the seed reward for the previous block.

### Mempool

Miners may select any transactions to include in a block. Transactions are held in a dictionary called a mempool. Transactions are hashed with Blake3 and the mempool keys are the resulting hashes which act as the ids for the transactions.

### Validation

Each transaction must be verified and deemed valid in two places:

1. Before a transaction is selected for inclusion in a block.
2. During block validation.

The following must be true in order to deem a transaction as valid. (This does not include the seed transaction):

- [ ]  The transaction id is a valid hash over the transaction body
- [ ]  The public key associated to the unspent transaction output is derived from a Secp256k1 private key
- [ ]  The public key associated to the unspent transaction output used as the input, successfully validates the signature
- [ ]  The value of ARB associated with the input is greater than or equal to the sum of the values associated with the outputs

The following must be true in order to deem a seed transaction as valid:

- [ ]  The transaction id is a valid hash over the transaction body
- [ ]  The sum of the outputs is 10 ARB [*See stretch goal 2*]

## Blocks

Each block in Arbor includes a block header and a block body.

### Header

The block header contains:

- SHA256 hash of previous block header represented as a hexadecimal
- Blake3 root hash of transaction list [*See stretch goal 1*]
- Difficulty target for this block [*See stretch goal 3*]
    - The difficulty target is represented by an integer that is used to determine how many
- Nonce used to mine this block

### Body

The block body contains:

- List of transactions used to generate the Blake3 root hash

### Mining

Mining is the process of finding a valid block.

A block is deemed valid if:

- [ ]  Its block header and body includes all aforementioned data
- [ ]  All transactions in the block are deemed valid
- [ ]  The transaction list includes a seed transaction
- [ ]  When the block's header is hashed using SHA256, the resulting hash when represented as a hexadecimal has a prefix of letters "R" in lower or uppercase equal to the difficulty target

### Consensus

When an Arbor node receives a block it must validate the block before accepting it in its local chain.

## Messaging

TBD

# Resources

Secp256k1 Elliptic Curve

[https://github.com/ethereum/js-ethereum-cryptography#secp256k1-submodule](https://github.com/ethereum/js-ethereum-cryptography#secp256k1-submodule)

Blake3 Hashing Algorithm

[https://github.com/BLAKE3-team/BLAKE3](https://github.com/BLAKE3-team/BLAKE3)

SHA256 Hashing Algorithm

[https://cryptojs.gitbook.io/docs/#hashing](https://cryptojs.gitbook.io/docs/#hashing)

ECDSA

[https://github.com/cryptocoinjs/secp256k1-node](https://github.com/cryptocoinjs/secp256k1-node)

# Stretch Goals (Optional)

1. Instead of hashing an array of transactions to generate the root hash, construct a Merkle tree (aka Merkle DAG) and use the Merkle root as the hash that gets included in the block header.
2. Use a halving schedule for the seed rewards.
3. Include timestamps in the block header and use them to adjust the difficulty to keep the time between blocks predictable.
4. Improve this specification.
