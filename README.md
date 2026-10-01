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

🎯 Project Overview
Football players are often labelled as “selfish” when they choose to shoot instead of passing.
However, judging a decision only by its outcome can be misleading:
A missed shot does not necessarily imply a poor decision, and a goal does not always imply an optimal one.

This project reframes that subjective football discussion as a data-science problem by comparing the expected value of an actual shot with the historical expected value of continuing the possession through a pass.
Research Question
Can we quantify and compare player-level shooting decision quality using expected value differences derived from match-event data?

📊 Data at a Glance
Data Layer	Scale	Purpose
Premier League match results	3,800 matches / 10 seasons	Match-level historical context
StatsBomb match data	418 matches	Match metadata
StatsBomb event data	1,443,184 events	Event-level analysis
Shot table	10,837 shots	Actual shooting decisions
Pass table	404,785 passes	Passing behaviour
Pass → shot sequences	52,180	Estimate alternative passing value
Pitch representation	16 zones	Spatial aggregation


Coverage note: the ten-season window refers to match-level historical results. StatsBomb Open Data provides a smaller Premier League event-level window, so the two sources do not have identical temporal coverage.

🧠 Core Idea
For every shot, the project compares:
- xG_shot — expected-goal value of the shot actually taken
- xG_pass — estimated historical expected value of continuing possession through a pass from the same pitch zone
The central comparison is:
Δ = xG_pass - xG_shot
Interpretation
Δ > 0  → historical passing continuation had higher expected value
Δ < 0  → the shot itself had higher expected value
The project then extends this comparison using contextual information:
Decision Cost = Δ × Context Factor
The original framework considers contextual variables including:
Match Time
Score State
Recent Form
League Position
🔄 Methodology
<p align="center">
  <a href="Data%20preparation%20pipeline.png">
    <img
      src="Data%20preparation%20pipeline.png"
      alt="Data preparation pipeline"
      width="92%"
    >
  </a>
</p>

Premier League Results + StatsBomb Events
                    ↓
          Data Cleaning & Standardization
                    ↓
            Shot / Pass Extraction
                    ↓
              4 × 4 Pitch Zoning
                    ↓
     Same-Possession Pass → Shot Linking
                    ↓
       Passing-Alternative Estimation
                    ↓
             Match Context
                    ↓
             Decision Cost
                    ↓
           Player-Level Analysis
1. Event Extraction
StatsBomb data is stored as nested JSON.
The notebook transforms this into structured analytical tables by extracting fields including:
Event type
Team
Player
Possession
Shot xG
Shot outcome
Pitch coordinates
Pass destination
Match metadata
This produces separate shot and pass tables suitable for downstream analysis.
2. Spatial Feature Engineering
The StatsBomb pitch coordinate system is divided into a:
4 × 4 grid
producing:
16 pitch zones
Each shot and pass is assigned to one of these zones.
This allows the project to estimate historical passing value conditional on the spatial origin of the action.
3. Same-Possession Sequence Matching
For every pass, the pipeline searches for:
the next shot by the same team in the same match and the same possession.

This creates pass-to-shot sequences.
The successful execution identified:
52,180 pass → shot links
These sequences allow passes to inherit the xG of the subsequent shot.
4. Estimating the Passing Alternative
Pass-to-shot sequences are grouped by pitch zone.
For each zone:
xG_pass
=
Mean xG of subsequent shots
following passes from that zone
This creates a historical estimate of the expected value of continuing the possession instead of shooting immediately.
5. Shot vs Passing Alternative
Each shot is assigned the xG_pass corresponding to its pitch zone.
The pipeline then calculates:
delta = xG_pass - shot_xg
Example interpretation:
shot_xg = 0.03
xG_pass = 0.11

delta = +0.08
This indicates that historical possession continuations from that zone produced a higher expected value than the actual shot.
Conversely:
shot_xg = 0.57
xG_pass = 0.11

