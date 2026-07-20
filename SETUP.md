# Speech Studio — set up logins & saving (Firebase)

Your app now has teacher and student logins, per‑student saved speeches, and
teacher‑edit tracking (shown in red to students). To make that work on a
static host (GitHub Pages, Netlify, etc.) it needs a small free **Firebase**
project to store accounts and speeches. This takes about 10 minutes, once.

## 1. Create a Firebase project
1. Go to https://console.firebase.google.com and click **Add project**.
2. Give it any name (e.g. "speech-studio"), finish the wizard (you can turn
   off Google Analytics — it's not needed).

## 2. Turn on Authentication
1. In the left sidebar, click **Build → Authentication → Get started**.
2. Under **Sign-in method**, enable **Email/Password**.

## 3. Turn on Firestore (the database)
1. In the left sidebar, click **Build → Firestore Database → Create database**.
2. Choose **Production mode**, pick any region, click **Enable**.
3. Click the **Rules** tab and replace the contents with everything in
   `firestore.rules` (included alongside this file). Click **Publish**.

## 4. Get your web app config
1. Click the ⚙️ gear icon → **Project settings**.
2. Scroll to **Your apps**, click the **</>** (web) icon, register an app
   (any nickname), and copy the `firebaseConfig` object it shows you.
3. Open `index.html`, find this block near the bottom of the `<script>`
   (search for `firebaseConfig`), and paste your values in:

```js
const firebaseConfig = {
  apiKey: "YOUR_API_KEY",
  authDomain: "YOUR_PROJECT.firebaseapp.com",
  projectId: "YOUR_PROJECT_ID",
  storageBucket: "YOUR_PROJECT.appspot.com",
  messagingSenderId: "YOUR_SENDER_ID",
  appId: "YOUR_APP_ID"
};
```

## 5. Set a teacher setup code
Right below the config, you'll see:

```js
const TEACHER_SETUP_CODE = "CHANGE_ME_1234";
```

Change `CHANGE_ME_1234` to something private you choose. Anyone who has
this code can create a teacher account on your site, so keep it to
yourself (and change it before you share the link publicly). Once you've
created your own teacher account, you can optionally lock teacher sign‑ups
completely — see the note at the bottom of `firestore.rules`.

## 6. Deploy
Upload `index.html` to GitHub Pages, Netlify, or wherever you're hosting
it, exactly as you do today — no build step needed.

## How it works day-to-day

- **Teacher**: on the app, choose "I'm a teacher" → "Create a teacher
  account" (using your setup code, your name, and a **class code** you
  invent, e.g. `MRSMITH4`). After that, just log in with your email and
  password. Your dashboard lets you add students (name, username,
  password), see each student's progress, reset a forgotten password, and
  open a student's speech to read or edit it.
- **Students**: choose "I'm a student" and log in with the **class code**,
  **username**, and **password** you gave them. Their work autosaves as
  they type. Each student only ever has one speech — they edit it in
  place, and "Start over" clears it so they can write a new one.
- **Teacher edits**: when you edit a student's speech and save, the
  student sees your changes highlighted in red the next time they open
  their speech — added words in red, removed words with a red line
  through them. They click "Accept & keep editing" to merge your changes
  in and keep working on it.

## A couple of honest limitations

- Student passwords are stored in Firestore so you (the teacher) can look
  them up if a student forgets — this is convenient for a classroom tool
  but isn't bank-grade security. Don't reuse a sensitive password as a
  student's speech-app password.
- The teacher setup code is a simple shared secret, not a bulletproof
  gate. For extra safety once your account exists, you can edit
  `firestore.rules` to block all future teacher sign-ups (see the comment
  at the bottom of that file).
