# API Spezifikation + User Flows

Die aktuelle API-Spezifikation ist unter folgender Quelle zu finden:
<https://github.com/neXenio/kbls-pqc-demonstrator-api>
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

```yml
KYBER:
- public key base64: MIIFQzCBlwYJKoZIhvcNAQMBMIGJ...
- private key base64: MIIKBQIBADCBlwYJKoZIhvcNAQM...
- encryption salt: jv/DRgVf5WW5d5BTCyozOQ==

RSA:
- public key base64: MIICIjANBgkqhkiG9w0BAQEFAAOC...
- private key base64: MIIJRAIBADANBgkqhkiG9w0BAQE...
- encryption salt: dHY/g2fGyCTRlIrgK5keng==
```

2. Encryption key für die geheimen Schlüssel ableiten aus dem Nutzerpasswort und encryption salts

```
encryption key        = pbkdf2(password, encryption salt)
iv                    = sha256(public key)
encrypted private key = aes256gcm(private key, encryption key, iv)
```

3. Ergebnis als DTO encodieren (beispielhaft für Kyber)

```json
{
  "publicKey": {
    "publicKeyAlgorithm": "KYBER",
    "pkBase64": "MIIFQzCBlwYJKoZIhvcNAQMBMIGJ..."
  },
  "encryptedPrivateKey": {
    "skEncryptionAlgorithm": "AES_256_GCM_PBKDF2",
    "skCiphertext": "Rez0G6dTXc/Z6p7a9SB0noCR...",
    "skEncryptionSalt": "jv/DRgVf5WW5d5BTCyoz..."
  }
}
```

4. Key Pair DTOs mit user-ID aggregieren und an den Server schicken

![](../images/03-02-key-pair-registration.png)

> Das encryption salt für die Verschlüsselung des private keys ist aus kryptographischer Sicht an dieser Stelle
> redundant und unpräzise benannt:
>
> * Der resultierende encryption key wird nur einmal verwendet. Deshalb kann der IV hier ohne Sicherheitsbedenken
>   statisch sein (bspw. nur aus 0en bestehen) und das salt für PBKDF2 aus dem public key abgeleitet werden (bspw.
>   sha256(public key)). Somit ist ein separates encryption salt nicht erforderlich.
> * Das salt wird für PBKDF2 verwendet, nicht fürs Verschlüsseln. Das Verschlüsseln bedingt aber PBKDF2 und damit das salt.
>
> Wir verwenden an dieser Stelle trotzdem ein separates salt, um einerseits versehentlichen Fehlern an anderer Stelle
> vorzubeugen und andererseits weitere Verschlüsselungsmodi ohne API-Anpassungen zu ermöglichen.

## Board erstellen

Die Erstellung eines Boards erfordert die Erstellung eines Board keys und dessen Verschlüsselung. Der Nutzer, der
dieses Board erstellt, teilt das Board gewissermaßen mit sich selbst. Im oben beschriebenen hybriden
Schlüsseleinigungsverfahren nimmt der Nutzer die Rolle von Alice und Bob ein.

1. Board ID und Board Key generieren (16 zufällige Bytes)

```
board_id  = UUID.random()
board_key = prng(16)
```

2. Erstes Geheimnis mit Kyber erstellen und verschlüsseln

```
kyber_kem_result  = Kyber.kem(kyber_private_key_user, kyber_public_key_user)
secret1           = kyber_kem_result.secret
encrypted_secret1 = kyber_kem_result.encrypted_secret
```

3. Zweites Geheimnis mit RSA erstellen und verschlüsseln

```
rsa_kem_result    = RSA.kem(rsa_private_key_user, rsa_public_key_user)
secret2           = rsa_kem_result.secret
encrypted_secret2 = rsa_kem_result.encrypted_secret
```

4. Board Key verschlüsseln

```
key_encryption_key = HKDF(secret1, secret2)
iv = sha256(board_id)
encrypted_board_key = aes256gcm.enc(board_key, key_encryption_key, iv)
```

5. IDs für die public keys erstellen

```
id1 = sha256(kyber_public_key_user)
id2 = sha256(rsa_public_key_user)
```

6. Ergebnis als DTO encodieren

