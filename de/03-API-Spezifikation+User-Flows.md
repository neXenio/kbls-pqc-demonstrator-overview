# API Spezifikation + User Flows

Die aktuelle API-Spezifikation ist unter folgender Quelle zu finden:
https://github.com/neXenio/kbls-pqc-demonstrator-api
Die folgenden User Flows beschreiben, wie die API verwendet wird.

> Im Folgenden wird für hybride Schlüsselpaare der Einfachheit halber angenommen, dass sie aus einem 
> Kyber-Schlüsselpaar und einem RSA-Schlüsselpaar bestehen.
> 
> Die Mechanismen sind so konzipiert, dass Kyber durch ein anderes quantenresistentes Verfahren ersetzt werden kann 
> (bspw. McEliece) und RSA durch ein anderes klassisches Verfahren ersetzt werden kann (bspw. eine geeignete 
> Elliptic-Curve-Variante). Technisch lässt sich auch RSA mit EC kombinieren, aber dies hat keinen praktischen Nutzen.


## Nutzer registrieren

Am bestehenden Prozess der Nutzerregistrierung ändert sich nichts. Für die beschriebenen Funktionalitäten müssen aber 
für jeden Nutzer zwei Schlüsselpaare registriert werden. Die Verknüpfung erfolgt über die user-ID, die bei der 
Registrierung des Nutzers festgelegt wird (bspw. die E- Mail Adresse).

![](../images/03-01-user-registration.png)


## Schlüsselpaare registrieren

Jeder Nutzer benötigt für die beschriebenen Funktionalitäten zwei Schlüsselpaare, die clientseitig generiert und 
serverseitig persistiert werden. Jedes Schlüsselpaar enthält einen geheimen Schlüssel, der clientseitig verschlüsselt 
wird, damit er serverseitig nicht ausgelesen werden kann.

Dieser Prozess wird auch verwendet, um alte Schlüsselpaare durch neue abzulösen. Will ein Nutzer beispielsweise ein RSA 
Schlüsselpaar durch ein EC Schlüsselpaar ablösen, wird das aktuelle Kyber-Schlüsselpaar zusammen mit dem neuen EC 
Schlüsselpaar registriert. Auf die selbe Weise können technisch auch beide Schlüsselpaare gleichzeitig abgelöst werden, 
aber einen praktischen Nutzen gibt es dafür nicht.

1. Generiere Schlüsselpaare und encryption salts
```
KYBER
- public key base64: MIIFQzCBlwYJKoZIhvcNAQMBMIGJ...
- private key base64: MIIKBQIBADCBlwYJKoZIhvcNAQM...
- encryption salt: jv/DRgVf5WW5d5BTCyozOQ==

RSA
- public key base64: MIICIjANBgkqhkiG9w0BAQEFAAOC...
- private key base64: MIIJRAIBADANBgkqhkiG9w0BAQE...
- encryption salt: dHY/g2fGyCTRlIrgK5keng==
```
2. Encryption key für die geheimen Schlüssel ableiten aus dem Nutzerpasswort und encryption salts
```
encryption key = pbkdf2(password, encryption salt)
iv = sha256(public key)
encrypted private key = aes256gcm(private key, encryption key, iv)
```
3. Ergebnis als DTO encodieren (beispielhaft für Kyber)
```
{
  "publicKey": {
    "publicKeyAlgorithm": "KYBER",
    "pkBase64": "MIIFQzCBlwYJKoZIhvcNAQMBMIGJ..."
  }
  "encryptedPrivateKey: {
    "skEncryptionAlgorithm": "AES_256_GCM_PBKDF2",
    "skCiphertext": "Rez0G6dTXc/Z6p7a9SB0noCR...",
    "skEncryptionSalt": "jv/DRgVf5WW5d5BTCyoz..."
  }
}
```
4. Key Pair DTOs mit user-ID aggregieren und an den Server schicken
