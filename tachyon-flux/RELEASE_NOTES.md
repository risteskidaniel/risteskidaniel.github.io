# Tachyon Flux release notes

The honest log of Tachyon Flux (internal name: Project Overlord): what we built, what failed, and what changed. Newest first.

---

## 2026-11-14 · International results and scorecard

**Added**
- Final scores for international matches (Nations League, AFCON and CONCACAF qualifiers), loaded automatically with every evening update.
- New scorecard group: **Live internationals (Nov 2026)**. These forecasts come from a separate national-team model (internal name xcomp), not from the club model, so they are scored on their own and never mixed into the club numbers.

**Fixed**
- The header showed "ClubElo ratings from Invalid Date"; it now shows the date of the ratings file.
- On matches that are not candidate picks, the "What is a candidate pick?" text showed a raw placeholder instead of the explanation.

**Behind the scenes (betting checklist, not shown on the site)**
- Stricter rules for picks: only 1X2 and 0–2 / 3+ goals; price 1.30–2.00; model and market within 8 points; no "0–2 goals" when the favourite is priced below 1.20; tighter limits for internationals. Last week's 20 legs went 12/20, exactly what the model expected (12.1), but the prices were too short to make money; under the new rules none of them would have been played.

---

## 2026-11-08 · Tachyon Flux goes public

**Added**
- Public match browser: choose a date, search a match, see how its forecast was built layer by layer, next to the result.
- 13,277 out-of-sample backtest forecasts (2024/25 season and July–December 2025) plus every live forecast since 3 November.
- Scorecard with calibration charts, and a "How the forecast is built" page.
- Export script that publishes only our own numbers: no odds, no prices, no bookmaker names.

**Weekend 7–8 November (live)**
- Saturday: 10 of 10 Top-20 legs right, against 6.8 expected. A roughly 2% outcome: good selection and good luck.
- Sunday: Brugge 3+, Djurgården and Midtjylland missed; Slavia 3+ came in. Variance works both ways.
- Market benchmark was the most accurate single layer on Saturday (Brier 0.082), then our model (0.095), then transformer and vector.

**Fixed**
- The most likely scoreline now comes from the same calibrated score grid as the 1X2 probabilities, so a 2–0 favourite no longer looks like it contradicts a 64% chance of 3+ goals.

---

## 2026-11-07 · Live layers and MC v3

**Added**
- MC v3: 300 MCMC posterior draws inside a 1,000,000-run minute-by-minute simulator, giving every probability a 90% range.
- Vector database (200 nearest past matches) and transformer (12-match sequences) as live second opinions.
- Probable line-ups, multilingual team news, injuries and a risk penalty for flags such as short rest or a new coach.

**Known limitations**
- Vector database: no clear gain in backtest, so it is a second opinion only.
- Transformer: level with the main model, so a second opinion only.
- Brownian drift and shock sizes in the simulator are assumptions, not yet measured.

---

## 2026-11-02 · Pure-1: two layers earned their place

**Added**
- Kalman ratings fed with chance quality (xG / shots on target) and ClubElo.
- Both beat Pure-0 on the same 8,912 matches of 2024/25: 1X2 log loss −0.0023 (90% interval −0.0032 to −0.0014), goals −0.0012.

**Failed (tested, not kept)**
- League-table stakes, weather, player-level data, two other Kalman variants and an alternative Dixon–Coles setup.

**Honest gap**
- The betting market is still more accurate than our model: about +0.013 in 1X2 log loss on 2024/25. We publish that number rather than hide it.

---

## 2026-11-02 · Pure-0: no odds anywhere

**Changed**
- Removed every market input. v1 had blended 50% bookmaker odds into its probabilities; Pure-0 uses none.

**Results (2024/25, 8,912 matches, never seen in training)**
- 1X2 log loss 1.0095; home, draw and away all calibrated (predicted 43.7 / 26.0 / 30.3%, observed 43.9 / 26.0 / 30.1%).
- High-confidence picks hold up: forecasts of 90–95% happened 92.8% of the time.

**Known limitations**
- 3+ goals ran 1.7 points high before a second recalibration fixed it.

---

## 2026-10-31 · v2: rebuilt from the foundation

**Why**
- v1 had more than 40 engine versions in a month and no reliable way to tell which change helped. Results could not be reproduced.

**Added**
- New database schema; every prediction stored with the run that made it.
- Leak-free walk-forward testing with fixed windows: fit 2023/24, confirm 2024/25, first half of 2026 sealed.
- Free data first: football-data.co.uk history, Open-Meteo weather, ClubElo; API-Football only for live data.

---

## 2026-10-13 → 10-31 · v1.x: more engines, same problem

**Added**
- Kalman ratings, Bayesian modifiers, a ChromaDB vector memory, squad-impact scoring, MCMC estimation, a 1,000,000-iteration Monte Carlo.

**Failed**
- Layers were added faster than they could be tested, and there was no fixed, sealed test window, so a good backtest could not be trusted.
- AI-assisted development without a written architecture kept losing agreed decisions and rewriting working code.

---

## 2026-09-16 → 10-04 · v1: the first engine

**Added**
- Dixon–Coles with time decay and ridge regularisation, a Monte Carlo simulator, accumulator builder, and 26,000+ historical matches.

**Failed**
- The raw model's 1X2 strike rate was only 35.3% in the August–September 2026 backtest.
- The fix was to blend 50/50 with bookmaker odds. That made the forecasts look better, but they were mostly the market's opinion, not ours.

**Lesson**
- If you need the market to look right, you have not built a model yet. That is why v2 removed the market entirely and earned every layer back.
