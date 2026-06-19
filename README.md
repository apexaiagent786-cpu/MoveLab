# MoveLab — Mobile Joint Tracker

A phone web-app (PWA) that uses your **phone camera** to track full-body
**joints** in real time, in both **frontal and side** views, and **record** the
joint motion for later analysis.

Built for two goals:
- **Games** — drive gameplay with real body movement.
- **Physio rehab** — track how the body's joints move.

> **Current scope: joint tracking only.** Joint-angle / range-of-motion
> measurement is intentionally **deferred** until tracking is validated as
> accurate in both frontal and side views. The hand model has been removed to
> keep tracking fast and focused. (The earlier angle engine still lives in the
> desktop version under `desktop/`.)

It uses MediaPipe Tasks Vision (Pose Landmarker, full model) on the phone GPU,
with per-joint One-Euro smoothing so the skeleton stays steady.

---

## Why it must be served over HTTPS
Phone browsers only give camera access on a **secure origin** (`https://…` or
`localhost`). Opening `index.html` as a `file://` on the phone will load the UI
but the camera will be blocked. Pick one of the options below.

### Option A — GitHub Pages (easiest, free, permanent link)
1. Create a free GitHub account and a new **public** repo, e.g. `movelab`.
2. Upload `index.html`, `manifest.json` (and this README).
3. Repo → **Settings → Pages → Source: `main` / root → Save**.
4. After ~1 min you get a URL like `https://<you>.github.io/movelab/`.
5. Open that URL **on your phone**, tap **Start Camera**, allow permission.
6. Optional: browser menu → **Add to Home Screen** → it installs like a real app.

### Option B — Same-WiFi from your laptop (no internet, needs HTTPS cert)
Camera over a LAN IP still needs HTTPS. Quickest is a tunnel:
```powershell
# in this folder, serve the files:
python -m http.server 8000
# then expose with a tunnel (gives an https URL to open on the phone):
npx localtunnel --port 8000      # or: ngrok http 8000
```
Open the printed `https://…` link on the phone.

### Option C — Test on the laptop first
```powershell
python -m http.server 8000
```
Open `http://localhost:8000/mobile_app/` in Chrome/Edge on the laptop
(`localhost` is treated as secure, so the webcam works for a quick check).

---

## Using it
| Control | Action |
|---|---|
| **Start Camera** | loads the model + camera (first load downloads ~10 MB) |
| **📷 Rear / 🤳 Front** | switch cameras — use **Rear** to film a patient, **Front** for yourself |
| **🔄 Flip** | mirror the image |
| **🏷 Labels** | show/hide the joint name on each landmark |
| **⏺ Record / ⏹ Stop** | capture joint motion; stopping opens the export panel |
| **↺ Reset** | clear the current recording |
| **⬇ CSV / ⬇ JSON** | save the recorded joint motion to the phone's Downloads |

- Joint dot colour = tracking confidence: **green** > 70%, **orange** 50–70%,
  **red** < 50% (e.g. the far-side limb in a side view).
- The top bar shows **View** (FRONTAL / SIDE / ANGLED), **fps**, and a chip with
  the delegate (GPU/CPU) and how many of 33 joints are currently tracked.
- Stand **2–3 m** back so your whole body is in frame; good light helps a lot.

## What gets recorded
- **CSV**: `time_s`, then for all 33 joints the `x, y, z, visibility` values.
- **JSON**: landmark names + every per-frame sample of all 33 joint positions.

## Coming next
Joint-angle / range-of-motion measurement, once tracking is confirmed accurate
in both frontal and side views — then games and rehab modes on top.
