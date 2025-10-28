# Bitcoin Core Source Code Questions

This document contains a curated list of questions to test a developer's understanding of the Bitcoin Core source code. The questions are categorized by difficulty level (beginner, intermediate, advanced) and cover a wide range of topics, from basic architecture to complex consensus rules.

## Beginner Level

1.  What is the main entry point of the Bitcoin Core daemon?
2.  What is the purpose of the `init.cpp` file?
3.  What is the UTXO set?
4.  What is the purpose of the `net_processing.cpp` file?
5.  What is the mempool?
6.  What is the purpose of the `wallet/` directory?
7.  What is the purpose of the `rpc/` directory?
8.  What is the Bitcoin Script language?
9.  What are the core consensus rules?
10. What are the fundamental data structures used in the Bitcoin protocol?

## Intermediate Level

1.  Explain the initialization sequence of the Bitcoin Core daemon.
2.  Explain the process of block validation in `validation.cpp`.
3.  Explain the process of transaction validation in `validation.cpp`.
4.  Explain how the node communicates with the network in `net_processing.cpp`.
5.  Explain how the mempool prioritizes and evicts transactions in `txmempool.cpp`.
6.  Explain how the wallet creates and signs a transaction in `wallet/wallet.cpp`.
7.  Explain how the RPC interface is implemented in `rpc/`.
8.  Explain how the Script language is executed in `script/interpreter.cpp`.
9.  Explain the proof-of-work algorithm in `consensus/`.
10. Explain the structure of a block and a transaction in `primitives/`.

## Advanced Level

1.  Explain the reorg mechanism in `validation.cpp`.
2.  Explain the block propagation mechanism in `net_processing.cpp`.
3.  Explain the transaction relay mechanism in `net_processing.cpp`.
4.  Explain the fee estimation algorithm in `wallet/fees.cpp`.
5.  Explain the coin selection algorithm in `wallet/coinselection.cpp`.
6.  Explain the implementation of a specific RPC command in `rpc/`.
7.  Explain the implementation of a specific Script opcode in `script/interpreter.cpp`.
8.  Explain the implementation of a specific consensus rule in `consensus/`.
9.  Explain the implementation of a specific data structure in `primitives/`.
10. Explain a potential attack vector on the Bitcoin network and how the Bitcoin Core source code mitigates it.
