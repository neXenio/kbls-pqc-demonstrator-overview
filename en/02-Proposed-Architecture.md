# Proposed Architecture

## Client-Server Model

We use a simple client-server model, where the server serves only to store and retrieve all necessary information. The business
logic is mainly performed on the client side, encryption and decryption is done exclusively on the client side. There are
any number of clients and a server through which the clients exchange information. The communication between clients and
server is done via a secured TLS connection.

The neXboard server consists of an auth server, which encapsulates the logic for identity and access management (IAM), and
an application server, which in particular stores the key material (public keys, encrypted private keys, and encrypted board
keys) and the encrypted post-it content. In the following the user has already identified themselves to the auth server,
"server" henceforth refers to the application server.

Other mechanisms such as authorization, caching, pagination, and high availability can be added to define access rights
in a fine-grained manner and to improve performance.

## Hybrid Key Agreement

For the demonstrator, a hybrid key agreement method is implemented based on the Kyber implementation for botan. In addition,
the key material used for this must be protected in a suitable way.

The result of the hybrid key agreement procedure is that two communication partners Alice and Bob have exchanged key material.
This procedure is designed generically, that is, it can be used for different use cases.

In the context of neXboard, this key material is used to encrypt a so-called board key. The board key is used to protect
the contents of all post-its on the board. The board key is therefore a data encryption key (DEK), because it encrypts data.
The key material exchanged between Alice and Bob is a key encryption key (KEK), because it encrypts the (data encryption) key.

### Motivation

The motivation for the KBLS project is that classical methods based on RSA or elliptic curves are not quantum resistant -
this will increasingly become a security risk in the course of the next few years. On the other hand, the quantum-resistant
methods developed so far have not yet been productively tested - so it could turn out in the course of the next few years
that there are viable attacks. Furthermore, the quantum-resistant methods are not designed in such a way that they can directly
replace the classical methods.

This results in the necessity to use an entirely new key agreement method. The idea of a hybrid key agreement procedure
serves several purposes:

* exchanging an algorithm in a modular way.
* to maintain the best security of two algorithms over a longer period of time.

This accomplishes on the one hand that the unknown long transition phase from classical to quantum resistant procedures
is secured: While the RSA crypto system loses security over time with the approach of quantum computers, the cryptographic
community gains confidence in the security of Kyber. This is because the security of methods is based on the fact that there
is no known way to break them. The longer research is done to break the security of an algorithm without significant attacks
becoming known, the more confident we can be in the assumption that this will continue.

On the other hand, by switching to a hybrid key agreement method, we are able to replace one of the algorithms at a later
point in time. For example, it might turn out that Kyber is insecure, but another quantum-resistant method such as McEliece
has proven itself secure. If we use a combination of RSA and Kyber, then at the time Kyber is compromised while RSA is still
secure, we can replace Kyber with McEliece without sacrificing (current) security or technical feasibility.

### Process

The process for the intended hybrid key agreement is as follows:

1. two communication partners Alice and Bob have two key pairs consisting of a public key accessible to both of them and
   a private key known only to them:
   * one key pair for a classical method, e.g. RSA
   * one key pair for a quantum resistant method, e.g. Kyber
2. Alice uses both of Bob's public keys to generate and encrypt one secret each. This mechanism is called the Key Encapsulation
   Mechanism (KEM). Alice can also use her private keys in addition to Bob's public keys to provide authenticity for the
   encrypted secrets. Alice sends the encrypted values to Bob. From the secrets, Alice derives the encryption key that she
   will use for later communication with Bob.
3. Bob uses his private keys to decrypt Alice's encrypted secrets. If Alice has authenticated the encryption, Bob also uses
   Alice's public keys. Subsequently, Bob also derives the secret communication key from the decrypted secrets using the
   same KDF.

The graphic below illustrates this process:

![Hybrid Key Agreement Process](../images/02-hybrid-encryption.png)

Sources

* <https://neilmadden.blog/2021/02/16/when-a-kem-is-not-enough/>
* Chapter 3.1 of "Kryptografie quantensicher gestalten", BSI (15.12.2021)

## Protecting Key Material

### Required Key Material

