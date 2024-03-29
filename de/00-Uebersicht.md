# KBLS × neXboard

## Übersicht

Im [Projekt KBLS](https://www.forschung-it-sicherheit-kommunikationssysteme.de/projekte/kbls) werden kryptografische
Verfahren entwickelt, die robust gegen Angriffe von Quantencomputern sind. Als Anwendungsfall verwenden wir den Web-Client
von [neXboard](https://nexboard.nexenio.com/app/login). neXboard bietet eine Lösung für digitale Whiteboards, auf denen die
Nutzer:innen Post-its erstellen und bearbeiten können - kollaborativ und in Echtzeit. Ziel ist, die Inhalte der Post-its
quantenresistent zu schützen, ohne die interaktiven Möglichkeiten des Boards einzuschränken.

Zu diesem Zweck wird für jedes Board ein sogenannter Board Key erstellt, der die Post-its symmetrisch verschlüsselt. Der
Board Key selbst wird so verschlüsselt, dass er mit beliebig vielen Nutzer:innen mit Zugriffsrechten geteilt werden kann
und quantenresistent geschützt ist. Werden Nutzer:innen die Zugriffsrechte entzogen, ändert sich der Board Key, damit alle
zukünftigen Inhalte geschützt sind.

Auf diese Weise prüfen und zeigen wir einerseits die Funktionalität, Bedienbarkeit und Performanz der Kryptobibliothek
[botan](https://github.com/randombit/botan), sowie anderseits die Praktikabilität eines hybriden Schlüsseleinigungsverfahrens.

## Details

* [Kontext und Umfang](./01-Kontext-und-Umfang.md)
* [Vorgesehene Architektur](./02-Vorgesehene-Architektur.md)
* [API Spezifikation + User Flows](./03-API-Spezifikation+User-Flows.md)
* [Überlegungen zur Weiterentwicklung](./04-Ueberlegungen-zur-Weiterentwicklung.md)
* [Testvektoren](./05-Testvektoren.md)

## Ausblick

Wir erhoffen uns durch die Realisierung des Demonstrators wichtige Einsichten in die folgenden Punkte:

* Performanz von botan bei Echtzeit-Kollaboration
* Bedienbarkeit von botan
* Praktikabilität eines hybriden Schlüsseleinigungsverfahrens für Krypto-Agilität

Aufbauend auf dem Demonstrator können zukünftig weitere Sicherheitsmechanismen konzipiert und implementiert werden:

* Schutz weiterer Information (der Titel eines Boards, die Farbe oder Position eines Post-its, etc.)
* Authentizität durch (hybride) digitale Signaturen
* Prüfung der Vollständigkeit verschlüsselter Daten
* Schlüsselmaterial beim Client speichern
