# Nimah Liquor Store — website + backend

This folder has two parts:

```
SHOP/
  index.html     ─┐
  styles.css       │  the website (frontend) — admin / worker / customer portals
  app.js          ─┘
  server/         ─── the backend — API + SQLite database + backups
    server.js
    db.js
    package.json
```

The website used to save data in the browser only. It now talks to the
backend in `server/`, so data is saved properly in one real database,
the same for every worker, admin and customer — not stuck on one device.

## 1. Start the backend

You need [Node.js](https://nodejs.org) installed (version 18 or newer).

```
cd server
npm install
npm start
```

You should see:

```
Nimah Liquor Store API running on http://localhost:4000
Database file: server/data/nimah.db
```

Leave this running — it's your database server. The very first time it
starts, it creates `server/data/nimah.db` and fills it with the default
accounts (`admin`/`admin123`, `worker`/`worker123`) and a few starter
products.

## 2. Open the website

In a **second** terminal, from the `SHOP` folder (not `SHOP/server`):

```
python3 -m http.server 8000
```

Then open `http://localhost:8000` in your browser. (Opening `index.html`
by double-clicking it usually won't work, because the browser blocks a
plain file from loading `app.js` next to it — serving it, even just
locally like this, avoids that.)

The site will now save everything through the backend. Try it: log in
as `worker`, add a sale, then log in as `admin` in another browser tab —
the dashboard reflects it immediately, because both are reading from
the same database on your machine.

## 3. Your data — saved, seen, updated, backed up

- **Saved & updated** — every product, purchase, sale and order a
  worker or customer enters is written straight into
  `server/data/nimah.db` through the API. Nothing depends on the
  browser tab staying open.
- **Seen** — the Admin Dashboard and every Worker page read from that
  same database, so numbers are always current, not a stale copy.
- **Backed up** — two ways:
  - *Automatic*: the server writes a full snapshot to
    `server/backups/` once a day on its own, keeping the most recent 14.
  - *Manual*: log in as Admin → **Backup & Restore** tab → **Download
    backup** gives you one `.json` file with everything, to keep on a
    USB drive, email to yourself, or store in cloud storage. The same
    screen has **Restore from a backup** to load one back in — useful
    after reinstalling, moving to a new computer, or undoing a mistake.

## 4. Moving to your real domain

When you're ready to put this on your actual shop website:

1. Deploy the `server/` folder to a host that can run Node.js
   (Render, Railway, a VPS, etc.) and note its URL, e.g.
   `https://api.nimahliquor.com`.
2. In `index.html`, uncomment this line and put that URL in:
   ```html
   <script>window.NIMAH_API_BASE = "https://api.nimahliquor.com/api";</script>
   ```
3. Upload `index.html`, `styles.css` and `app.js` to your web host.

That's the only change needed — all the app logic already talks to the
backend through that one address.

## Notes

- Passwords are stored in plain text in the database. That's fine for
  trying this out, but before real customers create real accounts,
  ask about adding password hashing — it's a small change to
  `server/server.js`.
- The database is one file (`server/data/nimah.db`). As long as you
  keep that file (and your backups), your records are safe even if
  you move the app to a different computer entirely.
