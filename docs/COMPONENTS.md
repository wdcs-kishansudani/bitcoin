# Bitcoin Core Components

This document provides a detailed, component-by-component reference for the Bitcoin Core codebase.

## Table of Contents

1.  [Validation Engine](#validation-engine)
2.  [Peer-to-Peer Network](#peer-to-peer-network)
3.  [Transaction Mempool](#transaction-mempool)
4.  [Block Production (Miner)](#block-production-miner)
5.  [Wallet](#wallet)
6.  [RPC Interface](#rpc-interface)

---

## 1. Validation Engine

*   **Role:** The validation engine is the heart of Bitcoin Core's consensus mechanism. It is responsible for ensuring that all transactions and blocks adhere to the network's rules. This component maintains the blockchain state, including the UTXO set, and is the ultimate authority on the validity of any piece of data.

*   **Key Files:**
    *   `src/validation.cpp`: The main implementation of the validation logic.
    *   `src/validation.h`: The main header file, defining key data structures and interfaces.
    *   `src/consensus/`: Directory containing consensus-critical validation logic.
    *   `src/policy/`: Directory containing policy rules, which are stricter than consensus rules.
    *   `src/txdb.cpp`: Manages the on-disk storage of the UTXO set.
    *   `src/pow.cpp`: Proof-of-work validation logic.

*   **Key Types/Structs/Classes:**
    *   `CChainState`: Manages the state of the blockchain, including the active chain, branches, and the UTXO set.
    *   `CBlockIndex`: Represents a block header in the block index tree.
    *   `CCoinsViewCache`: A cache on top of the UTXO set for efficient access and modification.
    *   `CTxIn`, `CTxOut`, `CTransaction`: The fundamental data structures for transactions.
    *   `CBlock`: The data structure for a block.

*   **Important Functions:**
    *   `CheckBlock(...)`: Performs a full validation of a block.
    *   `ContextualCheckBlock(...)`: Performs context-dependent checks on a block.
    *   `AcceptBlock(...)`: Attempts to accept a block into the block index.
    *   `ConnectBlock(...)`: Connects a valid block to the main chain, updating the UTXO set.
    *   `DisconnectBlock(...)`: Disconnects a block from the main chain during a reorg.
    *   `CheckInputs(...)`: A key function in transaction validation that checks the validity of transaction inputs and scripts.

*   **Typical Call Flow:**
    1.  A new block is received by the network layer (`net_processing.cpp`).
    2.  `ProcessNewBlock(...)` in `net_processing.cpp` calls `chainman.ProcessNewBlock(...)`
    3.  `CChainState::ProcessNewBlock` calls `CheckBlock(...)` to perform stateless validation.
    4.  If the block is valid, `AcceptBlock(...)` is called to add it to the block index.
    5.  `ConnectBlock(...)` is called to connect the block to the chain, which involves updating the UTXO set and calling `CheckInputs` for each transaction in the block.

*   **Common Pitfalls:**
    *   **Consensus vs. Policy:** It's crucial to distinguish between consensus rules (which, if violated, invalidate a block) and policy rules (which are local to a node and can be configured). Modifying consensus rules can lead to a hard fork.
    *   **Reorgs:** The chain can be reorganized if a longer valid chain is discovered. Code must be robust to blocks being disconnected and reconnected.
    *   **UTXO Set Management:** The UTXO set is a large and performance-critical data structure. Inefficient access or modification can have a significant impact on performance.

---

## 2. Peer-to-Peer Network

*   **Role:** The P2P network component is responsible for all communication between Bitcoin Core nodes. It handles peer discovery, connection management, and the relay of transactions and blocks. Its primary goal is to maintain a robust and well-connected network to ensure the timely propagation of data.

*   **Key Files:**
    *   `src/net_processing.cpp`: Handles the processing of messages received from peers.
    *   `src/net.cpp`: Manages connections to other nodes.
    *   `src/addrman.cpp`: Manages the database of known peers.
    *   `src/protocol.h`: Defines the P2P message formats.

*   **Key Types/Structs/Classes:**
    *   `CNode`: Represents a connection to a peer.
    *   `CConnman`: The connection manager, responsible for all P2P connections.
    *   `CNetMessage`: Represents a message received from a peer.
    *   `CAddressManager`: The peer address manager.

*   **Important Functions:**
    *   `CConnman::Start(...)`: Initializes and starts the connection manager.
    *   `ProcessMessage(...)` in `net_processing.cpp`: The main message handler for all P2P messages.
    *   `SendMessages(...)`: Queues messages to be sent to a peer.
    *   `PushMessage(...)`: A lower-level function for sending messages.

*   **Typical Call Flow:**
    1.  `CConnman` establishes connections to peers based on its address database.
    2.  A listening thread in `CConnman` accepts incoming connections.
    3.  For each peer, a message processing thread reads messages from the socket.
    4.  The message is deserialized and passed to `ProcessMessage(...)`.
    5.  `ProcessMessage` calls the appropriate handler based on the message type (e.g., `inv`, `tx`, `block`).
    6.  The handler processes the data and may, in turn, queue new messages to be sent to peers.

*   **Common Pitfalls:**
    *   **Message Spam:** A malicious peer could spam the node with a high volume of messages. The networking code includes protections against this, such as message size limits and rate limiting.
    *   **Eclipse Attacks:** An attacker could attempt to isolate a node from the rest of the network by controlling all of its connections. The connection manager has logic to mitigate this by diversifying its peer set.
    *   **Serialization/Deserialization Bugs:** The P2P protocol relies on a precise serialization format. Bugs in this code can lead to consensus failures or vulnerabilities.

---

## 3. Transaction Mempool

*   **Role:** The transaction mempool is an in-memory cache of unconfirmed transactions. Its primary purpose is to hold transactions that have been validated but not yet included in a block. It also serves as a source of transactions for miners who are constructing new blocks.

*   **Key Files:**
    *   `src/txmempool.cpp`: The main implementation of the mempool.
    *   `src/txmempool.h`: The main header file, defining the `CTxMemPool` class.

*   **Key Types/Structs/Classes:**
    *   `CTxMemPool`: The main mempool class, which manages the collection of unconfirmed transactions.
    *   `CTxMemPoolEntry`: Represents a transaction in the mempool.
    *   `CFeeRate`: A class for representing and calculating transaction fees.

*   **Important Functions:**
    *   `CTxMemPool::addUnchecked(...)`: Adds a transaction to the mempool without performing full validation.
    *   `CTxMemPool::remove(...)`: Removes a transaction from the mempool.
    *   `CTxMemPool::queryHashes(...)`: Queries the mempool for transactions with specific hashes.
    *   `CTxMemPool::infoAll()`: Returns information about all transactions in the mempool.
    *   `getblocktemplate()` (in `rpc/mining.cpp`): Uses the mempool to construct a block template for miners.

*   **Typical Call Flow:**
    1.  A new transaction is received from the network and validated by the validation engine.
    2.  If the transaction is valid, `AcceptToMemoryPool(...)` in `validation.cpp` is called.
    3.  `AcceptToMemoryPool` creates a `CTxMemPoolEntry` and adds it to the mempool via `CTxMemPool::addUnchecked`.
    4.  The transaction is then relayed to other peers.
    5.  When a new block is mined, the transactions it contains are removed from the mempool.

*   **Common Pitfalls:**
    *   **Mempool Flooding:** A large number of low-fee transactions could be broadcast in an attempt to flood the mempool. The mempool has a size limit and fee-based eviction policies to mitigate this.
    *   **Transaction Pinning:** An attacker could exploit transaction dependency rules to prevent a high-fee transaction from being included in a block.
    *   **Fee Estimation:** The mempool is used to estimate the appropriate fee for a new transaction. Inaccurate fee estimation can lead to transactions being delayed.

---

## 4. Block Production (Miner)

*   **Role:** The miner is responsible for creating new blocks. This involves selecting transactions from the mempool, constructing a block template, and then solving the proof-of-work puzzle.

*   **Key Files:**
    *   `src/miner.cpp`: Contains the logic for creating and managing block templates.
    *   `src/node/blockassembler.cpp`: The BlockAssembler class, which is responsible for creating block templates.
    *   `src/pow.cpp`: Contains the proof-of-work algorithm.

*   **Key Types/Structs/Classes:**
    *   `BlockAssembler`: A class that constructs block templates.
    *   `CBlockTemplate`: Represents a template for a new block.

*   **Important Functions:**
    *   `BlockAssembler::CreateNewBlock(...)`: Creates a new block template.
    *   `CheckProofOfWork(...)`: Verifies the proof-of-work for a block.
    *   `getblocktemplate()` (in `rpc/mining.cpp`): The RPC call that allows external miners to get block templates.

*   **Typical Call Flow:**
    1.  A miner calls the `getblocktemplate` RPC.
    2.  `getblocktemplate` calls `BlockAssembler::CreateNewBlock`.
    3.  `CreateNewBlock` selects transactions from the mempool and constructs a `CBlockTemplate`.
    4.  The `CBlockTemplate` is returned to the miner.
    5.  The miner performs the proof-of-work computation.
    6.  When a solution is found, the miner submits the completed block to the network.

*   **Common Pitfalls:**
    *   **Empty Blocks:** Miners may choose to mine empty blocks. While this is allowed by the protocol, it can be inefficient and reduce the overall transaction throughput of the network.
    *   **Transaction Selection:** The choice of which transactions to include in a block can be complex. Miners must balance the desire to maximize fees with the need to create a block that will be accepted by the network.

---

## 5. Wallet

*   **Role:** The wallet manages the user's private keys, tracks their balance, and creates new transactions. It is a critical component for any user who wants to send or receive bitcoins.

*   **Key Files:**
    *   `src/wallet/wallet.cpp`: The main implementation of the wallet.
    *   `src/wallet/wallet.h`: The main header file, defining the `CWallet` class.
    *   `src/wallet/walletdb.cpp`: Handles the on-disk storage of the wallet.
    *   `src/wallet/rpcwallet.cpp`: Implements the wallet-related RPC calls.

*   **Key Types/Structs/Classes:**
    *   `CWallet`: The main wallet class.
    *   `CWalletTx`: Represents a transaction in the wallet.
    *   `CKey`: A private key.
    *   `CPubKey`: A public key.
    *   `CKeyStore`: A store for private keys.

*   **Important Functions:**
    *   `CWallet::CreateTransaction(...)`: Creates a new transaction.
    *   `CWallet::CommitTransaction(...)`: Commits a transaction to the wallet and broadcasts it to the network.
    *   `CWallet::ScanForWalletTransactions(...)`: Scans the blockchain for transactions that belong to the wallet.
    *   `CWallet::GetBalance()`: Returns the current balance of the wallet.

*   **Typical Call Flow:**
    1.  A user calls the `sendtoaddress` RPC.
    2.  `sendtoaddress` calls `CWallet::CreateTransaction` to create a new transaction.
    3.  `CreateTransaction` selects coins, signs the transaction, and then calls `CWallet::CommitTransaction`.
    4.  `CommitTransaction` stores the transaction in the wallet's on-disk database and then broadcasts it to the network via the P2P layer.

*   **Common Pitfalls:**
    *   **Key Management:** Securely storing and managing private keys is the most critical function of the wallet. A bug in this code could lead to the loss of user funds.
    *   **Fee Estimation:** The wallet must be able to estimate the appropriate fee for a transaction. If the fee is too low, the transaction may be delayed. If it's too high, the user will overpay.
    *   **Privacy:** The wallet has a responsibility to protect the privacy of its users. This includes things like using new addresses for each transaction and avoiding address reuse.

---

## 6. RPC Interface

*   **Role:** The RPC (Remote Procedure Call) interface provides a way for users and applications to interact with the Bitcoin Core node. It exposes a rich API for a wide range of functions, from checking the balance to creating and sending transactions.

*   **Key Files:**
    *   `src/rpc/server.cpp`: The main implementation of the RPC server.
    *   `src/rpc/blockchain.cpp`, `src/rpc/mining.cpp`, `src/rpc/net.cpp`, `src/rpc/rawtransaction.cpp`, `src/rpc/wallet.cpp`: These files contain the implementations of the various RPC calls.

*   **Key Types/Structs/Classes:**
    *   `CRPCCommand`: Represents an RPC command.
    *   `UniValue`: A class for representing JSON values.

*   **Important Functions:**
    *   `RPCHandleRequest(...)`: The main handler for RPC requests.
    *   The various functions that implement the RPC calls (e.g., `getblockchaininfo`, `sendtoaddress`).

*   **Typical Call Flow:**
    1.  A user or application sends a JSON-RPC request to the Bitcoin Core node.
    2.  The HTTP server in `httpserver.cpp` receives the request and passes it to the RPC server.
    3.  `RPCHandleRequest` parses the request and finds the corresponding `CRPCCommand`.
    4.  The `CRPCCommand`'s handler function is called.
    5.  The handler function performs the requested action and returns a `UniValue` object.
    6.  The `UniValue` object is serialized to JSON and sent back to the client.

*   **Common Pitfalls:**
    *   **Authentication:** The RPC interface is a powerful tool, and it's critical to ensure that it is properly secured. By default, it is only accessible from the local machine and requires a username and password.
    *   **Argument Parsing:** The RPC handlers must be careful to properly parse and validate their arguments. A bug in this code could lead to unexpected behavior or a crash.
    *   **Deprecation:** The RPC interface evolves over time, and some commands may be deprecated. It's important to keep up-to-date with the latest changes to avoid using outdated commands.
