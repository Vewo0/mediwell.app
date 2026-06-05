# MediWell App - FlutterFlow Pages und Flows

Diese Datei beschreibt die FlutterFlow Pages fuer Version 1.0 und die vorbereiteten Erweiterungen fuer Mitgliedschaften und QR-Raumzugang.

## Navigation

Startlogik nach Login:

1. Nicht eingeloggt: `LoginPage`
2. `role = customer`: `CustomerDashboardPage`
3. `role = employee`: `EmployeeDashboardPage`
4. `role = admin`: `AdminDashboardPage`
5. `isActive = false`: Logout und Fehlermeldung

## V1.0 Pflicht-Pages

### 1. LoginPage

Zweck:

Nutzer anmelden.

Widgets:

- MediWell Logo oder Text
- E-Mail TextField
- Passwort TextField
- Login Button
- Link zu `RegisterPage`
- Link zu `ForgotPasswordPage`

Actions:

1. Login mit Firebase Authentication.
2. Dokument `users/{currentUserUid}` laden.
3. Wenn `isActive != true`: Logout und Fehlermeldung.
4. Rolle pruefen.
5. Zur passenden Dashboard Page navigieren.

### 2. RegisterPage

Zweck:

Neuen Kunden registrieren.

Widgets:

- Vorname TextField
- Nachname TextField
- E-Mail TextField
- Telefonnummer TextField
- Passwort TextField
- Passwort wiederholen TextField
- Registrieren Button
- Link zurueck zu `LoginPage`

Actions:

1. Firebase Auth Account erstellen.
2. Firestore Dokument in `users` erstellen.
3. Document ID: `currentUserUid`.
4. Felder setzen:
   - `uid = currentUserUid`
   - `role = customer`
   - `firstName = Eingabe`
   - `lastName = Eingabe`
   - `email = authUser.email`
   - `phone = Eingabe`
   - `profileImageUrl = leer`
   - `dateOfBirth = leer`
   - `createdAt = Current Timestamp`
   - `updatedAt = Current Timestamp`
   - `isActive = true`
5. Navigation zu `CustomerDashboardPage`.

### 3. ForgotPasswordPage

Zweck:

Passwort zuruecksetzen.

Widgets:

- E-Mail TextField
- Button `Passwort zuruecksetzen`
- Link zurueck zu `LoginPage`

Actions:

1. Firebase Password Reset Email senden.
2. Erfolgsmeldung anzeigen.

### 4. CustomerDashboardPage

Zweck:

Startseite fuer Kunden.

Widgets:

- Begruessung: `Hallo, [firstName]`
- Liste: naechste Termine
- Hinweisbereich: aktuelle Informationen
- Navigation:
  - Termine
  - Profil
  - Mitgliedschaft, vorbereitet
  - Raumzugang, vorbereitet
  - Logout

Queries:

1. Current User Document aus `users`.
2. Appointments Query:
   - Collection: `appointments`
   - Filter: `customerId == currentUserUid`
   - Filter: `status in requested, confirmed`
   - Sort: `date ascending`
   - Limit: 3

### 5. CustomerProfilePage

Zweck:

Kunde bearbeitet eigenes Profil.

Widgets:

- Profilbild
- Upload Button
- Vorname TextField
- Nachname TextField
- Telefon TextField
- E-Mail Anzeige, nicht editierbar
- Speichern Button

Actions:

Profil speichern:

1. Dokument `users/{currentUserUid}` aktualisieren.
2. Nur diese Felder schreiben:
   - `firstName`
   - `lastName`
   - `phone`
   - `profileImageUrl`
   - `dateOfBirth`
   - `updatedAt`

Profilbild hochladen:

1. Upload nach `profile_images/{currentUserUid}/profile.jpg`.
2. Download URL in `profileImageUrl` speichern.

### 6. CustomerAppointmentsPage

Zweck:

Kunde sieht eigene Termine.

Widgets:

- Liste eigener Termine
- Status Badge
- Button `Termin buchen`
- Button `Absagen` nur bei `requested` oder `confirmed`

Queries:

- Collection: `appointments`
- Filter: `customerId == currentUserUid`
- Sort: `date ascending`

Actions:

- Button Termin buchen -> `BookAppointmentPage`
- Button Absagen:
  - `status = cancelled`
  - `updatedAt = Current Timestamp`

### 7. BookAppointmentPage

Zweck:

Kunde fragt einen Termin an.

Widgets:

- Dropdown Service Type
- Dropdown oder Text Service Name
- Date Picker
- Time Picker Start
- Time Picker Ende
- Notiz TextArea
- Button `Termin anfragen`

Service Types:

- `beratung`
- `wellness`
- `rehasport`
- `other`

Create Document in `appointments`:

- `appointmentId = leer oder Document ID`
- `customerId = currentUserUid`
- `employeeId = leer`
- `serviceType = Dropdown`
- `serviceName = Eingabe`
- `date = Date Picker`
- `startTime = Time Picker`
- `endTime = Time Picker`
- `status = requested`
- `notes = Eingabe`
- `createdAt = Current Timestamp`
- `updatedAt = Current Timestamp`

