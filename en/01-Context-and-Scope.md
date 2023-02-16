# Context and Scope

## Context KBLS

The KBLS project is motivated by the need to "provide new quantum computer-resistant methods in a form that can be used efficiently by as many as possible." The goal of the project is to "extend the botan crypto library with quantum computer resistant methods". Based on this, developers should be able to "implement solutions that can withstand attacks from quantum computers and thus guarantee long-term security of the processed data." In addition, "the performance and usability of the library will be evaluated."

To achieve these goals, a prototype demonstrator will be developed. This demonstrator will exemplify how the results from the previous work packages can be used. In particular, we hope to gain important insights with respect to

* the general usability of botan
* the specific use of botan for web clients
* the performance of applications with real-time requirements
* the realization of crypto-agility

In this regard, the most important intermediate results of the KBLS project are:

* [Implementation of Kyber](https://github.com/randombit/botan/pull/2872)
* [Implementation of Dilithium](https://github.com/randombit/botan/pull/2973)

## Use Case neXboard

The neXboard system provides a web-based solution for digital whiteboards. A board includes the following functionalities:

* creating post-its
* uploading images
* linking multiple post-its or images

The size and position of post-its and images can be changed. In addition, the color and content of post-its can be changed.
The content of a board can be edited by several users at the same time.

![neXboard-Applikation](../images/01-nexboard-screenshot.png)

Registered users can create boards and share them with other users. The access rights for a board are configurable: an
invitation can allow read or write access. Individual boards can also be shared via "public link", which is also accessible
without neXboard registration.

## Security Goals

The demonstrator aims to show that the results from previous work can be used to protect the contents of a board against
attacks by quantum computers. Two specific protection goals are derived from this:

* Confidentiality:
  * The content of all post-its on a board must only be accessible to authorized users.
  * Further, any content should not be readable by the server by default.
* Integrity:
  * Changes to the content of a post-it can only be made by authorized users.
  * In particular, only the integrity of each individual post-it is protected, not the integrity of the entire board.

Authorized users gain access to all content since the creation of a board (no backward secrecy). However, it should be possible
to revoke access rights so that content is no longer accessible from that point on (forward secrecy).

The measures taken to achieve this must fulfill these goals in their entirety. If one of the measures is susceptible to a
viable attack, or a measure is missing to ensure complete protection, then the protection goal is not met.

## Simplifying Assumptions

* Server is "honest-but-curious"[^1], which means it will not actively try to modify the communication, but it is not trusted
  with respect to confidentiality. In particular, also:
  * Client application integrity: the server delivers the frontend unchanged[^2] and does not modify the data stored for the Clients.
  * Completeness: the server is trusted not to withhold any post-its.
* Availability: The neXboard server is highly available.
* Security of cryptographic primitives: AES-256, SHA-256 and Kyber are quantum resistant up to a sufficient security level.

## Non-Goals

To show the limits of the security goals, we also name limitations of the system that are deliberately left unprotected.
remain unprotected. The alternative of excluding these constraints is only feasible with other constraints, e.g., on the
maintainability, operability, or performance of the system. Individual non-goals can later be changed to security goals,
but this has far-reaching consequences and may require significant changes to the concept.

* No special measures are taken to defend against an actively attacking application server.
* Confidentiality and integrity of the entire board is not ensured, only the confidentiality of all individual post-it
  contents. In particular, this concerns the metadata of post-its (position, size, color, links, version). So the server
  can, for example, move post-its or display old post-its.
* No authenticity or deniability: The unique authorship of a post-it does not have to be ensured.
  * Users have no way to clearly determine or deny who wrote a particular post-it.
  * Users also have no way to clearly determine or deny who issued a particular Board Key.
* No backward secrecy: Invited users get access to all previously shared data.
* Limited Forward Secrecy: If an attacker gains access to a board key, they will gain access to all post-it content that was
  encrypted under this board key. In particular, new post-it content also becomes accessible to her, which is created after
  the compromise, but before other users are excluded.

[^1]: Regarding all data except the password. Due to the current structure of neXboard, users have to log in with username
and password. In the described system, however, the password also plays a security-critical role for storing user's private
keys. Therefore, the neXboard server is actually implemented by two servers: an auth server, which receives the user password
for login, and the application server, which never receives the user password. Assuming an honest auth server (attack surface
is lower, since no user input other than user ID and password is processed and only login functionality has to be implemented),
the aforementioned attack model of a passive (honest-but-curious) attacker applies to the application server. This distinction
protects against a stronger attack model than in the normal case of the non-hybrid neXboard, in which case both components of
the server must be considered honest.

[^2]: As long as the frontend is part of the application, this attack vector remains inherent to the neXboard. If only
"static" clients were used, e.g. smartphone applications, the risk could be reduced by publishing the source code and
enabling of self-builds. However, since neXboard supports web clients in particular, this mitigation cannot be realized
and neXboard cannot achieve security against an actively malicious server.