delta = -0.46
suggests the actual shot represented the higher-value option.
🧩 Feature Engineering
<p align="center">
  <a href="Feature%20engineering.png">
    <img
      src="Feature%20engineering.png"
      alt="Feature engineering"
      width="92%"
    >
  </a>
</p>

The feature-engineering stage combines:
Spatial Features
Shot location
Pass location
Pitch zone
Expected-Value Features
shot_xg
xG_pass
delta
Match-State Features
Minute
Score difference before shot
Recent form
League position
Final Metric
Decision Cost
=
delta × context_factor
⚽ Score-State Reconstruction
The project reconstructs the score immediately before each shot.
Goal events are sorted chronologically within each match and accumulated to obtain:
Home goals before shot
Away goals before shot
The shooter-relative score difference is then calculated as:
score_diff_before_shot
This distinguishes situations such as:
Trailing
Drawing
Leading
and enables match-state-sensitive analysis.
📈 Decision Cost
The project defines several interpretable context factors:
minute_factor
score_factor
ranking_factor
form_factor
These are multiplied together:
context_factor
=
minute_factor
× score_factor
× ranking_factor
× form_factor
Finally:
decision_cost
=
delta × context_factor
The final analytical table is structured as:
One row = one shot decision

✨ Technical Highlights
- Processed 1.44M+ nested football event records
- Extracted 10.8K+ shot events
- Extracted 404K+ pass events
- Linked 52K+ same-possession pass-to-shot sequences
- Engineered a 4 × 4 spatial representation of the pitch
- Estimated historical alternative value using zone-level xG_pass
- Compared shot_xg against passing-continuation value
- Reconstructed score state before individual shots
- Integrated match-level and event-level football data
- Built an interpretable expected-value-based Decision Cost framework
📦 Project Artifacts
📓 Jupyter Notebook
The repository includes the full implementation notebook with preprocessing, feature engineering, and saved outputs.
📄 Full Execution Output
The repository also contains the preserved Google Colab execution output as a PDF.
This is useful because the project depends on public external data endpoints, and later local reruns may encounter network-side connection resets even though the original execution completed successfully.
🎤 Project Presentation
The presentation provides a visual summary of:
Research Question
Data Sources
Data Preparation
Feature Engineering
Decision Framework
⚠️ Limitations
This project is an exploratory football decision-analysis framework, not a production predictive model.
Passing alternative is a proxy
xG_pass represents a historical zone-level expected value.
It does not prove that a specific teammate or passing lane was actually available at the exact moment of the shot.
The event dataset does not contain complete player-tracking information such as:
Defender locations
Teammate positioning
Body orientation
Passing-lane obstruction
Off-ball movement
Different source coverage
The match-level historical data and StatsBomb event-level data cover different season windows.
Therefore, cross-source contextual joins depend strongly on:
Season alignment
Team-name normalization
Date alignment
The archived output shows that recent-form and league-position fields were not populated for some retained event-level rows. In those affected rows, the notebook's fallback logic uses neutral context weights.
For this reason, the most directly supported part of the analysis is:
Shot xG
       vs
Historical Passing-Alternative xG
       +
Pre-shot Score State
rather than interpreting the contextual weighting as a definitive causal measure.
🛠️ Tech Stack
Python
Pandas
NumPy
Requests
Jupyter Notebook
Google Colab
StatsBomb Open Data
Football-Data.co.uk
👥 Team & Acknowledgements
This project was completed as a team project for SC3021 Data Science.
Team
- Zi Feng
- Zhi You
- Yang
Special thanks to Zhi You and Yang for their collaboration and contributions throughout the project, including data preparation, analysis, discussion, and presentation development.
The project also makes use of publicly available football data from:
- StatsBomb Open Data
- Football-Data.co.uk
<p align="center">
  <strong>SC3021 Data Science Project</strong>
</p>

<p align="center">
  Turning a subjective football debate into an event-level expected-value analysis.
</p>
