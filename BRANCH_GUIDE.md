# Branch Navigation Guide

## Repository Structure

This repository uses **layer-based and phase-based branching** to organize 14 layers of analysis across 9 test phases.

---

## Layer Branches (14 total)

Each layer branch contains the Pine Script code for one specific system layer.

### Visualization Layers (Build First)

**`layer-12-quarter-background-bands`**
- Background color fills per quarter
- Q1: Light Blue, Q2: Light Green, Q3: Light Pink, Q4: Light Orange
- User toggle per cycle level

**`layer-13-quarter-dividers`**
- Vertical lines at quarter boundaries
- Day: thin solid white
- Week: thin dashed blue
- Month: medium solid purple
- Year: thick solid gold

**`layer-11-chart-annotations`**
- SSMT diagonal lines
- PSP markers
- LRL swing low reference
- NEWS event boxes
- Entry TF drill-down labels

### Core Logic Layers

**`layer-1-cycle-timeframe-mapping`**
- Maps user cycle selection to confirmation & entry timeframes
- Monthly → 4H/1H, Weekly → 1H/15M, Daily → 15M/5M, etc.

**`layer-2-triad-selection`**
- Loads 3 correlated symbols per user selection
- Forex (DXY/EURUSD/GBPUSD), Bonds (ZB1!/ZN1!/ZF1!), Indices (ES1!/NQ1!/YM1!)
- Applies inverse rule for Forex

**`layer-3-quarter-boundary-detection`**
- Detects current quarter phase (Q1/Q2/Q3/Q4)
- Based on Day/Week/Month/Year position

**`layer-3b-pqh-pql-levels`**
- Calculates previous quarter high/low per symbol
- Draws dashed reference lines (red=PQH, green=PQL)
- Updates at quarter boundary

**`layer-4-ssmt-detection`**
- Detects divergence: one asset breaks PQH/PQL, other fails
- Outputs SSMT_BULL / SSMT_BEAR
- Handles Forex inverse rule

**`layer-5-psp-detection`**
- Confirms SSMT with 3-candle swing point
- Middle candle closes opposite across assets
- Outputs PSP_BULL / PSP_BEAR

**`layer-6-smtfill-logic`**
- Detects Fair Value Gap on both assets at same timestamp
- One fills gap, other fails
- Outputs SMT_FILL_BULL / SMT_FILL_BEAR

**`layer-7-session-timefilter`**
- Validates signal within session window (UTC)
- Confirms quarter phase match
- Outputs IN_SESSION

**`layer-8-htf-ssmt-validation`**
- Checks if SSMT also present on Weekly/Monthly/Yearly
- Raises confidence score
- Outputs HTF_SSMT

**`layer-9-confluence-decision-engine`**
- Scores confluence 1–5
- Outputs PRIME SETUP / HIGH CONFIDENCE / ENTER / WAIT / PASS

**`layer-10-true-open-lines`**
- Draws horizontal lines at True Week/Month/Year Opens
- Monday 6pm UTC, 2nd Monday of month, April 1

**`layer-14-dashboard-alerts`**
- Dashboard table with all 21 metrics
- 8 named alert conditions

---

## Phase Branches (9 total)

Each phase branch groups related layers for testing.

### Phase 1: Quarter Visualization
**`phase-1-quarter-visualisation`**
- Contains: Layers 12, 13, 11 (partial)
- Test: Do quarter bands and dividers render correctly?

### Phase 2: PQH / PQL
**`phase-2-pqh-pql`**
- Contains: Layers 3, 3B
- Test: Do PQH/PQL lines update at quarter boundaries?

### Phase 3: SSMT Detection
**`phase-3-ssmt`**
- Contains: Layers 1, 2, 3B, 4
- Test: Does SSMT fire only on true divergence?

### Phase 4: PSP Detection
**`phase-4-psp`**
- Contains: Layers 4 (dependency), 5
- Test: Does PSP confirm SSMT correctly?

### Phase 5: Annotations & True Open
**`phase-5-annotations-true-open`**
- Contains: Layers 10, 11
- Test: Do annotations and True Open lines appear correctly?

### Phase 6: SMT Fill
**`phase-6-smtfill`**
- Contains: Layers 1, 2, 6
- Test: Does SMT Fill detect synced FVG gaps?

