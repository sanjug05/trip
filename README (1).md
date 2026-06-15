# ✈️ Trip Planner PRO

A group travel companion you can share with anyone — **no accounts or passwords required**.
One person creates a trip, shares a **QR code / trip code**, and everyone who joins sees
expenses, bookings, the itinerary, chat and more update **live**.

Built as a single `index.html` (React + Firebase), so it deploys anywhere static.

---

## How people use it

1. **Create** — open the app → *Create Trip* → enter your name + trip details. You get a 7-character trip code.
2. **Invite** — tap **👥 Invite** to show a **QR code** + shareable link + the code (with WhatsApp/native share).
3. **Join** — others scan the QR (or enter the code) → type their name → they're in. Works for groups of 10–15+.
4. **Collaborate** — anyone can add expenses; everything syncs to everyone within ~1 second.

No login screen: each device is signed in **anonymously** in the background. Anyone can
optionally **Sign in with Google** so they can recover their trips on a new phone.

---

## Production setup (one time)

### 1. Firebase console (project `ais-showroom-dashboard`, or your own)
- **Authentication → Sign-in method:** enable **Anonymous** and **Google**.
- **Authentication → Settings → Authorized domains:** add your deploy domain
  (e.g. `your-app.vercel.app` / `your-app.netlify.app`).
- **Firestore Database:** create it, then publish the rules in [`firestore.rules`](./firestore.rules).
- **Storage:** enable it, then publish the rules in [`storage.rules`](./storage.rules).

> Using your own Firebase project? Replace the `firebaseConfig` block near the top of
> `index.html` with your project's web config.

Deploy the rules with the Firebase CLI:

```bash
npm i -g firebase-tools
firebase login
firebase deploy --only firestore:rules,storage
```

### 2. Hosting (Vercel or Netlify)

Both just serve the static `index.html` (configs included):

- **Vercel:** `vercel.json` is set up — import the repo or run `vercel`.
- **Netlify:** `netlify.toml` is set up — connect the repo or drag-and-drop the folder.

After deploying, the QR codes automatically point to your live URL.

---

## Security model

- **Before:** hardcoded plaintext passwords in the page + an open Firestore document
  meant anyone could read/modify all data. ❌
- **Now:** every device must be authenticated (anonymous or Google) to touch any data,
  and trips are private behind an unguessable code (~34 billion combinations). ✅
- **Images** (receipts/docs) go to **Cloud Storage**, not the database, so trip
  documents stay tiny and never hit Firestore's 1MB limit.
- **Concurrent edits are safe:** writes run in a Firestore transaction that merges your
  change onto the latest server state, so two people editing at once no longer overwrite
  each other (the old "last write wins" data-loss bug is fixed).

---

## What's in this version

- ✅ Public, no-password onboarding (anonymous auth) + optional Google sign-in
- ✅ Create / Join trips by **QR code** and short code
- ✅ Multiple trips per device with a quick switcher
- ✅ Live group sync with concurrency-safe (non-clobbering) writes
- ✅ Editable trip name / dates / duration (no longer a hardcoded 2026 trip)
- ✅ Receipt & document images stored in Cloud Storage
- ✅ Firestore + Storage security rules
- ✅ Network-first service worker (installable PWA, offline-capable)
- ✅ All the original features: itinerary, bookings, packing, expenses + settlement
  solver, tasks, documents, polls, decisions, notes, links, check-ins, chat, analytics

### Ideas for next iterations
- Move list collections (expenses/tasks) to Firestore sub-collections for very large trips
- Push notifications via the service worker
- ICS calendar export, map view of bookings, multi-currency per expense
- Automated tests + CI
