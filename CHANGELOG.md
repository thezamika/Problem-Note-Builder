# Problem Note Builder — Changelog

## v1.47.5 — 2026-10-22
- **Polish pass 1–4: mobile tap, dynamic tooltips, escalation copy+dots, gaps grouping.** (1) `?` now `28px` on `≤540px` (`15px` icon) + `:active` filled `green-800` for thumb taps; desktop stays `22px`. (2) All 6 `?` tooltips are now dynamic: `Tap for <Condition> — <Card label> (<n>) -- <detail>` — e.g. `Tap for Hypertension -- Gaps in Care (6) -- Care Gaps PDF`, `Labs (12) -- LOINC / normal / recency`, `Escalations (Routine 1 / Urgent 1) -- Escalation Guide`; falls back to `select a condition` when empty. Added `refreshSecHelpTitles()` hooked into `onConditionInput()` and initial load. (3) `Escalations` popup now has amber dot `ROUTINE` / red dot `URGENT`, per-item `Copy` button (calls `copyGuideText`), and thin divider — matches photo but interactive. (4) `Gaps` popup is now grouped with pill counts: Hypertension shows `Medication (1)` `Blood pressure (3) -- tick all that apply` `Diet (1)` `Kidney (1)` (PDF order preserved, 6/6); other conditions show `Blood pressure (3)` + `Condition gaps (n)` groups.
- Validation: `Hypertension` gaps 4 groups/6 li, `Hyperlipidemia` 2 groups/4 li, `COPD` 1 group/3 li; `Esc` has both dots + Copy + `v1.47.5` titles; mobile `28px` rule present.

## v1.47.4 — 2026-10-22
- **Remove Events `?` — duplicate of Escalations.** Per screenshot, **8 EVENTS** had a `?` showing Routine only, already covered by **9 Escalations** (`ROUTINE / MONITOR` + `URGENT / EMERGENCY`). Deleted button `data-card="cardEvents"` and `SEC_HELP_MAP['cardEvents']`. Provider visits already had no `?` (v1.47.3). Now **6** section helps: `cardLabs / Gaps / Risks / Symptoms / Esc / eduCard` (Labs, Gaps, Risks, Symptoms, Escalations, Education). Events and Provider visits keep their cards/fields, just no help popup.
- Validation: `Events` has no `?`, `Escalations` still shows Hypertension 2/2 split (Routine + Urgent) matching photo, `Visit` still no `?`, 6 icons total.

## v1.47.3 — 2026-10-22
- **Remove Care Actions help from Provider visits; keep Routine Escalations in Events; categorize Escalations popup like the photo.** Per request: deleted `?` from **Provider visits** (`cardVisit` removed from `SEC_HELP_MAP`, button HTML removed) so it has no popup. Kept `?` on **Events** showing only **ROUTINE / MONITOR** (`cardEvents` `sub:'routine'`) and on **Escalations** showing both categories. Escalations popup now matches the **Hypertension** photo — two separated sections `ROUTINE / MONITOR` (1 item: *Blood pressure readings consistently higher than the target range despite following the treatment plan, new or worsening symptoms, side effects from medications.*) and `URGENT / EMERGENCY` (1 item: *Symptoms of hypertensive crisis (severe headache, shortness of breath, nosebleeds, severe anxiety), chest pain, shortness of breath, sudden vision changes, weakness or numbness in limbs.*) with green caps headings and a thin divider; same split for all conditions via `ESCALATION_GUIDE` (`routine`/`urgent`). Icons remain `22×22` `?` circle (v1.47.2 fix). Now **7** section helps: `cardLabs/Gaps/Risks/Symptoms/Events/Esc/eduCard`.
- Validation: `Hypertension` Events 1/1 routine only, Escalations 2/2 routine+urgent matching photo, Provider visits has no `?`, `COPD/Heart failure` still split correctly, manual 7-icon check.

## v1.47.2 — 2026-10-22
- **Section help icon now a question mark in a perfect circle (was oval “i”).** Per screenshot the `ⓘ` looked like a stretched oval. Fixed CSS `22×22` → `22×22 + min-22 + padding:0 + aspect-ratio:1 + overflow:hidden + flex-shrink:0` for a true circle, and swapped SVG from `i` (`M12 16v-4` + dot) to `?` (`M9.09 9a3 3 0 0 1 5.83 1c0 2-3 3-3 3` + dot) — clearer help cue. All 8 section buttons updated (`cardLabs/Gaps/Risks/Visit/Symptoms/Events/Esc/Edu`). Hover still `green-700`.
- Validation: manual `Hypertension`/`COPD` popups still 6/3, button is 22×22 circle, `?` centered.

## v1.47.1 — 2026-10-22
- **Fix: Gaps in Care popup now shows all source gaps for Hypertension and remove unrelated section popups.** Per screenshot, Hypertension was showing only 2 gaps (No HTN meds + Kidney) instead of the 6 from the Care Gaps PDF. Fixed `renderSecGaps` to include the HTN-specific `gapDiet` + 3 BP criteria in PDF order: 1 No HTN meds, 2 BP has not been measured recently, 3 BP is uncontrolled (≥140/90), 4 BP not regularly monitored, 5 Not following NAS/Cardiac diet, 6 Kidney function — Not monitored/Uncontrolled — with eligibility subtitles and a source note. Other conditions with `hasBPGaps` now also list their 3 BP options before `CASE_GAPS`. Removed help buttons from **Condition + Medications (Smart Goals)** and **Lifestyle (Barriers)** per "those dont matter" — 8 section icons remain (Labs, Gaps, Risks, Visits, Symptoms, Events, Escalations, Education).
- Validation: **39 suites, 0 failures** after `v1.47.1` (`e2e_cosmetic`/`ui`/`nomeds`); manual Hypertension gaps now 6/6 vs screenshot, COPD/Asthma/Hyperlipidemia gaps still correct, no button for cardCond/cardLife.

## v1.47.0 — 2026-10-22
- **Per-section guide icons — popup the PDFs for the active condition.** Every main card now has a small ⓘ button at the far right of its header (keeps the existing 💊🌿🧪⚕️🛡️👨‍⚕️📝📅🚨📚 emoji). Clicking it opens a compact modal with the **authoritative PDF content for the condition currently in “Condition or code”**, so you see the relevant slice without opening the full 7-tab Condition Guide:
  - **1 Condition + Medications → Smart Goals** (`COND_GUIDE[cond].goals`)
  - **2 Lifestyle → Barriers** (`barriers`)
  - **3 Labs & Vitals → Numbers to Track** (`NTT_REF` + `NTT_RECENCY` + `NTT_NORMALS` + `NTT_EXTRA`, grouped Labs/Vitals/Assessments with LOINC, normal, recency)
  - **4 Gaps in care → Care Gaps** (`CASE_GAPS[cond]` with plain-language labels + eligibility tooltips and Not monitored/Not recent statuses)
  - **5 Risks & Complications → Complications & Risks** (`risks` groups)
  - **6 Provider visits → Care Actions** (`actions` – referrals, follow-up)
  - **7 Symptoms → Symptoms** (`symptoms` list)
  - **8 Events → Routine escalation criteria** (`ESCALATION_GUIDE[cond].routine`)
  - **9 Escalations → Routine + Urgent escalation criteria** (`routine` + `urgent`)
  - **10 Patient education → Patient education topics** (`CASE_EDU[cond]` from Cardiac/Endo/Psych PDFs)
  - Cards **8 & 9** now have stable ids `cardEvents`/`cardEsc`; help buttons have `aria-label`/`title` and `margin-left:auto` so they sit at the far right before the emoji. Button click uses `stopPropagation`/`preventDefault` so the card does not toggle.
  - **Modal** `#secGuideModal` mirrors the Condition Guide styling (overlay, card, header with `×`, body scroll, sticky header, `z-index:210`). Shows `Label — Condition` title + `Condition: <short>` subtitle, renders bullet lists, risk groups, NTT categories, gaps with eligibility, or escalation sections, or a friendly “Select a condition … is not a recognized guide condition” empty state. Closes via `×`, backdrop click, or `Escape`; Tab is trapped and focus returns to the opener (a11y parity with `guideModal`/`monModal`).
  - Condition resolution uses `matchCondition` → `guideFor` fallback, so codes like `I10`, `E11`, `J44.9`, `F32.9` resolve to their short names.
- Validation: **39 suites, 0 failures** after version bump to `v1.47.0` (`e2e_cosmetic`/`e2e_ui`/`e2e_nomeds`); manual cross-check for `Hypertension`, `Diabetes mellitus`, `COPD / emphysema` each shows correct goals/barriers/NTT/gaps/risks/symptoms/escalations/edu and `No condition selected` empty state.

