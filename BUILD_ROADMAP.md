# Build Roadmap — 14 Layers + 9 Test Phases

## Phase 1: Quarter Visualization
**Objective:** Build quarter background bands and vertical dividers

### Layers Involved
- **Layer 12:** Quarter Background Bands
- **Layer 13:** Quarter Dividers
- **Layer 11:** Chart Annotations (labels)

### Tasks

| Layer | Task | Status | Test |
|-------|------|--------|------|
| 13 | Draw vertical dividers per cycle | — | Lines at correct Q boundaries? |
| 12 | Background fill per Q phase | — | Colors match Q1/Q2/Q3/Q4? |
| 13 | Quarter labels (D-Q1, W-Q2 etc.) | — | Labels at top of each divider? |

### Acceptance Criteria
- [ ] Day, Week, Month, Year rows render independently
- [ ] Colors: Q1=Blue, Q2=Green, Q3=Pink, Q4=Orange (10% opacity)
- [ ] Divider lines correct thickness & style per cycle level
- [ ] Labels visible at top of chart
- [ ] User can toggle each cycle level on/off

### Timeline
**Estimated: 1–2 days**

---

## Phase 2: PQH / PQL
**Objective:** Calculate and draw previous quarter high/low reference levels

### Layers Involved
- **Layer 3:** Quarter Boundary Detection
- **Layer 3B:** Previous Quarter High / Low

### Tasks

| Layer | Task | Status | Test |
|-------|------|--------|------|
| 3B | Dashed horizontal per symbol | — | Levels match prior quarter range? |
| 3B | Recalculate per cycle | — | Levels update at new quarter? |

### Acceptance Criteria
- [ ] PQH (red) and PQL (green) lines draw at correct price levels
- [ ] Quarter length calculation correct per cycle (63 bars / 30 bars / 6 bars, etc.)
- [ ] Lines update at quarter boundary transition
- [ ] Works for all 3 symbols (sym_A, sym_B, sym_C)
- [ ] Dashed line style persists when not in active quarter

### Timeline
**Estimated: 1–2 days**

---

## Phase 3: SSMT Detection
**Objective:** Detect divergence across consecutive quarters

### Layers Involved
- **Layer 1:** Cycle & Timeframe Mapping
- **Layer 2:** Triad Selection
- **Layer 3B:** Previous Quarter High / Low
- **Layer 4:** SSMT Detection Logic

### Tasks

| Layer | Task | Status | Test |
|-------|------|--------|------|
| 4 | Divergence logic across symbols | — | Fires only on true divergence? |
| 4 | SSMT annotation on chart | — | Label at correct candle? |
| 4 | Forex inverse rule | — | Bearish DXY = bullish pairs? |

### Acceptance Criteria
- [ ] Detects when sym_A breaks PQH but sym_B/C fail
- [ ] Detects when sym_B breaks PQH but sym_A/C fail
- [ ] No false signals outside quarter transition
- [ ] For Forex triad: DXY direction inverse to EURUSD/GBPUSD
- [ ] Annotation marks divergence zone correctly
- [ ] SSMT_BULL and SSMT_BEAR output correctly

### Timeline
**Estimated: 2–3 days**

---

## Phase 4: PSP Detection
**Objective:** Confirm SSMT with precision swing point (3-candle crack in correlation)

### Layers Involved
- **Layer 4:** SSMT Detection Logic (dependency)
- **Layer 5:** PSP Detection Logic

### Tasks

| Layer | Task | Status | Test |
|-------|------|--------|------|
| 5 | 3-candle crack in correlation | — | Fires only after valid SSMT? |
| 5 | PSP marker at middle candle | — | Marker on correct candle? |
| 5 | Entry TF map | — | Entry TF matches cycle? |

### Acceptance Criteria
- [ ] Requires valid SSMT first (no orphaned PSP signals)
- [ ] Detects swing high/low on at least one symbol
- [ ] Middle candle closes opposite direction (sym_A bullish → sym_B bearish)
- [ ] PSP marker placed at bar[-1] (middle candle)
- [ ] Entry TF correct per cycle (4H PSP → 1H entry, etc.)
- [ ] PSP_BULL and PSP_BEAR output correctly

