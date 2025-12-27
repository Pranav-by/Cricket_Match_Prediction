# 🏏 Cricket Match Prediction System (ODI)

> A complete, end-to-end deep learning project built from scratch using **Cricsheet data**, **PyTorch**, and **real cricket logic**.

---

## 📌 Project Objective

Build a **realistic and production-grade cricket analytics system** that can:

* Predict **match win probability**
* Learn **match momentum** from ball-by-ball data
* Serve as a base for **score prediction** and **player performance modeling**

This project strictly avoids:

* Data leakage
* Random shuffling of time-series
* Unrealistic accuracy claims

---

## 🧠 Tech Stack

* **Language**: Python
* **Framework**: PyTorch
* **Data Source**: Cricsheet (YAML)
* **Format**: ODI (50 overs)
* **Notebook**: `app.ipynb`

---

## 📂 Project Structure

```
cricket-match-prediction/
│
├── data/
│   ├── raw_yaml/
│   ├── clean_ball_by_ball.csv
│   ├── match_summary_valid.csv
│   └── match_features_step5.csv
│
├── models/
│   └── step6_win_predictor.pth
│
├── app.ipynb
└── README.md
```

---

## 🔵 STEP 1 — Raw Data Collection

* Source: **Cricsheet.org**
* Download ODI YAML files
* Each file represents one match with full metadata and deliveries

✔️ Trusted source
✔️ Ball-by-ball accuracy

---

## 🔵 STEP 2 — YAML → Structured CSV Conversion

### Output Files

#### 1️⃣ `clean_ball_by_ball.csv`

Per delivery information:

```
match_id, innings, batting_team, bowling_team,
over, ball,
batter, non_striker, bowler,
runs_batter, runs_extras, runs_total,
wicket, player_out
```

#### 2️⃣ `match_summary_valid.csv`

Match-level summary:

```
match_id,
team_1, team_2,
team_1_runs, team_2_runs,
winner,
team_1_won
```

✔️ YAML winner parsed correctly
✔️ `yaml_winner` column dropped later

---

## 🔵 STEP 3 — Data Cleaning & Validation

Handled:

* No-result / abandoned matches
* Partial innings
* Missing outcome information

Decision:

> Rain-abandoned matches are **naturally excluded** during sequence creation.

---

## 🔵 STEP 3.5 – 3.8 — Match Metadata Completion

Additional features added:

* Toss winner
* Toss decision
* Venue

Ensured all matches have:

* Valid teams
* Valid outcomes
* Consistent identifiers

---

## 🔵 STEP 4 — Match-Level Feature Engineering

Key features engineered:

* `team_1_won_toss`
* `team_1_batted_first` *(correctly derived from toss decision)*
* Team average runs
* Recent form averages
* Venue encoding

⚠️ Important correction:

> Toss winner ≠ batting first (team may choose to bowl)

---

## 🔵 STEP 5 — Final Feature Dataset

### File Created

```
match_features_step5.csv
```

### Used For

* Baseline win prediction model

### Target Variable

```
team_1_won (0 or 1)
```

---

## 🔵 STEP 6 — Baseline Win Prediction (Feed-Forward NN)

### Model Type

**Feed-Forward Neural Network (PyTorch)**

```
Input → Dense(64) → ReLU
      → Dense(32) → ReLU
      → Dense(1)  → Sigmoid
```

### Training Details

* Time-based train/test split
* Feature normalization
* Binary Cross Entropy loss

### Performance

* Accuracy ≈ **0.61** (realistic & honest)

### Model Saved

```
models/step6_win_predictor.pth
```

Saved:

* Model weights
* Input dimension
* Feature mean & std

---

## 🔵 STEP 7 — Sequence Modeling with LSTM (Momentum Learning)

### Purpose

Introduce **time-awareness** using ball-by-ball sequences.

---

## 🔵 STEP 7.1 — Define LSTM Training Sample

> **One training sample = early innings sequence → match outcome**

This enables:

* Live win prediction
* Momentum understanding

---

## 🔵 STEP 7.2 — ODI-Specific Sequence Creation

### Design Choices

* Format: **50-over ODI**
* Overs used: **First 20 overs**
* Balls per over: **6**

```
Sequence length = 20 × 6 = 120 balls
```

### Ball-Level Features

```
runs_total
wicket
over
ball
```

### Tensor Shapes

```
X → (num_matches, 120, 4)
y → (num_matches, 1)
```

### CSVs Used

* `clean_ball_by_ball.csv`
* `match_summary_valid.csv`

### STEP 7.3

* PyTorch LSTM architecture

### STEP 7.4

* LSTM training & evaluation

### STEP 7.5

* Compare Step 6 vs Step 7 performance

### STEP 8 (Future)

* Player-level LSTM
* Score prediction
* Ensemble models

---

## ✅ Project Principles

* No data leakage
* Time-respecting splits
* Realistic accuracy expectations
* Cricket-first logic

---

> This project is designed to scale from research → production without architectural changes.
