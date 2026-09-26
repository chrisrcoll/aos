# Warscroll Companion — Home Screen App Setup

This folder is a complete, installable Progressive Web App (PWA). Once set
up, it launches from your iPhone's Home Screen with its own icon, no Safari
address bar, and works offline. Data (your lists, imported art) is saved
directly on your device and survives closing the app.

## Why you can't just double-tap index.html

iOS requires a PWA to be served over `https://` (or `http://localhost`) for
"Add to Home Screen" to install it properly with offline support — opening
the file directly (`file://...`) won't register the service worker or let
you add a working icon. You need to serve these 5 files from *somewhere*.
Easiest options, roughly in order of effort:

### Option A — GitHub Pages (free, permanent, recommended)

1. Create a free GitHub account if you don't have one.
2. Create a new repository, e.g. `warscroll-companion`.
3. Upload all 5 files in this folder (`index.html`, `manifest.json`, `sw.js`,
   `icon-192.png`, `icon-512.png`) to the repo root.
4. In the repo's Settings → Pages, set the source to your main branch.
5. GitHub gives you a URL like `https://yourname.github.io/warscroll-companion/`.
6. Open that URL in Safari on your iPhone → tap Share → **Add to Home Screen**.

This is free, requires no server maintenance, and the URL stays stable.

### Option B — Any static host you already use

Netlify, Vercel, Cloudflare Pages, or even a personal site's file host all
work identically — just upload these 5 files to a folder and visit the URL
in Safari. Drag-and-drop deploy (Netlify Drop, for instance) takes under a
minute with no account needed for a one-off.

### Option C — Run a local server on your own Mac/PC (same Wi-Fi only)

Good for testing before you commit to a public host.

```bash
cd WarscrollCompanionPWA
python3 -m http.server 8000
```

Then on your iPhone (same Wi-Fi network), visit `http://YOUR_COMPUTER_IP:8000`
in Safari. Find your computer's IP via System Settings → Wi-Fi → Details (Mac)
or `ipconfig` (Windows). This only works while your computer is on and the
server is running — not suitable as your everyday link, but fine to confirm
everything installs correctly first.

## Installing to your Home Screen

Once you can load the page in **Safari** (must be Safari, not Chrome — iOS
only allows installable PWAs from Safari):

1. Tap the **Share** button (square with an arrow, bottom of screen)
2. Scroll down and tap **Add to Home Screen**
3. Confirm the name ("Warscroll") and tap **Add**

You'll get a real icon on your Home Screen. Opening it launches full-screen
with no browser UI, exactly like a native app.

## What goes on GitHub vs. what stays on your device

**GitHub Pages hosts the app only** — the 5 files in this folder
(`index.html`, `manifest.json`, `sw.js`, two icon PNGs). That's it. Nothing
else needs to go there, and nothing else should.

**Do not upload PDFs, battletomes, or warscroll images to GitHub** — even in
a private repo. The whole point of the personal-use approach we've built is
that official art and rules text never leave your own devices. Uploading
them to any third-party server, GitHub included, breaks that.

Instead, your content flow is:

1. On your **computer**: run `extract_pdf.py` against a PDF you own → get a
   `.json` file.
2. Get that JSON onto your **phone** — AirDrop, iCloud Drive, or emailing it
   to yourself all work. It never needs to touch GitHub.
3. In the app, open the **Import** tab → tap the matching content type →
   pick that JSON file from your phone's Files app.
4. Review/correct what was extracted, tap **Save** — it's now stored in the
   app's local `localStorage`, on your phone only.

The app itself (the code) is public-ish on GitHub Pages (or private if you
paid for GitHub's private-Pages tier); your actual army data and any
imported art/warscroll images are not — they live only in your phone's
browser storage, exactly like the lists you build.

## What "goes into" the app from GitHub vs. locally

To be precise about the two different kinds of "content" this app handles:

- **App code** (this HTML/JS) → lives on GitHub Pages → downloaded once,
  then cached offline by the service worker.
- **Your data** (lists, imported units/spells/rules, home screen art) →
  never touches GitHub → lives in `localStorage` on whichever device you're
  using, seeded only by files you import directly from that device's
  storage.

## What "progressive memory" means here

Every list, unit nickname, and imported art swatch is saved to the browser's
`localStorage` the moment you make a change — there's no explicit "Save"
button because it's automatic. Closing the app, restarting your phone, or
losing signal doesn't lose your data; it's all local to the device.

Two things worth knowing:
- **iOS can clear PWA storage** if the app goes completely unused for a long
  time (Apple's policy, not something in your control). Use **Settings →
  Export All Lists** inside the app periodically to save a JSON backup
  somewhere durable (iCloud Drive, email to yourself, etc.), and **Import
  from Backup** to restore.
- This is separate storage from Safari's regular browsing data — clearing
  your general Safari history/cache does not affect it, but "Clear All
  Website Data" in Settings → Safari would.

## Updating the app later

If I make further changes to `index.html`, just re-upload the new version to
whichever host you chose (same filename, same location) — the service worker
will detect the change and cache the new version automatically next time you
open the app (may take one extra launch to fully refresh).
