# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Commands

```bash
npm install       # Install dependencies
npm start         # Start Express server on http://localhost:3000
```

No test suite exists (`npm test` exits with code 1).

## Architecture

**Galería Demo** is a Node.js/Express e-commerce gallery for fashion boutiques. It has three layers:

### Backend (`server.js`)
Single Express file serving both static frontends and REST API. Key behaviors:
- CORS whitelist in `ALLOWED_ORIGINS` — add production domains here when deploying
- Admin authentication via `x-admin-key` header (value set in `.env` as `ADMIN_KEY`)
- Firebase Admin SDK initialized from `serviceAccountKey.json` locally or `FIREBASE_SERVICE_ACCOUNT` env var in production
- Product images uploaded to Firebase Cloud Storage; signed URLs stored in Firestore

**API routes:**
- `GET/POST/DELETE /api/productos` — product CRUD with image upload (Multer multipart)
- `GET/POST/DELETE /api/categorias` — category management (`?cascade=true` deletes associated products)
- `GET /healthz` / `GET /healthz/deep` — health checks

### Storefront (`tienda/`)
Vanilla JS single-page gallery. Key files:
- `content.js` — exports `siteContent` object with store name, slogan, WhatsApp number, Instagram handle. **This is the first file to edit when adapting for a new client.**
- `scripts.js` — Firebase client init, real-time Firestore `onSnapshot()` listeners per category, carousel navigation, image modal with swipe/keyboard, WhatsApp link generation
- `index.html` — static shell; categories are dynamically injected at runtime from Firestore

### Admin Panel (`admin/`)
Vanilla JS ES module. Login page stores the admin key in `localStorage` as `vg_admin_key` and `sessionStorage.autorizado`. Key behaviors in `admin.js`:
- Detects server URL from `window.location` (localhost vs production)
- Batch image upload: multiple files selected, each gets its own name/price/category fields before submitting
- Firestore `onSnapshot()` keeps product list in sync

## Firestore Data Model

- **`productos`**: `{ id, nombre, precio (string), categoria (lowercase string), imagen (signed URL) }`
- **`categorias`**: `{ id, nombre (lowercase string) }`

## Environment Variables

```env
ADMIN_KEY=...                         # Password for admin panel
NODE_ENV=development|production
FIREBASE_STORAGE_BUCKET=gs://...      # Cloud Storage bucket
FIREBASE_SERVICE_ACCOUNT=...          # JSON string (production only)
```

## Adapting for a New Client

1. `tienda/content.js` — store name, slogan, WhatsApp, Instagram
2. `tienda/scripts.js` and `admin/admin.js` — `firebaseConfig` object (same values in both)
3. `server.js` — `ALLOWED_ORIGINS` and `ALLOWED_HOSTS` arrays
4. `.env` — `ADMIN_KEY`, `FIREBASE_STORAGE_BUCKET`, `NODE_ENV`
5. Provide `serviceAccountKey.json` locally or set `FIREBASE_SERVICE_ACCOUNT` in production