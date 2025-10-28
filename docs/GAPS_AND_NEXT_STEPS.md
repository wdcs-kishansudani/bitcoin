# Gaps and Next Steps

This document lists anything that appears missing or ambiguous in the codebase, recommended follow-ups, and up to 8 prioritized questions to ask maintainers.

## Missing or Ambiguous Areas

1.  **UNVERIFIED:** The codebase lacks a comprehensive architectural overview document. While `docs/developer-notes.md` provides some useful information, it is not a substitute for a high-level architectural diagram and a description of the major components and their interactions.

2.  **UNVERIFIED:** The error handling in the P2P layer is inconsistent. Some functions return a boolean value to indicate success or failure, while others throw an exception. A consistent error handling strategy would make the code easier to read and maintain.

3.  **UNVERIFIED:** The wallet's coin selection algorithm is not well-documented. It is difficult to understand how it works and what its privacy and performance characteristics are.

4.  **UNVERIFIED:** The codebase lacks a clear policy on the use of C++ features. Some parts of the code use modern C++ features, while others use an older style of C++. A consistent policy would make the code more readable and maintainable.

## Recommended Follow-ups

1.  **Create an architectural overview document:** This document should include a high-level diagram of the major components and their interactions, as well as a description of each component's role and responsibilities.

2.  **Refactor the P2P error handling:** The P2P error handling should be refactored to use a consistent strategy. This could be done by either using exceptions everywhere or by using a consistent set of error codes.

3.  **Document the coin selection algorithm:** The coin selection algorithm should be documented so that it is easier to understand and maintain. This documentation should include a description of the algorithm's privacy and performance characteristics.

4.  **Establish a C++ style guide:** A C++ style guide should be established to ensure that the codebase is consistent and readable. This style guide should specify which C++ features are allowed and how they should be used.

## Prioritized Questions for Maintainers

1.  What is the long-term vision for the architecture of Bitcoin Core? Are there any plans to refactor the major components?
2.  What is the preferred error handling strategy for the P2P layer?
3.  Are there any plans to document the coin selection algorithm?
4.  Is there a C++ style guide that should be followed?
5.  What is the process for getting a new feature merged into Bitcoin Core?
6.  Are there any plans to add support for a new script opcode?
7.  What is the best way to get involved in the Bitcoin Core development process?
8.  Are there any other areas of the codebase that need improvement?
