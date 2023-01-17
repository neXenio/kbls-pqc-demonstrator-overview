# Überlegungen zur Performance

## Ein einzelnes Salt für Verschlüsselung der privaten Schlüssel

Das derzeitige Konzept sieht vor den KEK für die Verschlüsselung der privaten Schlüssel aus dem Passwort mittels PBKDF2
zu berechnen. Diese Ableitung sollte (da Passwort-basiert) "lange" dauern. Nun werden aber nicht beide privaten Schlüssel
mit dem selben KEK verschlüsselt, sondern separate KEKs mit unterschiedlichen Salts generiert. Das bedeutet, dass die teure
KDF mehrfach berechnet wird.

Eine performantere Konstruktion, die sowohl unterschiedliche Schlüssel, eine Härtung durch zufälliges Salt und lange Ableitzeit
umsetzt wäre die Ableitung eines Master-Schlüssels auf Basis des Passworts und die Ableitung spezifischer Schlüssel durch
eine schnelle KDF mit Domänenseparierung:

```python
master_key                  = pbkdf2(user_password, "encryptPrivateKeys" || salt)
kyber_secret_key            = hkdf(master_key, "KyberSecretKey")
rsa_secret_key              = hkdf(master_key, "RsaSecretKey")

kyber_iv                    = sha256(kyber_public_key)
rsa_iv                      = sha256(rsa_public_key)

encrypted_kyber_private_key = aes256gcm.encrypt(kyber_private_key, kyber_secret_key, kyber_iv)
encrypted_rsa_private_key   = aes256gcm.encrypt(rsa_private_key, rsa_secret_key, rsa_iv)
```

Zu beachten ist hier, dass dies eine Änderung des DTO mit sich zieht.