```json
{
  "boardId": "77c8be2d-9895-45ae-96da-b7234a210c4c",
  "source": { "id1": "59ec81ac05fdc91...", "id2": "06fafdfcc94157d..." },
  "target": { "id1": "59ec81ac05fdc91...", "id2": "06fafdfcc94157d..." },
  "encryptedBoardKey": "mW4mDrwpDcSvOBEDgBzN7DGKLd+FtRZViAIrDUCe3RTxNILBpv1kWQ==",
  "hybridEncryptionMode": "KYBER_768_RSA_4096",
  "encryptedKdfInput1": "MIIE4zCBlwYJKoZIhvcNAQMBMIGJAkEA...",
  "encryptedKdfInput2": "b5fwR9PJzjrA6TEx9ukiUXxvCSZp2h2e..."
}
```

7. Board Encryption Data DTO an den Server schicken

![](../images/03-03-create-board.png)

## Board bearbeiten

Sobald ein Board erstellt oder geöffnet ist, liegt die Board ID sowie der Board key vor. Jedes erstellte Post-It erhält
eine eindeutige ID. Jede Änderung an einem Post-It wird mit einem aktuellen Zeitstempel versehen. Aus der Post-It ID
und dem Zeitstempel ergibt sich der IV für die Verschlüsselung des Inhalts.

Weil sich der Board key im Laufe der Zeit ändern kann, wird zudem der Board key referenziert, mit dem der Post-It
Inhalt verschlüsselt wurde.

Änderungen können in Batches an den Server geschickt werden.

1. Post-It Inhalt

```
# given: board_key, postit_id, postit_content
timestamp                = System.now()
iv                       = sha256(postit_id, timestamp)
encrypted_postit_content = aes256gcm.enc(postit_content, board_key, iv)
board_key_id             = sha256(board_key)
```

2. Ergebnis als DTO encodieren

```json
{
  "objectId": "05402bfa9ff8bb20df8f29776e32c80c51b8fda88e1216b09fa54b5c9c5b3fd7",
  "timestamp": 1669823977123521245,
  "dataEncryptionMode": "AES_256_GCM",
  "ciphertext": "6Qe3UMxK7RBvr4Md9kV2+2VW2I3tsoAIaSci8nrZ/bp8HfLL4VG2zQ==",
  "boardKeyId": "cf5a8d5983625d5b3c662a843720aa387d41e8a9d8d4964d1e72a24021ce32f0"
}
```

3. Änderungen aggregieren und an den Server schicken

![](../images/03-04-edit-board.png)

## Board öffnen

Voraussetzung für eine Nutzerin Alice, ein Board zu öffnen, ist, dass das Board erstellt und mit Alice geteilt wurde.
Wir nehmen der Einfachheit halber an, Alice hat das Board selber erstellt und damit bereits mit sich selbst geteilt.
Zur besseren Veranschaulichung dieses Prozesses nehmen wir zudem an, dass Alice den Client seitdem neu gestartet hat
und lediglich ihre Zugangsdaten kennt.

Um das Board zu öffnen, muss zuerst der Board Key entschlüsselt werden. Dazu sind einerseits die geheimen Schlüssel des
Nutzers erforderlich und andererseits der verschlüsselte Board key. Alice holt sich beides vom Server:

1. Abrufen vom hybriden Schlüsselpaar beim Server

![](../images/03-05-01-get-keys.png)

```
user_id_alice               = "alice@acme.com"
hybrid_key_pair             = GET /keys/{user_id_alice}
encrypted_kyber_private_key = hybrid_key_pair.keyPair1.encryptedPrivateKey
encrypted_rsa_private_key   = hybrid_key_pair.keyPair2.encryptedPrivateKey
```

2. Entschlüsseln der geheimen Schlüssel

```
kyber_encryption_salt = encrypted_kyber_private_key.encryption_salt
kyber_encryption_key  = pbkdf2(password, kyber_encryption_salt)
kyber_ciphertext      = encrypted_kyber_private_key.skCiphertext
kyber_iv              = sha256(kyber_public_key)
kyber_private_key     = aes256gcm.dec(kyber_ciphertext, kyber_encryption_key, kyber_iv)

rsa_encryption_salt = encrypted_rsa_private_key.encryption_salt
rsa_encryption_key  = pbkdf2(password, rsa_encryption_salt)
rsa_ciphertext      = encrypted_rsa_private_key.skCiphertext
rsa_iv              = sha256(rsa_public_key)
rsa_private_key     = aes256gcm.dec(rsa_ciphertext, rsa_encryption_key, rsa_iv)
```