For the hybrid key agreement, two key pairs are required for all users as described above. The result of this procedure
is a KEK which, in the context of neXboard, is specific to the recipient side and varies with each call. The KEK is already
sufficiently protected, because it can only be determined by knowing the private keys.

### Safety Concept

Public keys do not need to be protected in terms of their confidentiality. However, their integrity is important, since
they are the central anchor of the user identity. Since we assume correct behavior of the server, they can be stored centrally
at the server. This also allows all users to gain access to the public keys of other users.

Private keys must be protected. One option is to store the private keys locally with the users. Since many users want to
access the neXboard from different devices, this option is not implemented without further ado. For better usability, the
private keys are instead stored encrypted on the server. The symmetric key for this encryption is derived from the individual
password and additional entropy. The concrete process is described in [API Specification](03-API-Specification%2BUser-Flows.md#Register-Key-Pairs).

## Protecting Post-it Content in neXboard

The contents of the post-its are encrypted under the current board key. The concrete process is described in [API Specification](03-API-Specification%2BUser-Flows.md#Board-edit).

Each post-it has a unique ID and each change to the post-it is assigned a unique timestamp, which the server stores unencrypted
as metadata. In order to avoid having to re-encrypt a lot of data during [revocation of board access rights](03-API-Specification%2BUser-Flows.md#Revoke-board-access-rights),
several board keys can exist in parallel, but only the newest one can be used to change or add content.

## Discussion of the encryption mode for Post-it content

It has been known for years that for encryption not only confidentiality, but also integrity must be guaranteed by the
cipher used, since otherwise decryption oracles that occur in practice are sufficient to break the encryption. Therefore,
so-called authenticated encryption modes (AEADs) are commonly used, such as Galois Counter Mode (GCM).

For the neXboard use case, the conventional AEADs are unfortunately not suitable. In particular, GCM does not have the property
that a ciphertext can be successfully decrypted exclusively under one key. In particular, it is even easy for attackers
to create two keys, each of which can decrypt a common ciphertext into a potentially meaningful plaintext. In the case of
a collaborative board, where users upload their own encrypted messages and asymmetrically encrypt the symmetric key to the
recipients, this feature can obviously be used in a harmful way. This is also true despite the fact that the symmetric keys
are not entirely free to choose by the use of the KEMs. As described in the [original paper on Message Franking](https://eprint.iacr.org/2017/664.pdf),
a simple Encrypt-Then-HMAC construction can be used to circumvent this problem. However, such a construction requires a
master key that is used to derive two subkeys in a collision-resistant manner. Since no opening is required, the multiple-opening
security presented in the paper is not necessary.

Let's consider the case with GCM again: If the post-it is to be meaningful under both keys, there must be a meaningful
plaintext pair (since there is only one ciphertext) whose bitwise difference is exactly the bitwise difference of the keystreams
(GCM is based on the CTR mode). Accordingly, the attack is non-trivial for long plaintexts. An attack during the step of
solving the GHASH polynomial must select one ciphertext block at a time to correctly obtain the common tag. Therefore, such
an attack scales poorly to multiple post-its and may not be practical if the text volume is large enough. In contrast, implementation
decisions by a client could cause post-its with non-printable characters to be hidden and thus practically enable an attack
after all. Additionally, malicious users are able to force a key rotation directly before and after an attack on a single
post-it, making a targeted attack possible in any case.

Accordingly, in neXboard we decide to not use the AEAD GCM when encrypting post-it content, but instead use an Encrypt-Then-HMAC
construction (CTR + HMAC). For other use cases where the property of the key commitment is not critical, GCM is used instead.

## Recovering Security after Loss of Key Material

If key material (password, private key, or board key) has been compromised, a quantum computer network attacker can
intercept and decrypt the encrypted post-it content using network traffic, since TLS does not yet provide post-quantum security.
If the attacker has already intercepted an exchange with the encrypted content, the confidentiality of the data is lost.
If the attacker has not yet intercepted an exchange with the post-it contents, they can intercept the contents with the next
retrieval by a user and decrypt them.

If it becomes known before this call that key material has been compromised (e.g., if a password manager is lost), different
measures can be taken to protect confidentiality and integrity as far as possible, depending on the key material.