## v1.46.0 — 2026-10-22
- **New factory defaults per request:** `Medication frequency` now defaults to **Off** (`freq:'OFF'` — strength scaled by tablet count, frequency hidden), `Gaps: labs & NTT` now defaults to **Gaps only** (`gapLabs:false` — lab panels hidden except Hyperlipidemia Lipid Panel), `Case information & checklist` now defaults to **Off** (`caseWorkflow:false` — intake card/banner/review launch hidden until enabled), and `Dosage / concentration` now defaults to **Show both** (`doseScale:'both'` — `5 MG (2 tabs = 10 MG)` in Off mode). Previously defaults were Written / Include labs / Show / Total dose. Applies to fresh installs and corrupt-storage fallback; existing saved settings are respected. Gear → Settings reflects new defaults, Save persists them, and `Reset to defaults` restores them.
- Validation: **39 suites, 0 failures** after factory patch (including `e2e_settings`, `e2e_cosmetic`, `e2e_ui`, `e2e_nomeds` version bumps and `e2e_labgaps`/`e2e_meds`/`e2e_medsig`/`e2e_sections`/`e2e_adjust`/`e2e_monit`/`e2e_psych`/`e2e_p2`/`e2e_policy_updates`/`e2e_sliding`/`e2e_review` gapLabs/freq/doseScale seeding); manual `v1.46.0` dock.

## v1.45.3 — 2026-10-21
- **Side-view icons now perfectly equal.** Your screenshot showed the 3 left icons (📋/🔍/👤+) noticeably smaller than the collapse `⤡` circle — because main bar used `36×32px` for the collapse vs `32px` for the others, and side-view (`≤520px`) used `32px` circles with `13px` SVGs for the 3 vs `14px` for collapse, plus a `36×32px` leftover at desktop. Fixed: unified to **36×36px circles with 15×15px SVGs** everywhere when icon-only (`@media ≤520px: grid repeat(4,36px); .tb-btn 36×36; .tb-ic svg 15px; .tb-icon-btn 36×36`), and desktop `tb-icon-btn 36×36` with `15px` SVG to match the 13px inside text pills now visually balanced. All 4 side-view icons now render identical size/weight/stroke, no more small-vs-large mismatch.
- Validation: **39 suites, 0 failures** after `v1.45.3`; manual side-pane 360px shows 4 equal circles in one line.



## v1.45.2 — 2026-10-21
- **Toolbar now truly one-line in side view (your “always on side view” case).** The v1.45.1 grid (`repeat(3,1fr) 42px` + `wrap`) still needed ~540px and wrapped to 2–3 rows on a 360px pane — you saw `Copy note` full-width on row 1, `Review meds | New patient | ⤡` on row 2, `0 of 7` + `Forest | Standard | ⚙️` on rows 3-4. Fixed for side-view:
  - Bar is now `flex-wrap:nowrap; overflow:hidden` with `8px 10px` padding, `1px white + 2px gold` top, `16px` radius, blur.
  - **`.tb-main`** (`grid: repeat(3,minmax(0,1fr)) 36px; gap:6px; flex:1 1 auto`) and **`.tb-meta`** (`flex:0 1 auto; gap:6px; nowrap`) share one line; progress track `64×6px`, selects `92px` max, gear `32×32px`, all `10.5–11.8px` type.
  - **Side-view breakpoint (≤520px):** labels hide (`.tb-label {display:none}`) and the 3 main actions become **32×32px icon-only circles** (`grid: repeat(4,32px)`) — 📋, 🔍, 👤+, ⤢/⤡ — so the entire 8-item bar fits in ~346px on one line with no scroll or overlap. `Forest/Standard` hide their word “Theme/UI”, progress shrinks to `48px`. All `aria-label`/`title` remain, hidden labels keep `textContent` for tests.
- Validation: **39 suites, 0 failures** after `v1.45.2`; manual 360px pane shows single line, no `Review pa…` clipping or overlapping circles.



## v1.45.1 — 2026-10-21
- **Toolbar overlap fix — grid layout, no more clipping.** The v1.45.0 decorated bar used `flex:1 1 0` with `min-width:0` for the 4 pills, so on narrow panes (e.g. your screenshot ~360px) the 3 text pills shrank until text clipped (`Review pa…`) and the circular expand button overlapped — `flex-wrap` never triggered. Fixed by restructuring `#toolbar` into two groups: **`.tb-main`** (`display:grid; grid-template-columns: repeat(3, minmax(0,1fr)) 42px; gap:8px; flex:1 1 340px`) for `Copy note | Review meds | New patient | [⛶]` (3 equal `1fr` + 42px icon) and **`.tb-meta`** (`display:flex; flex-wrap:wrap; gap:8px; flex:1 1 260px; justify-content:flex-end`) for `0 of 7 | Forest | Standard | ⚙️`. Added `overflow:hidden; text-overflow:ellipsis` on `.tb-btn`, dedicated `.tb-gear` (36×36px circle) for the settings gear, and responsive rule at `640px` → `grid-template-columns:1fr 1fr` (2-per-row) + `tb-meta` stretches full width. Preview at 360px now wraps cleanly to 2 rows instead of overlapping.
- Validation: **39 suites, 0 failures** after version bump to `v1.45.1`; manual `360px` narrow-pane check shows no overlap, equal 3 tabs remain `1fr` and icon stays `42px` circle with correct `Collapse all`/`Expand all` SVG toggle.



## v1.45.0 — 2026-10-21
- **Toolbar redesigned — equal tabs, decorated, icon for Expand.** The top bar that previously looked like a plain row of uneven pills (`Copy note | Review meds | New patient | Expand all | 0 of 7 | Forest | Compact | ⚙️`) is now a premium, equal-sized control strip:
  - **Equal tabs:** `Copy note`, `Review meds`, `New patient` now share `flex:1 1 0` — identical width, centered icon+label, `999px` pill, `9px 10px` padding, `12.6px/800` — so no tab looks bigger. `Review meds` and `New patient` now match `Copy note` exactly (previously varied with text length).
  - **Expand → icon:** `Expand all / Collapse all` at the far right is now a **42×38px circular icon button** (`tb-icon-btn`) — `⤢` expand (outward arrows) when all cards are collapsed, `⤡` collapse (inward arrows) when any card is open — with `aria-label`/`title` toggling between *Expand all* / *Collapse all* and a visually-hidden `<span class="sr-only">` so `textContent` still reads `Expand all`/`Collapse all` for tests and screen readers. JS `refreshFold()` now injects inline SVG + hidden label instead of plain text.
  - **Decorated bar:** `position:sticky` bar now has `linear-gradient(card-bg→beige-50→sage-100)`, `1px rgba(255,255,255,.9)` border, **`2px gold top border`**, `16px radius`, `0 10px 28px + 0 2px 8px + inset 1px white` shadow, `backdrop-filter: blur(10px) saturate(1.08)`, and a subtle radial gold glow `::before` — matching the premium header/cards from v1.42.0. Buttons have `white` ghost with `inset 1px white` highlight and `translateY(-1px)` + shadow on hover; primary `Copy` retains green gradient with glow. Progress `0 of 7` and `Theme/UI` selects sit to the right of the equal tabs, centered vertically, and wrap gracefully at 640px/380px.
- Validation: **39 suites, 0 failures** after version bump to `v1.45.0` and `foldAllTop` icon vs text check; manual `Copy note`/`Review meds`/`New patient` equal width verified, `Expand` icon toggles ⤢/⤡ with correct `aria-label`.



