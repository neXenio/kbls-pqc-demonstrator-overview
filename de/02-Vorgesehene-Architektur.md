# Vorgesehene Architektur

## Client-Server-Modell

Wir verwenden ein einfaches Client-Server-Modell, wobei der Server lediglich zum Speichern und Abrufen aller notwendigen
Informationen dient. Die Businesslogik erfolgt überwiegend clientseitig, Ver- und Entschlüsselung erfolgt ausschließlich
clientseitig. Es gibt beliebig viele Clients und einen Server, über den sich die Clients austauschen.

Weitere Mechanismen wie Authentifizierung und Autorisierung, Caching, Pagination und Hochverfügbarkeit können ergänzt
werden, um Zugriffsrechte feingranular zu definieren und die Performanz zu verbessern.

## Hybrides Schlüsseleinigungsverfahren

Für den Demonstrator wird ein hybrides Schlüsseleinigungsverfahren umgesetzt basierend auf der Kyber-Implementierung für
botan. Darüber hinaus muss das Schlüsselmaterial, das hierfür zum Einsatz kommt, auf geeignete Weise geschützt werden.

Ergebnis des hybriden Schlüsseleinigungsverfahrens ist, dass zwei Kommunikationspartner Alice und Bob Schlüsselmaterial
ausgetauscht haben. Dieses Verfahren ist generisch konzipiert, d.h. es kann für verschiedene Use Cases verwendet werden.

Im Kontext von neXboard wird dieses Schlüsselmaterial dafür verwendet, einen sogenannten Board key zu verschlüsseln. Der
Board key wiederum wird verwendet, um die Inhalte aller Post-its im Board zu schützen. Der Board key ist also ein data
encryption key (DEK), weil dieser Daten verschlüsselt. Das zwischen Alice und Bob ausgetauschte Schlüsselmaterial ist
ein key encryption key (KEK), weil dieser den (data encryption) key verschlüsselt.

### Motivation

Die Motivation für das KBLS-Projekt ist, dass klassische Verfahren basierend auf RSA oder Elliptischen Kurven nicht
quantenresistent sind - dies wird im Laufe der nächsten Jahre zunehmend zum Sicherheitsrisiko. Auf der anderen Seite
sind die bisher entwickelten quantenresistenten Verfahren noch nicht produktiv erprobt - es könnte sich also im Laufe
der nächsten Jahre herausstellen, dass es praktikable Angriffe gibt. Darüber hinaus sind die quantenresistenten
Verfahren nicht derart gestaltet, dass die klassischen Verfahren direkt ausgetauscht werden können.

Daraus ergibt sich die Notwendigkeit, ein gänzlich neues Schlüsseleinigungsverfahren zu verwenden. Die Idee eines
hybriden Schlüsseleinigungsverfahrens erfüllt mehrere Zwecke:

* die Möglichkeit ein Verfahren tatsächlich direkt auszutauschen
* die beste Sicherheit von zwei Verfahren über einen längeren Zeitraum zu erhalten

Dies bewerkstelligt zum einen, dass die unbekannt lange Übergangsphase von klassischen zu quantenresistenten Verfahren
abgesichert ist: während das RSA-Kryptosystem über die Zeit mit dem Herannahen von Quantencomputern an Sicherheit verliert,
gewinnt die kryptografische Gemeinschaft Vertrauen in die Sicherheit von Kyber. Das liegt daran, dass die Sicherheit von
Verfahren darauf basiert, dass keine Möglichkeit bekannt ist, sie zu brechen. Je länger daran geforscht wird, die Sicherheit
eines Verfahrens zu brechen, ohne dass signifikante Angriffe bekannt werden, desto sicherer können wir in der Annahme gehen,
dass dies so bleibt.

Zum anderen sind wir durch den Wechsel auf ein hybrides Schlüsseleinigungsverfahren dazu in der Lage, eins der Verfahren
später auszutauschen. Beispielsweise könnte sich herausstellen, dass Kyber unsicher ist, aber sich ein anderes
quantenresistentes Verfahren wie McEliece bewährt hat. Wenn wir ein hybrides Verfahren aus RSA und Kyber verwenden und
zu dem Zeitpunkt, an dem Kyber kompromittiert wird, RSA weiterhin sicher ist, können wir Kyber gegen McEliece austauschen
ohne Einbußen bei der Sicherheit oder technischen Machbarkeit hinnehmen zu müssen.

### Ablauf

Der Ablauf für das vorgesehene hybride Schlüsseleinigungsverfahren ist wie folgt:

1. Zwei Kommunikationspartner Alice und Bob haben zwei Schlüsselpaare, bestehend aus einem öffentlichen Schlüssel
   (public key) zugänglich für beide und einem geheimen Schlüssel (private key), der nur ihnen bekannt ist:
   * ein Schlüsselpaar für ein klassisches Verfahren, bspw. RSA
   * ein Schlüsselpaar für ein quantenresistentes Verfahren, bspw. Kyber
