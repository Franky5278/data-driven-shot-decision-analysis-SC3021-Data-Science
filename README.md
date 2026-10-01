<p align="center">
  <img src="assets/hero.jpg" alt="Selfish Player — Data-Driven Shot Decision Analysis" width="100%">
</p>

<h1 align="center">⚽ Data-Driven Shot Decision Analysis</h1>

<p align="center">
  <b>From “Selfish” to Optimal — evaluating football shot choices with expected goals, possession sequences, and match context.</b>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/Python-3.x-3776AB?logo=python&logoColor=white" alt="Python">
  <img src="https://img.shields.io/badge/Pandas-Data%20Analysis-150458?logo=pandas&logoColor=white" alt="Pandas">
  <img src="https://img.shields.io/badge/Jupyter-Notebook-F37626?logo=jupyter&logoColor=white" alt="Jupyter">
  <img src="https://img.shields.io/badge/StatsBomb-Open%20Data-111111" alt="StatsBomb">
  <img src="https://img.shields.io/badge/SC3021-Data%20Science-B7FF00" alt="SC3021">
</p>

<p align="center">
  <a href="#overview">Overview</a> •
  <a href="#data-at-a-glance">Data</a> •
  <a href="#methodology">Methodology</a> •
  <a href="#project-output">Output</a> •
  <a href="#repository-structure">Repository</a>
</p>

Overview
Football discussions often label a player as “selfish” when they shoot instead of passing. This project reframes that subjective judgment as a data-science problem: can shot-selection quality be quantified using expected-goal value, possession history, spatial context, and match state?
Research question: Can we quantify and compare player-level shooting decision quality using an aggregated Decision Cost derived from match-event data?

The core idea is simple: compare the expected value of the actual shot with a historically estimated passing alternative from the same pitch zone, then adjust that difference using contextual pressure features.
Data at a Glance
Data layer	Coverage in the archived run	Role
Premier League match results	3,800 matches / 10 seasons	Recent form and league-table context
StatsBomb event data	418 matches / 1,443,184 events	Shots, passes, possession, xG, locations
Shot table	10,837 shots	Actual shooting decisions
Pass table	404,785 passes	Candidate passing sequences
Same-possession pass → shot links	52,180 sequences	Estimate passing-alternative value
Spatial representation	16 pitch zones	Aggregate historical xG_pass


Coverage note: the ten-season window applies to match-level results. StatsBomb's open Premier League event coverage in this project contains 418 matches from the available EPL seasons, so event-level and match-level coverage are not identical.

Methodology
<p align="center">
  <img src="assets/pipeline.jpg" alt="Data preparation pipeline" width="92%">
</p>

The analysis follows a reproducible event-processing pipeline:
Match Results + StatsBomb Event Data
                ↓
      Cleaning & Standardization
                ↓
        Shot / Pass Extraction
                ↓
          Pitch Zoning (4 × 4)
                ↓
 Same-Possession Pass → Shot Linking
                ↓
     Passing-Alternative Estimation
                ↓
       Match-Context Features
                ↓
            Decision Cost
1. Event extraction
Nested StatsBomb JSON is flattened into structured match, shot, and pass tables. Each shot keeps its location, shooter, xG, time, team, possession, and match metadata.
2. Passing-alternative estimation
For each pass, the pipeline searches for the next shot by the same team in the same possession. These pass-to-shot sequences are grouped by pitch zone to estimate a historical passing value:
xG_pass = mean xG of subsequent shots following passes from that zone
This is a proxy for the expected value of choosing to continue the possession rather than shooting immediately.
3. Shot-vs-pass comparison
For each shot:
Δ = xG_pass − xG_shot
- Δ > 0 → the historical passing alternative had higher expected value.
- Δ < 0 → the shot itself had higher expected value.
4. Context-aware decision cost
The project defines a contextual weighting framework using match time, score state, recent form, and league position:
Decision Cost = Δ × Context Factor
<p align="center">
  <img src="assets/feature_engineering.jpg" alt="Feature engineering" width="92%">
</p>

Project Output
The repository keeps the original course notebook together with two lightweight review artifacts:
- 📓 Notebook: [`notebooks/shot_decision_analysis.ipynb`](notebooks/shot_decision_analysis.ipynb) — full implementation and saved notebook outputs.
- 📄 Full execution output: [`docs/full_output.pdf`](docs/full_output.pdf) — preserved Colab execution trace and intermediate results.
- 🎤 Presentation: [`docs/presentation.pdf`](docs/presentation.pdf) — visual summary of the research question, data preparation, and feature engineering.
The PDF output is intentionally included because the workflow depends on public external data endpoints; it preserves a successful execution artifact even when a later local rerun encounters network-side connection issues.
Key Technical Features
- Multi-source data integration across event-level and match-level football data.
- 1.44M+ event processing from nested StatsBomb JSON.
- Possession-aware sequence matching to connect passes with subsequent shots.
- Spatial feature engineering using a 4 × 4 pitch-zone representation.
- Expected-value comparison between shot_xg and historical xG_pass.
- Context engineering from time, score state, recent form, and league position.
- Transparent analytical metric through an interpretable Decision Cost formulation.
Repository Structure
data-driven-shot-decision-analysis/
│
├── README.md
├── assets/
│   ├── hero.jpg
│   ├── pipeline.jpg
│   └── feature_engineering.jpg
│
├── notebooks/
│   └── shot_decision_analysis.ipynb
│
└── docs/
    ├── presentation.pdf
    └── full_output.pdf
Limitations
This project is an exploratory decision-analysis framework rather than a production football model. xG_pass is a historical zone-level proxy, not a direct observation of an available passing lane. The data does not include full tracking information such as defender positions, teammate availability, body orientation, or passing-lane obstruction. In addition, event-level and match-level sources have different season coverage, so contextual cross-source joins are sensitive to season and team-name alignment.
These limitations are important when interpreting Decision Cost: it should be read as a transparent analytical construct for comparing shot decisions, not as a definitive measure of player intent.
Tech Stack
Python · Pandas · NumPy · Requests · Jupyter / Google Colab · StatsBomb Open Data · Football-Data.co.uk
<p align="center">
  <b>SC3021 Data Science Project</b><br>
  Turning a subjective football debate into an event-level expected-value analysis.
</p>
