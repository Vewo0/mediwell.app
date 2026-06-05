# MediWell App - Schritt-fuer-Schritt Umsetzung in FlutterFlow

## Phase 1: Firebase verbinden

1. Neues FlutterFlow-Projekt anlegen.
2. Firebase-Projekt verbinden.
3. Authentication aktivieren.
4. Firestore aktivieren.
5. Storage aktivieren.
6. In Firebase die Regeln aus `firebase/firestore.rules` einfuegen.
7. In Firebase die Regeln aus `firebase/storage.rules` einfuegen.

## Phase 2: Collections anlegen

Aktiv fuer V1.0:

1. `users`
2. `appointments`

Vorbereiten fuer V2 und V3:

1. `memberships`
2. `rooms`
3. `room_access`
4. `access_logs`
5. `notifications`

Alle Felder stehen in `flutterflow/COLLECTIONS_V1.md`.

## Phase 3: Rollen sauber umsetzen

1. Nach Registrierung wird immer `role = customer` gesetzt.
2. `admin` und `employee` werden nicht über die Registrierung vergeben.
3. Admin- und Mitarbeiterrollen werden manuell in Firebase oder später über Admin UI gesetzt.
4. Nach Login immer `users/{currentUserUid}` laden.
5. Wenn `isActive != true`, sofort ausloggen.
6. Danach anhand von `role` weiterleiten:
   - `customer` -> `CustomerDashboardPage`
   - `employee` -> `EmployeeDashboardPage`
   - `admin` -> `AdminDashboardPage`

## Phase 4: Auth Pages bauen

1. `LoginPage`
2. `RegisterPage`
3. `ForgotPasswordPage`

Actions:

- Login
- Registrierung
- Passwort Reset
- Logout

## Phase 5: Kundenbereich bauen

1. `CustomerDashboardPage`
2. `CustomerProfilePage`
3. `CustomerAppointmentsPage`
4. `BookAppointmentPage`

Wichtig:

Kunden schreiben bei Terminen nur:

- neue Termine mit `status = requested`
- eigene Terminabsagen mit `status = cancelled`

## Phase 6: Adminbereich bauen

1. `AdminDashboardPage`
2. `AdminCustomersPage`
3. `AdminAppointmentsPage`

Admin darf:

- Kunden aktivieren/deaktivieren
- Termine bestätigen
- Termine absagen
- Termine abschließen
- später Mitgliedschaften und Raumzugänge verwalten

## Phase 7: Mitarbeiterbereich vorbereiten

1. `EmployeeDashboardPage` anlegen.
2. Für V1.0 einfache Inhalte verwenden:
   - heutige Termine
   - Kunden suchen
   - Terminverwaltung
3. Kein Zugriff auf Admin-Einstellungen.
4. Keine Rollenverwaltung.

## Phase 8: Mitgliedschaften vorbereiten

Collection:

`memberships`

Für V1.0 reicht:

1. Collection in FlutterFlow anlegen.
2. Testdaten in Firebase eintragen.
3. Navigation optional verstecken.

Später:

1. `CustomerMembershipPage` bauen.
2. `AdminMembershipsPage` bauen.
3. `allowedRooms` auswerten.
4. Daraus `room_access` erstellen.

## Phase 9: QR-Raumzugang vorbereiten

Collections:

- `rooms`
- `room_access`
- `access_logs`

QR-Payload:

`mediwell://access/{accessId}?token={qrToken}&v={qrCodeVersion}`

Regeln:

1. QR-Code nie für Zahlungen nutzen.
2. QR-Code nie für Terminbuchung nutzen.
3. QR-Code nie für Registrierung nutzen.
4. QR-Code nie mit Firebase UID als Payload bauen.

Späterer Scan-Flow:

1. QR scannen.
2. `accessId` und `qrToken` auslesen.
3. `room_access/{accessId}` laden.
4. Token, Status, Zeitraum und Raum prüfen.
5. `access_logs` schreiben.

Für Produktion:

Eine Cloud Function ist sicherer als reine Client-Prüfung.

## Phase 10: Testdaten eintragen

1. Drei Firebase Auth Nutzer erstellen.
2. UIDs kopieren.
3. Platzhalter in `firebase/seed/mediwell_v1_test_data.json` ersetzen.
4. Firestore-Dokumente anlegen:
   - users
   - appointments
   - memberships
   - rooms
   - room_access
   - access_logs
   - notifications

## Phase 11: Security Tests

1. Kunde darf eigenes Profil öffnen.
2. Kunde darf fremde Kunden nicht sehen.
3. Kunde darf eigenen Termin erstellen.
4. Kunde darf fremden Termin nicht sehen.
5. Kunde darf eigenen Termin nur aus `requested` oder `confirmed` absagen.
6. Admin darf Kunden und Termine verwalten.
7. Mitarbeiter darf Kunden und Termine sehen/verwalten, aber keine Admins verwalten.
8. Kunde darf eigene Mitgliedschaft lesen.
9. Kunde darf eigenen Raumzugang lesen.
10. Staff darf Zutrittslogs erstellen.

## Phase 12: V1.0 Abschluss

V1.0 ist fertig, wenn:

1. Kunde registriert sich.
2. Kunde meldet sich an.
3. Kunde bearbeitet Profil.
4. Kunde lädt Profilbild hoch.
5. Kunde fragt Termin an.
6. Kunde sieht eigene Termine.
7. Kunde sagt Termin ab.
8. Admin meldet sich an.
9. Admin sieht Kunden.
10. Admin deaktiviert Kunden.
11. Admin sieht Termine.
12. Admin bestätigt, storniert und schließt Termine ab.
13. Mitarbeiter wird separat geroutet.
