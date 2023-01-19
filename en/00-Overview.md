# KBLS × neXboard

## Overview

In the KBLS project, cryptographic methods are developed that are robust against attacks from quantum computers. As a use case, we use the web client of neXboard. neXboard provides a solution for digital whiteboards on which users can create and edit Post-Its - collaboratively and in real time. The goal is to protect the content of the Post-Its in a quantum resistant way without limiting the interactive possibilities of the board.

For this purpose, a so-called Board Key is created for each board, which symmetrically encrypts the Post-Its. The board key itself is encrypted in such a way that it can be shared with any number of users with access rights and is quantum-resistently protected. When a user's access rights are revoked, the board key changes to protect all future content.

In this way, we test and demonstrate the functionality, usability, and performance of the botan crypto library on the one hand, and the practicality of a hybrid key agreement procedure on the other.

## Details

* [Context and Scope](./01-Context-and-Scope.md)
* [Proposed Architecture](./02-Proposed-Architecture.md)
* [API Specification + User Flows](./03-API-Specification+User-Flows.md)
* [Considerations for Further Development](./04-Considerations-for-Further-Development.md)

## Outlook

We hope that the realization of the demonstrator will provide important insights into the following:

* Performance of botan in real-time collaboration.
* Usability of botan
* Practicability of a hybrid key agreement procedure for crypto-agility.

Beyond the described goals, we would like to achieve the following points:

* Protection of other information (the title of a board, the color or position of a post-it, etc.).

Building on the demonstrator, further security mechanisms can be designed and implemented in the future:

* Authenticity through (hybrid) digital signatures.
* Mechanisms to check completeness of encrypted data
* Storing key material at the client
