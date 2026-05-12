# Golf Hide — How to put it online and use it on the course

You have everything you need in this `golf-app/` folder. Below are three hosting options ranked by simplicity. All three are free, ad-free, and work great on iPhone Safari/Chrome.

## Files in this folder

| File | What it is |
|---|---|
| `index.html` | The whole app (HTML + CSS + JS in one file) |
| `manifest.webmanifest` | Tells iOS this is an installable app |
| `sw.js` | Service worker — caches the app so it works offline on the course |
| `icon-192.png`, `icon-512.png`, `apple-touch-icon.png` | Home-screen icons |
| `DEPLOY.md` | This file |

---

## Option 1 — Netlify Drop (recommended, ~60 seconds)

1. Go to https://app.netlify.com/drop
2. Drag the entire `golf-app` folder onto the page.
3. Netlify gives you a URL like `https://shimmering-golf-1234.netlify.app` — that's your app.
4. Optional: sign up (free) to keep the URL forever and rename it (e.g. `golf-hide.netlify.app`).

Why Netlify Drop: no account needed for a quick test, no command line, no ads, free TLS, automatic CDN. If you want a custom domain later (e.g. `score.yourdomain.com`), Netlify lets you point one to it for free.

## Option 2 — GitHub Pages

1. Create a new public repo on github.com (e.g. `golf-hide`).
2. Upload all files from this folder into the repo.
3. In the repo's **Settings → Pages**, set **Source = Deploy from a branch**, **Branch = main**, **Folder = / (root)**.
4. Wait ~1 minute. Your URL will be `https://YOUR-USERNAME.github.io/golf-hide/`.

Why GitHub Pages: zero cost forever, version-controlled, easy to update by editing files in GitHub.

## Option 3 — Cloudflare Pages

Similar to Netlify, also free. Connect a GitHub repo and Cloudflare auto-deploys.

---

## On your iPhone — install it like an app

Once you have the URL:

1. Open it in **Safari** (PWA install only works in Safari on iOS, not Chrome).
2. Tap the **Share** button at the bottom.
3. Scroll down → tap **Add to Home Screen**.
4. Name it "Golf Hide" → **Add**.

Now you have a Golf Hide icon on your home screen. Tap it and it opens full-screen with no Safari chrome — feels like a native app.

The service worker caches the app, so it works **offline once loaded** (perfect for poor reception in the middle of the fairway). All scores are saved to local storage on the phone.

---

## How to use the app on the course

**Setup (clubhouse before the round):**
- Open the **Settings** tab.
- Pick **Course** (Masters or Classic) — pars are fixed by the course you pick.
- Add players (up to 4). For each player, set the per-player **round-end bonus**: enable it, set the stroke threshold (e.g. 80) and points awarded if they break it.
- Set the **stake** ($/pt).
- Toggle the **rules** you want and edit any point value inline. Yes/No stats (GIR, U&D, FW, Short 1st putt, 3-putt) score once per hole. Counter stats (Sand, Penalty, OB) score per occurrence. Score-vs-par categories: Birdie, Eagle, Double bogey (+2), Triple bogey (+3), Double par (≥ 2× par).

**On the course:**
- The **Play** tab is a one-hole-at-a-time wizard.
- Big score input + a -/+ stepper. Counters for each enabled rule.
- The sticky leaderboard at the top updates after every entry.
- Tap **Save & Next hole ›** to advance, or use the hole-jump buttons (1–18) to revisit a hole.

**After the round:**
- The **Leaderboard** tab shows full standings, a per-player settlement (each-vs-each), and the round summary.
- Hit **Reset round** to start a new round with the same setup, or **New round** for a full reset.

---

## Updating the app later

If you want to tweak rules or add a new course, edit `index.html` (look for the `COURSES = {…}` and `DEFAULT_PTS = {…}` sections near the top of the `<script>` block). Re-deploy by:
- Netlify Drop: drag the folder again (or use the dashboard's drag-and-drop).
- GitHub Pages: commit the change.

If you change the app and want iPhones to fetch the new version (instead of the cached old one), bump the `CACHE` version in `sw.js` (e.g. `golf-hide-v1` → `golf-hide-v2`).

---

## Notes about course pars

- **Masters** pars are verified from your existing `scorecard.html` (par 72: front 4-5-4-4-3-4-5-3-4 / back 4-5-3-4-4-5-4-3-4).
- **Classic** is par 72 with confirmed par-3s on holes 3, 7, and 17 (island green) and confirmed par-5s on holes 13 & 14. The remaining par distribution is a best-fit default — adjust on the **Settings** tab → "Edit pars per hole" if needed.
