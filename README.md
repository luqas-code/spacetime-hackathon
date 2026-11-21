[README.md](https://github.com/user-attachments/files/35212336/README.md)
# spacetime — *Your space. Your time.*

Book a private apartment in Paris for a few hours, by what you need to do: rest between trains, work, meet, cook or create.
SpaceTime is a three-sided marketplace prototype: **Space Owners** list and authorise spaces, **Space Managers** (existing
property-management companies) decide which daytime hours to open around their overnight bookings, and **Guests** book by
purpose. All three interfaces share one live backend, so an action on one phone appears on the others without a refresh.

Two AI features are at the core of the demo:
- **Smart Availability** (manager): checks cleaning and turnover time around overnight stays, estimates the owner's net
  revenue and the risk of losing an overnight booking, and recommends which daytime slots to open.
- **SpaceTime Match** (guest): turns a plain-language request into requirements and ranks the spaces that fit, with the reasons.

---

## What's in this repository

| File | What it is |
|---|---|
| `spacetime-latest-source-only.zip` | The full source code of the app |
| `spacetime-data-fix.zip` | 3 files missing from the first zip (`src/data/`) plus a corrected `.gitignore` |
| `README.md` | This file |

Both zips are needed. The setup below unpacks the source first, then adds the fix on top.

---

## Run it in GitHub Codespaces (nothing to install)

### 1. Create the Codespace
On this repository's page: **Code → Codespaces → Create codespace on main**. Wait until a terminal appears at the bottom.

### 2. Paste this into the terminal and press Enter
It unpacks both zips, installs everything and starts the app.

```bash
rm -rf /tmp/st && unzip -oq spacetime-latest-source-only.zip -d /tmp/st && SRC=$(dirname "$(find /tmp/st -name package.json -not -path '*/node_modules/*' | head -1)") && cp -r "$SRC"/. . && rm -rf /tmp/st && unzip -oq spacetime-data-fix.zip && npm install --include=dev && npm run demo
```

The first run takes 2–3 minutes. The app is ready when the terminal shows:

```
SpaceTime server on http://localhost:8787
```

### 3. Open the app
1. Click the **Ports** tab next to the terminal.
2. Right-click port **8787** → **Port visibility** → **Public** (needed for phones and for anyone else).
3. Click the globe icon next to port 8787, or copy its `https://…-8787.app.github.dev` address and open it on each phone.

### Next time you open the same Codespace
The files are already unpacked, so just start the app:

```bash
npm start
```

---

## Step by step (same result as the one-line command)

```bash
# 1. Unpack the source code into this folder
rm -rf /tmp/st && unzip -oq spacetime-latest-source-only.zip -d /tmp/st && SRC=$(dirname "$(find /tmp/st -name package.json -not -path '*/node_modules/*' | head -1)") && cp -r "$SRC"/. . && rm -rf /tmp/st
```

```bash
# 2. Add the fix (src/data/ and the corrected .gitignore)
unzip -oq spacetime-data-fix.zip
```

```bash
# 3. Check that the key files are there (all four should be listed)
ls src/data/vocab.ts src/store/useStore.ts server/index.ts shared/optimiser.ts
```

```bash
# 4. Check Node.js — it must be 22.13 or newer
node -v
```

If it shows an older version:

```bash
nvm install 22 && nvm use 22
```

```bash
# 5. Install, including the build tools
npm install --include=dev
```

```bash
# 6. Build and start
npm run demo
```

---

## Demo accounts

The password for every account is **`spacetime`**. The sign-in screen also has one-tap buttons for each account.

| Role | Email | What they do |
|---|---|---|
| **S · Space Owner** | `sophie@spacetime.demo` | Lists an apartment, sets permitted uses, assigns a manager, sees earnings |
| **M · Space Manager** | `malik@spacetime.demo` | Runs Smart Availability, approves daytime slots, coordinates cleaning |
| **I · Guest** | `isaac@spacetime.demo` | Describes what he needs, gets matched, books by the hour |

Open the app in three phones or three separate browser windows (private windows or different browser profiles) and sign in
with a different account in each.

---

## Try the three-phone demo

1. **Owner (Sophie):** *List a space* → *Sample details* → add photos → *Use my verified compliance file* → assign **Malik** →
   *Submit for review* → *Publish now*.
2. **Manager (Malik):** the new property appears on the dashboard → open it → *Smart Availability* → choose **tomorrow** →
   review the recommended 08:00–16:00 window → *Approve*.
3. **Guest (Isaac):** open the AI search from Discover or Search and paste:
   > I'm arriving at Gare de Lyon at 9 am with two kids, and my accommodation check-in isn't until 4 pm. We need somewhere to shower and rest.

   Answer **Tomorrow** → open the new *Family apartment · Gare de Lyon* → book 09:00–16:00.
4. **Manager:** the booking appears live → assign cleaning → message the guest.
5. **Owner:** the booking and estimated owner proceeds appear on the dashboard.

---

## Reset the demo data

Everyone using the same link shares one database. To start fresh, stop the app with **Ctrl+C**, then run:

```bash
npm run db:reset && npm start
```

---

## Troubleshooting

| What you see | Fix |
|---|---|
| `unzip: cannot find or open …` | Run `ls` and check the zip names match exactly. |
| `Cannot find module '@/data/vocab'` (or `@/data/places`, `@/data/properties`) | The fix zip wasn't applied: run `unzip -oq spacetime-data-fix.zip`, then `npm run demo`. |
| `sh: 1: tsc: not found` | Run `npm install --include=dev`, then `npm run demo`. |
| `SpaceTime needs Node.js 22.13 or newer` | Run `nvm install 22 && nvm use 22`, then `npm install --include=dev && npm run demo`. |
| Blank page or “taking too long to load” | Wait for `SpaceTime server on http://localhost:8787` in the terminal, then reload. Make sure the link is for port **8787**. |
| Link shows an error after a while | The Codespace stopped (it sleeps when idle). Reopen it and run `npm start`. |
| Others can't open the link | Set port 8787 to **Public** in the Ports tab. |

You can check the server directly by adding `/api/health` to the end of the link. It should show `{"ok":true, …}`.

---

## Run it on your own computer

Requires [Node.js](https://nodejs.org) 22.13 or newer. Unzip `spacetime-latest-source-only.zip`, unzip `spacetime-data-fix.zip`
into the same folder, then:

```bash
npm install --include=dev
```

```bash
npm run demo
```

Open http://localhost:8787. Phones on the same Wi-Fi can use `http://<your-computer's-IP>:8787`.

---

## Optional keys

The app runs fully without any keys. Copy `.env.example` to `.env` to switch on extras:

| Variable | Effect |
|---|---|
| `ANTHROPIC_API_KEY` | Claude parses guest requests (including French) and writes Smart Availability explanations. Without it, a built-in rule-based parser is used. |
| `RESEND_API_KEY`, `EMAIL_FROM`, `EMAIL_TEST_RECIPIENT` | Sends booking emails. Without them, emails appear in the app as previews. |
| `GEOAPIFY_API_KEY` | Street-address autocomplete. Without it, OpenStreetMap and a built-in list of Paris places are used. |

---

## Built with

React, TypeScript, Vite and Tailwind CSS for the app; a Node.js server with the built-in SQLite database; Leaflet maps;
live updates over Server-Sent Events. Interface in English and French.

All spaces, people and reviews are fictional. No payments are taken, door codes are not connected to real locks, and
commission rates are proposed assumptions.
