# Orion — Deployment (iOS-first PWA)

This folder is the complete, installable Orion. Drop it on any HTTPS static host
and it becomes a home-screen app that loads offline.

## Files
- `index.html` — the app (the single-file Orion, now PWA-aware)
- `sw.js` — service worker (offline app-shell cache)
- `manifest.webmanifest` — install metadata
- `icon-192.png`, `icon-512.png`, `icon-512-maskable.png`, `apple-touch-icon.png`
- `DEPLOY.md` — this file

Keep them in the **same directory**. The entry point must be `index.html`.

## Host it (HTTPS is required — a service worker won't run over http:// or file://)
**GitHub Pages:** push this folder to a repo, Settings → Pages → deploy from the
branch/root. Your app is at `https://<user>.github.io/<repo>/`.

**Cloudflare Pages:** New project → upload this folder (or connect the repo).
Build command: none. Output directory: `/`.

Either way, open the resulting `https://…/` URL on your phone.

> The app still opens fine straight from a file (double-clicking `index.html`)
> for quick local use — it just won't act as an installed PWA or cache offline
> until it's served over HTTPS.

## Install
- **iPhone (Safari):** open the URL → tap **Share** → **Add to Home Screen**.
  (iOS has no automatic install prompt; the drawer's **Install Orion** button
  shows these same steps. A one-time hint banner also appears.)
- **Android / desktop Chrome:** an install prompt appears automatically — use it,
  the address-bar install icon, or the drawer's **Install Orion** button.

Once installed it launches full-screen with the constellation icon, its own
status bar, and offline loading.

## On phones
The layout collapses to a chat-first view: bounded avatar on top, conversation
filling the screen, input pinned above the keyboard. Everything secondary —
stats, status rows, missions, the action cluster (PING / TEACH / REFLECT / SLEEP /
DISCONNECT / etc.), and the legend — lives behind the **⚙ console drawer** in the
header. The drawer also holds **Install Orion** and a **Reduce effects** toggle
(drops the heavy aura blurs — flip it on if the handheld GPU stutters; it's
automatically on if your OS is set to Reduce Motion). The desktop dashboard is
unchanged.

## What stays online (never cached)
The service worker only caches the app shell. The Anthropic brain
(`api.anthropic.com`) and your Moon Core worker (`*.workers.dev`) are **always
fetched live** and never cached — so chat and cloud sync behave normally online,
and simply fail-soft offline (the app still opens and shows last state). State
changes made while offline (e.g. form switches) are queued and synced on
reconnect. Conversation is never stored unencrypted.

## Security / continuity notes
- The Anthropic API key is held in the browser's `localStorage` and used for a
  direct browser→Anthropic call. Anyone with access to the device/profile can
  read it. Host it somewhere only you use, and rotate the key if needed.
- An installed iOS PWA has a **separate storage sandbox** from Safari, so the key
  and local session don't carry over from the browser tab. Your Moon Core
  (cloud) is the source of truth — re-run the Initiation Protocol in the
  installed app once and it re-awakens with full memory.

## Worth checking on a real iPhone (headless tests can't reproduce Safari exactly)
- Input stays above the keyboard while typing (the visualViewport handler).
- Safe-area padding clears the Dynamic Island / home indicator.
- `dvh` sizing in installed standalone mode.
- FX smoothness — if it stutters, the **Reduce effects** toggle is the fix.