3. Abrufen aller für Alice verschlüsselten Board keys beim Server, weitere Schritte exemplarisch für den ersten Board key

![](../images/03-05-03-get-boards.png)

```
id1 = sha256(kyber_public_key)
id2 = sha256(rsa_public_key)

all_board_key_encryption_data = GET /boards/?id1={id1}&id2={id2}
board_key_encryption_data     = all_board_key_encryption_data.encryptionDataList.[0]

board_id   = board_key_encryption_data.boardId
source_id1 = board_key_encryption_data.source.id1
source_id2 = board_key_encryption_data.source.id2

source_hybrid_public_key = GET /keys/?id1={source_id1}&id2={source_id2}
source_kyber_public_key  = source_hybrid_public_key.pk1
source_rsa_public_key    = source_hybrid_public_key.pk2
```

4. Entschlüsseln des Board keys

```
enc_kdf_input1 = board_key_encryption_data.encryptedKdfInput1
enc_kdf_input2 = board_key_encryption_data.encryptedKdfInput2
kdf_input1     = Kyber.decrypt(enc_kdf_input1, kyber_private_key, source_rsa_public_key)
kdf_input2     = Kyber.decrypt(enc_kdf_input2, kyber_private_key, source_rsa_public_key)

encryption_key = hkdf(kdf_input1, kdf_input2)
iv             = sha256(board_id)

enc_board_key = board_key_encryption_data.encryptedBoardKey
board_key     = aes256gcm.dec(enc_board_key, encryption_key, iv)
board_key_id  = sha256(board_key)
```

5. Abrufen aller Post-It Inhalte beim Server mit anschließender Entschlüsselung

![](../images/03-05-05-get-events.png)

```
board_events = GET /events/{board_id}

same_board_key_id = lambda board_event: board_event.boardKeyId == board_key_id
events_encrypted_for_board_key = filter(same_board_key_id, board_events)

for board_event in events_encrypted_for_board_key:
  ciphertext       = board_event.ciphertext
  iv               = sha256(board_event.objectId, event.timestamp)
  board_event_data = aes256gcm.dec(ciphertext, board_key, iv)
```

## Board mit anderen Nutzern teilen

Der Prozess, ein Board mit einem anderen Nutzer zu teilen, ist im Wesentlichen der gleiche wie der Prozess, ein Board
zu erstellen. Die einladende Nutzerin heißt im folgenden Alice, der eingeladene Nutzer heißt Bob.

Der Prozess für Bob, das Board anschließend zu öffnen, ist der gleiche wie für Alice bereits oben beschrieben ist.

1. Alice kennt die Board ID und den Board Key
2. Erstes Geheimnis mit Kyber erstellen und verschlüsseln

```
kyber_kem_result  = Kyber.kem(kyber_private_key_alice, kyber_public_key_bob)
secret1           = kyber_kem_result.secret
encrypted_secret1 = kyber_kem_result.encrypted_secret
```

3. Zweites Geheimnis mit RSA erstellen

```
rsa_kem_result    = RSA.kem(rsa_private_key_alice, rsa_public_key_bob)
secret2           = rsa_kem_result.secret
encrypted_secret2 = rsa_kem_result.encrypted_secret
```

4. Board key verschlüsseln

```
key_encryption_key  = HKDF(secret1, secret2)
iv                  = sha256(board_id)
encrypted_board_key = aes256gcm.enc(board_key, key_encryption_key, iv)
```

5. IDs für die public keys erstellen

```
id1_source = sha256(kyber_public_key_alice)
id2_source = sha256(rsa_public_key_alice)
id1_target = sha256(kyber_public_key_bob)
id2_target = sha256(rsa_public_key_bob)
```

6. Ergebnis als DTO encodieren

