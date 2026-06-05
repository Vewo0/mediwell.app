# MediWell App - FlutterFlow Collections

Diese Datei beschreibt die Collections fuer FlutterFlow. Fuer Version 1.0 muessen `users` und `appointments` aktiv gebaut werden. `memberships`, `rooms`, `room_access` und `access_logs` sollten bereits mit derselben Struktur vorbereitet werden, damit Version 2.0 und 3.0 ohne Datenmigration starten koennen.

## users

Collection Name:

`users`

Document ID:

Firebase Auth UID

| Name | FlutterFlow Typ | Required | Beispiel |
| --- | --- | --- | --- |
| uid | String | Ja | customer_demo_uid |
| role | String | Ja | customer |
| firstName | String | Ja | Carla |
| lastName | String | Ja | Kunde |
| email | String | Ja | kunde@mediwell.example |
| phone | String | Nein | +49 6700 000003 |
| profileImageUrl | Image Path / String | Nein | leer |
| dateOfBirth | DateTime | Nein | 1990-04-18 |
| createdAt | DateTime | Ja | Current Timestamp |
| updatedAt | DateTime | Ja | Current Timestamp |
| isActive | Boolean | Ja | true |

Regel:

Ein Nutzer darf sich selbst nur als `customer` registrieren. `admin` und `employee` werden durch einen Admin oder in der Firebase Console gesetzt.

## appointments

Collection Name:

`appointments`

Document ID:

Auto ID

| Name | FlutterFlow Typ | Required | Beispiel |
| --- | --- | --- | --- |
| appointmentId | String | Nein | appointment_demo_requested |
| customerId | String | Ja | customer_demo_uid |
| employeeId | String | Nein | employee_demo_uid |
| serviceType | String | Ja | wellness |
| serviceName | String | Ja | Beratungsgespraech |
| date | DateTime | Ja | 2026-06-10 |
| startTime | String | Ja | 10:00 |
| endTime | String | Ja | 10:30 |
| status | String | Ja | requested |
| notes | String | Nein | Ersttermin |
| createdAt | DateTime | Ja | Current Timestamp |
| updatedAt | DateTime | Ja | Current Timestamp |

Statuswerte:

- `requested`: Kunde hat Termin angefragt.
- `confirmed`: Admin oder Mitarbeiter hat Termin bestaetigt.
- `cancelled`: Kunde, Admin oder Mitarbeiter hat Termin abgesagt.
- `completed`: Admin oder Mitarbeiter hat Termin abgeschlossen.

## memberships

Collection Name:

`memberships`

Document ID:

Auto ID

| Name | FlutterFlow Typ | Required | Beispiel |
| --- | --- | --- | --- |
| membershipId | String | Nein | membership_demo_active |
| customerId | String | Ja | customer_demo_uid |
| membershipType | String | Ja | Wellness Basis |
| startDate | DateTime | Ja | 2026-06-01 |
| endDate | DateTime | Ja | 2026-12-31 |
| status | String | Ja | active |
| priceMonthly | Double | Ja | 49 |
| allowedRooms | List<String> | Ja | room_demo_infrared |
| createdAt | DateTime | Ja | Current Timestamp |
| updatedAt | DateTime | Ja | Current Timestamp |

Statuswerte:

- `active`
- `paused`
- `cancelled`
- `expired`

FlutterFlow-Hinweis:

`allowedRooms` enthaelt die Dokument-IDs aus `rooms`. Daraus kann spaeter automatisch `room_access` erzeugt werden.

## rooms

Collection Name:

`rooms`

Document ID:

Auto ID oder feste Raum-ID

| Name | FlutterFlow Typ | Required | Beispiel |
| --- | --- | --- | --- |
| roomId | String | Ja | room_demo_infrared |
| roomName | String | Ja | Infrarotkabine |
| roomType | String | Ja | wellness |
| description | String | Nein | Raum fuer spaeteren QR-Zugang |
| isActive | Boolean | Ja | true |

Beispiele:

- `infrared`
- `massage`
- `rehab`
- `seminar`
- `sauna`
- `salt_cave`

## room_access

Collection Name:

`room_access`

Document ID:

Auto ID

| Name | FlutterFlow Typ | Required | Beispiel |
| --- | --- | --- | --- |
| accessId | String | Ja | access_demo_infrared_customer |
| customerId | String | Ja | customer_demo_uid |
| roomId | String | Ja | room_demo_infrared |
| validFrom | DateTime | Ja | 2026-06-01 |
| validUntil | DateTime | Ja | 2026-12-31 |
| accessType | String | Ja | membership |
| status | String | Ja | active |
| qrToken | String | Ja | zufaelliger Token |
| qrCodeVersion | Integer | Ja | 1 |
| lastRotatedAt | DateTime | Ja | Current Timestamp |
| createdAt | DateTime | Ja | Current Timestamp |
| updatedAt | DateTime | Ja | Current Timestamp |

QR-Payload fuer FlutterFlow:

`mediwell://access/{accessId}?token={qrToken}&v={qrCodeVersion}`

Wichtig:

Der QR-Code enthaelt nie die Firebase UID und darf nur fuer Raumzugang genutzt werden.

## access_logs

Collection Name:

`access_logs`

Document ID:

Auto ID

| Name | FlutterFlow Typ | Required | Beispiel |
| --- | --- | --- | --- |
| logId | String | Nein | access_log_demo_granted |
| customerId | String | Ja | customer_demo_uid |
| roomId | String | Ja | room_demo_infrared |
| timestamp | DateTime | Ja | Current Timestamp |
| accessGranted | Boolean | Ja | true |
| reason | String | Ja | active_room_access |
| scannedBy | String | Ja | employee_demo_uid |
| deviceId | String | Nein | frontdesk_demo_device |

## notifications

Collection Name:

`notifications`

V1.0 optional, spaeter fuer Push und Hinweise.

| Name | FlutterFlow Typ | Required | Beispiel |
| --- | --- | --- | --- |
| notificationId | String | Nein | notification_demo_welcome |
| userId | String | Ja | customer_demo_uid |
| title | String | Ja | Willkommen bei MediWell |
| message | String | Ja | Dein Profil ist bereit. |
| type | String | Ja | info |
| isRead | Boolean | Ja | false |
| createdAt | DateTime | Ja | Current Timestamp |

## Pflicht fuer V1.0

Aktiv bauen:

- `users`
- `appointments`

Vorbereiten:

- `memberships`
- `rooms`
- `room_access`
- `access_logs`
- `notifications`
