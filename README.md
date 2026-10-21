# Doctor App — GitHub Pages Edition

A static, mobile-first single-page app for finding doctors, hospitals, ambulances, blood donors, and health articles. This is the static conversion of the original Next.js project — split into clean `index.html` + `css/styles.css` + `js/app.js`, ready to host on GitHub Pages.

> **Repo**: https://github.com/sharifkhan40-creator/doctorapp/

---

## Repo layout

```
doctorapp/
├── index.html              # HTML shell — head + body markup only
├── css/
│   └── styles.css          # All UI styles (61 KB, mobile-first)
├── js/
│   └── app.js              # SPA logic — hash router, all views (196 KB)
├── api/
│   ├── README.md           # Documents every backend endpoint
│   └── stubs/*.json        # Example responses for each endpoint
├── ambulances/             # Sample ambulance photos
│   ├── amb1.png  amb2.png  amb3.png  amb4.png
├── icon-192.png            # PWA icon (192×192)
├── icon-512.png            # PWA icon (512×512)
├── logo.svg                # Brand logo
├── manifest.json           # PWA manifest
├── sw.js                   # Service worker (caches static assets, bypasses /api/)
├── robots.txt
└── .nojekyll               # Tells GitHub Pages to skip Jekyll processing
```

---

## Deploy to GitHub Pages

### Option A — Repo named `sharifkhan40-creator.github.io` (root hosting)

The site is built with **absolute paths** (`/css/styles.css`, `/js/app.js`, `/icon-192.png`, `/api/...`). Absolute paths resolve to the domain root, so the cleanest deploy is:

1. Push these files to the `main` branch of a repo named **`sharifkhan40-creator.github.io`**.
2. Open the repo → **Settings → Pages**.
3. Under **Build and deployment → Source**, choose **Deploy from a branch**.
4. Pick `main` / `(root)` and click **Save**.
5. Wait 1–2 minutes. Visit `https://sharifkhan40-creator.github.io/`.

### Option B — Project repo (`doctorapp`) hosted at `username.github.io/doctorapp/`

If you keep the repo named `doctorapp`, GitHub Pages will serve it at `https://sharifkhan40-creator.github.io/doctorapp/`. Because the site uses root-absolute paths (`/css/...`), **the assets will 404 in this subpath mode**. Fix it with one of:

- **Recommended:** Set a custom domain (e.g. `doctorapp.example.com`) under **Settings → Pages → Custom domain**. Root-absolute paths then work as-is.
- Or rename the repo to `sharifkhan40-creator.github.io` (see Option A).
- Or do a project-wide find-and-replace in `index.html`, `js/app.js`, `manifest.json`, `sw.js`, and `api/README.md`: change `"/` → `"/doctorapp/`. (Messy, but works.)

### Option C — Local preview

Any static server works. For example:

```bash
# Python (built-in)
python3 -m http.server 8080

# Or with Node
npx serve .
```

Then open <http://localhost:8080/>.

> **Note:** Use a real HTTP server — opening `index.html` directly via `file://` won't work because the service worker and fetch calls require an HTTP origin.

---

## What works on GitHub Pages

| Feature | Status on static hosting |
| --- | --- |
| Browse doctors / hospitals / ambulances / articles UI | ✅ Renders |
| Hash routing (`#/doctors`, `#/hospitals`, `#/articles`, …) | ✅ Works |
| AI chat panel, toasts, drawer, modals | ✅ Works (UI only) |
| PWA install + offline cache | ✅ Works (service worker caches static assets) |
| Data loaded from `/api/*` | ❌ Needs backend — see `api/README.md` |
| Login / register / password reset | ❌ Needs backend |
| Admin panel writes (add/edit/delete) | ❌ Needs backend |
| Medicine reminders, ratings, blood donor signup | ❌ Needs backend |

### Wiring a backend

The `api()` helper in `js/app.js` (around line 1280) is the single chokepoint for all backend calls. To point the static site at a deployed backend (e.g. the original Next.js app running on Vercel), edit it like so:

```js
// Before
const res = await fetch(path, { ... });

// After
const API_BASE = "https://your-backend.vercel.app";
const res = await fetch(API_BASE + path, { ... });
```

See `api/README.md` for the full endpoint reference and the `api/stubs/` folder for example response shapes.

---

## Source project

Converted from the original Next.js 16 + Prisma project (`Docapp.tar`). The original code lives in the `src/` folder of the source archive — this static version uses the same `index.html` body that the Next.js app served inside an iframe (`src/app/page.tsx`), split into three files.

Tech: vanilla JavaScript (no framework), CSS custom properties, hash-based router, `localStorage` for client-side caching.
