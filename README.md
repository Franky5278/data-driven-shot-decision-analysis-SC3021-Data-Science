<h1 align="center">⚽ Data-Driven Shot Decision Analysis</h1>

<p align="center">
  <strong>
    From “Selfish” to Optimal — Evaluating Football Shot Decisions
    with Expected Goals, Possession Sequences, and Match Context
  </strong>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/Python-3.x-3776AB?logo=python&logoColor=white">
  <img src="https://img.shields.io/badge/Pandas-Data%20Analysis-150458?logo=pandas&logoColor=white">
  <img src="https://img.shields.io/badge/Jupyter-Notebook-F37626?logo=jupyter&logoColor=white">
  <img src="https://img.shields.io/badge/StatsBomb-Open%20Data-111111">
  <img src="https://img.shields.io/badge/SC3021-Data%20Science-B7FF00">
</p>

<p align="center">
  <a href="SC3021_GP11_LAB2_16_15.ipynb"><b>📓 Notebook</b></a>
  &nbsp;•&nbsp;
  <a href="SC3021_Shot_Decision_Presentation_compressed.pdf"><b>🎤 Presentation</b></a>
  &nbsp;•&nbsp;
  <a href="SC3021%20GP11%20-%20Colab%20lab2%20overall%20output.pdf"><b>📄 Full Output</b></a>
</p>

---

## 🎯 Project Overview

Football players are often labelled as **“selfish”** when they choose to shoot instead of passing.

However, judging a decision only by its outcome can be misleading:

> A missed shot does not necessarily imply a poor decision, and a goal does not always imply an optimal one.

This project reframes that subjective football discussion as a **data-science problem** by comparing the expected value of an actual shot with the historical expected value of continuing the possession through a pass.

### Research Question

> **Can we quantify and compare player-level shooting decision quality using expected value differences derived from match-event data?**

---

## 📊 Data at a Glance

The archived successful run processed multiple layers of Premier League data.

| Data Layer | Scale | Purpose |
|---|---:|---|
| Premier League match results | **3,800 matches / 10 seasons** | Match-level historical context |
| StatsBomb match data | **418 matches** | Match metadata |
| StatsBomb event data | **1,443,184 events** | Event-level analysis |
| Shot table | **10,837 shots** | Actual shooting decisions |
| Pass table | **404,785 passes** | Passing behaviour |
| Pass → shot sequences | **52,180** | Estimate alternative passing value |
| Pitch representation | **16 zones** | Spatial aggregation |

The successful run produced 1.44M+ StatsBomb events, 10,837 shots, 404,785 passes, and 52,180 pass-to-shot sequences. :chatgpt-content-reference{index="0"} :chatgpt-content-reference{index="1"} :chatgpt-content-reference{index="2"}

> **Coverage note:** the ten-season window refers to match-level historical results.  
> StatsBomb Open Data provides a smaller Premier League event-level window, so the two sources do not have identical temporal coverage.

---

## 🧠 Core Idea

For every shot, the project compares:

- `xG_shot` — expected-goal value of the shot actually taken
- `xG_pass` — estimated historical expected value of continuing possession through a pass from the same pitch zone

The central comparison is:

```text
Δ = xG_pass - xG_shot