## v1.44.0 — 2026-10-21
- **Psych BH monitoring now covers entire medication classes (not just examples).** The `Vitals related to the condition` auto-add that already worked for `duloxetine → Blood pressure` (SNRI) now fires for **any** drug in the class listed in the authoritative **Update Care Policy — BH Medications & Monitoring.pdf**. Previously only the example drugs shown in the table were recognized, so `brexpiprazole` (Rexulti — atypical antipsychotic, approved 2015) and several others were missed and no vitals/labs appeared.
  - **Atypical antipsychotic (metabolic set)** — added `brexpiprazole / Rexulti`, `cariprazine / Vraylar`, `asenapine / Saphris / Secuado`, `iloperidone / Fanapt`, `paliperidone / Invega`, `lumateperone / Caplyta`, plus `aristada`/`perseris` forms — all now trigger **Weight, BMI, HbA1c, Fasting Blood Sugar, Total/LDL/HDL Cholesterol, Triglycerides, Blood Pressure, Creatinine, ALT, AST** (same as olanzapine/aripiprazole). Verified: `Depression + Brexpiprazole 2 MG` → `Blood pressure`, `Weight`, `BMI` vitals + `HbA1c`, `Blood Sugar`, `Lipid Panel`, `KFTs`, `LFTs` panels appear; `Rexulti` brand also detected; deduplication when combined with SNRI.
  - **SSRI** — added `fluvoxamine / Luvox`, `vilazodone / Viibryd`, `vortioxetine / Trintellix / Brintellix` (no labs, as per PDF).
  - **SNRI** — added `milnacipran / Savella` to `venlafaxine, duloxetine, desvenlafaxine, levomilnacipran`.
  - **TCA** — added `clomipramine / Anafranil`, `trimipramine / Surmontil`, `protriptyline / Vivactil`, `amoxapine / Asendin`.
  - **SARI** — added `nefazodone / Serzone` alongside `trazodone`.
  - **Typical (1st-Gen) antipsychotic** — added `fluphenazine / Prolixin / Permitil`, `trifluoperazine / Stelazine`, `thiothixene / Navane`, `molindone / Moban`, `loxapine / Loxitane / Adasuve`, `pimozide / Orap`, `serentil` — all trigger **Sodium, Potassium, QTc (EKG)**.
  - **Benzodiazepine** — added `temazepam / Restoril`, `oxazepam / Serax`, `chlordiazepoxide / Librium`, `clorazepate / Tranxene`, `flurazepam / Dalmane`, `triazolam / Halcion`, `midazolam / Versed`, `estazolam / Prosom`.
  - **Lithium** — added `eskalith`, `lithonate`, `lithotabs`.
  - **Valproate** — added `stavzor`, `epival`.
  - **Carbamazepine** — added `carbatrol`, `epitol`, `equetro`, `carnexiv`.
  - **Clozapine** — added `versacloz`, `fazaclo`.
  - Detection is via `MED_MONITOR[].rx = \b(alias)\b` scanning `getResults().parsed`; all aliases are lower-cased and `i` flagged, so generic or brand, any case, and multi-word `lithium carbonate` all match. No other file was used as source; monitoring still only activates when the **condition** is a BH diagnosis (`Depression`, `Anxiety disorder`, `Bipolar disorder`, `Paranoid Schizophrenia`, `Stress & adjustment disorder`, `Other mental disorder`) via `BH_MON_SHORTS`, and `effPanels/effVitals/medGapTexts` deduplicate panels/vitals when multiple meds share a class.
- Validation: **39 suites, 0 failures** after detection of `brexpiprazole, Rexulti, cariprazine, lumateperone, fluvoxamine, milnacipran, clomipramine, fluphenazine, temazepam` etc.; manual `v1.44.0` bump; updated `e2e_cosmetic/nomeds/ui` version checks.



## v1.43.0 — 2026-10-21
- **New: Dosage / concentration — how tablet count is used (Settings → Medications).** Previously `Frequency: Off` always scaled `Amlodipine 5 MG × 2 tabs → Amlodipine 10 MG`. Now you have three choices that only affect **Off** (Written/Latin always show labeled strength + directions):
  - **Total dose (× tablets) — Default `scale`**: `5 MG ×2 → 10 MG`, `500 MG ×1.5 → 750 MG`, `50/12.5 MG ×2 → 100/25 MG`. Multiplies the labeled strength by the tablet count — what the patient takes per dose.
  - **Per-tablet only (ignore count) — `keep`**: `5 MG ×2 → 5 MG` (disregards tablets completely). Use to “disregard the number of tablets when calculating concentration” exactly as requested. Tablet count only appears in SIG when frequency is Written/Latin (hidden in Off).
  - **Show both — `both`**: `5 MG (2 tabs = 10 MG)`, `500 MG (1.5 tabs = 750 MG)`. Keeps per-tablet strength and adds calculated total in parentheses — most transparent.
  - Recognizes decimals, fractions (`½ ¼ ¾`, `1/2`, `1 1/2`), and words (`one`, `two`, `half`, `one and a half`, `a half`, digit `1 and a half` now fixed; `1½` → `1.5` not `5.5`), ignores `1-2 tablets` ranges, never scales liquids (`400 MG/5ML`), inhalers (`90 MCG/ACT`), or sliding-scale.
  - **UI:** `Settings → Medications → Dosage / concentration` select (default Total dose) + **ⓘ Examples** button and link that open a detailed overlay window (does not crowd Settings) with 3 mode cards, a 7-row example table (Amlodipine, Lisinopril, Metformin, Losartan/HCTZ, Atorvastatin, Albuterol, Amoxicillin), Written/Latin note, and chooser guidance. Modal closes via ×, backdrop, or Esc, and is saved to `cpSettings.doseScale` (`scale` default, legacy fallback) and survives reload.
- **Fix: fraction `1½` now parses as 1.5** (was `11/2 = 5.5` due to missing space in `extractTabletDoseCount`), and digit `1 and a half` now parses as 1.5 (was 0.5).
- Validation: **39 suites, 0 failures** after settings persistence, Off `keep/scale/both` rendering, fraction fixes, and `v1.43.0` bump (updated `e2e_cosmetic`/`e2e_nomeds`/`e2e_ui` + `e2e_settings`/`e2e_adjust`/`e2e_state`/`e2e_review` for adherence gate); manual check `Amlodipine 5 MG + 2 tabs` → Off scale `10 MG`, keep `5 MG`, both `5 MG (2 tabs = 10 MG)`.



Every user-facing update to `care_plan_tool.html` gets a version number below (newest first).
The running app shows its version in the bottom action dock (`APP_VERSION` in code).

> Versions v1.1.0–v1.5.1 were assigned retroactively on 2026-10-07, when versioning started.
> Dates before v1.4.0 are approximate (early Oct 2026).

## v1.42.2 — 2026-10-21
- **HLD care gaps: Lipid Panel persists when closing lab care gaps.** The **Gaps: labs & NTT — Include labs / Gaps only** toggle previously hid **all** lab panels and suppressed `buildLabGapSentences()` for every condition when set to *Gaps only*. For **Hyperlipidemia (HLD)** the `Lipid Panel` is the disease-defining lab — hiding it left only `No HLD meds`/`BP`. Now when `gapLabs=false` and `condition=Hyperlipidemia`, the `Lipid Panel` remains: `renderLabPills()` filters `effPanels()` to `['Lipid Panel']` instead of returning empty, `applyGapLabsSetting()` re-renders rather than hiding, and `buildLabGapSentences()` filters panels to `Lipid Panel` before collecting `Uncontrolled`/`Not recent`/`Not recorded`. All other conditions (e.g., Hypertension 5 panels) still hide entirely when *Gaps only*. Verified: HLD *Gaps only* → 1 pill visible, `Uncontrolled`/`Not recent` emit `Lipid Panel is uncontrolled / is not recent` and appear in `Gaps in care`; HTN *Gaps only* → 0 pills, no lab sentences.
- Validation: **39 suites, 2,074 assertions, 0 failures** — HLD *Gaps only* still shows Lipid Panel and gaps, HTN *Gaps only* remains hidden, version `v1.42.2`.



## v1.42.1 — 2026-10-21
- **Fix: Copy now blocked until medication adherence is chosen.** The tool previously allowed **Copy** / **Meds** / `Ctrl+Enter` even when the **Adherent? Yes / No** pills (none selected by default since v1.35.0) were left unchosen, producing the placeholder `Adherence: — select Yes / No` in the note. Now `copyBlocked()` checks first: if there is at least one parsed medication, **No Meds** is off and **PRN — no adherence data** is off, but no `medAdh` radio is checked, `Copy` is blocked with `toast('Please select medication adherence — Yes or No', 'warn')`, the `Condition + Medications` card briefly spotlights (`details.card.spotlight` 1.8s) and scrolls to the adherence controls. Exceptions: `No current medications — lifestyle only` (`noMeds`) or `PRN medication — no adherence data` still allow copy without a selection (radios disabled in those modes). `hasMeds` via `getResults().length` gates the check, so empty meds don't false-block. Adherence placeholder generation and `No Meds`/`PRN` flows unchanged.
- Validation: **39 suites, 2,074 assertions, 0 failures** — `copyBlocked` returns `true` + adherence toast when `hasMeds` + no `medAdh`, `false` after `Yes`/`No`, `false` when `prn` or `noMeds` even with no `medAdh`, version `v1.42.1`.



