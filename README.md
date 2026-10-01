<h1 align="center">⚽ Data-Driven Shot Decision Analysis</h1>

<p align="center">
  <b>From “Selfish” to Optimal</b><br>
  Quantifying football shot-selection quality with xG, possession sequences, spatial features, and match context.
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
  &nbsp;&nbsp;•&nbsp;&nbsp;
  <a href="SC3021%20GP11%20-%20Colab%20lab2%20overall%20output.pdf"><b>📄 Full Output</b></a>
  &nbsp;&nbsp;•&nbsp;&nbsp;
  <a href="SC3021_Shot_Decision_Presentation_compressed.pdf"><b>🎤 Presentation</b></a>
</p>

🎯 Research Question
Can we quantify and compare player-level shooting decision quality using expected-value differences derived from match-event data?

The project compares the value of the shot actually taken with a historical estimate of the passing continuation available from the same pitch zone.
📊 Project at a Glance
Metric	Scale	Metric	Scale
Match-level history	3,800 matches	Historical window	10 seasons
StatsBomb matches	418	StatsBomb events	1,443,184
Shot events	10,837	Pass events	404,785
Pass → shot links	52,180	Pitch zones	16


Coverage note: the 10-season window applies to match-level results. StatsBomb event-level EPL coverage is smaller.

🔄 Pipeline
<p align="center">
  <a href="Data%20preparation%20pipeline.png">
    <img src="Data%20preparation%20pipeline.png" alt="Data preparation pipeline" width="94%">
  </a>
</p>

Stage	What happens	Output
1. Data ingestion	Load Premier League results + StatsBomb match/event data	Raw match and event tables
2. Cleaning	Flatten nested JSON, standardize fields, remove invalid records	Structured data
3. Event extraction	Separate shots and passes	Shot / pass tables
4. Spatial encoding	Divide pitch into a 4 × 4 grid	16 pitch zones
5. Sequence matching	Link each pass to the next same-team shot in the same possession	52K+ pass → shot links
6. Value estimation	Aggregate subsequent shot xG by pass-origin zone	xG_pass
7. Decision comparison	Compare xG_pass with actual shot_xg	delta
8. Context layer	Add score state, time, form, and league position	context_factor
9. Final metric	Weight expected-value difference by context	decision_cost


🧠 Decision Framework
Variable	Meaning
shot_xg	Expected-goal value of the shot actually taken
xG_pass	Historical mean xG of the next shot after passes from the same zone
delta	Difference between passing-continuation value and shot value
context_factor	Match-context weighting
decision_cost	Context-adjusted decision-value difference


Core comparison
delta = xG_pass - shot_xg
Result	Interpretation
delta > 0	Historical passing continuation had higher expected value
delta < 0	The shot itself had higher expected value


Contextual extension
decision_cost = delta × context_factor
🧩 Feature Engineering
<p align="center">
  <a href="Feature%20engineering.png">
    <img src="Feature%20engineering.png" alt="Feature engineering" width="94%">
  </a>
</p>

Feature Group	Examples	Purpose
Event features	shot xG, pass, possession, outcome	Describe each action
Spatial features	(x, y), pitch zone	Capture location-dependent value
Sequence features	same-possession pass → shot	Estimate continuation value
Match-state features	minute, score difference	Capture in-game pressure
Team-context features	recent form, league position	Add broader match context


⚽ Score-State Reconstruction
Goal events are ordered within each match to reconstruct the score immediately before each shot.
Derived Feature	Interpretation
score_diff_before_shot < 0	Shooting team is trailing
score_diff_before_shot = 0	Match is level
score_diff_before_shot > 0	Shooting team is leading


This allows decision quality to be interpreted relative to the state of the match rather than only by shot location.
📦 Repository Contents
File	Purpose
[`SC3021_GP11_LAB2_16_15.ipynb`](SC3021_GP11_LAB2_16_15.ipynb)	Main analysis notebook
[`SC3021 GP11 - Colab lab2 overall output.pdf`](SC3021 GP11 - Colab lab2 overall output.pdf)	Preserved successful execution output
[`SC3021_Shot_Decision_Presentation_compressed.pdf`](SC3021_Shot_Decision_Presentation_compressed.pdf)	Project presentation
[`Data preparation pipeline.png`](Data preparation pipeline.png)	Pipeline visual
[`Feature engineering.png`](Feature engineering.png)	Feature-engineering visual


✨ Key Technical Work
Area	Implementation
Data processing	1.44M+ nested StatsBomb events
Sequence modelling	52K+ same-possession pass → shot links
Spatial modelling	4 × 4 pitch-zone representation
Expected-value analysis	shot_xg vs historical xG_pass
Match context	Pre-shot score reconstruction
Data integration	Match-level + event-level football data
Final framework	Interpretable decision_cost metric


⚠️ Notes & Limitations
<details>
<summary><b>Click to expand</b></summary>


- xG_pass is a historical zone-level proxy, not direct evidence that a specific passing lane was open.
- Full tracking data is unavailable, so defender positions, teammate availability, body orientation, and passing-lane obstruction are not modelled.
- Match-level and event-level sources have different season coverage.
- Cross-source recent-form / league-position joins are therefore sensitive to season, date, and team-name alignment.
- The strongest directly supported component is the event-level comparison between shot_xg, historical xG_pass, and reconstructed pre-shot score state.
</details>

🛠️ Tech Stack
<p align="center">
  <img src="https://img.shields.io/badge/Python-3.x-3776AB?logo=python&logoColor=white">
  <img src="https://img.shields.io/badge/Pandas-150458?logo=pandas&logoColor=white">
  <img src="https://img.shields.io/badge/NumPy-013243?logo=numpy&logoColor=white">
  <img src="https://img.shields.io/badge/Jupyter-F37626?logo=jupyter&logoColor=white">
  <img src="https://img.shields.io/badge/Google%20Colab-F9AB00?logo=googlecolab&logoColor=white">
</p>

👥 Team
Member	Project
Zi Feng	SC3021 Data Science
Zhi You	SC3021 Data Science
Yang	SC3021 Data Science


<p align="center">
  Special thanks to <b>Zhi You</b> and <b>Yang</b> for their collaboration and contributions throughout the project.
</p>

<p align="center">
  <b>Turning a subjective football debate into an event-level expected-value analysis.</b>
</p>
