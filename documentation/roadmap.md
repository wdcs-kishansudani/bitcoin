# Bitcoin Core Source Code Reading Roadmap

This roadmap provides a structured guide for developers who want to understand the Bitcoin Core source code. It is organized into three levels: Beginner, Intermediate, and Advanced.

## Beginner Level: The 10,000-Foot View

This level is for developers who are new to the Bitcoin Core source code. It provides a high-level overview of the architecture and the main entry point of the application.

1.  **`bitcoind.cpp`**: Start by reading the `main()` function in `bitcoind.cpp`. This will give you a high-level understanding of how the application is initialized and started.
2.  **`init.cpp`**: Next, read the `AppInitMain()` function in `init.cpp`. This will give you a more detailed understanding of the initialization process and how the different components of the node are tied together.
3.  **High-Level Architecture**: Review the documentation in `documentation/core_modules.md` to get a high-level overview of the core modules and their responsibilities.

## Intermediate Level: The Core Modules

This level is for developers who have a basic understanding of the Bitcoin Core source code and want to dive deeper into the core modules.

1.  **`validation.cpp`**: Start by reading the `ConnectBlock()` and `DisconnectBlock()` functions in `validation.cpp`. This will give you a deep understanding of how the UTXO set is managed.
2.  **`net_processing.cpp`**: Next, read the `ProcessMessage()` function in `net_processing.cpp`. This will give you a deep understanding of how the node communicates with the network.
3.  **`txmempool.cpp`**: Read the `addUnchecked()` and `removeForBlock()` functions in `txmempool.cpp`. This will give you a deep understanding of how the mempool works.
4.  **`wallet/wallet.cpp`**: Read the `CreateTransaction()` and `AddToWallet()` functions in `wallet/wallet.cpp`. This will give you a deep understanding of how the wallet works.

## Advanced Level: The Nitty-Gritty Details

This level is for developers who have a deep understanding of the Bitcoin Core source code and want to explore the more complex topics.

1.  **`script/interpreter.cpp`**: Start by reading the `EvalScript()` function in `script/interpreter.cpp`. This will give you a deep understanding of how the Script language is executed.
2.  **`consensus/`**: Read the code in the `consensus/` directory. This will give you a deep understanding of the core consensus rules of the Bitcoin protocol.
3.  **`policy/`**: Read the code in the `policy/` directory. This will give you a deep understanding of the transaction relay policy of the Bitcoin Core node.
4.  **`primitives/`**: Read the code in the `primitives/` directory. This will give you a deep understanding of the fundamental data structures used in the Bitcoin protocol.