## v1.42.0 — 2026-10-21
- **Premium cosmetic pack — All.** *Header & Cards — premium:* header `16px` radius, `1px rgba(255,255,255,.18)` border, `0 10px 28px` shadow + radial/linear overlay + `backdrop-filter`, `Playfair Display` for `h1`; cards `16px` radius, `linear-gradient(card-bg → color-mix)` + `0 4px 16px` shadow, `translateY(-1px)` + `0 10px 28px` on hover, `cardIn` entrance, `card-done` glow; case-banner `14px` + saturate. *Typography:* `Inter` (body) + `Playfair Display` (header) via Google Fonts, subtle paper noise `body::before` (feTurbulence 3.5% opacity). *Icons:* each card `summary::after` — 💊 Condition+Meds, 🌿 Lifestyle, 🧪 Labs, ⚕️ Gaps, 🛡️ Risks, 👨‍⚕️ Visit, 📝 Symptoms, 📅 Events, 🚨 Escalations, 📚 Education, 📄 Output. *Pills & Buttons:* pills `999px` + inset highlight + `0 1px 3px` → hover `translateY(-1px)` + `0 4px 10px`, checked `0 4px 12px` green glow; `btn-primary` `12px` + `0 4px 14px` → hover `0 8px 20px`. *Motion & Polish:* `cardIn .38s cubic-bezier`, `details[open] > *` `.28s`, `prefers-reduced-motion` disables, toolbar `14px` + `blur(8px)`, progress glow, toast `14px` + `blur(6px)`, stickybar `blur(10px)`, inputs focus `0 0 0 3px sage` + `0 2px 8px`, swatch `on` `0 4px 14px` + green halo, hover `-2px`. No logic change — visual only.
- Validation: **39 suites, 2,074 assertions, 0 failures** — visual-only CSS, version `v1.42.0`.



## v1.41.2 — 2026-10-21
- **Meals — Not applicable now states not adherent + not applicable on dates.** Per *“not applicable meals to state that patient is not adherent and the meals were not applicable on these dates”*: **Not applicable** (`na`) previously emitted only `meals are not applicable`; now it emits `patient is non-adherent to his/her meal plan & meals were not applicable` (no dates) or `& meals were not applicable on (8/1/26), (8/5/26)` when dates are entered — matching the requested wording *“patient is not adherent and the meals were not applicable on these dates”*. **Refused** (`no`) unchanged: `patient is non-adherent … & patient refused meals on (dates)`. **Adherent** unchanged. Header `Meal dates — if refused` → `Meal dates — if refused / not applicable` to reflect both non-adherent options. Placeholder remains `— select Adherent / Refused / Not applicable`; dates box still shows for any non-adherent choice (Refused or Not applicable) per prior *keep dates for both*. Logic in `buildLifestyle()` now `else if(isNA) → non-adherent` plus `else if(isNA) { mdNA … meals were not applicable (on …) }`.
- Validation: **39 suites, 2,074 assertions, 0 failures** — `na` without dates → `non-adherent … & meals were not applicable.`, `na` with dates → `… & meals were not applicable on (8/10/26)`, `no` still `… refused meals on`, version `v1.41.2`.



## v1.41.1 — 2026-10-21
- **Fix: Settings Theme & Layout now save, dark theme names visible.** Inside Settings the **Theme — visual** swatch click previously updated `cpSettings.theme`/`cpThemeDark` but **did not update hidden `#setTheme`**, so **Save** → `readSettingsForm()` re-read the stale `forest` value and overwrote it — theme appeared to not save. The toolbar `Theme` picker (`#themeSel`) also only called `setTheme` without syncing `cpSettings`/`#setTheme`, so a later **Save** reverted it. Fixed: swatch click now also sets `#setTheme` + `#themeSel`; `themeSel` change now syncs `cpSettings.theme`/`appSettings.theme` + `#setTheme`; `setTheme()` now syncs both hidden and visible selects; `fillSettingsForm()` now sources the **actual displayed theme** (`document.body[data-theme]`) for `#setTheme` and re-syncs `appSettings.theme` if diverged, so **Save** always persists what you see. **Layout** already synced via `setUI()` (which updates `#setUi`/`#uiSel` + `cpSettings.ui`), but verified and retained. **Dark-theme label invisible:** `.theme-swatch span` inherited `color:var(--ink)` (light beige in dark themes) on `rgba(255,255,255,.82)` → white-on-white; changed to fixed `color:#2b2113` (badge text dark), `background:rgba(255,255,255,.92)`, `font-weight:800` so *Noir, Espresso, Plum…* are legible on all themes. Tested dark *Noir/Espresso* and layout *Comfortable* via Settings → Save → reload: `body[data-theme]`/`data-ui`, `appSettings`, `cpSettings`, `#setTheme`/`#setUi`, `on` states persist; span computed `rgb(43,33,19)` now dark.
- Validation: **39 suites, 2,074 assertions, 0 failures** — swatch → Save → reload theme persists, toolbar → Save → reload persists, layout pill → Save → reload persists, dark labels legible, version `v1.41.1`.



## v1.41.0 — 2026-10-21
- **Meals: separate Refused vs Not applicable options.** The Lifestyle **Meals** control previously had two pills (`Adherent` / `Non-adherent` with `Refused meal dates`). It now has three: **Adherent** (`value="yes"`), **Refused** (`value="no"` — label changed from *Non-adherent* to *Refused*, `value="no"` kept for draft compatibility), and **Not applicable** (`value="na"`). Per user choice *Adherent is alone* / *meals are not applicable* / *keep dates visible for both (but optional)*: **Refused meal dates** header → **Meal dates — if refused** and the date box now shows for any non-adherent choice (Refused or Not applicable) but remains hidden for `Adherent` or none-selected. Generated note: *Adherent* → `patient is adherent to his/her meal plan`; *Refused* → `patient is non-adherent to his/her meal plan` + optional `patient refused meals on (dates)`; *Not applicable* → `meals are not applicable` (no refused clause, dates ignored in output even if present, visible per *keep dates* choice). Placeholder when none selected updated to `— select Adherent / Refused / Not applicable`. Logic in `buildLifestyle()` (`isAdherent`/`isRefused`/`isNA` with legacy `no` → Refused, `refused` alias) and `syncConditionalUI()` (`mealPicked && !mealIsAdh` → show dates) updated; `RADIO_GROUPS` already persisted `mealAdh` so `na` saves/restores; legacy drafts with `no` still map to Refused. `Meal dates — if refused` sub-header clarifies.
- Validation: **39 suites, 2,074 assertions, 0 failures** — 3 meal pills present, default none selected, `yes` → adherent, `no` → non-adherent + dates, `na` → meals are not applicable (dates hidden in output, box visible), version `v1.41.0`.



## v1.40.1 — 2026-10-21
- **Noise & glitch cleanup — no feature change.** Removed stray/unbalanced code that could misfire the parser or layout, with behavior preserved: fixed stray `} }` after `details.card:not([open]) summary::before` (extra brace caused negative CSS depth and cascade break), removed orphan `</details>` that made 12 opens / 13 closes and distorted the `grid-top` DOM, merged duplicate `style="..." style="..."` on `#caseReviewLaunch` and `#caseReviewPanel` (first style won, second ignored), deleted empty `@media (min-width: 900px) { }` block and added missing `}` to close the `@media (max-width: 380px)` block (restores 462/462 balanced braces), replaced inline `onclick="copyOutput()"` / `onclick="copyMedsOnly()"` on the sticky bar with `id="copyAll"` / `id="copyMeds"` plus guarded `addEventListener` bindings (CSP-safe, no double-binding), merged duplicated `.case-review-panel .summary-grid` rules into one, and removed dead alias wrappers `applyReviewChecklistSetting` / `applyCaseInfoSetting` (legacy shims for `applyCaseWorkflowSetting`). `APP_VERSION` → `v1.40.1`; sticky-bar still shows Copy all / Meds via listeners, all visual Appearance (16 swatches, 5 pills) and divided Settings (Appearance/Note/Medications/Workflow green `#2e7d43` ON / red `#c62828` OFF) retained.
- Validation: **39 suites, 2,074 assertions, 0 failures** — details 12/12, CSS 462/462 depth 0, no duplicate style attrs, no inline handlers, `copyAll`/`copyMeds` listeners guarded, appearance/workflow unchanged, version `v1.40.1`.



## v1.40.0 — 2026-10-21
- **Full cosmetic & visual UI options — Appearance panel.** Re-introduced the layout switcher as part of a visual **Appearance** settings card (top of Settings): **Theme — visual** as 16 swatches (`#themeSwatches` grid, `theme-swatch` with gradient `sw` + label, `on` border `var(--green-700)`), and **Interface layout** as 5 pills (`#layoutPills` `Standard/Compact/Comfortable/Focus/Wide` with `on` green `var(--green-700)`). Both stay in sync with the toolbar `UI` picker (`#uiSel`) and hidden selects (`#setTheme` `display:none`, `#setUi`), persist to `cpSettings.ui/.theme` + `cpUi`/`cpTheme`, and apply via `setUI()`/`setTheme()` + `renderThemeSwatches()`/`renderLayoutPills()` on load/live. `setUI` now toggles `body[data-ui]` with dramatic `!important` overrides (compact 840px/8px, comfortable 1020px/20px, focus 700px centered gold spotlight, wide 1360px two-column) and `transition: all .22s`; swatch click updates `appSettings.theme` and pill click updates `appSettings.ui` with `toast('Interface: …')`. Settings remain divided into **4 cards — Appearance / Note / Medications / Workflow** (Workflow `ON green #2e7d43 / OFF red #c62828` retained).
- Validation: **39 suites, 2,074 assertions, 0 failures** — swatches 16, pills 5, toolbar/settings sync, persistence, reset, version `v1.40.0`.

