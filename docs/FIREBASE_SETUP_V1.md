# Firebase Setup für MediWell V1.0

## 1. Firebase Projekt erstellen

1. Firebase Console öffnen.
2. Neues Projekt erstellen.
3. Projektname: `mediwell-app`.
4. Google Analytics kann für V1.0 deaktiviert bleiben.

## 2. Firebase mit FlutterFlow verbinden

1. FlutterFlow Projekt öffnen.
2. Settings > Firebase öffnen.
3. Firebase Project auswählen.
4. FlutterFlow Setup Assistant ausführen.
5. Android, iOS und Web Apps nach Bedarf erzeugen.

## 3. Authentication aktivieren

Firebase Console:

1. Authentication öffnen.
2. Get started klicken.
3. Sign-in method öffnen.
4. Email/Password aktivieren.

## 4. Firestore erstellen

1. Firestore Database öffnen.
2. Datenbank erstellen.
3. Production Mode wählen.
4. Region möglichst nahe an Deutschland/EU wählen.

## 5. Firestore Rules einfügen

In Firebase Console:

1. Firestore Database öffnen.
2. Rules Tab öffnen.
3. Inhalt aus `firebase/firestore.rules` einfügen.
4. Publish klicken.

## 6. Storage aktivieren

1. Storage öffnen.
2. Get started klicken.
3. Production Mode wählen.
4. Rules aus `firebase/storage.rules` einfügen.

## 7. Indexes vorbereiten

Die Datei `firebase/firestore.indexes.json` enthält empfohlene Indexes für:

- Kundentermine nach Datum
- Admin-Terminlisten nach Status und Datum
- Kundenlisten nach Rolle und Nachname
- Benachrichtigungen nach Nutzer und Erstellzeit

Wenn FlutterFlow später einen fehlenden Index meldet, den Firebase-Link aus der Fehlermeldung öffnen und den Index bestätigen.

## 8. Testnutzer anlegen

In Firebase Authentication drei Nutzer anlegen:

| Rolle | E-Mail |
| --- | --- |
| Admin | admin@mediwell.example |
| Mitarbeiter | mitarbeiter@mediwell.example |
| Kunde | kunde@mediwell.example |

Danach die UID jedes Nutzers kopieren und in Firestore unter `users/{uid}` ein passendes Dokument anlegen.

Beispiele stehen in:

`firebase/seed/mediwell_v1_test_data.json`

## 9. Wichtig für FlutterFlow

FlutterFlow braucht dieselben Feldnamen wie Firestore.

Nicht ändern ohne Grund:

- `role`
- `customerId`
- `employeeId`
- `status`
- `isActive`
- `createdAt`
- `updatedAt`

Diese Feldnamen werden in Rules, Queries und Actions verwendet.
