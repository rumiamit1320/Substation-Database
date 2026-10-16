# GridOps Substation Cloud

GridOps is a browser-based substation telemetry register for transformer test results, protection relay settings, recent fault events, feeder loading, and custom fields.

## Deployment status

This repository is being prepared for GitHub Pages and Firebase. The application source must be present as `index.html` before the Pages workflow can publish it. The source currently supplied in the ChatGPT conversation has not yet been committed to this repository.

## Architecture

- Static frontend: HTML, JavaScript, Tailwind CSS, Lucide icons
- Shared data: Firebase Firestore
- Local development fallback: browser localStorage
- Hosting: GitHub Pages via GitHub Actions

## Before production use

1. Add the reviewed application source as `index.html`.
2. Integrate Firebase Authentication; the existing operator login form is not authentication.
3. Enforce Firestore rules based on authenticated users and authorized substation IDs.
4. Test concurrent edits, JSON import/export, offline behavior, and multiple substations.
5. In repository **Settings → Pages**, set the build and deployment source to **GitHub Actions**.
6. Push to `main`; inspect the **Actions** tab for the deployment result.

## Security notes

Firebase web configuration is not a server secret. Firestore Security Rules and Authentication are the security boundary. Do not deploy sensitive substation data with permissive rules. GitHub Pages sites are publicly reachable even if the source repository is private, so do not place private telemetry exports or backups in the published directory.

## Application scope to preserve

- Dashboard
- Transformer IR, earth resistance, oil BDV and temperature register
- Protection relay setting register
- Last five fault records
- Feeder maximum-load register
- Custom dynamic columns
- JSON backup import/export
- Optional Firestore synchronization
- Indian Standard Time display