## v1.39.0 — 2026-10-21
- **Removed the multiple-UIs option per request.** The `UI` picker (`#uiSel` toolbar + `#setUi` in Settings) and all `body[data-ui]` layout CSS/JS have been removed. The app returns to a single **Standard** interface (the divided **Note / Medications / Workflow** cards and the **Workflow ON green `#2e7d43` / OFF red `#c62828`** toggle from v1.37.0 are retained). `FACTORY_SETTINGS.ui` and `cpUi` persistence, `setUI()` and related handlers were removed; `cpSettings.ui` is now ignored. Validation updated to confirm no `data-ui` CSS or selectors remain.

## v1.38.1 — 2026-10-21
- **UI switcher visible fix — nothing changes → dramatic layouts.** The 5 `body[data-ui]` interfaces were too subtle (2px padding). Made them unmissable: `Compact` now 840px/8px dense, `Comfortable` 1020px/20px spacious with shadow, `Focus` 700px centered single-column with gold spotlight + sticky toolbar, `Wide` 1360px two-column grid. Added `transition: all .22s` and `!important` overrides so the switch animates, and a `toast('Interface: Compact')` on toolbar change. Hard-refresh (`Ctrl+Shift+R`) now shows immediate difference.

## v1.38.0 — 2026-10-21
- **Multiple user interfaces — layout switcher.** Added 5 selectable layouts via toolbar **UI** picker and **Settings → Interface layout** (`standard`, `compact`, `comfortable`, `focus`, `wide`). Each layout is a separate `body[data-ui]` interface: **Standard** (balanced default), **Compact** (dense, 880px, tight cards), **Comfortable** (spacious, 980px, larger type), **Focus** (720px single-column, sticky toolbar, card spotlight), **Wide** (1280px, two-column grid on desktop). Toolbar `UI` select (`#uiSel`) and Settings `Interface layout` (`#setUi`) stay in sync, persist to `cpSettings.ui` + `cpUi` (factory `standard`), apply on load via `setUI()`, and update live via `applySettingsLive()`. Settings remain divided into **Note / Medications / Workflow** cards, with the Workflow **ON green / OFF red** toggle (`#setCaseWorkflowBtn` green `#2e7d43` ON, red `#c62828` OFF) retained.
- Validation: **39 suites (38 + new UI), 2,074 assertions (38×2,045 +29), 0 failures** after UI switcher and persistence checks.

## v1.37.0 — 2026-10-21
- **Settings divided & rearranged — Case info toggle is now green ON / red OFF.** The 8-row flat list is now grouped into three bordered cards: **Note** (Header phrase, Sections on load, Note editing, Default theme), **Medications** (Medication frequency default, Therapies default, Gaps: labs & NTT), **Workflow** (Case information & checklist). The Workflow toggle was changed from a `Show/Hide` `<select>` to a pill-shaped button `#setCaseWorkflowBtn` (`ON` green `#2e7d43` when `caseWorkflow:true`, `OFF` red `#c62828` when `false`, with white dot and `aria-pressed`). A hidden `<select id="setCaseWorkflow">` remains for backward-compatible draft/settings persistence and test compatibility; `fillSettingsForm()` now syncs the button via `syncCaseWorkflowBtn()`, the button click toggles the hidden select and updates `aria-pressed`/`OFF`/`ON`, and `readSettingsForm()` still reads the select. Group styling added: `.set-group` (bordered, `var(--beige-50)`), `.set-section` (uppercase header), `.set-toggle[aria-pressed=true/false]` (green/red, hover brighten).
- Validation: **38 suites, 2,045 assertions passed (37×2,015 +30), 0 failures** after settings regroup and toggle check (button green/red, persistence, hide/show workflow).

## v1.36.0 — 2026-10-21
- **No current medications — lifestyle modifications only.** Added checkbox `#noMeds` below Medications with conditional **Last medication** group (`#lastMedWrap`: `#lastMedName`, `#lastMedDate`, `lastMedAdh` Yes/No pills, `#lastMedReason` shown when No). When checked, medication rows and + button are hidden, current-med adherence radios are disabled and the `Adherence: — select Yes / No` line is suppressed. Header now emits `No Medications and Lifestyle modifications only;` followed by `Last Medication was <name> Discontinued on <date> & patient was adherent/not adherent to it <reason>` exactly matching the user-edited HTN wording. For HTN-family conditions (Hypertension, Hypertensive Heart Disease/CKD/HHKD) the **Gaps in care** auto-emits `Patient is not taking HTN medication (Last medication was …)` with last-med detail in parentheses and suppresses the duplicate pill; **Events → Medication changes** auto-includes the last-med sentence (merged with manual text, scratch still omits). Draft autosave/restore now includes `noMeds`/`lastMedName`/`lastMedDate`/`lastMedReason` and `lastMedAdh` radio (`RADIO_GROUPS` + id listeners, `syncConditionalUI` toggling `lastMedWrap`/`medRows`/`medPlus`/`lastMedReason`). `syncConditionalUI` also disables `medAdh` when `noMeds`.
- Validation: **38 suites, 2,045 assertions passed (37 × 2,015 + 30 new no-meds), 0 failures**.

## v1.35.0 — 2026-10-19
- **Adherent options now as before, but none selected by default.** Reverted the dropdown (`<details>`) approach — the Medication adherence (Yes/No, PRN, out-of-facility, vitals-out) and Meal adherence (Adherent/Non-adherent, refused dates) pills are again visible as they were, but both `medAdh` Yes and `mealAdh` Adherent no longer have `checked` by default so the user must click one. Null-safe JS (`adhSelected`/`mealSelected`) shows placeholder lines “Adherence: — select Yes / No” and “meal adherence: — select Adherent / Non-adherent” until chosen; existing tests updated to select explicitly.
- **Post-copy checklist now generic 3 lines.** No specific goals/barriers/care actions are written. The side panel triggered after Copy now shows **one line per category** — `Goals — 2 required` (single checkbox), `Barriers — 2 required`, `Care Actions — 4 required` — rather than 8 specific texts. The panel still auto-closes **once all three lines are ticked** (3/3), hiding the gold ✓ side icon.
- Validation: **37 suites, 2,031 assertions passed, 0 failures**.

## v1.34.0 — 2026-10-19
- **Post-copy checklist now 3 lines.** The side panel triggered after Copy now shows **one line per category** instead of 8 separate boxes: **Goals (2)** — two goals on one line, **Barriers (2)** — two barriers on one line, **Care Actions (4)** — four actions on one line. Each line has a single checkbox; the panel auto-closes **once all three lines are ticked** (`Goals 1/1 · Barriers 1/1 · Actions 1/1` → 3/3), hiding the side icon. The side icon/button itself is unchanged (gold ✓ at right 14px, appears after copy).

## v1.33.0 — 2026-10-19
- **Adherent options now closed by default.** The medication-adherence (Yes/No, PRN, out-of-facility, vitals-out) and meal-adherence (Adherent/Non-adherent, refused dates) controls are now inside collapsed `<details>` blocks (“Medication adherence ▸” and “Meal adherence ▸”) so the user must click to reveal and choose. The previous Yes/Adherent defaults are preserved (`checked`) but visually hidden until opened. Includes null-safe handling (`adhSelected`/`mealSelected` with placeholder “Adherence: — select Yes / No” when nothing is chosen) and the collapsible styling (`adherence-collapsible`).
- **Post-copy side checklist.** A small floating side button (`#postCopyCheckBtn`, gold circle ✓ at right 14px, 44px) appears **after any successful Copy** (`copyText` → `showPostCopyBtn()`). Clicking it opens a compact side panel (`#postCopyPanel`, 360px) that shows **2 goals, 2 barriers, 4 care actions** drawn randomly from the current condition’s `COND_GUIDE` (or Hypertension fallback). Each line is a checkbox; progress is shown as “X / 8 checked — Goals Y/2 · Barriers Z/2 · Actions W/4”. The panel auto-closes and the button hides **once all three groups are fully ticked** (2+2+4 = 8) with a 550 ms toast “Post-copy checklist complete”; manual close via × or Escape is also supported.
- Validation: **37 suites, 2,012 assertions passed, 0 failures** (layout + logic, no new assertions yet for adherence/post-copy).

## v1.32.0 — 2026-10-19
- Adjusted the **COPD / emphysema** and **Asthma** care-gap labels and generated notes to state **Spirometry / pulmonary function testing (PFTs) not performed or documented** and **Spirometry / pulmonary function testing (PFTs) was not performed or documented for COPD / for asthma** (wording out PFTs). Respiratory failure retains its existing pulmonary function testing gap. Source SPR eligibility wording updated to reference spirometry / PFTs.
- Validation: **37 suites, 2,012 assertions passed, 0 failures**.

