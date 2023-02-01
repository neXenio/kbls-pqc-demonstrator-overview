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

Ergebnis des hybriden Schlüsseleinigungsverfahrens ist, dass zwei Kommunikationspartner:innen Alice und Bob Schlüsselmaterial
ausgetauscht haben. Dieses Verfahren ist generisch konzipiert, d.h. es kann für verschiedene Use Cases verwendet werden.

Im Kontext von neXboard wird dieses Schlüsselmaterial dafür verwendet, einen sogenannten Board Key zu verschlüsseln. Der
Board Key wiederum wird verwendet, um die Inhalte aller Post-its im Board zu schützen. Der Board Key ist also ein Data
Encryption Key (DEK), weil dieser Daten verschlüsselt. Das zwischen Alice und Bob ausgetauschte Schlüsselmaterial ist
ein Key Encryption Key (KEK), weil dieser den (Data Encryption) Key verschlüsselt.

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
ohne Einbußen bei der (aktuellen) Sicherheit oder technischen Machbarkeit hinnehmen zu müssen.

### Ablauf

Der Ablauf für das vorgesehene hybride Schlüsseleinigungsverfahren ist wie folgt:

1. Zwei Kommunikationspartner:innen Alice und Bob haben zwei Schlüsselpaare, bestehend aus einem öffentlichen Schlüssel
   (public key) zugänglich für beide und einem privaten Schlüssel (private key), der nur ihnen bekannt ist:
   * ein Schlüsselpaar für ein klassisches Verfahren, bspw. RSA
   * ein Schlüsselpaar für ein quantenresistentes Verfahren, bspw. Kyber
2. Alice benutzt Bobs öffentliche Schlüssel, um jeweils ein Geheimnis zu generieren und verschlüsseln. Dieser
   Mechanismus wird als Key Encapsulation Mechanism (KEM) bezeichnet. Alice kann darüber hinaus zusätzlich zu Bobs
   öffentlichen Schlüsseln auch ihre privaten Schlüssel verwenden, um Authentizität für die verschlüsselten Geheimnisse
   zu bewirken. Die verschlüsselten Werte schickt Alice an Bob. Aus den Geheimnissen leitet Alice mittels einer
   sogenannten Key Derivation Function (KDF) den geheimen Schlüssel (encryption key) ab, den sie für die spätere Kommunikation
   mit Bob verwenden wird.
3. Bob verwendet seine privaten Schlüssel, um die verschlüsselten Geheimnisse von Alice zu entschlüsseln. Falls Alice
   die Verschlüsselung authentifiziert hat, verwendet Bob zusätzlich die öffentlichen Schlüssel von Alice. Anschließend
   leitet auch Bob aus den entschlüsselten Geheimnissen mittels derselben KDF den geheimen Kommunikationsschlüssel ab.

Die untenstehende Grafik veranschaulicht diesen Prozess:

![Aufblauf der hybriden Schlüsseleinigung](../images/02-hybrid-encryption.png)

Quellen

* <https://neilmadden.blog/2021/02/16/when-a-kem-is-not-enough/>
* Kapitel 3.1 der Ausgabe "Kryptografie quantensicher gestalten" des BSI (15.12.2021)
* Java-Implementierung: <https://github.com/neXenio/kbls-pqc-demonstrator-client>

## Schutz des Schlüsselmaterials

### Benötigtes Schlüsselmaterial

Für das hybride Schlüsseleinigungsverfahren werden für alle Nutzer:innen wie oben beschrieben zwei Schlüsselpaare benötigt.
Das Ergebnis dieses Verfahrens ist ein KEK, der im Kontext von neXboard spezifisch für die Empfängerseite ist und mit jedem
Aufruf variiert. Der KEK ist bereits ausreichend geschützt, weil dieser nur durch das Wissen der privaten Schlüssel ermittelt
werden kann.

### Schutzkonzept

