# CNC SUB 1P Mobile PWA V6

## V6 changes
- Create manager accounts from the Admin-only Settings panel without signing out the Admin session.
- Manager profile documents are written to Firestore `users/{uid}` with role `MANAGER` and status `ACTIVE`.
- Admin can view all manager profiles and lock/unlock them.
- Locked accounts cannot load task data under the V6 Firestore Rules.
- Existing manager self-service password change remains available.

## Free deployment
This package is designed for Firebase Spark + GitHub Pages. It does not require Cloud Functions, Firebase Blaze, or background push notifications.

## Important security limitation
The browser uses a secondary Firebase Authentication app instance to create the new email/password account while keeping the Admin session active. Firestore Rules restrict user-profile writes and task data to authorized users, but Firebase Email/Password account creation itself is still a public Firebase Auth API operation. A person who reverse-engineers the public web client could create an Auth-only account; without a trusted backend, this cannot be made strictly Admin-only. Such an account has no `users/{uid}` profile and therefore cannot sign in to the application or read task data.

For stronger control over Auth account provisioning and Admin password resets, a trusted backend/Admin SDK is required. Do not put service-account credentials or private keys in this static website.

## Deploy V6
1. Extract the ZIP.
2. Upload/replace `index.html`, `firestore.rules`, and `README_V6.md` in the root of the existing GitHub repository.
3. Commit changes. GitHub Pages will publish the updated app.
4. In Firebase Console -> Firestore Database -> Rules, replace the rules with `firestore.rules` and Publish.
5. Open the app, refresh it, sign in as Admin, then use Settings -> Quản lý tài khoản nhân sự.

## Creating a manager
- Enter the manager's full Vietnamese display name.
- Enter an initial password (at least 6 characters; use a stronger password).
- Optional title/team.
- The app creates an internal alias based on the unaccented name, e.g. `Phạm Thị Cúc` -> `pham.thi.cuc@cnc-sub-1p.invalid`.
- The manager signs in with their displayed full name and the initial password.
- `.invalid` aliases do not receive email. Password reset emails are not available. Managers can change their own password after signing in.

## Locking a manager
Admin can set a manager profile status to `DISABLED` or `ACTIVE`. V6 Rules require an active profile for task access. A locked user's existing app session may remain visually open until refresh/re-authentication, but task reads and writes are denied by Rules.

## Testing checklist
- Sign in as Admin.
- Create one test manager.
- Confirm the manager appears in the account list.
- Sign out and sign in as the manager.
- Confirm the manager can read tasks and only update assigned tasks.
- Sign in as Admin and lock the test manager; refresh the manager session and verify task access is denied.
