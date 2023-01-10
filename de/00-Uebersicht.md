# KBLS × neXboard

## Übersicht

Im Projekt KBLS werden kryptographische Verfahren entwickelt, die robust gegen Angriffe von Quantencomputern sind. Als Anwendungsfall
verwenden wir den Web-Client von neXboard. neXboard bietet eine Lösung für digitale Whiteboards, auf denen die Nutzer Post-Its
erstellen und bearbeiten können - kollaborativ und in Echtzeit. Ziel ist, die Inhalte der Post-Its quantenresistent zu schützen,
ohne die interaktiven Möglichkeiten des Boards einzuschränken.

Zu diesem Zweck wird für jedes Board ein sogenannter Board Key erstellt, der die Post-Its symmetrisch verschlüsselt. Der
Board Key selbst wird so verschlüsselt, dass er mit beliebig vielen Nutzern mit Zugriffsrechten geteilt werden kann und
quantenresistent geschützt ist. Wenn einem Nutzer die Zugriffsrechte entzogen werden, ändert sich der Board Key, damit alle
zukünftigen Inhalte geschützt sind.

Auf diese Weise prüfen und zeigen wir einerseits die Funktionalität, Bedienbarkeit und Performanz der Kryptobibliothek botan,
sowie anderseits die Praktikabilität eines hybriden Schlüsseleinigungsverfahrens.

## Details

* [Kontext und Umfang](./01-Kontext-und-Umfang.md)
* [Vorgesehene Architektur](./02-Vorgesehene-Architektur.md)
* [API Spezifikation + User Flows](./03-API-Spezifikation+User-Flows.md)

## Ausblick

Wir erhoffen uns durch die Realisierung des Demonstrators wichtige Einsichten in die folgenden Punkte:

* Performanz von botan bei Echtzeit-Kollaboration
* Bedienbarkeit von botan
* Praktikabilität eines hybriden Schlüsseleinigungsverfahrens für Krypto-Agilität

Über die beschriebenen Ziele hinaus möchten wir gerne folgende Punkte erreichen:

* Schutz weiterer Information (der Titel eines Boards, die Farbe oder Position eines Post-its, etc.)

Aufbauend auf dem Demonstrator können zukünftig weitere Sicherheitsmechanismen konzipiert und implementiert werden:

* Authentizität durch (hybride) digitale Signaturen
* Mechanismen um Vollständigkeit der verschlüsselten Daten zu prüfen
* Schlüsselmaterial beim Client speichern