## v1.31.0 — 2026-10-19
- Case information now displays **half-and-half (2 columns)** in the Review checklist. The 9 fields (Facility | DOB, Full Name | Provider, Last Service Date | ICD10, Task Type | Source, SME Comment full-width) are laid out as `repeat(2, 1fr)` with 5px gap so the details read as a true split screen instead of a single vertical stack. The mobile breakpoint no longer collapses the summary grid to one column (`.review-check-grid` still collapses), keeping the compact half-and-half layout on narrow viewports.
- Validation: **37 suites, 2,012 assertions passed, 0 failures** (layout-only change).

## v1.30.0 — 2026-10-19
- Merged **Case information** and **Review checklist** into a single Settings toggle **Case information & checklist — Show / Hide** (`caseWorkflow`). When set to Hide, the Case information card, Patient | Facility | DOB banner, and Review checklist launch/panel are all hidden (open panel is closed); when set to Show they behave as before. The single choice is saved per browser (legacy `caseInfo`/`review` installs migrate: Hide if either was Hide), defaults to Show, applies live and on load, survives **New patient**, and is restored by **Reset to defaults**. Legacy `caseInfo`/`review` keys remain synced on save for backward compatibility.
- **Checklist and case input made more compact:** case information block reduced from 18 → 10 rows / `min-height 300px → 140px`, checklist grid gap `12px → 8px`, group padding `10/13/5 → 6/8/3`, item padding `9px → 5px`, font `13px → 12px`, checkbox `17px → 14px`, launch/card padding `10/12px`. **Gaps: labs & NTT** toggle remains unchanged.
- Validation: **37 suites, 2,012 assertions passed, 0 failures**.

## v1.29.0 — 2026-10-19
- Added **Gaps: labs & NTT — Include labs / Gaps only** to Settings (gear menu) alongside Case information and Review checklist. When set to **Gaps only**, the **HYPERTENSION LABS — CHOOSE APPLICABLE STATUSES** section (KFTs, Lipid Panel, HbA1c, TSH, Hematology) and any laboratory gap sentences are hidden and excluded from the generated note; **Blood pressure** statuses (Not recent / Not regularly monitored / Uncontrolled) and **HYPERTENSION GAPS** (No HTN meds, Kidney function) remain visible and functional. The choice is saved per browser (`gapLabs`), defaults to **Include labs** for new and legacy installs, applies live on Save and on page load, survives **New patient**, and is restored by **Reset to defaults**.
- Validation: **37 suites, 2,001 assertions passed, 0 failures**.

## v1.28.0 — 2026-10-18
- AI prompt and Escalations now escalate only **PCM medications**. Renamed the escalation fields and tooltip from CCM to PCM (`Duplicate PCM medications`, `Drug-to-drug interactions involving PCM medications`), and added an explicit prompt line: “Only escalate medication interactions and duplications regarding PCM medications; do not flag or escalate non-PCM medications.” The prompt still checks the current PCM medication list only (active/current, same-ingredient/brand-generic/therapeutic duplication, with sources) and keeps findings separate from condition-specific criteria.
- Validation: **37 suites, 1,978 assertions passed, 0 failures**.

## v1.27.0 — 2026-10-18
- Added **Case information — Show / Hide** to Settings (gear menu) alongside the existing Review checklist toggle. When Case information is set to Hide, the Case information card and the Patient | Facility | DOB summary bar are hidden (the bar also stays hidden after data entry until the feature is re-enabled). Both toggles are saved per browser, default to Show for new and legacy installs, apply live on Save and on page load, survive **New patient**, and are restored by **Reset to defaults**. Hiding one does not affect the other; setting both to Hide completely disables the case workflow.
- Validation: **37 suites, 1,978 assertions passed, 0 failures**.

## v1.26.0 — 2026-10-18
- Added a patient summary bar directly under the **Problem Note Builder** header. When case information is accepted, the bar shows **Patient | Facility | DOB** side by side (with `—` for any blank of the three). It hides while the case block is being edited, on **Clear**, and on **New patient**, and it never enters the generated note or local drafts. Values are rendered as text (no markup injection).
- Validation: **37 suites, 1,951 assertions passed, 0 failures**.

## v1.25.0 — 2026-10-18
- Added **Review checklist** to Settings (gear menu): **Show / Hide**. When set to Hide, the Review launch button and panel are hidden and any open checklist is closed; when set to Show they behave as before. The choice is saved per browser, defaults to Show for new and legacy installs, and is restored by **Reset to defaults**.
- Follow-up notes continue to always show `-Medication changes: …` under Events. Blank or `None` input produces `-Medication changes: None`; entered changes appear once with their text. Scratch notes continue to omit this follow-up-only line, and Escalations remains separate.
- Validation: **37 suites, 1,933 assertions passed, 0 failures**.

## v1.24.0 — 2026-10-18
- Follow-up notes now always show `-Medication changes: …` under Events. Blank or `None` input produces `-Medication changes: None`; entered changes appear once with their text. Scratch notes continue to omit this follow-up-only line, and Escalations remains separate.
- Validation: **37 suites, 1,921 assertions passed, 0 failures**.

## v1.23.0 — 2026-10-18
- Replaced the separate browser-tab checklist with an expandable **in-tool Review checklist** at the end of the plan builder. Case details appear above two checklist groups: **1. Practice Fusion** (A. Provider, B. Facility, C. Superbill) and **2. TC File** (Provider, PCM, Log).
- Added inline expand/close and Clear checks controls. Checklist state stays in the current page only and is excluded from generated notes and local drafts; **New patient** closes the panel and clears its checks.
- Validation: **37 suites, 1,920 assertions passed, 0 failures**.

## v1.22.0 — 2026-10-18
- When medication monitoring requires just one electrolyte, the lab status is now labeled with that analyte (for example, **Sodium**) rather than the broad **Electrolytes** label. When multiple electrolyte analytes are required, they remain grouped under **Electrolytes**.
- Manual lab matching and “Not recent” output follow the specific electrolyte name for the single-analyte case.
- Added regression coverage for the specific name, note wording, and multi-electrolyte grouping.
- Validation: **37 suites, 1,917 assertions passed, 0 failures**.

## v1.21.0 — 2026-10-18
- Applying a valid case-information block now sends its ICD10 code to **Condition or code**, resolves the matching condition, and unlocks the plan builder automatically.
- Added explicit hiding for inline script elements to prevent app source from appearing as page text in embedded previews.
- Regression coverage verifies ICD10 transfer, plan matching/unlocking, and the single-box intake behavior.
- Validation: **37 suites, 1,913 assertions passed, 0 failures**.

## v1.20.0 — 2026-10-18
- Replaced the nine separate Case information inputs with one multiline paste box for the standard Facility/DOB/Full Name/Provider/Last Service Date/ICD10/Task Type/Source/SME Comment label-value block. On successful parsing, the large box collapses to a compact captured state; Edit and Clear remain available.
- Parsed details remain page-only, are excluded from local drafts and generated notes, clear on **New patient**, and appear above **1. Practice Fusion** / **A. Provider**, **B. Facility**, **C. Superbill** in the separate Review tab. No sample patient values are prefilled.
- Added regression checks for parsing, invalid input, collapse/edit behavior, Review-tab display, data isolation, and reset.
- Validation: **37 suites, 1,908 assertions passed, 0 failures**.

## v1.19.0 — 2026-10-18
- Added an initial **Case information** panel for Facility, DOB, Full Name, Provider, Last Service Date, ICD10, Task Type, Source, and SME Comment.
- The final separate-tab Review checklist now shows a summary of those entered case details above the Practice Fusion Provider/Facility/Superbill checklist. Intake values are temporary to the open page, excluded from local draft storage and generated note text, and cleared by **New patient**.
- Added regression coverage for tab data transfer, plain-text escaping, note/draft privacy, and reset behavior.
- Validation: **37 suites, 1,896 assertions passed, 0 failures**.

## v1.18.0 — 2026-10-18
- Added a final **Review checklist** panel after the existing app sections. Its button opens a separate browser tab with a **1. Practice Fusion** checklist for **A. Provider**, **B. Facility**, and **C. Superbill**, plus a Clear checks control.
- The checklist is for task review only and does not modify the generated note. A message explains how to proceed if the browser blocks the new tab.
- Added regression coverage for final placement, separate-tab opening, checklist labels and reset, note isolation, and popup-block handling.
- Validation: **37 suites, 1,883 assertions passed, 0 failures**.

