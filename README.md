# Sequential A/B Testing: Frequentist vs Bayesian Analysis

> **Why you should stop peeking at p-values — and what to do instead.**

This project demonstrates the difference between Traditional Frequentist and Bayesian Sequential approaches to A/B testing using the ASOS dataset.

---

## The Problem

In traditional A/B testing, organizations typically:

1. Run an experiment for a fixed duration.
2. Check the p-value at the end.
3. Declare a winner if p < 0.05.

When analysts peek at results during the test, the p-value fluctuates wildly — sometimes dipping below 0.05 temporarily. This creates false triggers that lead teams to ship underperforming features.

This project quantifies this problem and presents the Bayesian solution.

---

## Dataset

We use the ASOS Digital Experiments Dataset, which contains time-series summary statistics for:
- 78 unique experiments
- 4 metrics per experiment (312 total experiment-metric streams)
- Summary stats at each time point: `count_c`, `count_t`, `mean_c`, `mean_t`, `variance_c`, `variance_t`

> **Note:** Place the dataset file `asos_digital_experiments_dataset.csv` in the project root directory before running the notebook.

---

## Analysis Overview

### Part 1: Frequentist Analysis — The Peeking Problem

We calculate Welch's t-test p-values at every time point for each experiment and visualize how they fluctuate over time.

**Key Finding:** Experiment `036afc` is a classic false trigger — its p-value dips below 0.05 mid-test but ends at ~0.45 (inconclusive). Peeking would have led to an incorrect decision.

#### Visual Evidence: The Danger of Telemetry Loss
During continuous frequentist monitoring, data pipeline errors can trigger catastrophic false positives. In Experiment `162a38`, a single-day telemetry failure caused the p-value to plummet to 0.0 before immediately returning to baseline. An automated script would have prematurely deployed a broken feature based on this noise.

![Telemetry Loss Anomaly](images/telemetry-loss.png)

### Part 2: Bayesian Sequential Analysis

We implement a Bayesian Sequential Testing framework using Monte Carlo sampling:

- **P(T > C):** Probability that Treatment outperforms Control
- **Expected Loss:** Average loss if we deploy Treatment prematurely

**Stopping Rules:**

| Condition | Decision |
|-----------|----------|
| Expected Loss < ε AND P(T > C) > 0.95 | STOP & DEPLOY |
| Expected Loss < ε AND P(T > C) < 0.05 | STOP & ROLLBACK |
| Otherwise | Keep Running |

### Part 3: Data Aggregator

For every experiment-metric stream, we compute:
- **Final Status:** Was the test a genuine winner at the end of the duration?
- **False Trigger:** Did it temporarily appear significant but end up inconclusive?

### Part 4: Full-Scale Savings Overview

We run the Bayesian framework across all 312 streams and calculate:
- Total experiments stopped early
- Total days saved
- Total user traffic saved

---

## Key Results

| Metric | Value |
|--------|-------|
| Total Streams Evaluated | 312 |
| Streams Stopped Early | 158 (50.6%) |
| Total Days Saved | ~5,567 days |
| Total Users Saved | ~2.65 billion impressions |

> By adopting Bayesian Sequential Testing, organizations can cut experiment duration in half while making safer deployment decisions.

---

## Project Structure

```text
├── images/
│   └── telemetry-loss.png               # Visual evidence of frequentist failure
├── Sequential_Testing_Analysis.ipynb    # Main notebook (combined & improved)
├── asos_digital_experiments_dataset.csv # Dataset
└── README.md                            # This file