### Phase 7: Filters
**`phase-7-filters`**
- Contains: Layers 7, 8
- Test: Do session and HTF filters work?

### Phase 8: Decision Engine
**`phase-8-decision-engine`**
- Contains: Layers 4–9
- Test: Does confluence score 1–5 correctly?

### Phase 9: Dashboard & Alerts
**`phase-9-dashboard-alerts`**
- Contains: Layers 1–14 (all)
- Test: Does dashboard display all metrics and fire alerts?

---

## Branch File Structure

Each layer/phase branch contains:

```
.<branch>/
├── src/
│   ├── indicators/
│   │   └── layer_X.pine      # Pine Script indicator code
│   ├── libraries/
│   │   └── helpers.pine      # Shared utility functions
│   └── utils/
│       └── constants.pine    # Configuration & constants
├── tests/
│   ├── LAYER_README.md       # Layer overview & logic
│   ├── test_checklist.md     # Phase-specific test steps
│   ├── backtest_results/
│   │   └── results_X.csv     # Backtest data
│   └── unit_tests/
│       └── layer_X_test.md   # Manual test scenarios
└── docs/
    └── PHASE_README.md       # Phase documentation
```

---

## Quick Start

### For Developers

1. **Clone repository**
   ```bash
   git clone https://github.com/kwasiyeboahme-boop/quarterly-theory-ssmt-psp.git
   cd quarterly-theory-ssmt-psp
   ```

2. **Check out a layer**
   ```bash
   git checkout layer-4-ssmt-detection
   ```

3. **Copy Pine Script**
   ```bash
   cat src/indicators/layer_4.pine
   ```

4. **Paste into TradingView Pine Editor**
   - Go to TradingView Pine Editor
   - Create new indicator
   - Paste code
   - Click "Add to Chart"

5. **Test using checklist**
   - Open `tests/test_checklist.md`
   - Run each test step
   - Log results in `tests/backtest_results/`

6. **Commit & tag**
   ```bash
   git add -A
   git commit -m "Layer 4: SSMT detection — tests passing"
   git tag v0.4
   ```

### For Traders

1. **Check out main branch**
   ```bash
   git checkout main
   ```

2. **Copy complete Pine Script**
   ```bash
   cat src/indicators/quarterly_theory_v1.0.pine
   ```

3. **Paste into TradingView**
   - Add to chart
   - Configure settings (Cycle, Triad, Session)
   - Watch for SSMT/PSP signals

---

## Dependency Map

```
Layer 1 (Cycle)            Layer 2 (Triad)
    ↓                           ↓
Layer 3 (Quarter)  ←────────────┘
    ↓
Layer 3B (PQH/PQL)
    ↓
Layer 4 (SSMT) ← Layers 1, 2, 3B
    ↓
Layer 5 (PSP) ← Layer 4
    ├→ Layer 11 (Annotations)
Layer 6 (SMT Fill) ← Layers 1, 2
    ├→ Layer 11 (Annotations)
Layer 7 (Session) ← Layer 3
Layer 8 (HTF) ← Layer 4
    ↓
Layer 9 (Decision) ← Layers 4, 5, 6, 7, 8
Layer 10 (True Open) → Layer 11
Layer 12 (Bands) ← Layer 3
Layer 13 (Dividers) ← Layer 3
    ↓
Layer 14 (Dashboard) ← All
```

---

## Git Commands Reference

### Switch branches
```bash
git checkout layer-4-ssmt-detection
git checkout phase-3-ssmt
git checkout main
```

### Create new branch from existing
```bash
git checkout -b my-branch layer-4-ssmt-detection
```

### Merge layer to phase
```bash
git checkout phase-3-ssmt
git merge layer-4-ssmt-detection
```

### Merge phase to main
```bash
git checkout main
git merge phase-3-ssmt
```

### Tag release
```bash
git tag v0.3
git push origin v0.3
```

### View branch history
```bash
git log --oneline --graph --all
```

---

## Contact

**Repository Owner:** kwasiyeboahme-boop
**Platform:** TradingView
**Language:** Pine Script v5

---

_Branch Guide v1.0 | Quarterly Theory SSMT/PSP System_