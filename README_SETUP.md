# ERIC Dashboard — GitHub Pages + Firebase real-time sync + Phone PWA

This package keeps the original Command Center dashboard features, but replaces its Claude-only database connector with Firebase Authentication and Cloud Firestore realtime listeners.

**Important: The app is not connected until you finish the Firebase setup.** No actual Firebase project or GitHub site was created for you.

## 0 — Backup existing dashboard data (critical)
1. Open your original dashboard where your data currently exists.
2. Preferences → **Export backup**. Keep the downloaded JSON file somewhere private.
3. Do not upload that JSON backup to a GitHub repository. GitHub Pages publishes website content publicly.
4. The original dashboard may use a Claude account-based database or browser localStorage. Those records will NOT automatically transfer to your new GitHub origin. Restore the backup later.

## 1 — Create a Firebase project (console.firebase.google.com)
1. **Create project** (e.g., `eric-dashboard`). Analytics can be disabled if you do not need it.
2. Project settings > **Your apps** > **Web app**. Register the app and copy its Firebase config.
3. Open this package's **firebase-config.js** and replace the five PASTE_ values with actual values from the Firebase Web App config. No API keys from other services should be placed here. Firebase web config is normally public, but only safe with correct rules.
4. **Build > Authentication** > Sign-in method > enable **Email/Password**.
5. Authentication > **Users** > **Add user**. Create your personal email and strong password. The app intentionally has no public account registration.
6. **Build > Firestore Database** > Create database. Choose an available region; **Production mode** (deny by default).
7. Firestore Database > **Rules**: paste the entire content of `firestore.rules` and **Publish**. The rules only allow each signed-in UID to read/write its own item documents.
8. Authentication > Settings > **Authorized domains**: add your GitHub Pages host (for example `yourusername.github.io`) if required by your auth provider. Do not include protocol or path.

## 2 — Publish in GitHub Pages
1. Sign in at github.com and make a new repository (e.g. `eric-dashboard`).
2. Upload the *contents* of this folder into the repository root. Ensure main webpage name is exactly `index.html`. Do not upload your personal JSON backup.
3. Repo **Settings → Pages → Build and deployment → Source: Deploy from a branch → Branch: main → Folder: /(root) → Save**.
4. Your URL is usually `https://YOURUSERNAME.github.io/eric-dashboard/`. Open it and sign in using the Firebase account created in Step 1.
5. If you see “Firebase setup required,” check that firebase-config.js has been updated and published (not the placeholder).

## 3 — Restore existing data
1. Open the published site, sign in, and wait for “Synced with Firebase” at the bottom left.
2. Go to **Preferences → Restore from file** and select the *private* JSON backup downloaded from the original dashboard.
3. Check a few tasks, bills and runs, then open the site on your phone and sign in with the same email.
4. Add a test task on phone; it should appear on the desktop when both are online.

## 4 — Install phone app (PWA)
- iPhone: Safari → open site → Share → Add to Home Screen → **Open as Web App** → Add.
- Android: Chrome → open site → menu (three dots) → **Install app** or **Add to Home screen**.
- Use the same Firebase login on all your devices. This is a web app/PWA, not an app-store native iOS/Android app.

## 5 — What is and is not synced
- Cloud Firestore via realtime snapshot listeners syncs dashboard content you create in the app, such as tasks, photography bookings, design jobs, bills, transactions, runs, training plans, videos, events, goals, and settings.
- Appearance theme, dismissed reminders, and cached Google data are still per-browser.
- The original **Google Calendar** and **linked Google Sheets** connections call Claude-specific MCP integrations, so they do **not** directly work on GitHub Pages. They require separate Google API/OAuth work if you want those integrations later. Core dashboard data sync does not depend on Google Calendar.
- The PWA can load part of its app shell offline; authenticated database operations require an internet connection for cross-device synchronization. Offline cached writes may be queued by Firestore on supporting browsers; do not assume another device saw them until online/synced.
- Concurrent edits to the same item may use last-write-wins behavior; avoid editing the same item simultaneously on phone and computer.

## 6 — Privacy and safety
- GitHub Pages publishes website code and static assets (public). Keep personal records in Firestore, not repository files.
- `firebase-config.js` is not a secret and is safe only if you use proper Firestore rules. Never upload service-account JSON, Firebase admin keys or passwords.
- The app uses Email/Password authentication. Add your own account in Firebase console and do not enable public sign-up unless needed.
- Keep regular private JSON backups in addition to cloud sync. Review Firebase usage quotas and costs if usage grows.
- Firebase only authenticates the email you register in your Firebase project. This has no automatic link to your original Claude data/account.

## Troubleshooting
- **Permission denied**: Check your Firestore Rules; this package uses `data/users/{uid}/{itemId}` (not `users/{uid}/items`).
- **Sign-in failed**: Enable Email/Password in Firebase Authentication, verify account exists and password is correct.
- **Signed in, no data**: The cloud database is new. Restore your original JSON backup into the new site.
- **Works on desktop but not phone**: Use the same Firebase account on both, check the internet connection and site URL.
- **Blank screen**: Open browser developer tools and look for a JavaScript error; confirm the files are in one folder structure and the Firebase SDK is not blocked.
- **Old version cached**: Refresh/reload the PWA. The service worker prefers the network, then falls back to a cached shell.

Official documentation:
- GitHub Pages: https://docs.github.com/en/pages/getting-started-with-github-pages/configuring-a-publishing-source-for-your-github-pages-site
- Firestore realtime listeners: https://firebase.google.com/docs/firestore/query-data/listen
- Firebase Security Rules: https://firebase.google.com/docs/rules/rules-and-auth
- Firebase Web SDK: https://firebase.google.com/docs/web/learn-more
- PWA installation: https://developer.mozilla.org/en-US/docs/Web/Progressive_web_apps/Guides/Making_PWAs_installable
