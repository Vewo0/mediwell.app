# MediWell/Nam Wellness - Leistungen, Make und Terminvorschläge

## Ziel

Diese Datei dokumentiert drei neue Arbeitsbereiche:

1. Leistungen 1:1 als Service-Katalog pflegen
2. Make-Automatisierung vorbereiten
3. Terminanfragen mit Gegenvorschlag abbilden

## 1. Leistungen 1:1 übernehmen

Leistungen sollen nicht frei eingetippt werden. Sie gehören in einen Service-Katalog.

Collection:

`service_catalog`

Felder:

- `serviceId`
- `businessId`
- `category`
- `serviceType`
- `serviceName`
- `variantName`
- `description`
- `durationMinutes`
- `price`
- `onlineBookable`
- `requiresEmployee`
- `isActive`
- `sortOrder`
- `createdAt`
- `updatedAt`

## Beispiel für Nam's Wellness Massage

Business:

- `businessId = nams_wellness_massage`
- Name: `Nam's Wellness Massage`
- Kategorie: Thai-Massage / Wellness Massage
- Adresse: `Nahestraße 40, 55593 Rüdesheim`
- Telefon: `01512 3234610`

### Service-Katalog aus sichtbarer Preisliste

Die Screenshots zeigen zwei Preislistenstände. Die neuere Google-Ansicht scheint höhere Preise zu zeigen. Für die App soll ein Admin die Preise später bearbeiten können.

Vorläufige Struktur:

#### Thai-Massage

- Thai-Massage Ganzkörper 120 Min, 95 EUR
- Thai-Massage Ganzkörper 90 Min, 75 EUR
- Thai-Massage Rücken-Nacken 60 Min, 55 EUR
- Thai-Massage Rücken-Nacken 30 Min, 30 EUR

#### Aroma-Massage

- Aroma-Massage Ganzkörper 120 Min, 105 EUR
- Aroma-Massage Ganzkörper 90 Min, 85 EUR
- Aroma-Massage Rücken-Nacken 60 Min, 65 EUR
- Aroma-Massage Rücken-Nacken 30 Min, 37 EUR

#### Hotstone Massage

- Hotstone Massage Ganzkörper 120 Min, 110 EUR
- Hotstone Massage Ganzkörper 90 Min, 90 EUR
- Hotstone Massage Rücken-Nacken 60 Min, 66 EUR
- Hotstone Massage Rücken-Nacken 30 Min, 42 EUR

#### Öl-Massage

- Öl-Massage Ganzkörper 120 Min, 95 EUR
- Öl-Massage Ganzkörper 90 Min, 75 EUR
- Öl-Massage Rücken-Nacken 60 Min, 50 EUR
- Öl-Massage Rücken-Nacken 30 Min, 30 EUR

Hinweis:

Einige Preise auf den Bildern sind unscharf. Vor Live-Nutzung muss der Admin die Preise im Service-Katalog prüfen.

## 2. Make-Automatisierung strukturieren

Die App soll nicht direkt Rechnungen versenden. Sie erstellt nur Datensätze für Make.

Collection:

`invoice_requests`

Make liest neue Datensätze mit:

- `status = pending`
- `makeProcessed = false`

Make verarbeitet dann:

1. Rechnung erzeugen
2. PDF erzeugen
3. E-Mail senden
4. Datensatz aktualisieren

Nach erfolgreicher Verarbeitung:

- `status = processed`
- `makeProcessed = true`
- `makeProcessedAt = aktueller Zeitpunkt`
- `makeError = leer`

Bei Fehler:

- `status = failed`
- `makeProcessed = false`
- `makeError = Fehlermeldung`

## 3. Terminanfrage mit Gegenvorschlag

Problem:

Kunde fragt einen Termin an. Admin kann ihn nicht annehmen, weil zu dieser Uhrzeit schon ein Termin ist. Admin soll nicht nur ablehnen, sondern eine neue Uhrzeit vorschlagen.

