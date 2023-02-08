# Überlegungen zur Weiterentwicklung

## Sicherheit: Schutz der privaten Schlüssel

### Dezentrale Speicher

Die privaten Schlüssel der Nutzer:innen werden zur besseren Bedienbarkeit aktuell verschlüsselt beim Server gespeichert.
Mit geeigneten Überlegungen und Einschränkungen bzgl. der Bedienbarkeit können die privaten Schlüssel beim Client
gespeichert werden. Dies schmälert die Konsequenzen eines bösartigen oder kompromittierten Servers signifikant.

### Alternativen zu PBKDF2

Die Verwendung von PBKDF2 erlaubt einen Offline-Angriff auf das `user_password` durch einen bösartigen oder kompromittierten
Server. Ein PAKE, beispielsweise [OPAQUE](https://eprint.iacr.org/2018/163.pdf), könnte hier Abhilfe schaffen. Wird hier
der serverseitige OPRF-Schlüssel gemeinsam mit der Passwortdatenbank geleakt, besteht das Problem allerdings trotzdem weiter.

## Sicherheit: Regelmäßige Rotation von Board Keys

Da die Board Keys unmittelbar die Inhaltsdaten verschlüsseln, ließe sich überlegen, diese nicht nur im Falle des Entfernens
einer Nutzer:in zu rotieren, sondern regelmäßig (etwa einmal pro Woche oder alle `n` Events). Der Nutzen beschränkt sich
aber auf den Fall, in dem eine Angreifer:in kurzzeitig und unbemerkt Zugang zum aktuellen Board Key erlangt hat und steht
den [untengenannten Überlegungen](#performanz-effiziente-einladung) in Bezug auf die Performanz entgegen.

## Performanz: Ein einzelnes Salt für Verschlüsselung der privaten Schlüssel

Das derzeitige Konzept sieht vor den KEK für die Verschlüsselung der privaten Schlüssel aus dem Passwort mittels PBKDF2
zu berechnen. Diese Ableitung sollte (da Passwort-basiert) "lange" dauern. Nun werden aber nicht beide privaten Schlüssel
mit demselben KEK verschlüsselt, sondern separate KEKs mit unterschiedlichen Salts generiert. Das bedeutet, dass die teure
KDF mehrfach berechnet wird.

Eine performantere Konstruktion, die sowohl unterschiedliche Schlüssel, eine Härtung durch zufälliges Salt und lange Ableitzeit
umsetzt, wäre die Ableitung eines Master-Schlüssels auf Basis des Passworts und die Ableitung spezifischer Schlüssel durch
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

## Performanz: Effiziente Einladung

Der Aufwand für das Hinzufügen neuer Nutzer:innen ist proportional zur Anzahl der Board Keys. Wird einem Board nämlich eine
neue Nutzer:in hinzugefügt, müssen alle noch im Einsatz befindlichen Board Keys an diese verschlüsselt werden. Theoretisch
können alle jemals existierenden Board Keys eines Boards gleichzeitig noch im Einsatz sein (z.B. ein Board Key je Post-it).
Da jeder Board Key mit einem neuen hybriden KEM-Aufruf einhergeht, kann bei einer Vielzahl an Board Keys die Dauer, die
das Hinzufügen der neuen Nutzer:in benötigt, theoretisch beträchtlich sein.

Um Einladungen effizienter durchzuführen, sind folgende Ansätze denkbar:

1. Bei einer Board Key Rotation werden alle bisherigen Post-it-Inhalte auf den neuen Board Key umgeschlüsselt; alte Board
Keys werden anschließend gelöscht. Dies hat zur Folge, dass das Ausladen von Nutzer:innen mehr Aufwand mit sich bringt,
dafür muss für neu eingeladene Nutzer:innen nur ein Board Key verschlüsselt werden.
2. Wird eine neue Nutzer:in eingeladen, wird der gleiche KEK verwendet, um die verschiedenen Board Keys zu verschlüsseln.