Öffentliche Schlüssel müssen nicht bezüglich ihrer Vertraulichkeit geschützt werden. Ihre Integrität ist allerdings wichtig,
da sie der zentrale Anker der Nutzeridentität sind. Da wir korrektes Verhalten des Servers voraussetzen, können diese zentral
beim Server gespeichert werden. Dadurch können zudem alle Nutzer:innen Zugriff auf die öffentlichen Schlüssel anderer Nutzer:innen
erhalten.

Private Schlüssel müssen geschützt werden. Eine Option besteht darin, die privaten Schlüssel lokal bei den Nutzer:innen
zu speichern. Da viele Nutzer:innen das neXboard von verschiedenen Geräten aus erreichen wollen, ist diese Option nicht
ohne weiteres umsetzbar. Für bessere Bedienbarkeit werden die privaten Schlüssel stattdessen verschlüsselt beim Server gespeichert.
Der symmetrische Schlüssel für diese Verschlüsselung leitet sich aus dem individuellen Passwort sowie zusätzlicher Entropie
ab. Der konkrete Prozess wird in der [API Spezifikation](03-API-Spezifikation%2BUser-Flows.md#Schlüsselpaare-registrieren)
beschrieben.

## Schutz der Post-it-Inhalte im neXboard

Die Inhalte der Post-its werden unter dem aktuellen Board Key verschlüsselt. Der konkrete Prozess wird in der [API Spezifikation](03-API-Spezifikation%2BUser-Flows.md#Board-bearbeiten)
beschrieben.

Jedes Post-it hat eine eindeutige ID und jede Änderung des Post-its ist mit einem eindeutigen Zeitstempel versehen, welche
der Server unverschlüsselt als Metadaten abspeichert. Um beim Prozess [Zugriffsrechte für ein Board entziehen](03-API-Spezifikation%2BUser-Flows.md#Zugriffsrechte-für-ein-Board-entziehen)
nicht viele Daten umschlüsseln zu müssen, können mehrere Board Keys parallel existieren, von denen aber nur mittels des
neusten Inhalte verändert oder hinzugefügt werden können.

## Diskussion des Modus für die Verschlüsselung von Post-it-Inhalten

Seit Jahren ist bekannt, dass für die Verschlüsselung nicht nur die Vertraulichkeit, sondern auch die Integrität durch den
eingesetzten Chiffre gewährleistet werden muss, da ansonsten in der Praxis vorkommende Entschlüsselungsorakel genügen, um
die Verschlüsselung auszuhebeln. Daher werden gemeinhin authentisierte Verschlüsselungsmodi, etwa Galois Counter Mode (GCM),
sogenannte AEADs genutzt.

Für den Anwendungsfall des neXboards sind die herkömmlichen AEADs leider nicht ohne Abstriche nutzbar. Insbesondere hat GCM
nicht die Eigenschaft, dass ein Ciphertext ausschließlich unter einem Schlüssel erfolgreich entschlüsselbar ist. Im Speziellen
ist es für Angreifer:innen sogar leicht, zwei Schlüssel zu kreieren, die einen gemeinsamen Ciphertext jeweils zu einem potenziell
sinnhaften Klartext entschlüsseln. Bei einem kollaborativen Board, in welches Nutzer:innen eigens verschlüsselte Nachrichten
hochladen und den symmetrischen Schlüssel jeweils an die Empfänger:innen asymmetrisch verschlüsseln, kann diese Eigenschaft
offensichtlich schadhaft genutzt werden. Dies gilt auch trotz der nicht gänzlichen freien Wahl der symmetrischen Schlüssel
durch den Einsatz der KEMs. Wie im [originalen Paper zu Message Franking](https://eprint.iacr.org/2017/664.pdf) beschrieben,
kann eine simple Encrypt-Then-HMAC-Konstruktion genutzt werden, um dieses Problem zu umgehen. Eine entsprechende Konstruktion
bedingt jedoch einen Master-Schlüssel, der zur kollisions-resistenten Ableitung zweier Unter-Schlüssel genutzt wird. Da
kein "Opening" vonnöten ist, ist die im Paper dargelegte "multiple-opening security" nicht notwendig.

Betrachten wir den Fall mit GCM erneut: Soll das Post-it unter beiden Schlüssel sinnhaft sein, muss es (da es nur einen Ciphertext
gibt) ein sinnhaftes Klartextpaar geben, dessen bitweise Differenz exakt der bitweisen Differenz der Keystreams (GCM basiert
auf dem CTR-Modus) entspricht. Entsprechend ist der Angriff für lange Klartexte nicht trivial. Da bei einem Angriff im Schritt
des Lösens des GHASH-Polynoms jeweils ein Ciphertextblock gezielt gewählt werden muss, um den gemeinsamen Tag korrekt zu
erhalten, skaliert der Angriff weiterhin umso schlechter auf mehrere Post-its und könnte bei entsprechendem Textvolumen nicht
praktikabel sein. Dagegen könnten durch Implementierungsentscheidungen eines Clients Post-its mit nicht druckbaren Zeichen
nicht angezeigt und somit der Angriff praktisch doch ermöglicht werden. Darüber hinaus können bösartige Nutzer:innen direkt
vor und nach einem Angriff auf ein einzelnes Post-it eine Key-Rotation bewirken, wodurch in jedem Fall ein gezielter Angriff
möglich ist.

Entsprechend entscheiden wir uns im neXboard bei der Verschlüsselung von Post-it-Inhalten dazu, nicht den AEAD GCM zu nutzen,
sondern verwenden stattdessen eine Encrypt-Then-HMAC-Konstruktion (CTR + HMAC). Für andere Anwendungsfälle, in denen die
Eigenschaft des Key-Commitments nicht entscheidend ist, wird hingegen GCM eingesetzt.

## Wiedererlangung der Sicherheit nach Verlust von Schlüsselmaterial

Wenn Schlüsselmaterial (Passwort, private Schlüssel oder Board Key) kompromittiert wurde, so kann eine Quantencomputer-Netzwerkangreifer:in
mittels des Netzwerkverkehrs die verschlüsselten Post-it-Inhalte abfangen und entschlüsseln, da TLS keine Post-Quanten-Sicherheit
bietet. Hat die Angreifer:in bereits einen Austausch mit den verschlüsselten Inhalten abgefangen, so ist die Vertraulichkeit
der Daten verloren. Hat sie noch keinen Austausch mit den Post-it-Inhalten abgehört, so kann sie diese mit dem nächsten
Abrufen durch eine Nutzer:in abfangen und entschlüsseln.

Wird vor diesem Aufruf bekannt, dass Schlüsselmaterial kompromittiert wurde (z.B. bei Verlust eines Passwort-Managers),
so können je nach Schlüsselmaterial unterschiedliche Schritte unternommen werden, um die Vertraulichkeit und Integrität
so weit wie möglich zu schützen.

Sind private Schlüssel einer Nutzer:in betroffen, so müssen diese rotiert werden (siehe [Schlüsselpaare registrieren](03-API-Spezifikation%2BUser-Flows.md#schlüsselpaare-registrieren))
und bisherige, für die Nutzer:in gespeicherte, verschlüsselte Board Keys gelöscht werden. Das gleiche gilt, wenn das Passwort
betroffen ist, wobei in diesem Fall zusätzlich das Passwort rotiert werden muss. Weiter sollten Board Keys als kompromittiert
angesehen werden, da sie bei Kompromittierung von Passwort oder privaten Schlüsseln über den nicht-postquantensicheren TLS-Datenverkehr
exponiert werden.

Ist ein Board Key betroffen, muss das Board komplett umgeschlüsselt werden (oder zumindest die zugehörigen Post-its).
Dies kann allerdings nur nach Abrufen der Daten und damit nach dem potentiellen Kompromittieren der Daten geschehen. Es
kann jedoch durch das Abrufen, Kopieren und Neu-Erstellen des Boards ein neues, nicht kompromittiertes Board erstellt werden.
Wird dieser Schritt mit dem Löschen des alten Boards ergänzt, so hat eine Netzwerk-Angreifer:in nur eine einzige Chance,
die Übertragung abzuhören. Dadurch stellt ein Boardwechsel eine bessere Alternative zum Akzeptieren der Kompromittierung
der Post-it-Inhalte dar.