### 8. AdminDashboardPage

Zweck:

Startseite fuer Admins.

Widgets:

- Anzahl Kunden
- Anzahl Termine
- Anzahl offene Terminanfragen
- Anzahl aktive Mitgliedschaften, vorbereitet
- Navigation:
  - Kundenverwaltung
  - Terminverwaltung
  - Mitgliedschaften, vorbereitet
  - Raumzugang, vorbereitet
  - Logout

Queries:

- Count `users` mit `role == customer`
- Count `appointments`
- Count `appointments` mit `status == requested`
- Count `memberships` mit `status == active`, spaeter

### 9. AdminCustomersPage

Zweck:

Admin sieht Kunden und kann sie aktivieren/deaktivieren.

Queries:

- Collection: `users`
- Filter: `role == customer`
- Sort: `lastName ascending`

Actions:

- `isActive = false`
- `isActive = true`
- `updatedAt = Current Timestamp`

### 10. AdminAppointmentsPage

Zweck:

Admin sieht und verwaltet Termine.

Queries:

- Collection: `appointments`
- Sort: `date ascending`

Actions:

- Bestaetigen: `status = confirmed`
- Absagen: `status = cancelled`
- Abschliessen: `status = completed`
- Jeweils `updatedAt = Current Timestamp`

### 11. EmployeeDashboardPage

Zweck:

Vorbereitete Mitarbeiter-Startseite. In V1.0 kann sie einfach bleiben.

Widgets:

- Heute anstehende Termine
- Kundenliste Button
- Terminverwaltung Button
- Zutritt pruefen Button, deaktiviert oder als Platzhalter

Queries:

- `appointments` nach heutigem Datum, optional
- `users` mit `role == customer`, optional

## V2 vorbereitete Pages: Mitgliedschaften

Diese Pages muessen fuer V1.0 nicht komplett fertig sein, aber die Navigation und Collections sind vorbereitet.

### CustomerMembershipPage

Zweck:

Kunde sieht eigene aktive Mitgliedschaft.

Query:

- Collection: `memberships`
- Filter: `customerId == currentUserUid`
- Filter: `status == active`
- Sort: `endDate ascending`

Anzeigen:

- Tarif
- Laufzeit
- Status
- erlaubte Raeume aus `allowedRooms`

### AdminMembershipsPage

Zweck:

Admin verwaltet Mitgliedschaften.

Funktionen:

- Mitgliedschaft erstellen
- Status aendern
- Laufzeit aendern
- erlaubte Raeume setzen

Wichtig:

Wenn `allowedRooms` geaendert wird, muss spaeter ein passender `room_access` Datensatz erstellt oder aktualisiert werden.

## V3 vorbereitete Pages: QR-Raumzugang

### CustomerRoomAccessPage

Zweck:

Kunde sieht freigeschaltete Raeume und QR-Code.

Query:

- Collection: `room_access`
- Filter: `customerId == currentUserUid`
- Filter: `status == active`
- Sort: `validUntil ascending`

QR Payload:

`mediwell://access/{accessId}?token={qrToken}&v={qrCodeVersion}`

Anzeigen:

- QR-Code
- Raumname
- Gueltig bis
- Status

### StaffQrScannerPage

Zweck:

Mitarbeiter oder Admin scannt QR-Code fuer Raumzugang.

Flow:

1. QR-Code scannen.
2. `accessId`, `token` und `v` aus Payload lesen.
3. Dokument `room_access/{accessId}` laden.
4. Token vergleichen.
5. Status muss `active` sein.
6. `validFrom <= jetzt <= validUntil` pruefen.
7. Passenden Raum pruefen.
8. Dokument in `access_logs` erstellen.

Hinweis:

FlutterFlow kann diesen Flow als Custom Action oder ueber eine Cloud Function stabiler abbilden. Fuer echte Produktion ist eine Cloud Function sicherer, weil der Token dort gehasht verglichen werden kann.

## V1.0 Abnahmeliste

Version 1.0 gilt als fertig, wenn:

- Ein Kunde sich registrieren kann.
- Ein Kunde automatisch ein `users` Dokument bekommt.
- Ein Kunde sich einloggen kann.
- Ein Kunde sein Profil bearbeiten kann.
- Ein Kunde einen Termin anfragen kann.
- Ein Kunde eigene Termine sehen kann.
- Ein Kunde einen eigenen Termin absagen kann.
- Ein Admin alle Kunden sehen kann.
- Ein Admin Kunden aktivieren/deaktivieren kann.
- Ein Admin alle Termine sehen kann.
- Ein Admin Termine bestaetigen, absagen und abschliessen kann.
- Ein Mitarbeiter nicht als Admin behandelt wird.
- Firestore Rules verhindern, dass Kunden fremde Daten sehen.
