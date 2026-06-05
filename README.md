# MediWell App

MediWell ist eine FlutterFlow-App mit Firebase Backend für ein Gesundheits- und Wellnesszentrum.

Die App soll Kunden, Mitarbeitern und Administratoren eine zentrale Plattform für Termine, Profile, Rehasport, Mitgliedschaften und später Raumzugang bieten.

## Version 1.0

V1.0 ist das technische Fundament:

- Firebase Authentication
- Firestore Rollenmodell
- Kundenprofil
- Terminbuchung
- Terminabsage
- Admin-Kundenübersicht
- Admin-Terminübersicht

## Wichtige Dateien

| Datei | Zweck |
| --- | --- |
| `PROJECT_OVERVIEW.md` | Produkt- und Funktionsübersicht |
| `database.md` | ursprüngliche Datenbankstruktur |
| `roles.md` | Rollen und Berechtigungen |
| `roadmap.md` | Versionsplanung |
| `firebase/firestore.rules` | Firestore Security Rules |
| `firebase/storage.rules` | Firebase Storage Rules |
| `firebase/firestore.schema.json` | technische Firestore Struktur |
| `firebase/seed/mediwell_v1_test_data.json` | Beispiel-Testdaten |
| `flutterflow/PAGES_TO_BUILD_V1.md` | Page-Bauplan für FlutterFlow |
| `flutterflow/COLLECTIONS_V1.md` | Collections und Felder für FlutterFlow |
| `docs/TECHNICAL_FLUTTERFLOW_V1.md` | technische FlutterFlow-Dokumentation |
| `docs/FIREBASE_SETUP_V1.md` | Firebase Setup |
| `docs/FLUTTERFLOW_IMPLEMENTATION_STEPS_V1.md` | Schritt-für-Schritt Umsetzung |
| `docs/ARCHITECTURE_REVIEW_AND_FIXES.md` | Architekturprüfung und behobene Inkonsistenzen |

## Umsetzung

Starte mit:

1. `docs/FIREBASE_SETUP_V1.md`
2. `flutterflow/COLLECTIONS_V1.md`
3. `flutterflow/PAGES_TO_BUILD_V1.md`
4. `docs/FLUTTERFLOW_IMPLEMENTATION_STEPS_V1.md`

## Spätere Versionen

Nicht in V1.0 enthalten:

- Rehasport-Verwaltung
- Mitgliedschaften
- QR-Code Raumzugang
- Make.com Automatisierungen
- Push-Benachrichtigungen
- Premium-Angebote
