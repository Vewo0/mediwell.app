# MediWell App - Architektur Review und Verbesserungen

## Geprüfte Bereiche

- Rollenmodell
- Terminverwaltung
- Mitgliedschaften
- Raumzugang per QR-Code
- Firestore Security Rules
- FlutterFlow Collections
- FlutterFlow Pages
- Testdaten

## Gefundene Probleme

### 1. Rollen waren zu grob abgesichert

Problem:

Die erste Rules-Version hat Rollen geprüft, aber deaktivierte Nutzer konnten in manchen Fällen weiterhin als Rolle erkannt werden.

Verbesserung:

`currentUserIsActive()` wurde eingeführt. `admin`, `employee` und `customer` gelten nur noch, wenn der Nutzer angemeldet ist, ein User-Dokument besitzt und `isActive == true` ist.

### 2. Kunden konnten zu viele Felder schreiben

Problem:

Bei User- und Termin-Dokumenten waren erlaubte Felder nicht eng genug begrenzt.

Verbesserung:

Die Rules enthalten jetzt Feld-Whitelists:

- `validUserKeys()`
- `validAppointmentKeys()`
- `validMembershipKeys()`
- `validRoomAccessKeys()`

### 3. Terminabsage war zu offen

Problem:

Ein Kunde konnte theoretisch einen eigenen Termin unabhängig vom bisherigen Status auf `cancelled` setzen.

Verbesserung:

Kunden dürfen nur eigene Termine aus `requested` oder `confirmed` absagen. Staff kann weiterhin Termine verwalten.

### 4. Mitarbeiterrolle war nicht klar genug

Problem:

Mitarbeiter wurden im ersten Page-Plan auf das Admin-Dashboard geleitet.

Verbesserung:

`EmployeeDashboardPage` wurde als eigene Zielseite ergänzt. In V1.0 darf sie einfach bleiben, verhindert aber eine falsche Admin-Denkweise im UI.

### 5. Mitgliedschaften waren nur erwähnt

Problem:

Mitgliedschaften waren vorbereitet, aber nicht sauber für FlutterFlow erklärt.

Verbesserung:

`memberships` ist jetzt vollständig in Schema, Collections-Datei, Indexes und Testdaten vertreten.

### 6. QR-Raumzugang brauchte eine sichere Grundidee

Problem:

Der QR-Zugang war als Funktion geplant, aber ohne konkrete Token-Architektur.

Verbesserung:

`room_access` enthält jetzt:

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

QR-Payload:

`mediwell://access/{accessId}?token={qrToken}&v={qrCodeVersion}`

Wichtig:

Der QR-Code enthält nie die Firebase UID und ist ausschließlich für Raumzugang vorgesehen.

## Architekturentscheidung QR-Code

Für FlutterFlow ist eine reine Client-Prüfung möglich:

1. QR-Code scannen.
2. `room_access/{accessId}` laden.
3. Token vergleichen.
4. Status und Gültigkeit prüfen.
5. Log in `access_logs` schreiben.

Für Produktion ist eine Cloud Function besser:

1. QR-Code scannen.
2. Payload an Cloud Function senden.
3. Token serverseitig prüfen.
4. Zugriff serverseitig loggen.

Die aktuelle Struktur unterstützt beide Wege.

## Ergebnis

Die Dateien sind jetzt besser auf FlutterFlow und Firebase ausgerichtet:

- klare Rollen
- bessere Security Rules
- stabilerer Terminstatus-Flow
- Mitgliedschaften sauber vorbereitet
- QR-Zugang ohne UID im QR-Code
- Testdaten für Admin, Mitarbeiter, Kunde, Mitgliedschaft, Raum, Zugang und Logs
