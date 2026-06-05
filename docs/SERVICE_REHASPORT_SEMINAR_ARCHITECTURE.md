# MediWell App - Leistungen, Rehasport und Seminare

## Ziel

Diese Datei dokumentiert die saubere Fachlogik fuer die Angebote von MediWell:

- Yoga
- Seminare
- Massagen
- Rehasport

Wichtig: Nicht jede Leistung ist ein normaler Termin. Besonders Rehasport darf nicht wie eine Massage behandelt werden, weil Rehasport an eine aerztliche Verordnung gebunden ist.

## Grundentscheidung

Die App unterscheidet vier Angebotsarten:

1. Einzeltermin
2. Kurs
3. Rehasport mit Verordnung
4. Seminar

Diese Angebotsarten duerfen nicht in eine einzige einfache Terminlogik gepresst werden.

## 1. Einzeltermin

Geeignet fuer:

- Ruecken- und Nackenmassage
- Teilmassage
- Infrarot-Waermekabine
- Gesundheitsberatung

Collection:

`appointments`

Logik:

Ein Kunde fragt einen konkreten Termin an:

- ein Datum
- eine Startzeit
- eine Endzeit
- eine Leistung
- ein Status

Beispiel:

Ruecken- und Nackenmassage am 04.08. von 10:15 bis 10:45.

Felder:

- `customerId`
- `serviceType`
- `serviceName`
- `date`
- `startTime`
- `endTime`
- `durationMinutes`
- `price`
- `requiresEmployee`
- `status`

## 2. Kurse

Geeignet fuer:

- Yoga
- regelmaessige Rehasportkurse als Kursplan

Collection:

`courses`

Ein Kurs beschreibt einen wiederkehrenden oder geplanten Kursblock.

Beispiel Yoga:

Yoga jeden Dienstag von 18:00 bis 19:00 Uhr.

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
- `isActive`
- `createdAt`
- `updatedAt`

Werte fuer `courseType`:

- `yoga`
- `rehasport`

## 3. Rehasport mit aerztlicher Verordnung

Rehasport ist kein normaler Termin.

Problem:

Bei einer Massage reicht:

- Startzeit
- Endzeit
- Dauer

Bei Rehasport reicht das nicht, weil der Patient eine aerztliche Verordnung hat. Diese Verordnung kann zum Beispiel 2 Jahre gueltig sein. Der Patient besucht in dieser Zeit regelmaessige Kurse.

Deshalb braucht die App zwei getrennte Dinge:

1. Die Verordnung des Patienten
2. Die Rehasport-Kurse im Kursplan

### Collection: rehab_prescriptions

Diese Collection speichert die Verordnung eines Patienten.

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

Statuswerte:

- `active`
- `expired`
- `completed`
- `cancelled`

Beispiel:

Samantha hat eine Rehasport-Verordnung von 01.08.2026 bis 01.08.2028.

Dann zeigt die App nicht nur einen einzelnen Termin. Die App zeigt:

- Rehasport Kurs: Montag 17:00 bis 17:45 Uhr
- Verordnung laeuft ab am: 01.08.2028
- genutzte Einheiten: 4 von 50

### Collection: rehab_enrollments

Diese Collection verbindet Patient, Verordnung und Kurs.

Felder:

- `enrollmentId`
- `customerId`
- `prescriptionId`
- `courseId`
- `status`
- `createdAt`
- `updatedAt`

Statuswerte:

- `active`
- `paused`
- `ended`

Beispiel:

Samantha ist mit ihrer aktiven Verordnung im Kurs `Orthopaedischer Rehasport Montag 17:00` angemeldet.

### Collection: rehab_attendance

Diese Collection speichert einzelne Teilnahmen.

Felder:

- `attendanceId`
- `customerId`
- `prescriptionId`
- `courseId`
- `sessionDate`
- `startTime`
- `endTime`
- `attended`
- `countsAgainstPrescription`
- `recordedBy`
- `createdAt`

Wenn `attended = true` und `countsAgainstPrescription = true`, kann `usedUnits` der Verordnung erhoeht werden.

## Anzeige fuer Kunden

Auf der Kundenseite soll Rehasport so angezeigt werden:

Titel:

`Rehasport`

Details:

- Kurs: Orthopaedischer Rehasport
- Wochentag: Montag
- Uhrzeit: 17:00 bis 17:45 Uhr
- Verordnung laeuft ab am: 01.08.2028
- Einheiten: 4 von 50 genutzt

Wichtig:

Die Uhrzeit gehoert zum Kurs. Das Ablaufdatum gehoert zur Verordnung.

Diese beiden Informationen duerfen nicht in einem normalen Termin vermischt werden.

## Admin Flow fuer Rehasport

Der Admin macht drei Schritte:

