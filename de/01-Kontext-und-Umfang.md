# Kontext und Umfang

## Kontext KBLS

Das Projekt KBLS ist motiviert durch die Notwendigkeit, "neue Quantencomputer-resistente Verfahren in einer Form zur
Verfügung zu stellen, die von möglichst vielen effizient genutzt werden können". Ziel des Projekts ist es, die
Kryptobibliothek botan "um Quantencomputer-resistente Verfahren zu erweitern". Darauf aufbauend sollen Entwickler:innen
"Lösungen umsetzen können, die Angriffen von Quantencomputern widerstehen und somit langfristige Sicherheit der
verarbeiteten Daten garantieren können". Darüberhinaus "wird die Leistungsfähigkeit und Benutzbarkeit der Bibliothek
evaluiert".

Um diese Ziele zu erreichen, soll im Arbeitspaket 13 ein prototypischer Demonstrator entwickelt werden. Der Demonstrator
soll exemplarisch aufzeigen, wie die Ergebnisse aus den vorangegangenen Arbeitspaketen genutzt werden können.
Insbesondere erhoffen wir uns wichtige Einsichten hinsichtlich

* der generellen Bedienbarkeit von botan
* der spezifischen Verwendung von botan für Web Clients
* der Performanz bei Anwendungen mit Echtzeit-Anforderung
* der Realisierung von Krypto-Agilität

Diesbezüglich sind die wichtigsten Ergebnisse aus den vorangegangenen Arbeitspaketen

* Empfehlungen + Implementierung für eine agile Krypto-API (AP2 + AP7)
* Spezifikation eines hybriden Schlüsseleinigungsverfahrens (AP5)
* Implementierung von Kyber (AP8)

## Use Case neXboard

Das System neXboard bietet eine Web-basierte Lösung für digitale Whiteboards. Ein Board umfasst folgende
Funktionalitäten:

* Erstellung von Post-Its
* Upload von Bildern
* Verknüpfung mehrerer Post-Its oder Bilder

Die Größe sowie Position von Post-Its und Bildern können verändert werden. Bei Post-Its können darüber hinaus die
Farbe und der Inhalt verändert werden. Die Inhalte eines Boards können von mehreren Nutzern gleichzeitig bearbeitet
werden.

![neXboard-Applikation](../images/01-nexboard-screenshot.png)

Registrierte Nutzer können Boards erstellen und mit anderen Nutzern teilen. Die Zugriffsrechte für ein Board sind
konfigurierbar: eine Einladung kann Zugriffe zum Lesen erlauben oder zum Schreiben. Einzelne Boards können außerdem per
"public link" geteilt werden, welcher auch ohne neXboard-Registrierung zugänglich ist.

## Schutzziele

Der Demonstrator soll zeigen, dass die Ergebnisse aus vorangegangenen Arbeitspaketen dafür verwendet werden können, die
Inhalte eines Boards vor Angriffen durch Quantencomputer zu schützen. Daraus leiten sich zwei konkrete Schutzziele ab:

* Vertraulichkeit: der Inhalt aller Post-Its auf einem Board darf nur für authorisierte Nutzer zugänglich sein
  * Weiter sollen jegliche Inhalte für den Server nicht standardmäßig lesbar sein
* Integrität: Veränderungen am Inhalt eines Post-Its können nur durch authorisierte Nutzer durchgeführt werden

Authorisierte Nutzer erhalten Zugriff auf alle Inhalte seit Erstellung eines Boards (keine backward secrecy).
Zugriffsrechte sollen aber entzogen werden können, damit Inhalte ab diesem Zeitpunkt nicht weiter zugänglich sind
(forward secrecy).

Die Maßnahmen, die dafür getroffen werden, müssen diese Ziele in ihrer Gesamtheit erfüllen. Wenn eine der Maßnahmen
anfällig ist für einen praktikablen Angriff oder eine Maßnahme fehlt, um einen vollständigen Schutz zu gewährleisten,
dann ist das Schutzziel nicht erfüllt.

## Vereinfachende Annahmen

Schützenswerte Information:

* Position, Größe, Farbe und Verknüpfungen eines Post-Its sind nicht zwingend zu schützen
* Authentizität: Die eindeutige Urheberschaft eines Post-Its muss nicht sichergestellt werden
* Server ist honest-but-curious[^1], das heißt er wird nicht aktiv versuchen die Kommunikation zu verändern, ihm wird aber
nicht bezüglich der Vertraulichkeit vertraut. Insbesondere auch:
  * Integrität der Client-Applikation: Der Server liefert das Frontend unverändert aus und verändert nicht die für die Clients
  gespeicherten Daten
  * Vollständigkeit: Dem Server wird vertraut, keine Post-Its zu unterschlagen
* Verfügbarkeit: der neXboard-Server ist hochverfügbar
* Sicherheit kryptografischer Primitive: AES-256, SHA-256 und Kyber sind bis zu einem ausreichenden Sicherheitslevel quantenresistent

[^1]: Bezüglich allen Daten außer dem Passwort. Aufgrund des bisherigen Aufbaus von neXboard müssen sich Nutzer
mit Nutzername und Passwort am Server anmelden. Im beschriebenen System nimmt das Passwort aber auch eine sicherheitskritische
Rolle beim Zwischenspeicher von privaten Nutzerschlüsseln ein. Daher wird der neXboard-Server tatsächlich durch zwei Server
implementiert: einen Auth-Server, der das Nutzerpasswort beim Login erhält, und den Applikationsserver, welcher das Nutzerpasswort
nie erhält. Unter der Annahme eines ehrlichen Auth-Servers (Angriffsoberfläche ist geringer, da kein Nutzerinput außer UserID
und Passwort verarbeitet wird und nur Login-Funktionalität umgesetzt werden muss), gilt für den Applikationsserver das erwähnte
Angriffsmodell eines passiven (honest-but-curious) Angreifers. Damit wird ein stärkeres Angriffsmodell abgesichert als im
Normalfall des nicht hybriden neXboards, in welchem beide Komponenten des Servers als ehrlich betrachtet werden müssen.
