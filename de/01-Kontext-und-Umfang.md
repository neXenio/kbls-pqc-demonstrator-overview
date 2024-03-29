# Kontext und Umfang

## Kontext KBLS

Das Projekt KBLS ist motiviert durch die Notwendigkeit, "neue Quantencomputer-resistente Verfahren in einer Form zur
Verfügung zu stellen, die von möglichst vielen effizient genutzt werden können". Ziel des Projekts ist es, die
Kryptobibliothek botan "um Quantencomputer-resistente Verfahren zu erweitern". Darauf aufbauend sollen Entwickler:innen
"Lösungen umsetzen können, die Angriffen von Quantencomputern widerstehen und somit langfristige Sicherheit der
verarbeiteten Daten garantieren können". Darüber hinaus "wird die Leistungsfähigkeit und Benutzbarkeit der Bibliothek
evaluiert".

Um diese Ziele zu erreichen, soll ein prototypischer Demonstrator entwickelt werden. Dieser Demonstrator soll exemplarisch
aufzeigen, wie die Ergebnisse aus den vorangegangenen Arbeitspaketen genutzt werden können. Insbesondere erhoffen wir uns
wichtige Einsichten hinsichtlich

* der generellen Bedienbarkeit von botan
* der spezifischen Verwendung von botan für Web Clients
* der Performanz bei Anwendungen mit Echtzeit-Anforderung
* der Realisierung von Krypto-Agilität

Diesbezüglich sind die wichtigsten Zwischenergebnisse des KBLS-Projekts:

