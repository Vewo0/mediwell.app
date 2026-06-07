# MediWell App - Security Review 2026-06-07

## Scope

Defensive review of the MediWell repository architecture, Firebase rules, Storage rules and FlutterFlow documentation.

Reviewed areas:

- Firebase Authentication assumptions
- Role-based authorization
- Firestore Security Rules
- Storage Security Rules
- Appointments
- Memberships
- QR room access
- Rehasport structures
- InvoiceRequest / Make.com preparation
- FlutterFlow page plan

This review follows OWASP Web Security Testing Guide principles for Authentication, Authorization, Session Management and Input Validation. No attack against third-party systems was performed.

## Security Score

Current score after fixes: **7.5 / 10**

Before this review the estimated score was **6 / 10** because the Firestore rules did not yet cover newly added collections and profile images were readable by every signed-in user.

## Summary of Changes Applied

### Firestore Rules Hardened

File:

`firebase/firestore.rules`

Changes:

- Added rules for newly documented collections:
  - `service_catalog`
  - `courses`
  - `rehab_prescriptions`
  - `rehab_enrollments`
  - `rehab_attendance`
  - `seminars`
  - `seminar_registrations`
  - `course_bookings`
  - `invoice_requests`
- Added field whitelists for new document types.
- Added status validation for Rehasport, seminars, bookings and invoice requests.
- Added support for legacy `role = user` as customer-like role while the app migrates to `role = customer`.
- Added service catalog validation for customer-created appointments to reduce price/service manipulation risk.
- Restricted InvoiceRequest access to staff/admin creation and admin processing.
- Restricted Rehasport prescription changes to admin only.
- Restricted Rehasport attendance creation to staff only.

### Storage Rules Hardened

File:

`firebase/storage.rules`

Changes:

- Profile image read access changed from all signed-in users to only the image owner.
- Profile image write access remains owner-only.
- Existing image type and 5 MB size checks remain active.

## Findings

### Finding 1: New Collections Were Missing Firestore Rules

Risk: High

Affected file:

`firebase/firestore.rules`

Problem:

The schema had grown to include Rehasport, courses, seminars and InvoiceRequests, but the Firestore rules still only covered the older collections. Firestore denies unmatched collections by default, which is safe from data leakage but can break app features. If later broad catch-all rules were added hastily, that could become a serious authorization bug.

Fix applied:

Explicit rules were added for each new collection with role checks and field validation.

### Finding 2: Profile Images Were Readable by Every Signed-In User

Risk: Medium

Affected file:

`firebase/storage.rules`

Problem:

`profile_images/{userId}/{fileName}` allowed read access to any signed-in user. That means a customer could potentially read another customer's profile image if they knew or guessed the path.

Fix applied:

Read access is now owner-only:

`allow read: if isOwnProfileImage(userId);`

Tradeoff:

Admin profile image display may need app-level handling or a later staff-safe storage pattern if admins must view profile images.

### Finding 3: Customer Appointment Price Manipulation Risk

Risk: High

Affected file:

`firebase/firestore.rules`

Problem:

If customers can send appointment writes directly, they could attempt to change `price`, `durationMinutes`, `requiresEmployee` or `serviceName` unless the backend verifies those values.

Fix applied:

Customer-created appointments now require a `serviceId` linked to `service_catalog`. The rule checks that:

- service is active
- service is online bookable
- serviceType matches catalog
- serviceName matches catalog
- durationMinutes matches catalog
- price matches catalog
- requiresEmployee matches catalog

### Finding 4: Rehasport Data Needed Strong Admin Ownership

Risk: High

Affected file:

`firebase/firestore.rules`

Problem:

Rehasport prescriptions affect medical/administrative entitlement. Customers must never be able to create, edit, extend or self-assign prescriptions.

Fix applied:

Only admin can create/update/delete:

- `rehab_prescriptions`
- `rehab_enrollments`

Customers can only read their own Rehasport data.

### Finding 5: InvoiceRequest Processing Must Not Be Customer-Controlled

Risk: High

Affected file:

`firebase/firestore.rules`

Problem:

InvoiceRequests are the handoff to Make.com. Customers must not be able to create, change, cancel or mark invoice requests as processed.

Fix applied:

- staff can create valid invoice requests
- admin can read/update/delete
- customers have no access
- amount must be greater than 0
- currency must be EUR
- status is restricted

