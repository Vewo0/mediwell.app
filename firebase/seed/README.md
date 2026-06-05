# MediWell V1.0 Testdaten

Diese Datei enthält Beispiel-Daten für Firebase Firestore:

- `admin_demo_uid`
- `employee_demo_uid`
- `customer_demo_uid`
- zwei Beispieltermine
- eine aktive Beispiel-Mitgliedschaft
- zwei Beispielräume
- einen vorbereiteten Raumzugang mit QR-Token
- einen Beispiel-Zutrittslog
- eine Beispielbenachrichtigung

## Wichtig

Die Werte `admin_demo_uid`, `employee_demo_uid` und `customer_demo_uid` sind Platzhalter.

Für echte Tests in Firebase:

1. Lege drei Benutzer in Firebase Authentication an.
2. Kopiere die Firebase Auth UID jedes Benutzers.
3. Ersetze die Platzhalter-UIDs in `mediwell_v1_test_data.json`.
4. Lege die Dokumente in Firestore mit exakt diesen Dokument-IDs an.

## Empfohlene Test-Accounts

| Rolle | Beispiel-E-Mail | Zweck |
| --- | --- | --- |
| Admin | admin@mediwell.example | Admin-Dashboard, Kundenübersicht, Terminverwaltung |
| Mitarbeiter | mitarbeiter@mediwell.example | Mitarbeiterrechte vorbereiten und prüfen |
| Kunde | kunde@mediwell.example | Registrierung, Profil, Termine buchen |

Passwörter werden nicht in Firestore gespeichert. Sie werden ausschließlich in Firebase Authentication verwaltet.

## QR-Testdaten

Der QR-Beispielzugang verwendet:

`mediwell://access/access_demo_infrared_customer?token=demo-token-change-before-production&v=1`

Dieser Token ist nur für lokale Tests gedacht. Für echte Nutzung muss pro Zugang ein zufälliger Token erzeugt und regelmäßig rotiert werden.
