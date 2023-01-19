# Überlegungen zur Weiterentwicklung

## Schutz der privaten Schlüssel

### Dezentrale Speicher

Die privaten Schlüssel der Nutzer:innen werden zur besseren Bedienbarkeit aktuell verschlüsselt beim Server gespeichert.
Mit geeigneten Überlegungen und Einschränkungen bzgl. der Bedienbarkeit können die privaten Schlüssel beim Client
gespeichert werden. Dies schmälert die Konsequenzen eines bösartigen oder kompromittierten Servers signifikant.

### Alternativen zu PBKDF2

Die Verwendung von PBKDF2 erlaubt einen Offline-Angriff auf das `user_password` durch einen bösartigen oder kompromittierten
Server. Ein PAKE, beispielsweise [OPAQUE](https://eprint.iacr.org/2018/163.pdf), könnte hier Abhilfe schaffen. Wird hier der
serverseitige OPRF-Schlüssel gemeinsam mit der Passwortdatenbank geleakt, besteht das Problem allerdings trotzdem weiter.