### Finding 6: Notifications Update Was Too Broad

Risk: Medium

Affected file:

`firebase/firestore.rules`

Problem:

A customer-owned notification could previously be updated more broadly than necessary.

Fix applied:

Customers can only update:

- `isRead`
- `updatedAt`

Staff can still manage operational notifications.

### Finding 7: FlutterFlow UI Must Not Be Treated as Security Boundary

Risk: High

Affected files:

- `flutterflow/PAGES_TO_BUILD_V1.md`
- Base44/FlutterFlow app routes

Problem:

Hiding admin buttons is not security. A customer may still try direct URLs such as `/admin/rechnungen` or `/admin/kunden`.

Recommendation:

Keep role checks in all admin pages before loading data. The Firebase rules now provide a second layer, but UI routing must still prevent confusing access and data flashes.

## Safe Test Cases

Use only test accounts.

Recommended accounts:

- one admin
- one employee
- two customers
- one inactive customer

### Authentication Tests

1. Open `/admin/rechnungen` while logged out.
   - Expected: redirected to login or access denied.

2. Open `/admin/kunden` as customer.
   - Expected: access denied or redirected to customer dashboard.

3. Set a test customer `isActive = false`, then login.
   - Expected: no protected data loads; user is logged out or blocked.

### User Authorization Tests

4. Customer A tries to read Customer B user document.
   - Expected: denied.

5. Customer A tries to update their own `role` to `admin`.
   - Expected: denied.

6. Customer A tries to update their own `isActive`.
   - Expected: denied.

### Appointment Tests

7. Customer A creates appointment with `customerId = Customer B`.
   - Expected: denied.

8. Customer A creates appointment with fake lower `price` than service catalog.
   - Expected: denied.

9. Customer A updates appointment status to `completed`.
   - Expected: denied.

10. Customer A cancels own appointment with status `requested`.
    - Expected: allowed.

11. Customer A cancels own appointment with status `completed`.
    - Expected: denied.

### Membership Tests

12. Customer A reads own membership.
    - Expected: allowed.

13. Customer A reads Customer B membership.
    - Expected: denied.

14. Customer A changes own membership to `active`.
    - Expected: denied.

### Rehasport Tests

15. Customer A reads own `rehab_prescriptions` document.
    - Expected: allowed.

16. Customer A edits `validUntil` on own prescription.
    - Expected: denied.

17. Customer A creates a new `rehab_enrollments` document for themselves.
    - Expected: denied.

18. Employee records `rehab_attendance` with `recordedBy` equal to their own UID.
    - Expected: allowed.

19. Employee records `rehab_attendance` with another user's `recordedBy`.
    - Expected: denied.

### InvoiceRequest Tests

20. Customer tries to read `invoice_requests`.
    - Expected: denied.

21. Customer tries to set `makeProcessed = true`.
    - Expected: denied.

22. Admin marks invoice request as processed.
    - Expected: allowed.

23. Staff creates invoice request with `amount = 0`.
    - Expected: denied.

### QR Room Access Tests

24. Customer reads own `room_access`.
    - Expected: allowed.

25. Customer edits own `room_access.status` to `active`.
    - Expected: denied.

26. Customer reads another customer's `room_access`.
    - Expected: denied.

27. Customer writes `access_logs`.
    - Expected: denied.

28. Employee writes `access_logs`.
    - Expected: allowed.

### Storage Tests

29. Customer uploads own profile image under `profile_images/{ownUid}/profile.jpg` with image content type under 5 MB.
    - Expected: allowed.

30. Customer uploads image under another user's profile path.
    - Expected: denied.

31. Customer uploads `.exe` or non-image file as profile image.
    - Expected: denied.

32. Customer reads another user's profile image path.
    - Expected: denied.

## Remaining Recommendations

1. Use a real Firebase Rules test suite before production.
2. Add Cloud Functions for sensitive operations later:
   - invoice request creation
   - late cancellation calculation
   - QR token verification
   - Rehasport usedUnits updates
3. Do not expose Make.com webhook URLs in the client.
4. Use server-side Make authentication or a secret only stored in Make/Firebase backend tooling.
5. Keep `role = user` only as a temporary migration bridge. Long term, use only:
   - `admin`
   - `employee`
   - `customer`
6. If admins must view customer profile images, create a separate controlled display flow instead of making all profile images readable to every signed-in user.
