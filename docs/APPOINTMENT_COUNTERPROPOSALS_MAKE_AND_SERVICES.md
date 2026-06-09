# MediWell - Leistungen, Make und Terminvorschlaege

## Ziel

Diese Datei dokumentiert drei neue Arbeitsbereiche fuer Version 1.x:

1. Leistungen 1:1 als Service-Katalog pflegen
2. Make-Automatisierung vorbereiten
3. Terminanfragen mit Gegenvorschlag abbilden

Quelle fuer Leistungen und Preise:

- https://www.mediwell.online/unsere-angebote
- Abgerufen und geprueft am 09.06.2026

## 1. Leistungen 1:1 uebernehmen

Leistungen sollen nicht frei eingetippt werden. Sie gehoeren in einen Service-Katalog.

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
- `durationLabel`
- `price`
- `priceLabel`
- `onlineBookable`
- `requiresEmployee`
- `isPackage`
- `packageValidityWeeks`
- `packageItems`
- `isActive`
- `sortOrder`
- `sourceUrl`
- `createdAt`
- `updatedAt`

Business:

- `businessId = mediwell_studio`
- Name: `MediWell.Studio`
- Website: `https://www.mediwell.online/unsere-angebote`
- Adresse laut Website-Karte: `Winterburger Str. 22, 55595 Bockenau, Deutschland`
- Telefon laut Website: `+4916096285823`

## Service-Katalog MediWell.Studio

### Kategorie: Massagen & Wellness

#### Einzelangebote

1. `Rücken- & Nackenmassage`
   - Dauer: 30 Minuten
   - Preis: 45 EUR
   - Beschreibung: `Lösen Sie Verspannungen gezielt mit unserer 30-minütigen Rücken- & Nackenmassage.`
   - Online buchbar: Ja
   - Mitarbeiter erforderlich: Ja

2. `Teilmassage`
   - Dauer: 20 Minuten
   - Preis: 39 EUR
   - Beschreibung: `Revitalisierende Teilmassage, die gezielt auf individuelle Bedürfnisse eingeht.`
   - Online buchbar: Ja
   - Mitarbeiter erforderlich: Ja

3. `Aromaöl-Rückenmassage`
   - Dauer: 30 Minuten
   - Preis: 59 EUR
   - Beschreibung: `Entspannende Aromaöl-Rückenmassage.`
   - Online buchbar: Ja
   - Mitarbeiter erforderlich: Ja

4. `Hot Stone Rückenmassage`
   - Dauer: 45 Minuten
   - Preis: 68 EUR
   - Beschreibung: `Wärme und Entspannung für den Rücken.`
   - Online buchbar: Ja
   - Mitarbeiter erforderlich: Ja

5. `Hot Stone Ganzkörpermassage`
   - Dauer: 60 Minuten
   - Preis: 89 EUR
   - Beschreibung: `Tiefe Entspannung mit Hot Stone Ganzkörpermassage.`
   - Online buchbar: Ja
   - Mitarbeiter erforderlich: Ja

6. `Honig-Entgiftungsmassage`
   - Dauer: 60 Minuten
   - Preis: 89 EUR
   - Beschreibung: `Reinigende und revitalisierende Honig-Entgiftungsmassage.`
   - Online buchbar: Ja
   - Mitarbeiter erforderlich: Ja

7. `Fußreflexzonenmassage`
   - Dauer: 45 Minuten
   - Preis: 55 EUR
   - Beschreibung: `Fußreflexzonenmassage zur Förderung des Wohlbefindens.`
   - Online buchbar: Ja
   - Mitarbeiter erforderlich: Ja

8. `Faszien- & Tiefenregeneration`
   - Dauer: 70 bis 80 Minuten
   - Dauer fuer App-Logik: 80 Minuten
   - Preis: 119 EUR
   - Beschreibung: `Intensive Regeneration für die Muskulatur.`
   - Online buchbar: Ja
   - Mitarbeiter erforderlich: Ja

9. `Premium Wellness & Regeneration`
   - Dauer: 80 Minuten
   - Preis: 99 EUR
   - Beschreibung: `Premium Wellness & Regeneration als Verwöhnzeit.`
   - Online buchbar: Ja
   - Mitarbeiter erforderlich: Ja

#### Wellness-Pakete