2. Alice benutzt Bobs öffentliche Schlüssel, um jeweils ein Geheimnis zu generieren und verschlüsseln. Dieser
   Mechanismus wird als Key Encapsulation Mechanism (KEM) bezeichnet. Alice kann darüber hinaus zusätzlich zu Bobs
   öffentlichen Schlüsseln auch ihre geheimen Schlüssel verwenden, um Authentizität für die verschlüsselten Geheimnisse
   zu bewirken. Die verschlüsselten Werte schickt Alice an Bob. Aus den Geheimnissen leitet Alice mittels einer
   sogenannten Key Derivation Function (KDF) den encryption key ab, den sie für die spätere Kommunikation mit Bob
   verwenden wird.
3. Bob verwendet seine geheimen Schlüssel, um die verschlüsselten Geheimnisse von Alice zu entschlüsseln. Falls Alice
   die Verschlüsselung authentifiziert hat, verwendet Bob zusätzlich die öffentlichen Schlüssel von Alice. Anschließend
   leitet auch Bob aus den entschlüsselten Geheimnissen mittels derselben KDF den encryption key ab.

Die untenstehende Grafik veranschaulicht diesen Prozess:

![Aufblauf der hybriden Schlüsseleinigung](../images/02-hybrid-encryption.png)

Quellen

* <https://neilmadden.blog/2021/02/16/when-a-kem-is-not-enough/>
* Kapitel 3.1 der Ausgabe "Kryptografie quantensicher gestalten" des BSI (15.12.2021)
* Java-Implementierung: <https://github.com/neXenio/kbls-pqc-demonstrator-client>

## Schutz des Schlüsselmaterials

### Benötigtes Schlüsselmaterial

Für das hybride Schlüsseleinigungsverfahren werden für jeden Nutzer wie oben beschrieben zwei Schlüsselpaare benötigt.
Das Ergebnis dieses Verfahrens ist ein KEK, der im Kontext von neXboard spezifisch für den Empfänger ist und mit jedem Aufruf
variiert. Der KEK ist bereits ausreichend geschützt, weil dieser nur durch das Wissen der private keys ermittelt werden kann.

### Schutzkonzept

Public keys müssen nicht bezüglich ihrer Vertraulichkeit geschützt werden. Ihre Integrität ist allerdings wichtig, da sie
der zentrale Anker der Nutzeridentität sind. Da wir korrektes Verhalten des Servers voraussetzen, können diese zentral beim
Server gespeichert werden. Dadurch können zudem alle Nutzer Zugriff auf die public keys anderer Nutzer erhalten.

Private keys müssen geschützt werden. Eine Option besteht darin, die private keys lokal bei den Nutzern zu speichern. Da
jeder Nutzer das neXboard von verschiedenen Geräten aus erreichen will, ist diese Option nicht ohne weiteres umsetzbar. Für
bessere Usability werden die private keys stattdessen verschlüsselt beim Server gespeichert. Der symmetrische Schlüssel
für diese Verschlüsselung leitet sich aus dem Passwort des Nutzers sowie zusätzlicher Entropie ab. Konkret
wird hierfür folgender Prozess durchgeführt:

1. `encryption_key = pbkdf2(user_password, "encryptPrivateKeys" || salt)`
2. `encrypted_pq_secret_key = aes256gcm(pq_secret_key, encryption_key, sha256(pq_public_key))`
3. `encrypted_classic_secret_key = aes256gcm(classic_secret_key, encryption_key, sha256(classic_public_key))`
<!--
   TODO Frage:
   In 03 ließt sich das aber nach unterschiedlichen Salts je Verfahren, was dann zu einer doppelten Berechnung
   der bewusst teueren PBKDF2 führt.
   Ich bin von gleiches Salt, anderer IV durch anderen PubKey ausgegangen, was performanter wäre (und bei wenigen,
   user-gewählten Pubkeys effektiv kein Kollisionsrisiko hat)
   Man könnte in geiler sowas machen wie
      master_key = pbkdf2(user_password, "encryptPrivateKeys" || salt)
      kyber_secret_key = hkdf(master_key, "KyberSecretKey")
      rsa_secret_key = hkdf(master_key, "RsaSecretKey")
   Dann muss man nur einmal die teuere KDF rechnen und hat darauf basierend schnelle KDF mit Domain-Separation nach Verfahren

   TODO: je nach antwort, fix 02 und/oder 03
-->

Bemerkungen:

* Die zum Einsatz kommenden kryptografischen Funktionen sind PBKDF2, AES-256 im Galois-Counter-Modus (AES-GCM) und im
  Counter-Modus (AES-CTR) sowie SHA-256
