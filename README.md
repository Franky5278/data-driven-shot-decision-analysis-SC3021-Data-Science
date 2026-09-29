# Data-Driven Shot Decision Analysis

## Overview

This project investigates football shot-selection decisions using multi-source event data and expected-goals (xG) analysis.

The goal is to evaluate whether a player's decision to shoot was reasonable relative to available passing alternatives within the same possession.

## Dataset

The analysis combines:

- 10 seasons of Premier League match records
- 1.44M+ StatsBomb event records
- 418 matches
- 10.8K+ shots
- 404K+ passes
- 52K+ same-possession pass-to-shot sequences

## Research Question

For a given shooting opportunity:

> Was shooting the best available decision, or was there a potentially better passing alternative?

## Methodology

The pipeline includes:

1. Multi-source data ingestion
2. Event cleaning and preprocessing
3. Shot and pass extraction
4. Same-possession sequence construction
5. xG-based shot-value estimation
6. Passing-alternative estimation
7. Match-state contextual analysis
8. Player-level decision evaluation

## Decision Framework

For each shot, the analysis compares:

- Actual shot xG
- Estimated value of available passing alternatives
- Match-state context
- Possession sequence information

The resulting framework is used to quantify and rank shooting decisions at the player level.

## Pipeline

```text
Premier League Data + StatsBomb Events
                ↓
        Data Preprocessing
                ↓
       Shot / Pass Extraction
                ↓
 Same-Possession Sequence Matching
                ↓
           xG Analysis
                ↓
 Passing-Alternative Estimation
                ↓
     Decision-Quality Evaluation
                ↓
        Player-Level Analysis
