# AmaziWatch: AI-Based Pipe Burst Detection for Kigali's Water Network

**04-655 A: AI for Engineers, Fall 2026 · Group 9 (Water Systems)**
Carlos Ngoga · Amos Akogbe · Abdulmujeeb Tiamiyu · Jok Akech Atem Mabior · George Ocran

AmaziWatch reads pressure and flow sensors and flags a likely pipe burst. It also names the zone the burst is in (A–D), so a WASAC technician can respond sooner. The model has to tell real bursts apart from the pressure swings caused by Kigali's water rationing.

---

## Where we are (midterm, Week 12)

Results on the 50 test days:

| Model | Supply | Detection | False alarms | Zone accuracy | Hours to detect |
|---|---|---|---|---|---|
| Fixed threshold | Steady | 55% | 17% | 47% | 3.1 |
| Fixed threshold | Rotation | 65% | 46% | 22% | 2.0 |
| Random Forest | Steady | 91% | 0.4% | 86% | 0.5 |
| Random Forest | Rotation | 85% | 2.0% | 86% | 0.7 |

With rationing switched on, the threshold alarm's false-alarm rate almost triples. The Random Forest keeps false alarms at 2%. These are simulated results and are likely optimistic.

---

## Repo layout

```
amazi-watch/
├── README.md               ← you are here
├── .gitignore
├── data/
│   ├── wasac/              ← real WASAC data, git-ignored (see data/wasac/README.md)
│   └── simulated/          ← generated sensor data, git-ignored (regenerate it)
├── sim/                    ← WNTR simulation: L-Town, zones, rotation, bursts
├── models/                 ← threshold baseline + Random Forest
├── eval/                   ← metrics, tables, figures
├── demo/                   ← alert demo (confirm / dismiss)
└── report/                 ← LaTeX for the final report
```

> Folder names are a proposal. Move your existing code into them, or update this tree to match.

## Getting the data

- **WASAC data:** shared for academic use only, and we report aggregated results only. **Never commit it.** Get it from the team Drive and follow [`data/wasac/README.md`](data/wasac/README.md).
- **Simulated data:** regenerate it locally with the scripts in `sim/`.

## Setup

```bash
python -m venv .venv && source .venv/bin/activate
pip install wntr scikit-learn pandas numpy matplotlib openpyxl
```

<!-- TODO: add the actual run commands, e.g.
python sim/run_simulation.py --days 160 --rotation
python models/train.py
python eval/evaluate.py
-->

---

## ✅ Done

- [x] Proposal submitted
- [x] WASAC data received: 94 Kimironko leak records and the Remera rationing schedules
- [x] WASAC data cleaned: timestamps parsed (81 records with report and repair times), areas split out of the schedules
- [x] EDA: reports by hour, time to repair, days with water per area (Figure 1)
- [x] L-Town (BattLeDIM) simulated in WNTR: 4 zones at heights from −8 to +18 m
- [x] Rotation from WASAC (A always on; B 5, C 3, D 2 days a week)
- [x] Bursts on 65% of days, placed by zone using the WASAC records, 4–15 L/s
- [x] 160 days at 15-minute steps, simulated with the rotation and with steady supply
- [x] Noise added, 1-hour windows, features (mean, min, change from the previous hour, difference from the median, inflow, hour, supply flags)
- [x] Data split by whole days in time order (90 / 20 / 50), so no test day leaks into training
- [x] Fixed-threshold baseline, tuned on train and validation
- [x] Random Forest for detection and zone in one model
- [x] Threshold vs Random Forest compared under steady supply and rotation (Table 2)
- [x] Midterm report and presentation

## 🔲 To do

Put your name next to anything you pick up. Items marked **[F]** come from the midterm evaluation feedback.

### Week 13: Make the results solid

**Evaluation**
- [ ] **[F]** Add precision, recall and F1 for each model — _owner:_
- [ ] **[F]** Report the results for each zone (A/B/C/D) separately, not only overall — _owner:_
- [ ] **[F]** Break out results for hours when rationing and a burst happen together — _owner:_
- [ ] Error analysis: what the Random Forest misses, and why (2 of 39 burst days) — _owner:_

**Models**
- [ ] **[F]** Add a simpler ML comparison: logistic regression and/or a single decision tree — _owner:_
- [ ] Class weighting (or oversampling) for zone D, which has only 28 windows — _owner:_
- [ ] Feature importance: show that the model uses inflow and the supply flags — _owner:_

**Simulation**
- [ ] **[F]** More burst days and more variety: sizes, locations, start times — _owner:_
- [ ] **[F]** Test with a different rotation (use another WASAC week) — _owner:_
- [ ] Sensitivity tests: more noise, smaller leaks, fewer sensors — _owner:_
- [ ] Bursts that end before midnight, so that 41% of windows are no longer labelled as leaks — _owner:_
- [ ] **[F]** Write down how close the simulation is to Kigali and where it falls short (topology, heights, rotation, burst sizes) — _owner:_

**WASAC**
- [ ] **[F]** Ask WASAC Remera for bulk meter or reservoir outflow readings — _owner:_

### Week 14: Demo and final report

- [ ] **[F]** Alert demo that replays simulated data and shows, for each alert, the predicted event, the zone, the confidence and the sensor readings behind it. The technician can **confirm** or **dismiss** the alert. — _owner: Abdulmujeeb_
- [ ] Make the whole pipeline run from one command (sim → train → eval) — _owner:_
- [ ] Final report: update the results and add the new metrics, per-zone tables and limitations — _owner: Carlos +_
- [ ] Respond to the evaluator: the Random Forest vs threshold comparison already exists (their numbers came from an earlier version), so point to Table 2 — _owner:_
- [ ] Slides — _owner:_

### Week 15

- [ ] Final presentation — _all_

---

## Rules for the repo

- Never commit WASAC data or paste individual records anywhere. Aggregated figures only.
- Don't commit large generated CSVs. Commit the scripts and settings that produce them.
- Fix random seeds so everyone gets the same numbers.
- Don't tune thresholds or model settings on test days.
