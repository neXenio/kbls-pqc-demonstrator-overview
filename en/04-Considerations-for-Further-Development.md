# Considerations for Further Development

## Security: Protecting Private Keys

### Decentralized Storage

The private keys of the users are currently stored encrypted on the server for better usability. With suitable considerations
and restrictions regarding usability, the private keys can be stored at the client. This significantly reduces the consequences
of a malicious or compromised server.

### Alternatives to PBKDF2

The use of PBKDF2 allows an offline attack against the `user_password` by a malicious or compromised server. A PAKE, for
example [OPAQUE](https://eprint.iacr.org/2018/163.pdf), could be used to remedy this. However, if the server-side OPRF key
is leaked together with the password database, the problem still exists.

## Security: Regular Board Key Rotation

Since the board keys directly encrypt the content data, it could be considered to rotate them not only when revoking access,
but regularly (e.g., once a week or every `n` events). The benefit is limited to a scenario where an attacker has gained
access to the current board key briefly and unnoticed, and is not in line with the [considerations below](#performance-efficient-invites)
in terms of performance.

## Performance: Single Salt for Private Key Encryption

The current concept derives the KEK for encrypting private keys from the password using PBKDF2 . This derivation should
take a "long time" (since it is password-based). But the same KEK is not used to encrypt both private keys, instead two
KEKs with different salts are generated. This means that the expensive KDF is calculated twice.

A more performant construction that implements both different KEKs, hardening by random salt and long derivation time would
be to derive a master key based on the password and to then derive specific keys through a fast KDF with domain separation:

```python
master_key                  = pbkdf2(user_password, "encryptPrivateKeys" || salt)
kyber_secret_key            = hkdf(master_key, "KyberSecretKey")
rsa_secret_key              = hkdf(master_key, "RsaSecretKey")

kyber_iv                    = sha256(kyber_public_key)
rsa_iv                      = sha256(rsa_public_key)

encrypted_kyber_private_key = aes256gcm.encrypt(kyber_private_key, kyber_secret_key, kyber_iv)
encrypted_rsa_private_key   = aes256gcm.encrypt(rsa_private_key, rsa_secret_key, rsa_iv)
```

Note that this requires a change in the DTO.

## Performance: Efficient Invites

The effort for adding new users is proportional to the number of board keys. If a new user is added to a board, all board
keys still in use must be encrypted for this user. In theory, all ever existing board keys of a board can be in use at
the same time (e.g. one board key per post-it). Since each board key is accompanied by a new hybrid KEM call, the time required
to add a new user can be considerable if there are a large number of board keys.

For more efficient invitations, the following approaches can be implemented (also in combination):

1. during a board key rotation, all previous post-it content is re-encrypted to the new board key; old board keys are subsequently
   deleted. Consequently, access revocation is more expensive, but only one board key has to be encrypted for newly invited users.
2. If a new user is invited, the same KEK is used to encrypt the different board keys.

## Performance: Scheduling Access Revocation

The effort for removing invited users is proportional to the number of board keys multiplied by the number of invited users.
During this process, it is also important to ensure that no other activities cause an inconsistent state, such as adding
new post-it content encrypted under an old board key, adding more users, or removing additional users.

To perform access revocation more efficiently, the following approaches can be implemented (also in combination):

1. the information necessary for complete access revocation (new board key encrypted for all invited users) is sent to the
   server, but not enforced immediately. The server chooses an appropriate or configured time (e.g., 6 p.m.) to fully implement
   the alterations. A subsequent invitation or access removal for additional users must take this into account.
2. the concept of a "maintenance user" is added, who is invited to the board. This user has access to key material, in
   particular all public keys and the board keys. The maintenance user performs the board key rotation at an appropriate
   or configured time and considers all access revocations (invitations work as usual).

As a result, the removed users will continue to have access to new post-it content for a certain or configurable period of time.
