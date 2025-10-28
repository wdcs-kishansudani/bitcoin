# Bitcoin Core Onboarding

This document provides a roadmap for new contributors to get up to speed with the Bitcoin Core codebase. It's divided into a "Quickstart Path" for a high-level overview and a "Deep Dive Path" for a more in-depth understanding.

## Quickstart Path

This path is designed to give you a working conceptual understanding of the Bitcoin Core codebase in the shortest amount of time.

### 1. Understand the Big Picture

*   **Goal:** Get a high-level overview of the major components and how they interact.
*   **Action:** Read `docs/WORKFLOW.md` and `docs/COMPONENTS.md`.

### 2. The Lifecycle of a Transaction

*   **Goal:** Follow a transaction from creation to confirmation.
*   **Action:** Read the following files in order, focusing on the key functions mentioned:
    1.  `src/wallet/rpcwallet.cpp`: `sendtoaddress()`
    2.  `src/wallet/wallet.cpp`: `CreateTransaction()`, `CommitTransaction()`
    3.  `src/validation.cpp`: `AcceptToMemoryPool()`
    4.  `src/txmempool.cpp`: `addUnchecked()`
    5.  `src/net_processing.cpp`: `RelayTransaction()`
    6.  `src/miner.cpp`: `BlockAssembler::CreateNewBlock()`
    7.  `src/pow.cpp`: `CheckProofOfWork()`
    8.  `src/validation.cpp`: `ConnectBlock()`

### 3. The Lifecycle of a Block

*   **Goal:** Follow a block from reception to connection.
*   **Action:** Read the following files in order, focusing on the key functions mentioned:
    1.  `src/net_processing.cpp`: `ProcessMessage()` (case "block")
    2.  `src/validation.cpp`: `CheckBlock()`, `AcceptBlock()`, `ConnectBlock()`

### 4. Tooling & Setup

*   **Goal:** Set up your development environment for efficient code navigation.
*   **Action:**
    *   **Build from source:** Follow the instructions in `doc/build-unix.md`.
    *   **ctags:** Run `ctags -R` in the `src/` directory to generate a tags file.
    *   **rg (ripgrep):** Use `rg` to search for symbols and strings in the codebase. For example, `rg "CChainState"` to find all occurrences of `CChainState`.
    *   **LSP (Language Server Protocol):** Set up `clangd` for your editor for features like code completion and go-to-definition.

## Deep Dive Path

This path is for those who want a more thorough understanding of the codebase. It's organized by component and includes suggested exercises.

### 1. Validation Engine

*   **File Order:**
    1.  `src/consensus/consensus.h`: Defines the basic consensus parameters.
    2.  `src/primitives/transaction.h`, `src/primitives/block.h`: Understand the fundamental data structures.
    3.  `src/uint256.h`, `src/arith_uint256.h`: Understand how large numbers are handled.
    4.  `src/pow.h`, `src/pow.cpp`: Understand the proof-of-work algorithm.
    5.  `src/chain.h`, `src/chain.cpp`: Understand the `CBlockIndex` and `CChain` classes.
    6.  `src/txdb.h`, `src/txdb.cpp`: Understand how the UTXO set is stored on disk.
    7.  `src/coins.h`, `src/coins.cpp`: Understand the `CCoinsViewCache` class.
    8.  `src/validation.h`, `src/validation.cpp`: This is the core of the validation logic. Spend a lot of time here.

*   **Key Functions to Step Through:**
    *   `CheckBlock(...)`
    *   `ContextualCheckBlock(...)`
    *   `AcceptBlock(...)`
    *   `ConnectBlock(...)`
    *   `DisconnectBlock(...)`
    *   `CheckInputs(...)`

*   **Exercises:**
    1.  **Add a new consensus rule:** For example, add a rule that blocks with a timestamp in the future are invalid.
    2.  **Trace a reorg:** Use a debugger to step through the `DisconnectBlock` and `ConnectBlock` functions during a reorg.
    3.  **Visualize the block index:** Write a script to parse the block index and generate a graph of the different branches.

### 2. Peer-to-Peer Network

*   **File Order:**
    1.  `src/protocol.h`: Understand the P2P message formats.
    2.  `src/netaddress.h`, `src/netaddress.cpp`: Understand how network addresses are represented.
    3.  `src/netbase.h`, `src/netbase.cpp`: Understand the low-level networking functions.
    4.  `src/addrman.h`, `src/addrman.cpp`: Understand the peer address manager.
    5.  `src/net.h`, `src/net.cpp`: Understand the `CConnman` and `CNode` classes.
    6.  `src/net_processing.h`, `src/net_processing.cpp`: Understand how P2P messages are processed.

*   **Key Functions to Step Through:**
    *   `CConnman::Start(...)`
    *   `ProcessMessage(...)`
    *   `SendMessages(...)`
    *   `PushMessage(...)`

*   **Exercises:**
    1.  **Add a new P2P message:** For example, add a `ping` and `pong` message.
    2.  **Simulate a network partition:** Use `iptables` to block all traffic between two nodes and observe how they behave.
    3.  **Visualize the peer graph:** Write a script to connect to a node and recursively crawl its peers to generate a graph of the network.

### 3. Transaction Mempool

*   **File Order:**
    1.  `src/txmempool.h`, `src/txmempool.cpp`: Understand the `CTxMemPool` class.
    2.  `src/policy/policy.h`, `src/policy/policy.cpp`: Understand the mempool's policy rules.

*   **Key Functions to Step Through:**
    *   `CTxMemPool::addUnchecked(...)`
    *   `CTxMemPool::remove(...)`
    *   `CTxMemPool::TrimToSize(...)`

*   **Exercises:**
    1.  **Visualize the mempool:** Write a script to connect to a node's RPC interface and get the contents of the mempool. Then, generate a graph of the transaction dependencies.
    2.  **Simulate a fee market:** Send a series of transactions with different fee rates and observe how they are prioritized by the mempool.

### 4. Wallet

*   **File Order:**
    1.  `src/wallet/wallet.h`, `src/wallet/wallet.cpp`: Understand the `CWallet` class.
    2.  `src/wallet/walletdb.h`, `src/wallet/walletdb.cpp`: Understand how the wallet is stored on disk.
    3.  `src/wallet/rpcwallet.cpp`: Understand the wallet-related RPC calls.
    4.  `src/script/sign.h`, `src/script/sign.cpp`: Understand how transactions are signed.

*   **Key Functions to Step Through:**
    *   `CWallet::CreateTransaction(...)`
    *   `CWallet::CommitTransaction(...)`
    *   `CWallet::ScanForWalletTransactions(...)`
    *   `CWallet::GetBalance()`

*   **Exercises:**
    1.  **Create a new type of address:** For example, add support for a new script type.
    2.  **Add a new RPC call:** For example, add an RPC call that returns the total number of transactions in the wallet.
    3.  **Trace a coin selection:** Use a debugger to step through the coin selection logic in `CreateTransaction`.
