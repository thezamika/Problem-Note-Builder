# Problem Note Builder — Care Plan Tool (v1.47.5)

**Live link after GitHub Pages:** `https://thezamika.github.io/care-plan/`

Single-file tool — no build, no npm. Works offline. Beige/green theme, 16 themes, 5 layouts.

### Use
1. Open your GitHub Pages link
2. Paste **Case information** → ICD10 auto-fills Condition
3. Work top to bottom (1→11), paste **Meds** (one per box, Frequency `Written ⇄ / Off / Latin`), fill Lifestyle / Labs-Vitals / Gaps / Risks / Visits / Symptoms / Events / Escalations / Education → **Copy all**

### What’s inside v1.47.5
- 6 `?` help popups (Labs, Gaps, Risks, Symptoms, Escalations, Education) — `22px` desktop / `28px` mobile, dynamic `Tap for <Condition> —` tooltips, grouped gaps (Hypertension 6 = Medication 1 + BP 3 + Diet 1 + Kidney 1), escalations split `ROUTINE (amber)` / `URGENT (red)` + per-item Copy
- All guides transcribed from PDFs: `COND_GUIDE`, `ESCALATION_GUIDE` (routine/urgent), `NTT_*`, `CASE_GAPS`, `CASE_EDU`
- Keeps: 11-card order, `made by A.Reda / Supervised by M.Ismail`, beige/green, doseScale `both`

### Deploy (you are here as `thezamika`)
You already have `index.html` — just push this folder to GitHub as repo `care-plan` and enable Pages (Settings → Pages → Branch `main` / root).

### Changelog
See [CHANGELOG.md](./CHANGELOG.md) — newest-first, bottom-dock version `v1.47.5`.

### File
- `index.html` — the whole app (608 KB)
- `README.md` — this file
- `CHANGELOG.md` — history
