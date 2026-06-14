# MoveLab — Mobile Motion & Rehab Tracker

A phone web-app (PWA) that uses your **phone camera** to track full-body movement
in 3-D, measure joint angles / range of motion (ROM), **record** every movement,
and **visualize** it as angle-vs-time charts plus a ROM + symmetry report.

Built for two goals:
- **Games** — drive gameplay with real body movement.
- **Physio rehab** — measure how far each joint moves and which muscles drive it.

It's the same goniometry engine as the desktop `knee_phase2_angle.py`
(world-coordinate angles + One-Euro smoothing), re-implemented in JavaScript with
MediaPipe Tasks Vision so it runs on the phone GPU.

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
| **⏺ Record / ⏹ Stop** | capture a session; stopping opens the Report |
| **📊 Report** | ROM table, L/R symmetry, and an angle-vs-time chart per joint |
| **↺ Reset** | clear session ROM (e.g. between patients/reps) |
| **⬇ CSV / ⬇ JSON** | save the recording to the phone's Downloads |

- A **green ●** next to a joint = the reading is *steady* (safe to trust/record).
- Stand **2–3 m** back so your whole body is in frame; good light helps a lot.

## What gets recorded
- **CSV**: `time_s`, and for every joint the raw 3-D angle + clinical flexion.
- **JSON**: same per-frame samples plus a per-joint ROM summary (min/max flexion).
- **Report charts**: flexion (or angle) vs time for each tracked joint, drawn in-app.

## Tracked joints & prime movers (rehab framing)
Knee (hamstrings/quads), Hip (iliopsoas/glutes), Elbow (biceps/triceps),
Shoulder (deltoid/pec), Ankle (gastrocnemius/tib. ant.).

> MediaPipe tracks **joints**, not muscles directly. Each joint angle reflects the
> action of its prime-mover muscle group — that mapping is shown in the report.

## Accuracy note
World-landmark angles are good for tracking and screening but are **not a
certified goniometer** — expect a few degrees of error. Use as a rehab/training
aid, not a calibrated diagnostic device.
