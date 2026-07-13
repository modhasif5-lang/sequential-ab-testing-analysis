# Sequential A/B Testing: Frequentist vs Bayesian Analysis

> **Why you should stop peeking at p-values — and what to do instead.**

This project compares traditional Frequentist A/B testing with Bayesian Sequential Testing using the ASOS Digital Experiments dataset. It demonstrates how continuous monitoring of p-values can produce misleading decisions and how Bayesian sequential methods provide a principled stopping strategy.

---

## Overview

Traditional A/B testing assumes that statistical significance is evaluated only after a predetermined sample size or experiment duration. In practice, teams often monitor experiments continuously and make decisions as soon as a p-value crosses a significance threshold.

This behavior, known as **peeking**, increases the probability of false positives and can lead to incorrect product decisions.

This project analyzes real-world experiment data to:

- Demonstrate the pitfalls of frequentist sequential monitoring
- Implement Bayesian Sequential Testing
- Compare stopping behavior across both approaches
- Estimate time and traffic savings from early stopping

---

## Tech Stack

- Python 3.11+
- NumPy
- Pandas
- SciPy
- Matplotlib
- Jupyter Notebook

---

## Dataset

This project uses the **ASOS Digital Experiments Dataset**, which contains summary statistics from online A/B experiments.

Dataset includes:

- 78 experiments
- 4 metrics per experiment
- 312 experiment–metric streams

Each observation contains:

- `count_c`
- `count_t`
- `mean_c`
- `mean_t`
- `variance_c`
- `variance_t`

Download the dataset from:

https://osf.io/64jsb/files/hq8sc

Place the downloaded file as:

```
asos_digital_experiments_dataset.csv
```

in the project root before running the notebook.

---

## Installation

Clone the repository:

```bash
git clone https://github.com/<your-username>/sequential-AB-testing.git
cd sequential-AB-testing
```

Install dependencies:

```bash
pip install numpy pandas scipy matplotlib
```

Launch the notebook:

```bash
jupyter notebook Sequential_Testing_Analysis.ipynb
```

---

## Analysis

### 1. Frequentist Analysis

The notebook computes Welch's t-test p-values at every observation to simulate continuous monitoring.

The analysis demonstrates how p-values fluctuate during an experiment and how temporary statistical significance can disappear by the end of the test.

It also highlights the impact of data anomalies, such as telemetry failures, on sequential monitoring.

---

### 2. Bayesian Sequential Analysis

A Bayesian Sequential Testing framework is implemented using Monte Carlo sampling.

For each observation, the notebook estimates:

- Probability that Treatment outperforms Control
- Expected Loss of deploying the treatment

Stopping rules:

| Condition | Decision |
|-----------|----------|
| Expected Loss < ε and P(Treatment > Control) > 0.95 | Stop and Deploy |
| Expected Loss < ε and P(Treatment > Control) < 0.05 | Stop and Rollback |
| Otherwise | Continue Experiment |

---

### 3. Experiment Summary

For every experiment–metric stream, the notebook records:

- Final experiment outcome
- Whether a temporary false trigger occurred
- Bayesian stopping point
- Time saved by early stopping

---

### 4. Overall Results

The Bayesian framework is evaluated across all 312 experiment streams.

| Metric | Value |
|---------|------:|
| Total Streams Evaluated | 312 |
| Streams Stopped Early | 158 (50.6%) |
| Total Days Saved | ~5,567 |
| Total User Impressions Saved | ~2.65 Billion |

The results show that Bayesian Sequential Testing can substantially reduce experiment duration while maintaining a principled decision process.

---

## Project Structure

```
sequential-AB-testing/
│
├── images/
│   └── telemetry-loss.png
│
├── Sequential_Testing_Analysis.ipynb
├── asos_digital_experiments_dataset.csv
└── README.md
```

---

## License

This project is licensed under the MIT License.

---

## Disclaimer

The ASOS Digital Experiments Dataset is provided for research purposes. The experiment results are not representative of ASOS's overall business operations, product development practices, or experimentation program, and no such conclusions should be drawn from this dataset.