* [Implementierung von Kyber](https://github.com/randombit/botan/pull/2872)
* [Implementierung von Dilithium](https://github.com/randombit/botan/pull/2973)

## Use Case neXboard

Das System neXboard bietet eine Web-basierte Lösung für digitale Whiteboards. Ein Board umfasst folgende
Funktionalitäten:

* Erstellung von Post-its
* Upload von Bildern
* Verknüpfung mehrerer Post-its oder Bilder

Die Größe sowie Position von Post-its und Bildern können verändert werden. Bei Post-its können darüber hinaus die
Farbe und der Inhalt verändert werden. Die Inhalte eines Boards können von mehreren Nutzer:innen gleichzeitig bearbeitet
werden.

![neXboard-Applikation](../images/01-nexboard-screenshot.png)

Registrierte Nutzer:innen können Boards erstellen und mit anderen Nutzer:innen teilen. Die Zugriffsrechte für ein Board sind
konfigurierbar: Eine Einladung kann Zugriffe zum Lesen oder zum Schreiben erlauben. Einzelne Boards können außerdem per
"public link" geteilt werden, welcher auch ohne neXboard-Registrierung zugänglich ist.

## Schutzziele

Der Demonstrator soll zeigen, dass die Ergebnisse aus vorangegangenen Arbeitspaketen dafür verwendet werden können, die
Inhalte eines Boards vor Angriffen durch Quantencomputer zu schützen. Daraus leiten sich zwei konkrete Schutzziele ab:

* Vertraulichkeit:
  * Der Inhalt aller Post-its auf einem Board darf nur für autorisierte Nutzer:innen zugänglich sein
  * Weiter sollen jegliche Inhalte für den Server nicht standardmäßig lesbar sein
* Integrität:
  * Veränderungen am Inhalt eines Post-its können nur durch autorisierte Nutzer:innen durchgeführt werden
  * Insbesondere wird nur die Integrität jedes einzelnen Post-its geschützt, nicht die des gesamten Boards

Autorisierte Nutzer:innen erhalten Zugriff auf alle Inhalte seit Erstellung eines Boards (keine backward secrecy).
Zugriffsrechte sollen aber entzogen werden können, damit Inhalte ab diesem Zeitpunkt nicht weiter zugänglich sind
(forward secrecy).

Die Maßnahmen, die dafür getroffen werden, müssen diese Ziele in ihrer Gesamtheit erfüllen. Wenn eine der Maßnahmen
anfällig ist für einen praktikablen Angriff oder eine Maßnahme fehlt, um einen vollständigen Schutz zu gewährleisten,
dann ist das Schutzziel nicht erfüllt.

## Vereinfachende Annahmen

* Server ist "honest-but-curious"[^1], das heißt er wird nicht aktiv versuchen, die Kommunikation zu verändern, ihm wird aber
nicht bezüglich der Vertraulichkeit vertraut. Insbesondere auch:
  * Integrität der Client-Applikation: Der Server liefert das Frontend unverändert aus[^2] und verändert nicht die für die
  Clients gespeicherten Daten.
  * Vollständigkeit: Dem Server wird vertraut, keine Post-its zu unterschlagen.
* Verfügbarkeit: Der neXboard-Server ist hochverfügbar.
* Sicherheit kryptografischer Primitive: AES-256, SHA-256 und Kyber sind bis zu einem ausreichenden Sicherheitslevel quantenresistent.

## Nicht-Ziele

Um die Grenzen der Schutzziele aufzuzeigen, benennen wir außerdem Einschränkungen des Systems, die bewusst ungeschützt
bleiben. Die Alternative, diese Einschränkungen auszuschließen, ist nur mit anderen Einschränkungen realisierbar, die
etwa die Wartbarkeit, Bedienbarkeit oder Performanz des Systems betreffen. Einzelne Nicht-Ziele können später in Ziele
geändert werden, dies hat allerdings weitreichende Folgen und erfordert teils signifikante Anpassungen am Konzept.

* Es werden keine besonderen Maßnahmen getroffen, um gegen einen aktiv angreifenden Applikationsserver zu verteidigen.
* Es wird nicht die Vertraulichkeit und Integrität des gesamten Boards sichergestellt, sondern nur die Vertraulichkeit
  aller einzelnen Post-it-Inhalte. Insbesondere werden die Metadaten von Post-its (Position, Größe, Farbe, Verknüpfungen, 
  Version) nicht besonders geschützt. Der Server kann also bspw. Post-its verschieben oder alte Post-its anzeigen.
* Keine Authentizität oder Deniability: Die eindeutige Urheberschaft eines Post-its muss nicht sichergestellt werden.
  * Nutzer:innen haben also keine Möglichkeit, eindeutig zu bestimmen oder abzustreiten, wer ein bestimmtes Post-it verfasst hat.
  * Nutzer:innen haben außerdem keine Möglichkeit, eindeutig zu bestimmen oder abzustreiten, wer einen bestimmten Board Key 
    ausgestellt hat.
* Keine Backward Secrecy: Eingeladene Nutzer:innen erhalten Einsicht auf alle bisher geteilten Daten.
* Begrenzte Forward Secrecy: Wenn eine Angreifer:in Zugriff auf einen Board Key erhält, erhält sie damit Zugriff auf alle
  Post-it-Inhalte, die unter diesem Board Key verschlüsselt werden. Insbesondere werden ihr auch neue Post-it-Inhalte zugänglich,
  welche nach der Kompromittierung, aber vor dem Ausschluss anderer Nutzer:innen, erstellt werden.

[^1]: Bezüglich allen Daten außer dem Passwort. Aufgrund des bisherigen Aufbaus von neXboard müssen sich Nutzer
mit Nutzername und Passwort am Server anmelden. Im beschriebenen System nimmt das Passwort aber auch eine sicherheitskritische
Rolle beim Zwischenspeicher von privaten Nutzerschlüsseln ein. Daher wird der neXboard-Server tatsächlich durch zwei Server
implementiert - einen Auth-Server, der das Nutzerpasswort beim Login erhält, und den Applikationsserver, welcher das Nutzerpasswort
nie erhält. Unter der Annahme eines ehrlichen Auth-Servers (Angriffsoberfläche ist geringer, da kein Nutzerinput außer UserID
und Passwort verarbeitet wird und nur Login-Funktionalität umgesetzt werden muss) gilt für den Applikationsserver das erwähnte
Angriffsmodell einer passiven (honest-but-curious) Angreifer:in. Damit wird ein stärkeres Angriffsmodell abgesichert als
im Normalfall des nicht hybriden neXboards, in welchem beide Komponenten des Servers als ehrlich betrachtet werden müssen.

[^2]: Solange das Frontend Teil der Applikation ist, verbleibt dieser Angriffsvektor dem neXboard inhärent. Würden ausschließlich
"statische" Clients, zum Beispiel Smartphone-Applikationen genutzt, so ließe sich das Risiko durch die Veröffentlichung
des Quellcodes und der Möglichkeit des Selber-Bauens reduzieren. Da neXboard jedoch insbesondere Web-Clients unterstützt,
kann diese Mitigation nicht realisiert werden und das neXboard kann keine Sicherheit gegen einen aktiv bösartigen Server
erreichen.
