# Bitcoin Core Question Bank

This document contains a large, organized question bank to test contributor readiness. The questions are grouped by component and difficulty.

## Table of Contents

1.  [Validation Engine](#1-validation-engine)
2.  [Peer-to-Peer Network](#2-peer-to-peer-network)
3.  [Transaction Mempool](#3-transaction-mempool)
4.  [Block Production (Miner)](#4-block-production-miner)
5.  [Wallet](#5-wallet)
6.  [RPC Interface](#6-rpc-interface)

---

## 1. Validation Engine

### Beginner

**Question 1:**

*   **Question:** What is the purpose of the `CheckBlock()` function in `src/validation.cpp`?
*   **Estimated Time:** 5 minutes
*   **Type:** Short Answer
*   **Model Answer:** The `CheckBlock()` function performs stateless checks on a new block. This includes checking the block's syntax, the proof-of-work, and the transaction merkle root. It does not check the validity of the block's transactions in the context of the current blockchain state.
*   **Code Reference:** `src/validation.cpp`, `CheckBlock()`

**Question 2:**

*   **Question:** What is the difference between consensus rules and policy rules?
*   **Estimated Time:** 10 minutes
*   **Type:** Short Answer
*   **Model Answer:** Consensus rules are the rules that all nodes on the network must follow to be in consensus. If a node breaks a consensus rule, it will be forked from the network. Policy rules are local to a node and can be configured. They are generally stricter than consensus rules and are used to protect a node from resource exhaustion attacks. For example, a node might have a policy to not relay transactions with a low fee.
*   **Code Reference:** `src/policy/policy.h`, `src/consensus/consensus.h`

### Intermediate

**Question 1:**

*   **Question:** Explain the process of a blockchain reorg. What happens when a node discovers a longer valid chain?
*   **Estimated Time:** 15 minutes
*   **Type:** Short Answer
*   **Model Answer:** A blockchain reorg happens when a node discovers a new chain of blocks that is longer and has more proof-of-work than its current main chain. The node will then switch to the new chain. This involves disconnecting the blocks from the old chain and connecting the blocks from the new chain. The `DisconnectBlock()` and `ConnectBlock()` functions in `src/validation.cpp` are used for this purpose.
*   **Code Reference:** `src/validation.cpp`, `DisconnectBlock()`, `ConnectBlock()`

**Question 2:**

*   **Question:** What is the UTXO set, and how is it used in transaction validation?
*   **Estimated Time:** 15 minutes
*   **Type:** Short Answer
*   **Model Answer:** The UTXO (Unspent Transaction Output) set is the set of all unspent transaction outputs in the blockchain. It is used to validate new transactions. When a new transaction is received, the node checks that the inputs to the transaction are in the UTXO set. If they are, the transaction is valid, and the UTXO set is updated to remove the spent outputs and add the new outputs.
*   **Code Reference:** `src/coins.h`, `src/txdb.cpp`

### Advanced

**Question 1:**

*   **Question:** How does Bitcoin Core prevent a malicious miner from creating a block with a timestamp far in the future?
*   **Estimated Time:** 20 minutes
*   **Type:** Short Answer
*   **Model Answer:** Bitcoin Core has a consensus rule that a block's timestamp cannot be more than two hours in the future. This is checked in the `ContextualCheckBlock()` function in `src/validation.cpp`. The two-hour limit is a compromise between allowing for some clock drift between nodes and preventing a malicious miner from creating a block with a timestamp far in the future.
*   **Code Reference:** `src/validation.cpp`, `ContextualCheckBlock()`

### Expert

**Question 1:**

*   **Question:** Design a new script opcode that allows for a transaction to be time-locked to a specific block height. Where in the codebase would you need to make changes to implement this?
*   **Estimated Time:** 30 minutes
*   **Type:** Whiteboard
*   **Model Answer:** To implement a new script opcode, you would need to make changes in the following places:
    1.  `src/script/script.h`: Add the new opcode to the `opcodetype` enum.
    2.  `src/script/interpreter.cpp`: Add a case for the new opcode in the `EvalScript()` function. The case would need to check the current block height and fail the script if the time-lock has not expired.
    3.  `src/test/script_tests.cpp`: Add new test cases to test the new opcode.
*   **Code Reference:** `src/script/script.h`, `src/script/interpreter.cpp`, `src/test/script_tests.cpp`

---

## 2. Peer-to-Peer Network

### Beginner

**Question 1:**

*   **Question:** What is the purpose of the `version` message in the P2P protocol?
*   **Estimated Time:** 5 minutes
*   **Type:** Short Answer
*   **Model Answer:** The `version` message is the first message that is sent when two nodes connect. It is used to exchange information about the nodes, such as their protocol version, services, and current block height.
*   **Code Reference:** `src/protocol.h`

**Question 2:**

*   **Question:** What is the difference between an `inv` message and a `getdata` message?
*   **Estimated Time:** 10 minutes
*   **Type:** Short Answer
*   **Model Answer:** An `inv` message is used to announce a new transaction or block to a peer. It contains a list of hashes of the new objects. A `getdata` message is used to request the full data for a transaction or block from a peer. It also contains a list of hashes.
*   **Code Reference:** `src/protocol.h`

### Intermediate

**Question 1:**

*   **Question:** How does Bitcoin Core discover new peers on the network?
*   **Estimated Time:** 15 minutes
*   **Type:** Short Answer
*   **Model Answer:** Bitcoin Core discovers new peers through a variety of methods, including:
    *   **DNS seeds:** A list of hardcoded DNS servers that return a list of IP addresses of known nodes.
    *   **`addr` messages:** Peers will send each other `addr` messages containing the IP addresses of other nodes they know about.
    *   **Hardcoded seeds:** A list of hardcoded IP addresses of known nodes.
*   **Code Reference:** `src/net.cpp`, `src/chainparams.cpp`

**Question 2:**

*   **Question:** What is an eclipse attack, and how does Bitcoin Core try to prevent it?
*   **Estimated Time:** 15 minutes
*   **Type:** Short Answer
*   **Model Answer:** An eclipse attack is an attack where an attacker tries to isolate a node from the rest of the network by controlling all of its connections. Bitcoin Core tries to prevent this by diversifying its peer set. It does this by connecting to a variety of different IP addresses and by trying to maintain connections to at least one peer on a different network.
*   **Code Reference:** `src/net.cpp`

### Advanced

**Question 1:**

*   **Question:** What is the purpose of the `feefilter` message?
*   **Estimated Time:** 20 minutes
*   **Type:** Short Answer
*   **Model Answer:** The `feefilter` message is used to tell a peer that you are not interested in receiving transactions with a fee rate below a certain threshold. This can be used to reduce the amount of bandwidth that is used to download low-fee transactions.
*   **Code Reference:** `src/net.cpp`, `src/protocol.h`

### Expert

**Question 1:**

*   **Question:** How would you design a new P2P message to relay a compact block?
*   **Estimated Time:** 30 minutes
*   **Type:** Whiteboard
*   **Model Answer:** A new P2P message to relay a compact block would need to contain the following information:
    *   The block header.
    *   A list of the short transaction IDs of the transactions in the block.
    *   A list of the prefilled transactions. These are the transactions that the peer is likely to not have in its mempool.
    *   The new message would be called `cmpctblock`.
*   **Code Reference:** `src/net.cpp`, `src/protocol.h`

---

## 3. Transaction Mempool

### Beginner

**Question 1:**

*   **Question:** What is the purpose of the mempool?
*   **Estimated Time:** 5 minutes
*   **Type:** Short Answer
*   **Model Answer:** The mempool is an in-memory cache of unconfirmed transactions. It is used to store transactions that have been validated but not yet included in a block. It also serves as a source of transactions for miners who are constructing new blocks.
*   **Code Reference:** `src/txmempool.h`

**Question 2:**

*   **Question:** What happens to a transaction in the mempool when a new block is mined?
*   **Estimated Time:** 10 minutes
*   **Type:** Short Answer
*   **Model Answer:** When a new block is mined, the transactions it contains are removed from the mempool.
*   **Code Reference:** `src/validation.cpp`, `RemoveForBlock()`

### Intermediate

**Question 1:**

*   **Question:** How does the mempool prioritize transactions for inclusion in a block?
*   **Estimated Time:** 15 minutes
*   **Type:** Short Answer
*   **Model Answer:** The mempool prioritizes transactions based on their fee rate. The fee rate is the fee of the transaction divided by its size. Transactions with a higher fee rate are more likely to be included in a block.
*   **Code Reference:** `src/txmempool.cpp`, `BlockAssembler`

**Question 2:**

*   **Question:** What is transaction pinning, and how does Bitcoin Core try to prevent it?
*   **Estimated Time:** 15 minutes
*   **Type:** Short Answer
*   **Model Answer:** Transaction pinning is an attack where an attacker exploits transaction dependency rules to prevent a high-fee transaction from being included in a block. Bitcoin Core tries to prevent this by limiting the number of ancestor and descendant transactions that a transaction can have in the mempool.
*   **Code Reference:** `src/txmempool.h`

### Advanced

**Question 1:**

*   **Question:** What is the purpose of the `maxmempool` setting?
*   **Estimated Time:** 20 minutes
*   **Type:** Short Answer
*   **Model Answer:** The `maxmempool` setting is used to limit the maximum size of the mempool. If the mempool exceeds this size, the node will evict the transactions with the lowest fee rates until the mempool is back within the limit.
*   **Code Reference:** `src/txmempool.cpp`, `TrimToSize()`

### Expert

**Question 1:**

*   **Question:** How would you design a new mempool eviction policy that is based on the age of a transaction?
*   **Estimated Time:** 30 minutes
*   **Type:** Whiteboard
*   **Model Answer:** A new mempool eviction policy that is based on the age of a transaction would need to store the time that each transaction was added to the mempool. When the mempool is full, the policy would evict the oldest transactions first. This would need to be implemented in the `TrimToSize()` function in `src/txmempool.cpp`.
*   **Code Reference:** `src/txmempool.cpp`, `TrimToSize()`

---

## 4. Block Production (Miner)

### Beginner

**Question 1:**

*   **Question:** What is the purpose of the `getblocktemplate` RPC?
*   **Estimated Time:** 5 minutes
*   **Type:** Short Answer
*   **Model Answer:** The `getblocktemplate` RPC is used by miners to get a new block template. The block template contains the information that is needed to mine a new block, such as the block header, the coinbase transaction, and the list of transactions to include in the block.
*   **Code Reference:** `src/rpc/mining.cpp`

**Question 2:**

*   **Question:** What is the coinbase transaction?
*   **Estimated Time:** 10 minutes
*   **Type:** Short Answer
*   **Model Answer:** The coinbase transaction is the first transaction in a block. It is created by the miner and is used to claim the block reward and the transaction fees from the transactions in the block.
*   **Code Reference:** `src/miner.cpp`

### Intermediate

**Question 1:**

*   **Question:** How does a miner choose which transactions to include in a block?
*   **Estimated Time:** 15 minutes
*   **Type:** Short Answer
*   **Model Answer:** A miner chooses which transactions to include in a block based on their fee rate. The miner will select the transactions with the highest fee rates until the block is full.
*   **Code Reference:** `src/node/blockassembler.cpp`

**Question 2:**

*   **Question:** What is the purpose of the `nBits` field in the block header?
*   **Estimated Time:** 15 minutes
*   **Type:** Short Answer
*   **Model Answer:** The `nBits` field in the block header is used to specify the difficulty of the proof-of-work puzzle. The difficulty is adjusted every 2016 blocks to ensure that a new block is mined approximately every 10 minutes.
*   **Code Reference:** `src/pow.cpp`

### Advanced

**Question 1:**

*   **Question:** What is the purpose of the `submitblock` RPC?
*   **Estimated Time:** 20 minutes
*   **Type:** Short Answer
*   **Model Answer:** The `submitblock` RPC is used by miners to submit a solved block to the node. The node will then validate the block and, if it is valid, add it to the blockchain.
*   **Code Reference:** `src/rpc/mining.cpp`

### Expert

**Question 1:**

*   **Question:** How would you design a new block template that includes a commitment to the state of the UTXO set?
*   **Estimated Time:** 30 minutes
*   **Type:** Whiteboard
*   **Model Answer:** A new block template that includes a commitment to the state of the UTXO set would need to add a new field to the block header. This field would contain a hash of the UTXO set. The `CreateNewBlock()` function in `src/node/blockassembler.cpp` would need to be modified to calculate this hash and add it to the block header.
*   **Code Reference:** `src/node/blockassembler.cpp`

---

## 5. Wallet

### Beginner

**Question 1:**

*   **Question:** What is the purpose of the `wallet.dat` file?
*   **Estimated Time:** 5 minutes
*   **Type:** Short Answer
*   **Model Answer:** The `wallet.dat` file is a file that stores the user's private keys, public keys, and other wallet-related information.
*   **Code Reference:** `src/wallet/wallet.h`

**Question 2:**

*   **Question:** What is the difference between a private key and a public key?
*   **Estimated Time:** 10 minutes
*   **Type:** Short Answer
*   **Model Answer:** A private key is a secret number that is used to sign transactions. A public key is a number that is derived from the private key and is used to verify the signature of a transaction.
*   **Code Reference:** `src/pubkey.h`

### Intermediate

**Question 1:**

*   **Question:** What is coin selection, and how does the wallet perform it?
*   **Estimated Time:** 15 minutes
*   **Type:** Short Answer
*   **Model Answer:** Coin selection is the process of choosing which coins to spend in a transaction. The wallet performs coin selection by trying to find a set of coins that is large enough to cover the amount of the transaction, while also minimizing the fee and the size of the transaction.
*   **Code Reference:** `src/wallet/wallet.cpp`, `CreateTransaction()`

**Question 2:**

*   **Question:** What is a BIP32 hierarchical deterministic wallet?
*   **Estimated Time:** 15 minutes
*   **Type:** Short Answer
*   **Model Answer:** A BIP32 hierarchical deterministic wallet is a type of wallet that allows you to generate a tree of keys from a single seed. This is useful for backups and for managing a large number of keys.
*   **Code Reference:** `src/wallet/wallet.h`

### Advanced

**Question 1:**

*   **Question:** What is the purpose of the keypool?
*   **Estimated Time:** 20 minutes
*   **Type:** Short Answer
*   **Model Answer:** The keypool is a pool of pre-generated keys that are used to improve the privacy of the wallet. When the wallet needs a new address, it will take a key from the keypool. This makes it more difficult for an attacker to link the addresses in the wallet to each other.
*   **Code Reference:** `src/wallet/wallet.h`

### Expert

**Question 1:**

*   **Question:** How would you design a new coin selection algorithm that is optimized for privacy?
*   **Estimated Time:** 30 minutes
*   **Type:** Whiteboard
*   **Model Answer:** A new coin selection algorithm that is optimized for privacy would need to avoid reusing addresses and would need to try to create change outputs that are not easily linked to the original inputs. This could be done by using a variety of techniques, such as creating multiple change outputs or by using a different change address for each transaction.
*   **Code Reference:** `src/wallet/wallet.cpp`, `CreateTransaction()`

---

## 6. RPC Interface

### Beginner

**Question 1:**

*   **Question:** What is the purpose of the RPC interface?
*   **Estimated Time:** 5 minutes
*   **Type:** Short Answer
*   **Model Answer:** The RPC (Remote Procedure Call) interface provides a way for users and applications to interact with the Bitcoin Core node. It exposes a rich API for a wide range of functions, from checking the balance to creating and sending transactions.
*   **Code Reference:** `src/rpc/server.cpp`

**Question 2:**

*   **Question:** How do you authenticate with the RPC interface?
*   **Estimated Time:** 10 minutes
*   **Type:** Short Answer
*   **Model Answer:** You authenticate with the RPC interface by providing a username and password. These are specified in the `bitcoin.conf` file.
*   **Code Reference:** `src/rpc/server.cpp`

### Intermediate

**Question 1:**

*   **Question:** What is the difference between the `getrawtransaction` RPC and the `gettransaction` RPC?
*   **Estimated Time:** 15 minutes
*   **Type:** Short Answer
*   **Model Answer:** The `getrawtransaction` RPC returns the raw transaction data in hex format. The `gettransaction` RPC returns a more detailed JSON object with information about the transaction, such as the number of confirmations and the block hash.
*   **Code Reference:** `src/rpc/rawtransaction.cpp`, `src/wallet/rpcwallet.cpp`

**Question 2:**

*   **Question:** What is the purpose of the `scantxoutset` RPC?
*   **Estimated Time:** 15 minutes
*   **Type:** Short Answer
*   **Model Answer:** The `scantxoutset` RPC is used to scan the UTXO set for transactions that match a certain set of descriptors. This is useful for importing a wallet into a new node.
*   **Code Reference:** `src/rpc/blockchain.cpp`

### Advanced

**Question 1:**

*   **Question:** What is the purpose of the `testmempoolaccept` RPC?
*   **Estimated Time:** 20 minutes
*   **Type:** Short Answer
*   **Model Answer:** The `testmempoolaccept` RPC is used to test whether a transaction would be accepted into the mempool without actually adding it. This is useful for testing new transaction types or for checking the validity of a transaction before broadcasting it.
*   **Code Reference:** `src/rpc/rawtransaction.cpp`

### Expert

**Question 1:**

*   **Question:** How would you design a new RPC call that returns a list of all the peers that are connected to the node?
*   **Estimated Time:** 30 minutes
*   **Type:** Whiteboard
*   **Model Answer:** A new RPC call that returns a list of all the peers that are connected to the node would need to do the following:
    1.  Get a list of all the connected peers from the connection manager.
    2.  For each peer, create a JSON object with information about the peer, such as its IP address, protocol version, and services.
    3.  Return a JSON array of these objects.
*   **Code Reference:** `src/rpc/net.cpp`, `src/net.h`
