Golf Card PWA

Files:
- index.html: main app
- manifest.json: PWA manifest
- sw.js: offline service worker
- icon-192.png / icon-512.png: app icons

IMPORTANT:
Place a local copy of html2canvas.min.js in this folder before deploying.
The app references ./html2canvas.min.js so image export also works offline.

Deploy the whole folder to HTTPS hosting (GitHub Pages, Netlify, Vercel, etc.).
On iPhone Safari: open the URL -> Share -> Add to Home Screen.
Then use Share / Save and choose Save Image to save the generated PNG to Photos.