## Empfohlener Ablauf

1. Kunde erstellt Terminanfrage.
2. Termin bekommt Status `requested`.
3. Admin öffnet Terminanfrage.
4. Admin wählt `Gegenvorschlag senden`.
5. Admin wählt neues Datum, neue Startzeit, neue Endzeit.
6. Admin schreibt optional eine Nachricht.
7. App erstellt einen Datensatz in `appointment_proposals`.
8. Appointment bekommt Status `counter_proposed`.
9. Kunde sieht den Vorschlag.
10. Kunde kann `Annehmen` oder `Ablehnen`.

## Collection: appointment_proposals

Felder:

- `proposalId`
- `appointmentId`
- `customerId`
- `createdBy`
- `originalDate`
- `originalStartTime`
- `originalEndTime`
- `proposedDate`
- `proposedStartTime`
- `proposedEndTime`
- `message`
- `status`
- `createdAt`
- `updatedAt`
- `respondedAt`

Statuswerte:

- `pending_customer`
- `accepted`
- `declined`
- `expired`

## Appointment Status erweitern

`appointments.status` sollte diese Werte erlauben:

- `requested`
- `counter_proposed`
- `confirmed`
- `cancelled`
- `completed`
- `rejected`

## Wenn Kunde annimmt

Wenn Kunde auf `Annehmen` klickt:

1. `appointment_proposals.status = accepted`
2. `respondedAt = aktueller Zeitpunkt`
3. Appointment übernimmt:
   - `date = proposedDate`
   - `startTime = proposedStartTime`
   - `endTime = proposedEndTime`
   - `status = confirmed`
   - `updatedAt = aktueller Zeitpunkt`

## Wenn Kunde ablehnt

Wenn Kunde auf `Ablehnen` klickt:

1. `appointment_proposals.status = declined`
2. `respondedAt = aktueller Zeitpunkt`
3. Appointment bekommt:
   - `status = rejected`
   - `updatedAt = aktueller Zeitpunkt`

Optional:

Admin bekommt eine Benachrichtigung:

`Kunde hat Terminvorschlag abgelehnt.`

## Admin UI

Auf AdminAppointmentsPage bei Terminanfrage:

Buttons:

- `Bestätigen`
- `Absagen`
- `Gegenvorschlag senden`

Bei `Gegenvorschlag senden` öffnet sich ein Modal:

- Datum
- Startzeit
- Endzeit
- Nachricht
- Button `Vorschlag senden`

## Kunden UI

Auf CustomerAppointmentsPage:

Wenn Appointment Status `counter_proposed` ist:

Zeige Karte:

- Ursprünglicher Wunsch
- Neuer Vorschlag
- Nachricht vom Admin
- Button `Annehmen`
- Button `Ablehnen`

## Warum eigene Collection?

Eine eigene `appointment_proposals` Collection ist besser als nur Felder direkt im Termin, weil:

- Verlauf nachvollziehbar bleibt
- später mehrere Vorschläge möglich sind
- Kunde/ Admin-Kommunikation klarer ist
- Make/Benachrichtigungen leichter werden

## Sicherheitsregel

Customer darf:

- eigene Vorschläge lesen
- eigene Vorschläge annehmen oder ablehnen

Customer darf nicht:

- vorgeschlagene Uhrzeit ändern
- Vorschlag für fremden Termin lesen
- Vorschlag für fremden Termin beantworten

Admin/Employee darf:

- Vorschläge erstellen
- eigene Terminanfragen verwalten

## Empfehlung für heute

Heute bauen:

1. Service-Katalog prüfen und sichtbar machen
2. Admin-Gegenvorschlag bei Terminanfragen
3. Customer Antwort auf Gegenvorschlag
4. Make-Automatisierung nur als `invoice_requests` Struktur belassen

Noch nicht bauen:

- echte Zahlung
- automatische Steuerlogik
- mehrere Vorschlagsrunden
- Chat-System
