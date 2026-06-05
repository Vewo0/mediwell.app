# MediWell App - Technische FlutterFlow Dokumentation

## Ziel der technischen Grundlage

Die App wird in FlutterFlow gebaut und nutzt Firebase als Backend. Version 1.0 setzt die Basis um:

- Firebase Authentication
- Rollenmodell
- Kundenprofil
- Terminverwaltung
- Adminbereich
- vorbereitete Mitarbeiterrolle

Zusätzlich sind Mitgliedschaften und QR-Raumzugang strukturell vorbereitet, damit Version 2.0 und 3.0 ohne Bruch weitergebaut werden können.

## Firebase Dienste

Aktivieren:

1. Firebase Authentication
2. Cloud Firestore
3. Firebase Storage

Später sinnvoll:

1. Cloud Functions für sichere QR-Prüfung
2. Firebase Cloud Messaging für Benachrichtigungen
3. Make.com für Automatisierungen

## Rollenmodell

Die Rolle steht in:

`users/{authUser.uid}.role`

Erlaubte Werte:

- `admin`
- `employee`
- `customer`

Zusätzlich muss gelten:

`users/{authUser.uid}.isActive == true`

Nur aktive Nutzer gelten in den Security Rules als echte Rolle.

## Rechte

### Admin

Darf:

- Kunden verwalten
- Mitarbeiter verwalten
- Termine verwalten
- Mitgliedschaften verwalten
- Räume verwalten
- Raumzugänge verwalten
- Dashboards und Statistiken sehen

### Employee

Darf:

- Kunden ansehen
- Termine verwalten
- Rehasport vorbereitend verwalten
- Zutritte prüfen
- Anwesenheiten dokumentieren

Darf nicht:

- Admins verwalten
- Rollen vergeben
- Systemeinstellungen ändern

### Customer

Darf:

- eigenes Profil sehen und bearbeiten
- eigene Termine buchen
- eigene Termine ansehen
- eigene Termine absagen
- eigene Mitgliedschaft lesen
- eigenen Raumzugang lesen

Darf nicht:

- andere Kunden sehen
- Mitarbeiter sehen
- Statistiken sehen
- Rollen ändern

## Firestore Collections

Aktiv für V1.0:

- `users`
- `appointments`

Vorbereitet:

- `memberships`
- `rehab_courses`
- `rooms`
- `room_access`
- `access_logs`
- `notifications`

Das vollständige Schema steht in:

`firebase/firestore.schema.json`

FlutterFlow-Feldlisten stehen in:

`flutterflow/COLLECTIONS_V1.md`

## Auth Flow

### Registrierung

1. Firebase Auth Account erstellen.
2. `users/{currentUserUid}` erstellen.
3. Immer `role = customer` setzen.
4. Immer `isActive = true` setzen.
5. Zur `CustomerDashboardPage` navigieren.

Wichtig:

FlutterFlow darf bei Registrierung keine Rollenauswahl anzeigen.

### Login

1. Firebase Login.
2. `users/{currentUserUid}` laden.
3. Wenn Dokument fehlt: Fehlermeldung und Logout.
4. Wenn `isActive != true`: Fehlermeldung und Logout.
5. Rolle prüfen:
   - `customer` -> `CustomerDashboardPage`
   - `employee` -> `EmployeeDashboardPage`
   - `admin` -> `AdminDashboardPage`

## Terminverwaltung

Collection:

`appointments`

Statuswerte:

- `requested`
- `confirmed`
- `cancelled`
- `completed`

### Kunde bucht Termin

Kunde erstellt ein Dokument mit:

- `customerId = currentUserUid`
- `status = requested`
- Datum und Uhrzeit
- Service
- Notiz optional

### Kunde sagt Termin ab

Kunde darf nur eigene Termine absagen.

Erlaubt nur von:

- `requested`
- `confirmed`

Danach:

- `status = cancelled`
- `updatedAt = Current Timestamp`

### Admin und Mitarbeiter verwalten Termine

Admin und Mitarbeiter dürfen:

- Termine erstellen
- Termine bestätigen
- Termine absagen
- Termine abschließen

Nur Admin darf Termine löschen.

## Mitgliedschaften

Collection:

`memberships`

Statuswerte:

- `active`
- `paused`
- `cancelled`
- `expired`

Wichtige Felder:

- `customerId`
- `membershipType`
- `startDate`
- `endDate`
- `status`
- `priceMonthly`
- `allowedRooms`

Architektur:

`allowedRooms` enthält Raum-Dokument-IDs aus `rooms`. Daraus kann später manuell oder automatisch `room_access` erzeugt werden.

## Raumzugang per QR-Code

Collections:

- `rooms`
- `room_access`
- `access_logs`

QR-Code ist ausschließlich für Raumzugang.

Nicht erlaubt:

- Zahlungen
- Terminbuchungen
- Registrierung

### QR Payload

Empfohlenes Format:

`mediwell://access/{accessId}?token={qrToken}&v={qrCodeVersion}`

Der QR-Code enthält nie:

- Firebase UID
- Zahlungsdaten
- Registrierungsdaten
- Terminerstellungsdaten

### room_access

Wichtige Felder:

- `accessId`
- `customerId`
- `roomId`
- `validFrom`
- `validUntil`
- `accessType`
- `status`
- `qrToken`
- `qrCodeVersion`
- `lastRotatedAt`

### Scan Flow

1. Mitarbeiter scannt QR-Code.
2. App liest `accessId`, `token`, `v`.
3. App lädt `room_access/{accessId}`.
4. App prüft:
   - Token stimmt
   - Status ist `active`
   - aktueller Zeitpunkt liegt zwischen `validFrom` und `validUntil`
   - Raum stimmt
5. App schreibt `access_logs`.

Für Produktion:

Die Token-Prüfung sollte später in eine Cloud Function verschoben werden.

## Storage

Profilbilder:

`profile_images/{currentUserUid}/profile.jpg`

Regeln:

- Nutzer darf eigenes Profilbild schreiben.
- Datei muss Bild sein.
- Datei muss kleiner als 5 MB sein.

## FlutterFlow App State

Empfohlen:

| Name | Typ | Zweck |
| --- | --- | --- |
| currentUserRole | String | Rolle nach Login |
| isAdmin | Boolean | Admin UI |
| isEmployee | Boolean | Mitarbeiter UI |
| isCustomer | Boolean | Kunden UI |

## Wichtigste Architekturregeln

1. Auth UID ist Dokument-ID in `users`.
2. Kundenregistrierung erzeugt immer `customer`.
3. Admin/Employee Rollen werden nicht öffentlich wählbar.
4. Termine werden nicht gelöscht, sondern per Status gesteuert.
5. Mitgliedschaften steuern später Raumzugänge.
6. QR-Code enthält keine UID.
7. Zutritte werden immer in `access_logs` dokumentiert.
