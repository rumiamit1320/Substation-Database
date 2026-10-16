# Firebase setup and access provisioning

## 1. Configure the project

- Register a Firebase Web App and copy its `apiKey`, `authDomain`, `projectId` and `appId` into GridOps → Configure Firebase.
- Enable Email/Password under Firebase Authentication.
- Create the Firestore database.
- Install the Firebase CLI and authenticate as a project administrator:
  ```sh
npm install -g firebase-tools
firebase login
firebase use YOUR_FIREBASE_PROJECT_ID
firebase deploy --only firestore:rules
```

Review the rules in this repository before deploying them.

## 2. Authorize a user for a substation

The rules use a Firebase Authentication custom claim named `substationIds`. Each user's claim must be assigned by a trusted server using the Firebase Admin SDK. Never let the browser assign its own claims or membership.

Example for a trusted Node.js admin script (run only in a protected administrative environment with the Firebase Admin SDK and appropriate service credentials):

```js
import { initializeApp, applicationDefault } from 'firebase-admin/app';
import { getAuth } from 'firebase-admin/auth';

initializeApp({ credential: applicationDefault(), projectId: 'YOUR_FIREBASE_PROJECT_ID' });
const uid = 'FIREBASE_AUTH_UID';
const substationIds = ['substation_132_33kv_main'];
await getAuth().setCustomUserClaims(uid, { substationIds });
console.log('Substation access assigned. Ask the user to sign in again to refresh their ID token.');
```

Important: custom claims are a complete claim object. If your application already uses other custom claims, preserve them when assigning new claims. Only trusted administrators should run this code. Never commit service-account JSON files or private keys to GitHub.

The substation ID must exactly match the document ID used in GridOps. For example, `substation_132_33kv_main` authorizes access to `gridops_substations/substation_132_33kv_main`.

## 3. Validate access

- A signed-out user must not read or write any substation document.
- An authenticated user without the matching `substationIds` claim must receive permission denied.
- A user with the matching claim may read and update that substation document.
- A user must not delete a substation document or access unrelated collections.
- Test rules in the Firebase Emulator or Rules Playground before using production data.

## 4. Known data-model limits

The current frontend writes transformer, relay, fault and feeder arrays into a single substation document. Firestore has a 1 MiB per-document limit, and whole-document writes can overwrite concurrent edits. For multi-operator production use, move records to subcollections, use server timestamps, and apply transactions or record-level writes.

## 5. GitHub Pages

The repository's workflow deploys the static root directory. Configure **Settings → Pages → Build and deployment → GitHub Actions**. A private repository may require a GitHub plan that supports Pages for private repositories. Do not store telemetry exports, backup JSON files or Firebase service credentials in the public site output.