* Der Wert `salt` besteht aus 16 Bytes, die von einem geeigneten Zufallszahlengenerator erstellt wurden, und wird
  gemeinsam mit `encrypted_pq_secret_key` und `encrypted_classic_secret_key` beim Server gespeichert.
* Der konstante String `encryptPrivateKeys` wird genutzt, um eine Domänenseparierung zugewährleisten, damit ein zum Beispiel
beim Auth-Server gespeicherter PBKDF2-Passworthash nicht dem encryption key entspricht.
* Der Wert `sha256(*_public_key)` wird auf 12 Bytes gestutzt, um der empfohlenen Größe für AES-GCM zu entsprechen.

## Schutz der Post-it-Inhalte im neXboard

Die Inhalte der Post-its werden unter dem aktuellen Board key verschlüsselt. Dafür wird ein zufälliger 12-Byte Initialisierungsvektor
`iv` genutzt.

* `iv                       = prng(12)`
* `encrypted_postit_content = aes256gcm(postit_content, board_key, iv)`

Jedes Post-it hat eine eindeutige ID `postit_id` und jede Änderung des Post-its ist mit einem eindeutigen Zeitstempel `ts`
versehen, welche der Server unverschlüsselt als Metadaten abspeichert.

## Diskussion des Modus für die Verschlüsselung

Seit Jahren ist bekannt, dass für die Verschlüsselung nicht nur die Vertraulichkeit, sondern auch die Integrität durch den
eingesetzten Chiffre gewährleistet werden muss, da ansonsten in der Praxis vorkommende Entschlüsselungsorakel genügen, um
die Verschlüsselung auszuhebeln. Daher werden gemeinhin authentisierte Verschlüsselungsmodi, etwa Galois Counter Mode (GCM),
sogenannte AEADs genutzt.

Für den Anwendungsfall des neXboards sind die herkömmlichen AEADs leider nicht ohne Abstriche nutzbar. Insbesondere hat GCM
nicht die Eigenschaft, dass ein Ciphertext ausschließlich unter einem Schlüssel erfolgreich entschlüsselbar ist. Im Speziellen
ist es für einen Angreifer sogar leicht, zwei Schlüssel zu kreieren, die einen gemeinsamen Ciphertext jeweils zu einem potenziell
sinnhaften Klartext entschlüsseln. Bei einem kollaborativen Board, in welches Nutzer eigens verschlüsselte Nachrichten hochladen
und den symmetrischen Schlüssel jeweils an die Empfänger asymmetrisch verschlüsseln, kann diese Eigenschaft offensichtlich
schadhaft genutzt werden. Dies gilt auch trotz der nicht gänzlichen freien Wahl der symmetrischen Schlüssel durch den Einsatz
der KEMs. Wie im [originalen Paper zu Message Franking](https://eprint.iacr.org/2017/664.pdf) beschrieben, kann eine simple
Encrypt-Then-HMAC-Konstruktion genutzt werden, um dieses Problem zu umgehen. Eine entsprechende Konstruktion bedingt jedoch
einen Master-Schlüssel, der zur kollisions-resistenten Ableitung zweier Unter-Schlüssel genutzt wird. Da kein "Opening" 
vonnöten ist, ist die im Paper dargelegte "multiple-opening security" nicht notwendig.

Betrachten wir den Fall mit GCM erneut: Soll das Post-It unter beiden Schlüssel sinnhaft sein, muss es (da es nur einen Ciphertext
gibt) ein sinnhaftes Klartextpaar geben, dessen bitweise Differenz exakt der bitweisen Differenz der Keystreams (GCM basiert
auf dem CTR-Modus) entspricht. Entsprechend ist der Angriff für lange Klartexte nicht trivial. Da bei einem Angriff im Schritt
des Lösens des GHASH-Polynoms jeweils ein Ciphertextblock gezielt gewählt werden muss, um den gemeinsamen Tag korrekt zu
erhalten, skaliert der Angriff weiterhin umso schlechter auf mehrere Post-Its und könnte bei entsprechendem Textvolumen nicht
praktikabel sein. Dagegen könnten durch Implementierungsentscheidungen eines Clients Post-Its mit nicht druckbaren Zeichen
nicht angezeigt und somit der Angriff praktisch doch ermöglicht werden. Darüber hinaus kann ein bösartiger Nutzer direkt vor
und nach seinem Angriff auf ein einzelnes Post-It eine Key-Rotation bewirken, wodurch in jedem Fall ein gezielter Angriff 
möglich ist.

Entsprechend entscheiden wir uns im neXboard bei der Verschlüsselung von Post-It-Inhalten dazu, nicht den AEAD GCM zu nutzen,
sondern verwenden stattdessen eine Encrypt-Then-HMAC-Konstruktion (CTR + HMAC). Für andere Anwendungsfälle, in denen die
Eigenschaft des Key-Commitments nicht entscheidend ist, wird hingegen GCM eingesetzt.
