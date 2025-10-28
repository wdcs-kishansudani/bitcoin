# Bitcoin Core Workflow

This document provides a high-level overview of the primary subsystems within Bitcoin Core and their interactions.

## Subsystem Interaction Diagram

```mermaid
graph TD
    subgraph "External World"
        Peer[Peer Node]
        User[User]
    end

    subgraph "Bitcoin Core Node"
        subgraph "Network Layer"
            Net[net_processing.cpp<br>net.cpp]
        end

        subgraph "Validation & Mempool"
            Validation[validation.cpp]
            Mempool[txmempool.cpp]
        end

        subgraph "Block Production"
            Miner[miner.cpp]
            BlockAssembler[node/blockassembler.cpp]
        end

        subgraph "User Interfaces"
            RPC[rpc/*]
            Wallet[wallet/*]
        end
    end

    Peer -- "Receives TXs/Blocks (inv)" --> Net
    Net -- "Process Messages" --> Validation
    Validation -- "Validate TX" --> Mempool
    Validation -- "Validate Block" --> BlockAssembler
    Mempool -- "Provides TXs" --> BlockAssembler
    BlockAssembler -- "Create Block Template" --> Miner
    Miner -- "Find PoW Solution" --> BlockAssembler
    BlockAssembler -- "Submit Block" --> Validation
    Validation -- "Broadcast new block" --> Net
    Net -- "Relay to Peers" --> Peer

    User -- "RPC Commands (e.g., sendtoaddress)" --> RPC
    RPC -- "Interact with" --> Wallet
    RPC -- "Interact with" --> Validation
    RPC -- "Interact with" --> Mempool
    Wallet -- "Create & Sign TX" --> Validation
```

## Component Descriptions

*   **Peer-to-Peer Network (`net_processing.cpp`, `net.cpp`):** Handles all communication with other nodes on the Bitcoin network. This includes discovering new peers, maintaining connections, and relaying transactions and blocks.
*   **Validation Engine (`validation.cpp`):** The heart of consensus enforcement. It validates new blocks and transactions against the full set of Bitcoin's rules (syntax, proof-of-work, script execution, etc.). It maintains the chain state (UTXO set) in `chain.h` and `coins.h`.
*   **Mempool (`txmempool.cpp`):** The transaction memory pool holds unconfirmed transactions that are waiting to be included in a block. It's an in-memory cache that prioritizes transactions based on fees.
*   **Miner (`miner.cpp`, `node/blockassembler.cpp`):** Responsible for creating new blocks. The `BlockAssembler` constructs a block template by selecting transactions from the mempool, and the `Miner` performs the proof-of-work computation.
*   **Wallet (`wallet/*`):** Manages the user's private keys, tracks balances, and creates and signs new transactions.
*   **RPC Interface (`rpc/*`):** Provides a JSON-RPC interface for users and applications to interact with the node (e.g., check balances, send transactions, get block information).