### Timeline
**Estimated: 2–3 days**

---

## Phase 5: Annotations & True Open
**Objective:** Draw all signal annotations and reference lines on chart

### Layers Involved
- **Layer 10:** True Open Lines
- **Layer 11:** Chart Annotations

### Tasks

| Layer | Task | Status | Test |
|-------|------|--------|------|
| 10 | True Open horizontal lines | — | Appear at correct price/time? |
| 11 | LRL diagonal line | — | Updates dynamically? |
| 11 | NEWS event box | — | Appears at news times? |

### Acceptance Criteria
- [ ] True Week Open line drawn at Monday 6pm UTC close price
- [ ] True Month Open line drawn at 2nd Monday of month open
- [ ] True Year Open line drawn at April 1 open
- [ ] LRL (Last Relative Low) diagonal updates to last swing low
- [ ] NEWS event box appears at high-impact news times (if news data available)
- [ ] Entry TF labels display correctly ("4H PSP → 1H Entry")

### Timeline
**Estimated: 1–2 days**

---

## Phase 6: SMT Fill
**Objective:** Detect Fair Value Gap synchronization and fill/fail divergence

### Layers Involved
- **Layer 1:** Cycle & Timeframe Mapping
- **Layer 2:** Triad Selection
- **Layer 6:** SMT Fill Logic

### Tasks

| Layer | Task | Status | Test |
|-------|------|--------|------|
| 6 | FVG detection both assets | — | FVG forms at same timestamp? |
| 6 | Fill / fail detection | — | Fires only on synced FVG + divergent fill? |

### Acceptance Criteria
- [ ] Detects FVG on sym_A at timestamp T
- [ ] Detects FVG on sym_B at same timestamp T
- [ ] Detects one asset filling gap while other fails
- [ ] Gap sync logic works across all 3 symbols
- [ ] Bullish FVG correctly identified (candle_1 high < candle_3 low)
- [ ] Bearish FVG correctly identified (candle_1 low > candle_3 high)
- [ ] SMT_FILL_BULL and SMT_FILL_BEAR output correctly

### Timeline
**Estimated: 2–3 days**

---

## Phase 7: Filters
**Objective:** Apply session time and higher timeframe validation filters

### Layers Involved
- **Layer 3:** Quarter Boundary Detection (dependency)
- **Layer 4:** SSMT Detection Logic (dependency)
- **Layer 7:** Session & Time Filter
- **Layer 8:** HTF SSMT Validation

### Tasks

| Layer | Task | Status | Test |
|-------|------|--------|------|
| 7 | Session time filter | — | Blocks signals outside session? |
| 8 | HTF SSMT filter | — | Raises score correctly? |

### Acceptance Criteria
- [ ] Signals only fire during active session window (UTC per Quarterly Theory)
- [ ] Time matches current quarter phase (Q1/Q2/Q3/Q4)
- [ ] HTF SSMT flag checks Weekly (1H TF), Monthly (4H TF), Yearly (Daily TF)
- [ ] HTF_SSMT = true only if SSMT present on higher timeframe
- [ ] IN_SESSION = false blocks downstream signals
- [ ] HTF_SSMT = true raises confidence score by 1

### Timeline
**Estimated: 1–2 days**

---

## Phase 8: Decision Engine
**Objective:** Score confluence 1–5 and output trade decision

### Layers Involved
- **Layer 4:** SSMT Detection Logic (dependency)
- **Layer 5:** PSP Detection Logic (dependency)
- **Layer 6:** SMT Fill Logic (dependency)
- **Layer 7:** Session & Time Filter (dependency)
- **Layer 8:** HTF SSMT Validation (dependency)
- **Layer 9:** Confluence Scoring & Decision Engine

### Tasks

| Layer | Task | Status | Test |
|-------|------|--------|------|
| 9 | Confluence scoring 1–5 | — | Score updates per layer? |
| 9 | PRIME / HIGH / ENTER / WAIT / PASS | — | Decision matches state? |

