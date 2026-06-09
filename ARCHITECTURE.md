# System Architecture — 14 Layers

## Layer Overview

### LAYER 1 — Cycle & Timeframe Mapping
**Purpose:** User selects active cycle → system maps to correct confirmation TF and entry TF

- Input: User selection (Monthly / Weekly / Daily / 90M / Micro)
- Output: `conf_tf`, `entry_tf`
- Mapping table:
  - Monthly → 4H conf, 1H entry
  - Weekly → 1H conf, 15M entry
  - Daily → 15M conf, 5M entry
  - 90M → 5M conf, 1M entry
  - Micro → 1M conf, 30s/Tick entry

### LAYER 2 — Triad Selection
**Purpose:** User selects asset triad → system loads sym_A, sym_B, sym_C

- Input: User selection (Forex / Bonds / Indices)
- Output: sym_A (lead), sym_B (correlated 1), sym_C (correlated 2)
- Triads:
  - Forex: DXY / EURUSD / GBPUSD (DXY inverse rule)
  - Bonds: ZB1! / ZN1! / ZF1! (duration spread)
  - Indices: ES1! / NQ1! / YM1! (risk-on/off)

### LAYER 3 — Quarter Boundary Detection
**Purpose:** Detect active quarter (Q1 / Q2 / Q3 / Q4) based on time position

- Day: Asia (Q1) / LO (Q2) / NY (Q3) / LC (Q4)
- Week: Monday (Q1) / Tuesday (Q2) / Wednesday (Q3) / Thursday (Q4)
- Month: Week 1 (Q1) / Week 2 (Q2) / Week 3 (Q3) / Week 4 (Q4)
- Year: Jan–Apr (Q1) / Apr–May (Q2) / May–Nov (Q3) / Dec (Q4)

### LAYER 3B — Previous Quarter High / Low (PQH / PQL)
**Purpose:** Calculate reference levels that SSMT measures against

- PQH = highest high of prior quarter per symbol
- PQL = lowest low of prior quarter per symbol
- Quarter length: ~63 bars (adjusted per timeframe)
- Draw dashed horizontals: red (PQH) / green (PQL)

### LAYER 4 — SSMT Detection Logic
**Purpose:** Detect divergence between consecutive quarters

- One asset breaks PQH / PQL
- Other asset fails to break
- Output: SSMT_BULL / SSMT_BEAR / NONE
- Special rule for Forex: DXY direction opposite to pairs

### LAYER 5 — PSP Detection Logic
**Purpose:** Confirm SSMT with precision swing point

- Requires valid SSMT first
- 3-candle swing structure on conf_tf
- Middle candle closes opposite direction across assets
- Output: PSP_BULL / PSP_BEAR / NONE

### LAYER 6 — SMT Fill Logic
**Purpose:** Additional confirmation via FVG gap behavior

- FVG must form at same timestamp on both assets
- One fills gap, other fails
- Bullish FVG: candle_1 high < candle_3 low
- Bearish FVG: candle_1 low > candle_3 high
- Output: SMT_FILL_BULL / SMT_FILL_BEAR / NONE

### LAYER 7 — Session & Time Filter
**Purpose:** Validate signal falls within trading session window

- Session windows per Quarterly Theory (UTC)
- Asia KZ / LO / NY / PM sessions with Q1–Q4 phases
- Output: IN_SESSION = true / false

### LAYER 8 — HTF SSMT Validation
**Purpose:** Raise confidence if SSMT also present on higher timeframes

- Check Weekly cycle (1H TF)
- Check Monthly cycle (4H TF)
- Check Yearly cycle (Daily TF)
- Output: HTF_SSMT = true / false

### LAYER 9 — Confluence Scoring & Decision Engine
**Purpose:** Score signal quality 1–5 and output trade decision