```json
{
  "boardId": "77c8be2d-9895-45ae-96da-b7234a210c4c",
  "source": { "id1": "59ec81ac05fdc91...", "id2": "06fafdfcc94157d..." },
  "target": { "id1": "afb702e1abc4c77...", "id2": "8a885ac0dff6161..." },
  "encryptedBoardKey": "2dbDmY3h+OyV4MDNoeDC7zwb0NuIt/5UY2tIndhwD1slRSRt2QdYgA==",
  "hybridEncryptionMode": "KYBER_768_RSA_4096",
  "encryptedKdfInput1": "YjVmd1I5UEp6anJBNlRFeDl1a2lVWHh2...",
  "encryptedKdfInput2": "OGE4ODVhYzBkZmY2MTYxMTRiODk4NzA4..."
}
```

7. Board Encryption Data DTO an den Server schicken

![](../images/03-06-share-board.png)

## Public Link für ein Board erstellen

Public Links für ein Board zu erstellen ist technisch möglich, aber nicht zielführend, weil dies die Zweckmäßigkeit von
End-to-End-Encryption und zero-knowledge-Encryption verletzt. Insbesondere wäre der Server befähigt, die Inhalte der
Post-Its zu lesen, was das Schutzziel verletzt.

## Zugriffsrechte für ein Board entziehen

Wenn Nutzern die Zugriffsrechte entzogen werden, müssen alle künftigen Post-It Inhalte auf eine andere Weise
verschlüsselt werden, um das Schutzziel weiterhin zu erfüllen. Dazu wird ein neuer Board key erstellt und verteilt.
Dieser Prozess funktioniert weitestgehend wie die Prozesse für das Erstellen und Teilen des Boards.

1. Neuen Board Key generieren (16 zufällige Bytes)

```
new_board_key = prng(16)
```

2. Erstes Geheimnis mit Kyber erstellen

```
kyber_kem_result  = Kyber.kem(kyber_private_key_user, kyber_public_key_user)
secret1           = kyber_kem_result.secret
encrypted_secret1 = kyber_kem_result.encrypted_secret
```

3. Zweites Geheimnis mit RSA erstellen

```
rsa_kem_result    = RSA.kem(rsa_private_key_user, rsa_public_key_user)
secret2           = rsa_kem_result.secret
encrypted_secret2 = rsa_kem_result.encrypted_secret
```

4. Board Key verschlüsseln

```
key_encryption_key  = HKDF(secret1, secret2)
iv                  = sha256(board_id)
encrypted_board_key = aes256gcm.enc(board_key, key_encryption_key, iv)
```

5. IDs für die public keys erstellen

```
id1 = sha256(kyber_public_key_user)
id2 = sha256(rsa_public_key_user)
```

6. Ergebnis als DTO encodieren

```json
{
  "boardId": "77c8be2d-9895-45ae-96da-b7234a210c4c",
  "source": { "id1": "59ec81ac05fdc91...", "id2": "06fafdfcc94157d..." },
  "target": { "id1": "59ec81ac05fdc91...", "id2": "06fafdfcc94157d..." },
  "encryptedBoardKey": "mW4mDrwpDcSvOBEDgBzN7DGKLd+FtRZViAIrDUCe3RTxNILBpv1kWQ==",
  "hybridEncryptionMode": "KYBER_768_RSA_4096",
  "encryptedKdfInput1": "MIIE4zCBlwYJKoZIhvcNAQMBMIGJAkEA...",
  "encryptedKdfInput2": "b5fwR9PJzjrA6TEx9ukiUXxvCSZp2h2e..."
}
```

7. Board Encryption Data DTO an den Server schicken

```
TODO: image
```

8. Schritte 2-7 für alle Nutzer mit Zugriffsrechten wiederholen - exklusive aller Nutzer, deren Zugriffsrechte entzogen
   wurden.
9. Server informieren, dass der Board key gewechselt wurde

```
TODO: image
```

> Schritt 9 stellt sicher, dass der alte Board key nicht weiterhin benutzt wird. Anschließend verhindert der Server das
> Hinzufügen von Änderungen, die unter einem anderen Board key verschlüsselt wurden.
>
> Es ist möglich, dass in der Übergangszeit, also in der Zeit zwischen Schritt 1 und Schritt 9, weitere Post-It Inhalte
> unter dem alten Board key verschlüsselt und gepostet werden. Die initiale Absicht, weitere Post-It Inhalte
> unzugänglich zu machen, wird also erst mit etwas Verzögerung technisch durchgesetzt.

> Nach Schritt 9 müssen alle Clients den neuen Board key beim Server erfragen, siehe dazu den Workflow "Board öffnen".