1. Patient auswaehlen.
2. Rehasport-Verordnung anlegen.
3. Patient einem Rehasport-Kurs zuordnen.

### Schritt 1: Verordnung anlegen

Admin gibt ein:

- Patient
- gueltig von
- gueltig bis
- Anzahl Einheiten
- Einheiten pro Woche, optional
- Arztname, optional
- Notiz, optional
- Verordnungsdokument, optional

### Schritt 2: Kurs zuordnen

Admin waehlt:

- Rehasport-Kurs
- Status `active`

Danach sieht der Patient den Kurs in seiner App.

## 4. Seminare

Seminare sind keine normalen spontanen Termine.

Seminare sind Events mit festem Preis.

Collection:

`seminars`

Felder:

- `seminarId`
- `title`
- `description`
- `date`
- `startTime`
- `endTime`
- `price`
- `maxParticipants`
- `currentParticipants`
- `location`
- `status`
- `createdAt`
- `updatedAt`

Preisregel:

Seminare kosten immer 120 Euro.

Also:

`price = 120`

Statuswerte:

- `planned`
- `open`
- `full`
- `cancelled`
- `completed`

Optional spaeter:

`seminar_registrations`

Felder:

- `registrationId`
- `seminarId`
- `customerId`
- `status`
- `createdAt`

## 5. Yoga

Yoga sollte wie ein Kurs behandelt werden, nicht wie eine Massage.

Collection:

`courses`

Beispiel:

- `courseType = yoga`
- `title = Yoga Kurs`
- `weekday = Mittwoch`
- `startTime = 18:00`
- `endTime = 19:00`
- `maxParticipants = 10`

Wenn Kunden Yoga buchen sollen, braucht man spaeter:

`course_bookings`

Felder:

- `bookingId`
- `courseId`
- `customerId`
- `status`
- `createdAt`

## Einfache Regel fuer die App

### Normale Terminbuchung

Nur diese Leistungen gehoeren in `appointments`:

- Ruecken- und Nackenmassage
- Teilmassage
- Infrarot-Waermekabine
- Gesundheitsberatung

### Rehasport

Rehasport gehoert in:

- `rehab_prescriptions`
- `courses`
- `rehab_enrollments`
- `rehab_attendance`

### Yoga

Yoga gehoert in:

- `courses`
- spaeter `course_bookings`

### Seminare

Seminare gehoeren in:

- `seminars`
- spaeter `seminar_registrations`

## Warum diese Architektur besser ist

Diese Struktur verhindert, dass die App spaeter kaputt wird.

Ein normaler Termin beantwortet:

Wann ist mein einzelner Termin?

Eine Rehasport-Verordnung beantwortet:

Wie lange darf dieser Patient Rehasport machen?

Ein Rehasport-Kurs beantwortet:

Wann findet der Kurs jede Woche statt?

Eine Teilnahme beantwortet:

War der Patient an diesem Datum wirklich da?

Ein Seminar beantwortet:

Wann findet dieses konkrete Seminar statt und was kostet es?

## Empfehlung fuer Version 1.0

Jetzt aktiv bauen:

- Massage-Terminbuchung
- Gesundheitsberatung-Terminbuchung
- einfache Admin-Terminverwaltung

Jetzt vorbereiten:

- `courses`
- `rehab_prescriptions`
- `rehab_enrollments`
- `rehab_attendance`
- `seminars`

Noch nicht aktiv bauen:

- automatische Rehasport-Abrechnung
- automatische Einheitenzaehlung
- Online-Zahlung
- Seminarzahlung
- komplette Kursbuchung

## Base44 Prompt fuer diesen Umbau

```text
Bitte verbessere die MediWell App fachlich so, dass Rehasport, Yoga, Seminare und normale Termine sauber getrennt sind.

Normale Terminbuchung darf nur fuer Einzeltermine genutzt werden:
- Ruecken- und Nackenmassage
- Teilmassage
- Infrarot-Waermekabine
- Gesundheitsberatung

Rehasport darf nicht als normaler einzelner Termin behandelt werden.
Rehasport besteht aus:
1. einer aerztlichen Verordnung des Patienten
2. einem Rehasport-Kurs mit fester Uhrzeit
3. einer Zuordnung des Patienten zu diesem Kurs
4. spaeter einer Anwesenheitsliste

Bitte bereite diese Collections vor:
- courses
- rehab_prescriptions
- rehab_enrollments
- rehab_attendance
- seminars

Seminare kosten immer 120 Euro.
Yoga soll als Kurs in courses gespeichert werden.
Bitte aendere die Kunden-Terminbuchung so, dass Rehasport und Seminare dort nicht als normale buchbare Einzeltermine erscheinen.
Rehasport Einfuehrung darf als Beratung/Einzeltermin bleiben, aber Orthopaedischer Rehasport soll nur ueber Verordnung und Kursplan laufen.
```
