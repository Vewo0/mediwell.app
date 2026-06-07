# MediWell App - Rehasport Admin- und Kundenflow

## Ziel

Rehasport soll nicht wie ein normaler Termin funktionieren.

Der richtige Ablauf ist:

1. Kunde registriert sich.
2. Admin öffnet das Profil des Kunden.
3. Admin klickt auf `Rehasport-Verordnung anlegen`.
4. Admin legt die ärztliche Verordnung an.
5. Admin ordnet den Kunden einem Rehasport-Kurs zu.
6. Kunde sieht Rehasport in seiner App wie einen Stundenplan, solange die Verordnung aktiv ist.

## Warum nicht als normaler Termin?

Ein normaler Termin hat:

- Datum
- Startzeit
- Endzeit

Eine Rehasport-Verordnung hat zusätzlich:

- Gültig von
- Gültig bis
- Anzahl bewilligter Einheiten
- genutzte Einheiten
- zugeordneter Kurs

Die Kurszeit ist nicht dasselbe wie das Ablaufdatum der Verordnung.

Richtig anzeigen:

- Kurs: Orthopädischer Rehasport
- Wochentag: Montag
- Uhrzeit: 17:00 bis 17:45 Uhr
- Verordnung läuft ab am: 01.08.2028
- Einheiten: 4 von 50 genutzt

Falsch anzeigen:

- Rehasport von 17:00 bis 01.08.2028

## Admin Flow

### AdminCustomersPage

Die Kundenliste bleibt bestehen.

Admin klickt auf einen Kunden.

Dann öffnet sich:

`AdminCustomerDetailPage`

## AdminCustomerDetailPage

Diese Seite zeigt:

- Name
- E-Mail
- Telefonnummer
- Anschrift
- Status aktiv/inaktiv
- Termine des Kunden
- Rehasport-Verordnungen des Kunden

Buttons:

- `Kunde bearbeiten`
- `Rehasport-Verordnung anlegen`
- `Termin für Kunde erstellen`, optional später

## Button: Rehasport-Verordnung anlegen

Beim Klick öffnet sich:

`AdminCreateRehabPrescriptionPage`

oder ein Modal mit denselben Feldern.

## AdminCreateRehabPrescriptionPage

Pflichtfelder:

- Patient/Kunde, automatisch aus Kundenprofil gesetzt
- Gültig von
- Gültig bis
- Anzahl Einheiten
- Einheiten pro Woche, optional
- Rehasport-Kurs auswählen

Optionale Felder:

- Arztname
- Diagnose/Notiz
- Dokument hochladen

Beim Speichern werden zwei Datensätze erstellt:

1. `rehab_prescriptions`
2. `rehab_enrollments`

## Collection: rehab_prescriptions

Felder:

- `prescriptionId`
- `customerId`
- `status`
- `validFrom`
- `validUntil`
- `prescribedUnits`
- `usedUnits`
- `unitsPerWeek`
- `doctorName`
- `diagnosisNotes`
- `documentUrl`
- `createdBy`
- `createdAt`
- `updatedAt`

Beim Anlegen:

- `status = active`
- `usedUnits = 0`
- `customerId = gewählter Kunde`
- `createdBy = aktueller Admin`

## Collection: rehab_enrollments

Felder:

- `enrollmentId`
- `customerId`
- `prescriptionId`
- `courseId`
- `status`
- `createdAt`
- `updatedAt`

Beim Anlegen:

- `status = active`
- `customerId = gewählter Kunde`
- `prescriptionId = neu angelegte Verordnung`
- `courseId = ausgewählter Rehasport-Kurs`

## Collection: courses

Rehasport-Kurse werden hier gespeichert.

Felder:

- `courseId`
- `courseType`
- `title`
- `description`
- `weekday`
- `startTime`
- `endTime`
- `trainerId`
- `roomId`
- `maxParticipants`
- `currentParticipants`
- `isActive`
- `createdAt`
- `updatedAt`

Für Rehasport gilt:

- `courseType = rehasport`

Beispiel:

- `title = Orthopädischer Rehasport`
- `weekday = Montag`
- `startTime = 17:00`
- `endTime = 17:45`

## Kundenansicht

Kunde sieht im Dashboard oder auf einer eigenen Seite:

`RehasportPage`

Wenn keine aktive Verordnung vorhanden ist:

`Noch keine aktive Rehasport-Verordnung`

Wenn aktive Verordnung vorhanden ist:

Karte anzeigen:

- Rehasport-Kursname
- Wochentag
- Uhrzeit von `startTime` bis `endTime`
- Verordnung gültig von `validFrom` bis `validUntil`
- Verordnung läuft ab am `validUntil`
- Einheiten: `usedUnits` von `prescribedUnits` genutzt
- Status

## Aktivitätsregel

Eine Verordnung gilt als aktiv, wenn:

- `status = active`
- heutiges Datum liegt zwischen `validFrom` und `validUntil`

Wenn `validUntil` überschritten ist:

- Verordnung soll nicht mehr als aktiv angezeigt werden
- optional Admin-Hinweis: `Rehasport-Verordnung abgelaufen`

## Wichtig für Version 1.0

Jetzt bauen:

- AdminCustomerDetailPage
- Button `Rehasport-Verordnung anlegen`
- AdminCreateRehabPrescriptionPage oder Modal
- RehasportPage für Kunden als Anzeige
- einfache courses Liste für Rehasport-Kurse

Noch nicht bauen:

- automatische Abrechnung
- Krankenkassenlogik
- digitale Unterschrift
- automatische Einheitenzählung
- Make.com Automatisierung für Rehasport
