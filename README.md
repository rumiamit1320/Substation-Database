# GridOps Substation Cloud

Responsive browser-based substation register for transformer condition readings, relay settings, the latest five fault events, feeder loading, custom fields, CSV exports and JSON backup/restore.

## Current implementation

- Single-page static frontend: HTML, JavaScript, Tailwind CSS and Lucide icons.
- Local preview mode using browser `localStorage`.
- Optional Firebase Email/Password Authentication and Firestore sync.
- Configurable substation document ID and operator/session metadata.
- Responsive navigation, dashboard KPIs, searchable registers, custom fields and timestamps in IST.
- GitHub Actions workflow at `.github/workflows/deploy-pages.yml`.

## Run locally

Open `index.html` in a modern browser or serve the repository directory using a local static HTTP server. Local preview mode is browser-specific and is not user authentication or a shared database.

## GitHub Pages

1. Open repository **Settings → Pages**.
2. Select **GitHub Actions** as the build/deployment source.
3. Push changes to `main` or manually start **Deploy GridOps to GitHub Pages** from the Actions tab.
4. Wait for the workflow to finish and open the Pages URL reported by the workflow.

GitHub Pages availability for a private repository depends on the GitHub plan and repository settings. If Pages is not available for this private repository, use a supported static host such as Vercel or change the repository visibility only if that is acceptable.

## Configure Firebase

1. Create or select a Firebase project and register a Web App.
2. Enable **Authentication → Sign-in method → Email/Password**.
3. Create a Firestore database.
4. Open GridOps → **Configure Firebase** and enter the Web App configuration.
5. Deploy `firestore.rules` using the Firebase CLI after reviewing `docs/FIREBASE_SETUP.md`.
6. Provision each operator with an administrator-assigned `substationIds` custom claim. Newly created accounts are not automatically authorized to read operational data.

Firebase web configuration identifies the project; it is not a substitute for access control. Never deploy permissive rules for operational data. The included rules deny access unless the authenticated user's token contains the relevant substation ID.

## Important limitations before operational use

- Cloud sync stores the module arrays in a single Firestore document per substation. Firestore documents have a 1 MiB size limit; split records into collections if the register grows substantially.
- Whole-document writes can overwrite concurrent changes from multiple operators. This version is suitable for controlled testing, not high-concurrency operational logging.
- Test Auth, claims, Firestore rules, backup restore, offline behavior, and multiple browser sessions before relying on cloud records.
- Local preview data is not centrally backed up.
- This application is a record-keeping interface, not a protection relay, SCADA control system, or safety instrumented system. Verify all engineering data against approved records.

## Modules retained

- Dashboard
- Transformer IR, earth resistance, oil BDV and temperature
- Protection relay settings
- Latest five fault logs
- Feeder maximum-load register
- Custom dynamic columns
- JSON backup import/export and CSV module export
- Optional Firestore synchronization
- Indian Standard Time timestamps