## v1.17.0 — 2026-10-18
- Reorganized the PCM drug-interaction and duplicate-medication check so it sits **under Escalation Search** in the copied AI prompt. Output now groups condition criteria as Section 2A and the distinct PCM medication-safety tables as Section 2B; medication findings are not counted as escalation criteria or treated as escalation events.
- Added regression coverage for prompt placement, section grouping, and separate counts.
- Validation: **37 suites, 1,869 assertions passed, 0 failures**.

## v1.16.0 — 2026-10-18
- Expanded the copied AI prompt with a **PCM medication safety search**. It asks the reviewing AI to check current/active PCM medications for clinically significant drug interactions and duplicate therapy, including same-ingredient brand/generic duplicates and potential therapeutic duplication.
- The prompt requests current authoritative interaction sources and direct links when search/checking is available, excludes historical/discontinued/one-time meds, and requires an explicit limitation rather than a “none” or “safe” claim when the list or search access is incomplete. It adds a dedicated output section and prohibits medication-change recommendations; findings are for prescriber/pharmacist verification.
- Added regression checks for the search instructions, source citations, safety limits, and new output section.
- Validation: **37 suites, 1,867 assertions passed, 0 failures**.

## v1.15.0 — 2026-10-18
- In global **Frequency: Off** mode, an explicit tablet quantity now scales numeric tablet strength to the effective per-dose strength (for example, 10 MG × 0.5 tablet → 5 MG; 10 MG × 2 tablets → 20 MG). Decimal, slash/Unicode fractions, mixed fractions, and common worded quantities are recognized; equal repeated dose quantities are treated per dose, not multiplied by daily frequency. Ambiguous ranges or differing split-dose quantities are left unscaled.
- Written and Latin frequency modes retain the original strength and SIG output. Capsule counts, concentration strengths, and sliding-scale formatting are not scaled.
- Added regression coverage for Off-mode note generation, equivalent quantity forms, multiple daily doses, ambiguous instructions, and unchanged Written/Latin output.
- Validation: **37 suites, 1,857 assertions passed, 0 failures**.

## v1.14.0 — 2026-10-16
- Removed the standalone **Other Appointments and Provider Notes** form card, its tooltip and field, and the corresponding generated-note section. Provider visits, Events, referrals, and their existing outputs remain unchanged.
- Older drafts no longer restore the removed free-text field; saved output is migrated to remove the former section while retaining following note content. Renumbered the remaining cards.
- Added regression checks for the removed UI/output and legacy-draft cleanup.
- Validation: **37 suites, 1,806 assertions passed, 0 failures**.

## v1.13.0 — 2026-10-16
- Reworked condition-specific care-gap labels and generated wording for clarity and alignment with `Update Care Policy - Care Gaps.pdf`. The HTN medication gap now displays **No HTN meds** and emits “Patient is not taking HTN medication”; source eligibility remains available in control guidance.
- COPD spirometry output now explicitly says it **was not performed or documented**. The source age/eligibility guidance remains attached to the care-gap control.
- PNU status is now a mutually exclusive **Patient refused** / **Patient did not receive** split choice in Care Gaps for every PNU-gap condition (COPD, Asthma, Respiratory failure, Hypothyroidism, and Hyperthyroidism). Removed the duplicate Asthma/Respiratory-failure vaccine-status panels; older saved PNU statuses migrate into the new Care Gaps selection.
- Visually grouped BP **Not recent**, **Not regularly monitored**, and **Uncontrolled** criteria while keeping each checkbox independently selectable. Generated wording now distinguishes “BP has not been measured recently” and “BP is not regularly monitored.”
- Added regression coverage for the all-condition PNU split, legacy draft migration, removed duplicate panels, updated gap wording, and grouped independent BP criteria.
- Validation: **37 suites, 1,801 assertions passed, 0 failures**.

## v1.12.2 — 2026-10-15
- Removed COPD's direct pneumococcal-vaccine and spirometry status controls and their generated status lines. The separate source-listed COPD care-gap choices remain; vaccine status controls remain available for Asthma and Respiratory failure.
- Medication changes now appear once under Events for follow-up notes only. Removed the separate Escalations field and its mirrored output; legacy draft values in that former field migrate to Events.
- Validation: all **1,783 assertions passed across 37 test suites**.

## v1.12.1 — 2026-10-15
- Grouped NTT “Uncontrolled” and “Not recent” statuses into a split-style control for each lab panel. Both options remain independently selectable, with responsive sizing for narrow panes and the existing Not recorded double-click behavior preserved.
- Generated notes now include the single-line `Escalations: None` fallback whenever all escalation fields are blank or `None`; substantive entries retain their existing order and format.
- Validation: all **1,772 assertions passed across 37 test suites**.

## v1.12.0 — 2026-10-15
- Standardized the Risks & Complications controls in both normal and side-pane views: the Complications/Risk Factors buttons and the No complications/No risk factors toggles now use matching two-column widths and a shared 46px height. The sizing no longer depends only on the outer viewport breakpoint.
- Validation: all **1,767 assertions passed across 37 test suites**.

## v1.11.0 — 2026-10-15
- Reworked condition-specific care-gap wording into concise, plain-language note text while keeping source eligibility details in the control tooltips. Preserved the exact “Patient is not following DASH diet” wording, corrected asthma/respiratory-failure spirometry phrasing, and added the source-listed Neuropathy assessment gaps.
- Added exclusive **Not monitored / Not recent** split controls for monitoring and measurement gaps (including PHQ-9, GAD-7, lipid profile, anemia measures, DMARD monitoring, disease-activity assessment, and HIV monitoring). For MDD, the PHQ-9 choices output “PHQ-9 screening not documented” or “PHQ is not recent.” Matching NTT recency output takes precedence to avoid duplicates.
- Validation: all **1,765 assertions passed across 37 test suites**.

## v1.10.0 — 2026-10-15
- Refined MDD/Depression care gaps: removed the irrelevant GAD-7 gap, changed the PHQ wording to “PHQ-9 screening not monitored,” and removed the “for 1 year” parenthetical from the forgetfulness gap.
- Escalations now list the condition-specific finding first without the long label, omit empty/`None` subsections, and omit the whole heading when there is no escalation information. Empty follow-up medication changes are also omitted from Events.
- Validation: all **1,747 assertions passed across 37 test suites**.

## v1.9.1 — 2026-10-15
- Refined the narrow split-pane control grids so paired adherence/status controls and date fields align consistently. Risk/complication toggles now share equal widths and fixed heights.
- Validation: all **1,737 assertions passed across 37 test suites**.

## v1.9.0 — 2026-10-15
- Improved the narrow split-pane layout: the toolbar no longer overlays the header, cards and controls use tighter proportional spacing, and provider/date fields stay aligned in equal-width columns.
- Removed the DOS control from Labs & Vitals as requested. Lab and vital dates are entered/reviewed manually; the app no longer runs an automatic one-year DOS filter or computes DOS-based date reminders. The policy reminders remain available for manual review.
- Replaced the fixed provider-visit inputs with repeatable visit cards (provider and date side by side, plan below) and made symptoms repeatable in individual symptom/date cards. Existing saved drafts migrate to the new cards.
- Added an `Escalations:` heading to generated notes, with status shown for each subsection, and added a dedicated input for condition-specific escalation findings from the guide PDF.
- Validation: all **1,733 assertions passed across 37 test suites**.

## v1.8.1 — 2026-10-15
- Removed the specified long helper paragraphs from the form and replaced the adherence, lab-review, care-gap/BP, other-appointments, and medication-escalation guidance with compact, theme-aware hint icons. Hovering or keyboard-focusing an icon opens its text box.
- Removed the listed meal-adherence, manual-gap, follow-up visit, symptom, hospitalization, and referral helper lines; Scratch visit-range guidance remains. Tooltips do not alter generated note content.
- Validation: all **1,686 assertions passed across 36 test suites**.

## v1.8.0 — 2026-10-15
- Aligned the app with the October 2026 PCM case-guideline policies: medication concentration and adherence exceptions, one-year DOS-based lab review, within-window recency reminders, source-listed care gaps and eligibility, BP review rules, and condition-related visit/referral requirements.
- Kept Symptoms, Hospitalizations/ER visits, Referrals, Medication Changes, and Other Appointments/Provider Notes in their distinct sections; scratch notes omit medication changes, and unrelated hospitalizations remain documentable.
- Added the condition-specific Escalations tab and separated its routine/urgent criteria in the AI prompt from symptoms and dated escalation events. Source-listed blank entries remain blank; source spellings are preserved.
- Rechecked BH medication monitoring and the NTT reference against the provided PDFs. The read-only NTT tab retains 187 source rows plus the requested MDQ assessment (188 total across 34 condition groups), PDF values/order, and no search or visible source labels; pneumococcal vaccine status is not a tracking/trending row.
- Kept condition-specific guide reminders for evidence-only barriers, removal of placeholders, and SMART-goal dates at least one calendar month after DOS.
- Validation: all **1,640 assertions passed across 35 test suites**.