Pakete sind fuer die App wichtig, aber in Version 1.0 besser nicht direkt als normaler Einzeltermin buchbar. Sie sollen als Angebot sichtbar sein und spaeter als Mitgliedschaft/Paketlogik ausgebaut werden.

10. `3er Paket Rücken- & Nackenmassage + Infrarot-Wärmekabine`
    - Preis: 129 EUR
    - Inhalt: `3 × Rücken- & Nackenmassage (30 Min.)` und `3 × Infrarot-Wärmekabine (15 Min.)`
    - Gültigkeit: 6 Wochen
    - Ersparnis laut Website: 6 EUR
    - Online buchbar: Nein in V1
    - Mitarbeiter erforderlich: Ja
    - `isPackage = true`

11. `5er Paket Rücken- & Nackenmassage + Infrarot-Wärmekabine`
    - Preis: 210 EUR
    - Inhalt: `5 × Rücken- & Nackenmassage (30 Min.)` und `5 × Infrarot-Wärmekabine (15 Min.)`
    - Gültigkeit: 8 Wochen
    - Ersparnis laut Website: 15 EUR
    - Online buchbar: Nein in V1
    - Mitarbeiter erforderlich: Ja
    - `isPackage = true`

12. `8er Paket Rücken- & Nackenmassage + Infrarot-Wärmekabine`
    - Preis: 320 EUR
    - Inhalt: `8 × Rücken- & Nackenmassage (30 Min.)` und `8 × Infrarot-Wärmekabine (15 Min.)`
    - Gültigkeit: 12 Wochen
    - Ersparnis laut Website: 40 EUR
    - Online buchbar: Nein in V1
    - Mitarbeiter erforderlich: Ja
    - `isPackage = true`

## Wichtige Buchungsregel

Bei normalen Terminanfragen sollen Kunden nur Leistungen buchen koennen, bei denen gilt:

- `isActive = true`
- `onlineBookable = true`
- `isPackage = false`

Pakete sollen in V1 sichtbar sein, aber nicht als einzelner Termin direkt gebucht werden. Fuer Pakete braucht die App spaeter Paketguthaben oder eine kleine Membership-Logik.

## 2. Make-Automatisierung strukturieren

Die App soll nicht direkt Rechnungen versenden. Sie erstellt nur Datensaetze fuer Make.

Collection:

`invoice_requests`

Make liest neue Datensaetze mit:

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
3. Admin oeffnet Terminanfrage.
4. Admin waehlt `Gegenvorschlag senden`.
5. Admin waehlt neues Datum, neue Startzeit und neue Endzeit.
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
3. Appointment uebernimmt:
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

Bei `Gegenvorschlag senden` oeffnet sich ein Modal:

- Datum
- Startzeit
- Endzeit
- Nachricht
- Button `Vorschlag senden`

## Kunden UI

Auf CustomerAppointmentsPage:

Wenn Appointment Status `counter_proposed` ist:

Zeige Karte:

- Urspruenglicher Wunsch
- Neuer Vorschlag
- Nachricht vom Admin
- Button `Annehmen`
- Button `Ablehnen`

## Warum eigene Collection?

Eine eigene `appointment_proposals` Collection ist besser als nur Felder direkt im Termin, weil:

- Verlauf nachvollziehbar bleibt
- spaeter mehrere Vorschlaege moeglich sind
- Kunde/Admin-Kommunikation klarer ist
- Make/Benachrichtigungen leichter werden

## Sicherheitsregel

Customer darf:

- eigene Vorschlaege lesen
- eigene Vorschlaege annehmen oder ablehnen

Customer darf nicht:

- vorgeschlagene Uhrzeit aendern
- Vorschlag fuer fremden Termin lesen
- Vorschlag fuer fremden Termin beantworten

Admin/Employee darf:

- Vorschlaege erstellen
- eigene Terminanfragen verwalten

## Empfehlung fuer heute

Heute bauen:

1. Service-Katalog mit den echten MediWell-Angeboten aus `https://www.mediwell.online/unsere-angebote`
2. Admin-Gegenvorschlag bei Terminanfragen
3. Customer Antwort auf Gegenvorschlag
4. Make-Automatisierung nur als `invoice_requests` Struktur belassen

Noch nicht bauen:

- echte Zahlung
- automatische Steuerlogik
- mehrere Vorschlagsrunden
- Chat-System
- Paketguthaben fuer Wellness-Pakete
