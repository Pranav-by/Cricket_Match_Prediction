# 🧠 LOW-LEVEL BLUEPRINT

## Generic Cricket Match Prediction System (Any Team vs Any Team)

## 1️⃣ Problem Definition

### Objective

Design a deep learning–based system that predicts:

1. First-innings total score
2. Win probability of Team A vs Team B
3. Expected runs scored by each player

The system works for **any international team vs any team** using historical data.

---

## 2️⃣ System Assumptions

* Predictions are probabilistic, not exact
* Playing XI is known before prediction
* No live match data is used
* Weather impact is approximated via venue history

---

## 3️⃣ Data Sources (Raw Input Layer)

### Data Types

* Match-level data
* Team-level statistics
* Player-level innings data

### Example Fields

* Match date
* Teams
* Venue
* Toss result
* Innings scores
* Player runs per match

---

## 4️⃣ Data Storage Structure

```
data/
 ├── matches.csv
 ├── teams.csv
 ├── players.csv
 ├── player_innings.csv
 └── venues.csv
```

Each file contains atomic (non-derived) values.

---

## 5️⃣ Data Cleaning Pipeline

1. Remove abandoned / no-result matches
2. Standardize team names
3. Standardize player names
4. Remove records with missing innings data
5. Sort all data chronologically

---

## 6️⃣ Feature Engineering Layer

### 6.1 Match-Level Features

* Venue average first-innings score
* Toss decision (bat/bowl → binary)
* Match type encoding (ODI / T20)

---

### 6.2 Team-Level Features

Computed using last **N matches**:

* Average runs scored
* Average wickets lost
* Bowling economy
* Win rate

(Rolling features, not lifetime averages)

---

### 6.3 Player-Level Features

Computed using last **N innings**:

* Runs scored
* Strike rate
* Balls faced
* Batting position

Stored as ordered sequences.

---

## 7️⃣ Feature Vector Construction

### 7.1 Total Score Model

**Input (X₁):**

```
[
  team_recent_scores_sequence,
  venue_avg_score,
  opponent_bowling_strength
]
```

**Target (y₁):**

```
actual_first_innings_total
```

---

### 7.2 Win Probability Model

**Input (X₂):**

```
[
  predicted_score,
  team_A_form,
  team_B_form,
  toss_result,
  venue_stats
]
```

**Target (y₂):**

```
match_winner (0 or 1)
```

---

### 7.3 Player Runs Model

**Input (X₃):**

```
[
  player_last_N_innings_sequence,
  batting_position,
  opponent_bowling_strength
]
```

**Target (y₃):**

```
player_runs
```

---

## 8️⃣ Sequence Builder (for LSTM)

* Sliding window approach
* Maintain chronological order
* No shuffling before sequence creation

Example:

```
[Match t-5, t-4, t-3, t-2, t-1] → Predict match t
```

---

## 9️⃣ Model Architectures

### 9.1 Total Score Prediction Model

**Type:** LSTM + Dense (Regression)

```
Input Sequence
 → LSTM (hidden = 64)
 → Dropout (0.2)
 → Dense (32) + ReLU
 → Dense (1)
```

**Loss:** Mean Squared Error

---

### 9.2 Win Probability Prediction Model

**Type:** Feed-Forward Neural Network (Classification)

```
Input Vector
 → Dense (64) + ReLU
 → Dense (32) + ReLU
 → Dense (1) + Sigmoid
```

**Loss:** Binary Cross Entropy

---

### 9.3 Player Runs Prediction Model

**Type:** LSTM + Dense (Regression)

```
Input Sequence
 → LSTM (hidden = 64)
 → Dropout (0.2)
 → Dense (32) + ReLU
 → Dense (1)
```

**Loss:** Mean Absolute Error

---

## 🔟 Training Strategy

1. Time-based train/validation/test split
2. Older matches → training
3. Recent matches → testing
4. Fixed random seed for reproducibility

---

## 1️⃣1️⃣ Evaluation Metrics

| Prediction Task | Metric        |
| --------------- | ------------- |
| Total score     | MAE, RMSE     |
| Win probability | Accuracy, AUC |
| Player runs     | MAE           |

---

## 1️⃣2️⃣ Prediction Pipeline

```
User inputs match details
 → Fetch recent statistics
 → Build feature vectors
 → Run trained models
 → Aggregate predictions
 → Display results
```

---

## 1️⃣3️⃣ Final Outputs

* Expected total score (with range)
* Win probability for both teams
* Expected runs per player

---

## 1️⃣4️⃣ Deployment / Usage

* Local execution (CPU)
* Optional Streamlit UI
* Prediction time < 3 seconds

---

## 🧾 Summary

The system follows a modular deep learning pipeline including data collection, preprocessing, feature engineering, and sequence modeling. LSTM architectures are used for time-dependent predictions such as total score and player performance, while a feed-forward neural network is used for win probability prediction based on aggregated match features. The system is generic and capable of predicting outcomes for any team versus any team using historical cricket data.
