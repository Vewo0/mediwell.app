# MediWell App

## Projektübersicht

MediWell ist ein Gesundheits- und Wellnesszentrum in Bockenau (Deutschland).

Die App soll Kunden, Mitarbeitern und Administratoren eine zentrale Plattform für Terminverwaltung, Rehasport, Mitgliedschaften und Raumzugang bieten.

Die App wird mit FlutterFlow entwickelt und nutzt Firebase als Backend.

---

# Ziel der App

Die App soll möglichst viele Verwaltungsaufgaben digitalisieren und automatisieren.

Ziele:

- Digitale Kundenverwaltung
- Terminbuchung
- Rehasport-Verwaltung
- Mitgliedschaftsverwaltung
- Raumzugang per QR-Code
- Automatisierungen über Make.com
- Modernes Wellness-Erlebnis

---

# Benutzerrollen

## Kunde

Ein Kunde kann:

- Sich registrieren
- Sich anmelden
- Profil bearbeiten
- Profilbild hochladen
- Termine buchen
- Termine verwalten
- Rehasport-Kurse ansehen
- Mitgliedschaften ansehen
- QR-Code für Raumzugang nutzen
- Benachrichtigungen erhalten

---

## Mitarbeiter

Ein Mitarbeiter kann:

- Kundendaten einsehen
- Termine verwalten
- Rehasport verwalten
- Zutrittsrechte prüfen
- Anwesenheiten dokumentieren

---

## Administrator

Ein Administrator kann:

- Kunden verwalten
- Mitarbeiter verwalten
- Termine verwalten
- Mitgliedschaften verwalten
- Rehasport verwalten
- Raumzugänge verwalten
- Statistiken ansehen
- Dashboard nutzen

---

# Seitenstruktur

## Kundenbereich

### Dashboard
Zeigt:

- Begrüßung
- Nächste Termine
- Aktive Mitgliedschaft
- Aktuelle Informationen

### Termine

- Termin buchen
- Termin absagen
- Termin verschieben
- Terminübersicht

### Rehasport

- Kursübersicht
- Kursinformationen
- Teilnahmeübersicht

### Mitgliedschaft

Zeigt:

- Tarif
- Laufzeit
- Status

### Raumzugang

Separate Seite.

Diese Seite dient ausschließlich dem Raumzugang.

Sie enthält:

- Persönlichen QR-Code
- Gültigkeitsstatus
- Freigeschaltete Räume
- Zutrittsverlauf

Wichtig:

Der QR-Code dient nur für Zutrittskontrollen.

Der QR-Code darf NICHT verwendet werden für:

- Terminbuchungen
- Zahlungen
- Registrierungen

### Profil

- Name
- Telefonnummer
- E-Mail
- Profilbild

---

# Adminbereich

## Dashboard

Zeigt:

- Anzahl Kunden
- Anzahl Termine
- Anzahl Mitglieder
- Rehasport-Auslastung
- Umsatzstatistiken

## Kundenverwaltung

- Kunde erstellen
- Kunde bearbeiten
- Kunde deaktivieren

## Terminverwaltung

- Termine erstellen
- Termine ändern
- Termine löschen

## Rehasportverwaltung

- Kurse verwalten
- Teilnehmer verwalten

## Mitgliedschaften

- Mitgliedschaften erstellen
- Mitgliedschaften ändern
- Mitgliedschaften kündigen

## Raumzugangsverwaltung

- Räume anlegen
- Räume freischalten
- Zutrittsrechte vergeben
- Zutrittsprotokolle anzeigen

---

# Design

Stil:

- Modern
- Professionell
- Wellness
- Hochwertig

Farben:

- Weiß
- Violett

Designziele:

- Einfach verständlich
- Wenige Klicks
- Große Buttons
- Mobile First

---

# Technische Anforderungen

Frontend:
- FlutterFlow

Backend:
- Firebase

Automatisierungen:
- Make.com

Authentifizierung:
- Firebase Authentication

Datenbank:
- Cloud Firestore

Dateispeicher:
- Firebase Storage

Push-Benachrichtigungen:
- Firebase Cloud Messaging

---

# Langfristige Erweiterungen

Geplante Funktionen:

- Sauna
- Salzgrotte
- Infrarotkabine
- Online-Seminare
- Digitale Check-ins
- Automatische Erinnerungen
- Kunden-App mit Premium-Mitgliedschaften

---

# Entwicklungsziel

Die App soll professionell, skalierbar und für mehrere hundert Nutzer geeignet sein.
