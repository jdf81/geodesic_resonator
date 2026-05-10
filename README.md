# Geodesic Resonator

A sound-reactive geodesic geometry instrument, built as a PWA installable on iPhone.

## Files

- `index.html` — the app (Three.js visualizer + audio engine + UI)
- `manifest.webmanifest` — PWA manifest
- `sw.js` — service worker (offline support)
- `icon-192.png`, `icon-512.png`, `apple-touch-icon.png` — icons

## What the user can customize

- **Shape** — Icosahedron · Octahedron · Tetrahedron · Dodecahedron · Sphere
- **Complexity** — subdivision detail (0–5 triangles per face level)
- **Scale** — overall size of the shape
- **Hue** — full 0–360° color wheel (drives material, emissive glow, rim light, wireframe)
- **Sensitivity** — how strongly the shape reacts to sound
- **Spin** — auto-rotation speed
- **Render mode** — solid fill, wireframe, or both
- Settings persist via `localStorage`. Drag on the canvas to manually rotate.

## Audio behavior

The microphone feeds an `AnalyserNode` (FFT 512). Vertices displace along their
normals using frequency bins picked by each vertex's latitude + a spatial seed,
so different parts of the shape react to different parts of the spectrum (bass
near the poles, treble in the middle bands, varied by location). Overall punch
also drives a small uniform scale pulse.

## How to deploy & install on iPhone

iOS Safari requires PWAs to be served over **HTTPS** for mic access and the
"Add to Home Screen" install path. Three options:

### Option 1 — Drop on any static host (easiest)
Upload all six files to **GitHub Pages, Netlify, Cloudflare Pages, Vercel,
Surge, or Render**. Open the URL in iPhone Safari, tap the share button, then
**"Add to Home Screen"**. Launch from the home screen for full-screen mode.

### Option 2 — Local LAN testing with HTTPS
```bash
# In the directory:
npx http-server -S -C cert.pem -K key.pem -p 8443
# (generate a self-signed cert first via mkcert; trust it on your iPhone)
```

### Option 3 — Quick try over plain HTTP on your laptop
Mic access works over `http://localhost` and over `http://<ip>` if you add the
host to Safari's "Insecure content allowed" experimental flag (not
recommended). HTTPS is the proper path.

## iPhone-specific notes baked in

- `apple-mobile-web-app-capable` + `status-bar-style=black-translucent` for
  full-bleed standalone mode
- `viewport-fit=cover` plus `env(safe-area-inset-*)` so the HUD clears the
  notch and home indicator
- `100dvh` for the dynamic viewport (URL bar that hides/shows)
- `touch-action: none` and `overscroll-behavior: none` to disable rubber-band
  scrolling and pinch-zoom
- Mic permission requested **on the user's tap** (iOS gates `getUserMedia`
  behind a user gesture)
- `AudioContext.resume()` after construction (iOS suspends until unlocked)
- `webkitAudioContext` fallback for older iOS

## Browser support

Modern Safari (iOS 14+), Chrome, Firefox, Edge. Uses ES module imports +
import maps (iOS 16.4+ for the importmap feature; on older iOS the CDN
fallback path will fail — upgrade or inline-bundle Three.js if you need to
support iOS 15.x).
