# MediWell App - Projektstruktur

Diese Struktur ergänzt die vorhandenen Konzeptdateien:

- `PROJECT_OVERVIEW.md`
- `database.md`
- `roles.md`
- `roadmap.md`

## Struktur

```text
firebase/
  firebase.json
  firestore.rules
  firestore.indexes.json
  firestore.schema.json
  storage.rules
  seed/
    README.md
    mediwell_v1_test_data.json

flutterflow/
  COLLECTIONS_V1.md
  PAGES_TO_BUILD_V1.md

docs/
  FIREBASE_SETUP_V1.md
  ARCHITECTURE_REVIEW_AND_FIXES.md
  FLUTTERFLOW_IMPLEMENTATION_STEPS_V1.md
  PROJECT_STRUCTURE.md
  TECHNICAL_FLUTTERFLOW_V1.md
```

## Zweck der Ordner

### firebase

Enthält alle Firebase-nahen Dateien:

- Firestore Security Rules
- Storage Rules
- Firestore Indexes
- technisches Collection-Schema
- Beispiel-Testdaten

### flutterflow

Enthält konkrete Bauanleitungen für FlutterFlow:

- welche Collections angelegt werden müssen
- welche Pages für V1.0 gebaut werden müssen
- welche Queries und Actions pro Page nötig sind

### docs

Enthält technische Projektdokumentation:

- Firebase Setup
- FlutterFlow Setup
- Reihenfolge der Umsetzung
- Projektstruktur

## V1.0 Fokus

Version 1.0 baut nur das Fundament:

- Auth
- Rollen
- Profile
- Termine
- einfacher Adminbereich

Alles rund um Rehasport, Mitgliedschaften, Raumzugang und Automatisierung ist vorbereitet, aber nicht Teil des ersten Bauabschnitts.