- Score += 1 for each: SSMT, PSP, SMT Fill, HTF SSMT, In Session
- Decisions:
  - Score 5: PRIME SETUP
  - Score 4: HIGH CONFIDENCE ENTER
  - Score 3: ENTER
  - Score 2: WAIT — MISSING CONFIRMATION
  - Score ≤1: PASS

### LAYER 10 — True Open Lines
**Purpose:** Draw reference lines at market open prices

- True Week Open = Monday 6pm UTC close
- True Month Open = 2nd Monday of month open
- True Year Open = April 1 open
- Draw dashed horizontals extending right

### LAYER 11 — Chart Annotations
**Purpose:** Mark all signals visually on chart

- SSMT: diagonal line connecting divergent highs/lows + label
- PSP: marker/dot at middle candle
- LRL: diagonal line from last swing low
- NEWS: red box at high-impact news times
- Entry TF labels: "4H PSP → 1H Entry", etc.

### LAYER 12 — Quarter Background Bands
**Purpose:** Visualize time cycles with colored background bands

- Q1: Light Blue (10% opacity)
- Q2: Light Green (10% opacity)
- Q3: Light Pink / Salmon (10% opacity)
- Q4: Light Orange / Peach (10% opacity)
- Stacked rows: Day / Week / Month / Year (user toggle per level)

### LAYER 13 — Quarter Dividers
**Purpose:** Draw vertical lines at quarter boundaries

- Day dividers: thin solid white
- Week dividers: thin dashed blue
- Month dividers: medium solid purple
- Year dividers: thick solid gold
- Labels at top: D-Q1, W-Q2, M-Q3, Y-Q4, etc.

### LAYER 14 — Dashboard Table & Alerts
**Purpose:** Display all metrics and fire 8 alert conditions

**Dashboard metrics:**
- Active Cycle, Confirmation TF, Entry TF
- Active Triad, Symbols (A/B/C)
- Session, Time Valid
- Quarter (Day / Week / Month / Year)
- PQH / PQL per symbol
- Break Asset / Fail Asset / Break Direction
- SSMT / PSP / SMT Fill Status
- FVG Sync Valid, True Open Level
- News Event Active, HTF Confirmation
- Confluence Score (1–5), DECISION

**Alert conditions:**
1. SSMT Detected
2. PSP Detected
3. SSMT + PSP Confirmed
4. SMT Fill Detected
5. Full Confluence — PRIME SETUP
6. High Confidence Enter
7. Time Alignment Invalid
8. No Trade / Pass

---

## Data Flow

```
User Input (Cycle + Triad)
    ↓
Layer 1: Map timeframes
Layer 2: Load symbols
Layer 3: Detect quarter phase
Layer 3B: Calculate PQH/PQL
    ↓
Layer 4: Detect SSMT divergence
Layer 5: Detect PSP crack
Layer 6: Detect SMT Fill
    ↓
Layer 7: Filter by session
Layer 8: Check HTF SSMT
    ↓
Layer 9: Score confluence (1–5)
Layer 9: Output decision
    ↓
Layer 10–13: Draw annotations
Layer 14: Display dashboard + alerts
    ↓
Trade Signal Output
```

---

## Build Dependencies

**Phase 1 (Quarter Viz)** → Layers 12, 13, 11
**Phase 2 (PQH/PQL)** → Layer 3B (depends on Layer 3)
**Phase 3 (SSMT)** → Layer 4 (depends on Layers 1, 2, 3B)
**Phase 4 (PSP)** → Layer 5 (depends on Layer 4)
**Phase 5 (Annotations)** → Layers 10, 11 (depends on Layers 4, 5)
**Phase 6 (SMT Fill)** → Layer 6 (depends on Layers 1, 2)
**Phase 7 (Filters)** → Layers 7, 8 (depends on Layers 3, 4)
**Phase 8 (Decision)** → Layer 9 (depends on Layers 4, 5, 6, 7, 8)
**Phase 9 (Dashboard)** → Layer 14 (depends on all previous)

---

_Architecture v1.0 | Quarterly Theory SSMT/PSP System_