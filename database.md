# MediWell App – Datenbankstruktur

## Übersicht

Die App nutzt Firebase Firestore als Datenbank.

Die wichtigsten Collections sind:

- users
- appointments
- memberships
- rehab_courses
- rooms
- room_access
- access_logs
- notifications

---

# users

Speichert alle Nutzer der App.

## Felder

- uid
- role
- firstName
- lastName
- email
- phone
- profileImageUrl
- dateOfBirth
- createdAt
- updatedAt
- isActive

## Rollen

Mögliche Werte für role:

- admin
- employee
- customer

---

# appointments

Speichert Termine.

## Felder

- appointmentId
- customerId
- employeeId
- serviceType
- serviceName
- date
- startTime
- endTime
- status
- notes
- createdAt
- updatedAt

## Status

- requested
- confirmed
- cancelled
- completed

---

# memberships

Speichert Mitgliedschaften.

## Felder

- membershipId
- customerId
- membershipType
- startDate
- endDate
- status
- priceMonthly
- allowedRooms
- createdAt
- updatedAt

## Status

- active
- paused
- cancelled
- expired

---

# rehab_courses

Speichert Rehasport-Kurse.

## Felder

- courseId
- title
- description
- weekday
- startTime
- endTime
- maxParticipants
- currentParticipants
- trainerId
- roomId
- isActive

---

# rooms

Speichert Räume im MediWell-Gebäude.

## Felder

- roomId
- roomName
- roomType
- description
- isActive

## Beispiele

- Infrarotkabine
- Massageraum
- Rehasportraum
- Seminarraum
- Sauna
- Salzgrotte

---

# room_access

Speichert, welcher Kunde Zugang zu welchem Raum hat.

## Felder

- accessId
- customerId
- roomId
- validFrom
- validUntil
- accessType
- status
- createdAt
- updatedAt

## accessType

- membership
- appointment
- manual

## status

- active
- expired
- blocked

---

# access_logs

Speichert Zutrittsversuche.

## Felder

- logId
- customerId
- roomId
- timestamp
- accessGranted
- reason
- scannedBy
- deviceId

---

# notifications

Speichert Benachrichtigungen.

## Felder

- notificationId
- userId
- title
- message
- type
- isRead
- createdAt

---

# Wichtige Regeln

Der QR-Code dient ausschließlich für Raumzugang.

Der QR-Code darf NICHT genutzt werden für:

- Zahlungen
- Terminbuchungen
- Registrierung

Der QR-Code prüft nur:

- Wer ist der Nutzer?
- Für welchen Raum ist er berechtigt?
- Ist die Berechtigung aktuell gültig?
