# NSC First-Pitch Dataset

This repository contains first-pitch batting data from the 2026 National Softball Championship. Every plate appearance's first pitch was manually recorded from the scorer's booth and digitized into a structured dataset.

The dataset includes games from nine provincial teams only. Games involving team Hong Kong team, team Nanjing, and games whose live broadcasts were canceled due to weather conditions are excluded.

## Data structure

`2026垒球锦标赛.xlsx` — a single matrix on `Sheet1`:

- **Rows** = batters, grouped by team (team label in column A, batter initials + handedness in column B, e.g. `syj(L)`)
- **Columns** = pitchers, grouped by team (team label in row 1, pitcher initials in row 2)
- **Cell** = the first pitch of the batter's *n*-th plate appearance against that pitcher; a pitcher spans multiple columns, one per PA in chronological order
- Row and column team blocks share the same order, so each team's own (empty) block falls on the diagonal — batters never face their own pitchers

## Notation

Each cell is `[swing][outcome]`:

| Prefix | Meaning |
|---|---|
| `0` | no swing |
| `1` | swing |

| Code | Meaning |
|---|---|
| `B` | ball (taken) |
| `S` | strike — called if `0S`; swinging miss **or** foul if `1S` |
| `F` | fly/line out (not distinguished) |
| `FO` | force out / groundout |
| `FC` | fielder's choice |
| `SH` | sacrifice bunt |
| `E` | reached on error |
| `T` | bunt single |
| `1B` `2B` `3B` `HR` | single / double / triple / home run |
| `HBP` | hit by pitch |
| `ibb` | intentional walk (no pitch thrown — excluded from all rates) |
| `1ib` | illegal batting |
| `NA` | missing data (excluded) |

`0B` / `0S` / `1S` mean the plate appearance continued past the first pitch; its final outcome is **not** recorded in this dataset.

## Metric definitions

- **Strike** = `0S` + every swing (`1S` and all balls in play), following the MLB F-Strike% convention. Note this slightly overstates the true zone rate, since swings at balls out of the zone also count. "Strike rate on takes" (`0S / (0S + 0B + HBP)`) is the umpire-only view.
- **On base** = `1B` / `2B` / `3B` / `HR` / `FC`. Reaching on error (`E`) and `HBP` are *not* counted; edit the `ONBASE` set to change this.
- **Denominators**: league-wide rates use **all** valid first pitches (no minimum-sample filter — every pitch is a real event). Per-player tables apply display thresholds only: pitchers n ≥ 30, batters n ≥ 10 (`MIN_N` in the scripts). Individual on-base rates are still noisy at these sample sizes, so raw counts are printed alongside percentages.

## Scripts

| Script | Output |
|---|---|
| `1_strike_rate.py` | league first-pitch strike rate, and strike rate on takes |
| `2_swing_rate.py` | league first-pitch swing rate |
| `3_onbase_rate.py` | league first-pitch on-base rate (per PA and per ball in play) |
| `4_pitchers.py` | per-pitcher strike% and on-base% against, sorted by strike% (n ≥ 30) |
| `5_batters.py` | per-batter swing% and on-base%, sorted by sample size (n ≥ 10) |

Each script is standalone. Set `FILE` at the top to the spreadsheet path (default assumes the same directory).

## Usage

```
pip install openpyxl
python -X utf8 1_strike_rate.py
```

The `-X utf8` flag avoids garbled Chinese team names on Windows consoles (or set `PYTHONUTF8=1` once).

## Headline results

From 1,717 valid first pitches:

| Metric | Value |
|---|---|
| First-pitch strike rate | 58.5% |
| Strike rate on takes | 36.8% |
| First-pitch swing rate | 34.3% |
| First-pitch on-base rate (per PA) | 6.3% |
| First-pitch on-base rate (per ball in play) | 37.0% |