### Acceptance Criteria
- [ ] SSMT valid → +1 score
- [ ] PSP valid → +1 score
- [ ] SMT Fill valid → +1 score
- [ ] HTF SSMT true → +1 score
- [ ] IN_SESSION true → +1 score
- [ ] Score 5 → PRIME SETUP
- [ ] Score 4 → HIGH CONFIDENCE ENTER
- [ ] Score 3 → ENTER
- [ ] Score 2 → WAIT — MISSING CONFIRMATION
- [ ] Score ≤1 → PASS
- [ ] Score updates in real-time per candle

### Timeline
**Estimated: 1–2 days**

---

## Phase 9: Dashboard & Alerts
**Objective:** Display full metrics table and fire 8 named alerts

### Layers Involved
- **All previous layers** (dependencies for data)
- **Layer 14:** Dashboard Table & Alerts

### Tasks

| Layer | Task | Status | Test |
|-------|------|--------|------|
| 14 | All dashboard rows visible | — | Table readable and live? |
| 14 | All 8 alert conditions | — | Fire once per bar correctly? |

### Dashboard Rows
- [ ] Active Cycle
- [ ] Confirmation TF
- [ ] Entry TF
- [ ] Active Triad
- [ ] Symbols (sym_A / sym_B / sym_C)
- [ ] Session
- [ ] Time Valid
- [ ] Quarter — Day / Week / Month / Year
- [ ] PQH per symbol
- [ ] PQL per symbol
- [ ] Break Asset / Fail Asset / Break Direction
- [ ] SSMT Status (Bullish / Bearish / None)
- [ ] PSP Status (Bullish / Bearish / None)
- [ ] PSP Detected TF / Entry TF
- [ ] SMT Fill Status
- [ ] FVG Sync Valid
- [ ] True Open Level
- [ ] News Event Active
- [ ] HTF Confirmation
- [ ] Confluence Score (1–5)
- [ ] DECISION

### Alert Conditions
1. [ ] SSMT Detected
2. [ ] PSP Detected
3. [ ] SSMT + PSP Confirmed
4. [ ] SMT Fill Detected
5. [ ] Full Confluence — PRIME SETUP
6. [ ] High Confidence Enter
7. [ ] Time Alignment Invalid
8. [ ] No Trade / Pass

### Acceptance Criteria
- [ ] Dashboard table renders and updates each bar
- [ ] All 21 rows display with live data
- [ ] Alerts fire once per signal (no duplicates per bar)
- [ ] Alert messages clear and actionable
- [ ] Dashboard positioned and sized appropriately
- [ ] Text readable at various chart zoom levels

### Timeline
**Estimated: 2–3 days**

---

## Total Build Timeline

| Phase | Duration | Cumulative |
|-------|----------|------------|
| Phase 1 | 1–2 days | 1–2 days |
| Phase 2 | 1–2 days | 2–4 days |
| Phase 3 | 2–3 days | 4–7 days |
| Phase 4 | 2–3 days | 6–10 days |
| Phase 5 | 1–2 days | 7–12 days |
| Phase 6 | 2–3 days | 9–15 days |
| Phase 7 | 1–2 days | 10–17 days |
| Phase 8 | 1–2 days | 11–19 days |
| Phase 9 | 2–3 days | 13–22 days |

**Estimated Total: 2–3 weeks** (running linearly)

---

## Git Workflow

### Per Phase
1. Check out phase branch: `git checkout phase-X-name`
2. Work on associated layer branches
3. Test using checklist in `tests/test_checklist.md`
4. Commit: `git commit -m "Phase X complete: [task description]"`
5. Merge to main: `git merge phase-X-name`
6. Tag release: `git tag v0.X`

### Per Layer
1. Work on layer branch: `git checkout layer-X-name`
2. Create Pine Script code in `src/indicators/layer_X.pine`
3. Unit tests in `tests/unit_tests/layer_X_test.md`
4. Backtest results in `tests/backtest_results/layer_X.csv`
5. Commit: `git commit -m "Layer X: [description] — tests passing"`

---

_Build Roadmap v1.0 | Quarterly Theory SSMT/PSP System_