# Quarterly Theory SSMT / PSP Dashboard

**Pine Script v5 | TradingView | Layer-by-Layer Build**

## Project Overview

A sophisticated multi-asset trading system that detects **SSMT (Sequential SMT)** divergences, confirms with **PSP (Precision Swing Points)**, and scores confluence (1–5) to output precise trade decisions.

### Key Metrics
- **14 Layers** of analysis
- **9 Test Phases** for validation
- **3 Asset Triads** (Forex, Bonds, Indices)
- **5 Time Cycles** (Monthly → Micro)
- **Confluence Score 1–5** (PRIME SETUP → PASS)

## Repository Structure

```
quarterly-theory-ssmt-psp/
├── main                           # Production-ready code
├── layer-1-cycle-timeframe-mapping
├── layer-2-triad-selection
├── layer-3-quarter-boundary-detection
├── layer-3b-pqh-pql-levels
├── layer-4-ssmt-detection
├── layer-5-psp-detection
├── layer-6-smtfill-logic
├── layer-7-session-timefilter
├── layer-8-htf-ssmt-validation
├── layer-9-confluence-decision-engine
├── layer-10-true-open-lines
├── layer-11-chart-annotations
├── layer-12-quarter-background-bands
├── layer-13-quarter-dividers
├── layer-14-dashboard-alerts
├── phase-1-quarter-visualisation
├── phase-2-pqh-pql
├── phase-3-ssmt
├── phase-4-psp
├── phase-5-annotations-true-open
├── phase-6-smtfill
├── phase-7-filters
├── phase-8-decision-engine
└── phase-9-dashboard-alerts
```

## Build Roadmap

### Phase 1: Quarter Visualization (Layers 12–13)
- [ ] Draw vertical dividers per cycle
- [ ] Background fill per Q phase (colors: Blue/Green/Pink/Orange)
- [ ] Quarter labels (D-Q1, W-Q2, etc.)

### Phase 2: PQH / PQL (Layer 3B)
- [ ] Calculate previous quarter high/low per symbol
- [ ] Draw dashed horizontal reference lines
- [ ] Recalculate at new quarter boundary

### Phase 3: SSMT Detection (Layer 4)
- [ ] Divergence logic across correlated assets
- [ ] SSMT annotation on chart
- [ ] Forex inverse rule (DXY opposite to pairs)

### Phase 4: PSP Detection (Layer 5)
- [ ] 3-candle crack in correlation
- [ ] PSP marker at middle candle
- [ ] Entry TF mapping per cycle

### Phase 5: Annotations & True Open (Layers 10–11)
- [ ] True Open horizontal lines
- [ ] LRL diagonal line
- [ ] NEWS event box

### Phase 6: SMT Fill (Layer 6)
- [ ] FVG detection on both assets
- [ ] Fill / fail detection
- [ ] Gap sync validation

### Phase 7: Filters (Layers 7–8)
- [ ] Session time filter
- [ ] HTF SSMT filter

### Phase 8: Decision Engine (Layer 9)
- [ ] Confluence scoring 1–5
- [ ] PRIME / HIGH / ENTER / WAIT / PASS

### Phase 9: Dashboard & Alerts (Layer 14)
- [ ] Dashboard table with all metrics
- [ ] 8 alert conditions

## Asset Triads

| Triad | Symbol A | Symbol B | Symbol C | Note |
|-------|----------|----------|----------|------|
| Forex | DXY | EURUSD | GBPUSD | DXY inverse to pairs |
| Bonds | ZB1! | ZN1! | ZF1! | Duration spread |
| Indices | ES1! | NQ1! | YM1! | Risk-on / risk-off |

## Time Cycles & Timeframes

| Cycle | Confirmation TF | Entry TF |
|-------|-----------------|----------|
| Monthly | 4H | 1H |
| Weekly | 1H | 15M |
| Daily | 15M | 5M |
| 90M | 5M | 1M |
| Micro | 1M | 30s / Tick |

## Quick Start

1. **Select a Layer** from the branch list
2. **Review LAYER_README.md** in that branch
3. **Copy Pine Script code** from `src/indicators/`
4. **Paste into TradingView Pine Editor**
5. **Test using checklist** in `tests/test_checklist.md`
6. **Merge to main** when phase completes

## Documentation

- `ARCHITECTURE.md` — Full system design
- `BUILD_ROADMAP.md` — Detailed phase breakdown
- `BRANCH_GUIDE.md` — How to navigate branches
- `QUARTERLY_THEORY.md` — Time cycle reference

## Contact & Support

**Author:** kwasiyeboahme-boop
**Platform:** TradingView
**Language:** Pine Script v5

---

_Quarterly Theory SSMT/PSP System | v1.0_