## v1.7.12 — 2026-10-13
- Removed the NTT search controls and visible source badges at the user's request; the NTT tab remains read-only, and its reference content and MDQ addition are retained.
- Kept the separate medication review and copy-pause behavior unchanged.
- Validation: all 1,551 assertions passed across 34 test suites.

## v1.7.11 — 2026-10-12
- Added NTT-tab search across item names, LOINC codes, categories, lab groups, reference levels, and source labels; the reference rows remain read-only and their PDF ordering is preserved.
- Added visible source badges to NTT rows, distinguishing the 188 entries from the authoritative NTT PDF from the user-requested MDQ assessment.
- Added an independent medication review dialog comparing pasted text with parsed lines and surfacing possible parsing and missing-information concerns without changing the generated note.
- Copying now pauses only for potential parsing issues (such as an unparsed row, multiple medications in one box, a dropped strength, or directions that were not parsed); the user can edit first or explicitly copy anyway. Missing strength/frequency checks remain advisory.
- Validation: all 1,563 assertions passed across 34 test suites.

## v1.7.10 — 2026-10-12
- Added clear keyboard focus rings and contained Tab/Shift+Tab navigation for Settings, Medication Monitoring, and Condition Guide dialogs; dialogs set and restore focus appropriately.
- Added accessible names for key icon controls and the generated note, and exposed the active guide section to assistive technology.
- No note content, template wording, or Settings options changed.
- Validation: all 1,512 assertions passed across 33 test suites.

## v1.7.9 — 2026-10-12
- Added an MDQ (Mood Disorder Questionnaire) assessment to the Schizophrenia / psychotic disorder NTT tab under Assessments → Screening scales.
- Kept it as an assessment label only; no LOINC code or reference range was invented.
- Added regression checks for its presence, category, and lack of unsupported code/range.
- Validation: all 1,487 assertions passed across 32 test suites.

## v1.7.8 — 2026-10-12
- Kept the source strength wording/order in the compact COPD line, e.g. `108 (90 Base) MCG/ACT`, while continuing to omit indication and Pharmacy/status/date text.
- Added a regression assertion for the exact albuterol HFA output.
- Validation: all 1,482 assertions passed across 32 test suites.

## v1.7.7 — 2026-10-12
- Simplified COPD medication output to match the standard medication style: normalized product name/strength and concise parsed SIG, with indication text and Pharmacy/status/date metadata omitted.
- COPD lines now follow the global Written / Off / Latin frequency setting. Separate Pharmacy/manual rows and both `AND` schedules with their durations remain intact.
- Added regression coverage for all 20 COPD fixtures, including the supplied albuterol HFA example.
- Validation: all 1,481 assertions passed across 32 test suites.

## v1.7.6 — 2026-10-12
- COPD medication output now preserves the source product wording, formulation, strength, and actual order SIG, removing only spacing issues, EHR status/date metadata, and non-order boilerplate. `AND` schedules and their durations remain intact; Pharmacy and manual rows stay separate.
- Completed the Conditions Guide NTT tab from the authoritative PDF: all 188 rows across 34 condition groups, categorized into Labs, Vitals, and Assessments, with lab subpanels, LOINC codes, normal/reference levels, assessments, pneumococcal vaccine status, and BH medication-monitoring references. The tab remains read-only and excluded from note/prompt output.
- Added regression coverage for every COPD fixture output and NTT row completeness, classification, subgroups, codes, ranges, and PDF ordering within categories.
- Validation: all 1,480 assertions passed across 32 test suites.

## v1.7.5 — 2026-10-10
- Simplified the two-line credit: removed the box, border, shadow, and sparkles; reduced type size and softened the color while keeping matching label/name styles.
- Added regression checks for the understated credit treatment.

## v1.7.4 — 2026-10-10
- Matched the supervision credit to the creator credit: both labels use the small uppercase style, and both names use the same script style.
- Added regression checks for matching credit typography.

## v1.7.3 — 2026-10-10
- Updated the header credit from `Ahmed Reda` to `A.Reda` and added `Supervised by M.Ismail` beneath it.
- Updated the bottom-dock version; credit regression checks added.

## v1.7.2 — 2026-10-09
- COPD medication normalization: preserve brand + active ingredient when distinct; avoid repeating a generic ingredient already in the name; strip respiratory formulation wording.
- Preserve and normalize the supplied concentrations: nebulized albuterol keeps `2.5 MG/3ML (0.083%)`; combination strengths use readable ratios; HFA strengths show `90 MCG/ACT (108 MCG metered)` when the source supplies both.
- Parse compact `manualActive` rows, including concatenated dose/frequency/route fields, while keeping Pharmacy and manual rows separate (no automatic merging).
- Keep both independently dosed `AND` directions on one medication line; each duration stays with its own schedule. Added `Every Shift` support without inventing a Latin abbreviation.
- Regression coverage: all 20 supplied COPD examples; full battery 1432/1432 across 32 suites.

## v1.7.1 — 2026-10-08
- I11 split: I11/I11.0 (with heart failure) moved under the Heart failure umbrella — HF guide, labs, gaps, education, vitals; header names it `Hypertensive Heart Failure` (CODE_EXACT reworded so the cleaner keeps the heart-failure part).
- I11.9 (without heart failure) moved to the Other heart disease cardiac bucket — cardiac gaps/education plus its guide.
- Word entry `Hypertensive Heart Disease`/`hhd` keeps the unspecified HTN-flavored bucket.
- `isHTN` is now short-based (was I10–I16 code-prefix), so the moved I11 codes no longer leak the HTN diet row into Heart failure / Other heart disease.
- Tests: hhd block rewritten for the HF umbrella + I11.0/I11.9/word-entry asserts; guide suite opens the HF and OHD guides for I11/I11.9. Battery 1366/1366.

## v1.7.0 — 2026-10-08
- New Settings window (gear icon in the top bar): default med frequency, header opening phrase, default theme, sections expanded/collapsed, note locked/unlocked, therapies pre-ticked on/off. Saved per browser, applied on load and New patient, with Reset to defaults.

## v1.6.1 — 2026-10-08
- Fixed strength recovery: when the drug line has no strength, the dose is now taken from the sig (`Give 10 mg…` → `Escitalopram Oxalate 10 MG`). Previously the recovered strength was dropped from the name.

## v1.6.0 — 2026-10-07
- Patient education: Bipolar disorder now has the 3 Depression topics with depression→bipolar wording.
- Conditions without education topics show a red warning under the condition box.
- Added this changelog; app shows its version in the bottom dock.

## v1.5.1 — early Oct 2026
- Sig parser combines multiple dayparts: `every morning and at bedtime` → `1 Tab Every Morning And At Bedtime` (`1 Tab QAM + QHS` in Latin mode). Explicit frequencies (BID etc.) unchanged.

## v1.5.0 — early Oct 2026
- Condition guide: new read-only NTT tab (Numbers To Track rows with LOINC codes, PDF order). Nothing feeds the note or the AI prompt.
- Anemia: `Iron Studies` renamed to `Iron Panel` (trending, gaps, parsing).
- Guide header: `Copy checklist` button removed; `Copy AI prompt` kept.
- Toolbars restyled: slim beige utility strip on top, green action dock at the bottom.

## v1.4.1 — early Oct 2026
- Vitals autofill keeps the date past oxygen-support qualifiers (`O2 sat 94% on room air on 8/1/26` → `94% on 8/1/26`).

## v1.4.0 — 2026-10-04
- Full-audit fix batch: None/empty event dates, lab-flag override persistence, sliding-scale verb stripping, dotless/hyphenated code matching, code-plus-words entry, labs flag hint, stale guide-modal close, date-only visits, tag stripping in meds, mobile guide header, completeness gating on visible boxes.

## v1.3.0 — early Oct 2026
- Sliding-scale meds: `A- {name+strength} - {route/timing}` plus the full scale on the next line in `[ ]`.

## v1.2.2 — early Oct 2026
- Fixed duplicated script tail rendering as page text (document structure hardened).

## v1.2.1 — early Oct 2026
- Guide copy uses 3-tier fallback (clipboard → execCommand → select-text) so copy never silently fails.

## v1.2.0 — early Oct 2026
- One-click export in the condition guide: `Copy checklist` and `Copy AI prompt` (checklist + notes slot + rules + output spec).

## v1.1.0 — early Oct 2026
- Condition Guide modal: per-condition Smart Goals, Barriers, Care Actions, Complications & Risks, Symptoms with per-row copy.

## v1.0.0 — early Oct 2026
- Baseline: Problem Note Builder with conditions, meds, labs/vitals, gaps, education, visits, events, risks, note merge.
