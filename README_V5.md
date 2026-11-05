# CNC SUB 1P V5 - Mobile PWA (Firebase Spark)

## V5 includes
- Name + password login UI backed by Firebase Authentication Email/Password.
- Admin UID pinned to the Firebase user provided by the project owner.
- Firestore collections: `users/{uid}` and `tasks/{taskId}`.
- Realtime task listener after authentication.
- No demo tasks or default passwords are seeded.
- PWA manifest, icon, and basic app-shell service worker.
- No Cloud Functions, no FCM background push, no paid extension.

## Important account convention
The UI accepts a manager's name. It converts that name to a deterministic Firebase Auth email:
`normalized-name@cnc-sub-1p.invalid`.
The Admin name `Vũ Đình Tú` maps to the already-created account `admin.tu@cnc-sub-1p.invalid`. Other manager names are normalized (remove Vietnamese accents, spaces become dots), e.g. `Nguyễn Văn A` → `nguyen.van.a@cnc-sub-1p.invalid`.
The `.invalid` domain is an internal, non-mailbox identifier; do not use it for email delivery or password-reset emails.

## Existing Admin profile
Firestore `users/{uid}` must already exist with:
- `username`: `admin.tu`
- `displayName`: `Vũ Đình Tú`
- `role`: `ADMIN`
- `status`: `ACTIVE`

Admin UID: `Iy2xpQNDfGS5LYdM15shqELoGWz2`

## Before using this build
1. In Firebase Console, confirm Email/Password is enabled.
2. Confirm Firestore is Standard edition and database ID is `(default)`.
3. Replace the currently published Firestore Rules with the contents of `firestore.rules` and Publish. This enables signed-in users to read tasks, allows Admin to create/edit/delete tasks, and allows an assigned manager to update only status/acknowledgement fields.
4. In Authentication → Users, create each manager account with email exactly matching the normalization rule, e.g. `nguyen.van.a@cnc-sub-1p.invalid`. Password must be unique and at least 6 characters.
5. Copy the Firebase Auth UID. In Firestore → Data, create `users/{UID}` with string fields:
   - `username`: normalized username, e.g. `nguyen.van.a`
   - `displayName`: exact manager name, e.g. `Nguyễn Văn A`
   - `role`: `MANAGER`
   - `status`: `ACTIVE`
6. Do not create any other document with `role: ADMIN`. The rules only permit Admin UID above to be Admin.

## Hosting (free tier only)
- Deploy using Firebase Hosting Spark. Do not add billing or upgrade to Blaze.
- `firebase.json` and `firestore.rules` are included for Firebase CLI deployment.
- Hosting deploy from a computer with Firebase CLI: `firebase deploy --only hosting`.
- Firestore Rules can be pasted/published in Console; no CLI required.

## Limitations / safety
- This package is a V5 integration build, not yet production-certified. Test with one Admin and one test manager before loading production tasks.
- Manager account provisioning is manual through Firebase Console in this free/no-server phase. The app cannot securely create Auth users from the browser without a trusted backend.
- Password reset email is not usable for `.invalid` aliases. Admin must reset passwords in Firebase Console.
- Task reads are available to all signed-in users to support department-wide visibility. Do not store confidential HR/medical/personnel data in task descriptions.
- The service worker caches the app shell only. Background push notifications are intentionally not included.
- Firebase free quotas can be exceeded; monitor usage. No paid plan is configured by this package.
