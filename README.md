# Black-Box Optimisation (BBO) Capstone Project

## Table of contents

- [1. Project overview](#1-project-overview)
  - [What is black-box optimisation?](#what-is-black-box-optimisation)
  - [Real-world relevance](#real-world-relevance)
  - [For a general audience](#for-a-general-audience)
  - [Career impact](#career-impact)
- [2. Project presentation](#2-project-presentation)
- [3. Datasheet and model card](#3-datasheet-and-model-card)
- [4. Inputs and outputs](#4-inputs-and-outputs)
- [5. Challenge objectives](#5-challenge-objectives)
- [6. Technical approach](#6-technical-approach)
  - [Early phase (Weeks 1–3)](#early-phase-weeks-13)
  - [Hybrid modelling (Week 4)](#hybrid-modelling-week-4)
  - [Bayesian and ensemble phase (Weeks 5–6)](#bayesian-and-ensemble-phase-weeks-56)
  - [Boundary and cluster-aware phase (Weeks 7–9)](#boundary-and-cluster-aware-phase-weeks-7-9)
  - [Final refinement (Weeks 10–12)](#final-refinement-weeks-10-12)
- [7. Query history (Rounds 1–12)](#7-query-history-rounds-1–12)
- [8. Summary of best-so-far outputs](#8-summary-of-best-so-far-outputs)
- [9. Final conclusions](#9-final-conclusions)
- [10. What I learned](#10-what-i-learned)
- [11. Reproducibility](#11-reproducibility)
- [12. Documentation and repository structure](#12-documentation-and-repository-structure)
- [13. Visualisations](#13-visualisations)
- [14. Architecture diagram](#14-architecture-diagram)
- [15. Weekly strategy](#15-weekly-strategy)

---

## 1. Project overview
The BBO capstone project simulates real‑world machine learning scenarios where the underlying function is unknown and direct analytical solutions are unavailable. The challenge is to query these black‑box functions strategically, learn from the responses, and move closer to optimal solutions with limited information.

This repository contains my full solution to the 13 week Black Box Optimisation (BBO) Capstone Challenge.  
The task: maximise eight unknown functions (2D–8D) with **one query per function per week**, no gradients, and no structural information.

The project demonstrates:

- **Surrogate-based optimisation** under extreme data scarcity  
- **Progressive refinement** of modelling and acquisition strategies  
- **Ensemble learning, trust-region behaviour, and dimensionality reduction**  
- A **complete, reproducible optimisation pipeline**

### What is black-box optimisation?

Black-box optimisation (BBO) is the problem of optimising an unknown function where we have no access to its internal structure, gradient information, or closed-form expression. We can only observe input–output pairs by querying the function.

In this project, each query is a vector in \([0,1]^d\), and each function returns a single scalar output. The goal is to find high-performing regions with as few evaluations as possible.

### Real-world relevance

BBO is central to many real-world applications, for example:

- **Hyperparameter tuning** in machine learning, where the mapping from parameters to performance is unknown and expensive to evaluate  
- **Drug discovery**, where testing compound combinations is costly and time-consuming  
- **Engineering design** and **financial modelling**, where simulations or evaluations are slow and noisy  

In all these settings, evaluations are expensive, so each query must be chosen strategically.

### For a general audience

In this project, I had to improve eight unknown systems using only a small number of weekly trials. I could not see the equations behind the systems, so each new test had to be chosen carefully using what previous results suggested.

I started by exploring widely, then gradually switched to more targeted methods once I had a better idea of where good results might be. Different functions needed different strategies: simpler models for some, Bayesian optimisation for others, and ensemble-based surrogates for the more complex cases. Overall, the project shows how structured experimentation can improve results even when data are limited and the system is mostly unknown.

### Career impact

Completing this project strengthens skills in:

- **Experimental design under uncertainty**  
- **Surrogate modelling and acquisition-driven sampling**  
- **Decision-making with limited, noisy data**  
- **Documentation and communication of ML workflows**

These are critical abilities for roles in data science, quantitative research, and AI systems engineering.

---

## 2. Project presentation

A structured project presentation covering the BBO approach, strategy evolution, patterns and insights, decision-making, and next steps is available in:

- `PROJECT_PRESENTATION.md` — Overview of the optimisation approach, how the strategy evolved, key data patterns, exploration vs exploitation, and reflections for stakeholders.

---

## 3. Datasheet and model card

Documentation for reproducibility and transparency:

- `docs/datasheet.md` — Documents the BBO capstone dataset: motivation, composition, collection process, preprocessing, intended/inappropriate uses, and distribution/maintenance.  
- `docs/model_card.md` — Describes the optimisation approach: overview, intended use, strategy across the twelve rounds, performance summary, assumptions and limitations, and ethical considerations.

These follow the spirit of datasheets and model cards to make the project auditable and portfolio-ready.

---

## 4. Inputs and outputs
- **Inputs:**  
  Each query is a vector of real numbers, with dimensionality depending on the function (from 2D up to 8D). Values are constrained to the unit interval `[0,1]`.

  **Example format:**
  ```
  f1: [0.500000, 0.500000]
  f3: [0.200000, 0.600000, 0.800000]
  ```

- **Outputs:**  
  Each query returns a single scalar response value. This represents the performance signal of the function at that point. Outputs may be positive, negative, or near zero, depending on the hidden structure.

  **Example:**
  ```
  f1 output: 2.6752879910742468e-9
  f5 output: 73.84870222928335
  ```
---

## 5. Challenge objectives
The objective is to identify query points that optimise each function (maximisation in most cases). Key constraints include:

- Limited queries: Each round allows only one query per function, so efficiency is critical.  
- Unknown structure: Functions may be non‑linear, multi‑modal, or noisy, requiring flexible modelling.  
- Response delay: Outputs are only revealed after submission, so strategies must be planned.  

Success means balancing exploration (searching new regions) with exploitation (refining known good areas), while building interpretable models that guide decision‑making.

- Exploration: Discovering new promising regions.
- Exploitation: Refining known high‑value areas
- Interpretability: Building transparent surrogate models
- Efficiency: Minimising wasted queries

---

## 6. Technical approach
### Week 1
- **Strategy:** Pure exploration. I spread queries evenly across the domain to gain coverage and establish baseline outputs.  
- **Outcome:** Identified initial regions of interest, but many outputs were near zero or negative.

### Week 2
- **Strategy:** Early exploitation. I shifted queries toward symmetry and balanced coordinates, guided by Round 1 results.  
- **Outcome:** Several functions improved (e.g., Function 2 rose from ~0.17 to ~0.44). Others declined, showing the need for more nuanced modelling.

### Week 3
- **Strategy:** Surrogate‑guided refinement. I used prior outputs to train simple models (SVM classification for high vs. low regions, plus regression for ranking). Queries were chosen near promising points, with small perturbations to test local curvature.  
- **Outcome:** Functions with strong Round 1 results (e.g., Function 5, 7, 8) were revisited with slight adjustments. Functions with weak signals (e.g., Function 3) were explored in new regions.

### Methods and Balance
- **ML methods:**  
  - Soft‑margin SVMs to classify regions into high vs. low performance.  
  - Kernel SVMs (RBF) to capture non‑linear surfaces.  
  - Heuristic perturbations to probe local curvature.  

- **Exploration vs. exploitation:**  
  - Exploit strong regions (~60–70% of queries).  
  - Explore uncertain or boundary regions (~30–40%).  

- **Uniqueness:**  
  My approach emphasizes interpretability: margin distances from SVMs provide a clear signal of confidence, guiding whether to exploit or explore. This reduces wasted queries and builds a transparent decision trail.

### Week 4
- **Strategy:** Hybrid scoring with multiple ML algorithms. I generated Week 4 inputs by averaging Week 1–3 inputs element‑wise. Each function was tested with Gradient Boosting, Random Forest, SVR, and Neural Networks.  
- **Evaluation:** Used 2‑fold cross‑validation with MSE and MAE, plus a stability penalty (difference between Week 4 prediction and Week 3 output). Combined into a weighted hybrid score:
  ```
  Hybrid Score = 0.5MSE + 0.3MAE + 0.2*Stability
  ```
- **Outcome:** Models were selected per index based on lowest hybrid score. Random Forest dominated high‑variance functions, Gradient Boosting excelled in nonlinear recovery, SVR handled oscillations, and Neural Networks captured subtle nonlinear corrections.

#### Methods and Balance
**ML methods:**  
- Gradient Boosting for nonlinear recovery.  
- Random Forest for high‑variance outputs.  
- SVR for oscillatory or near‑stable functions.  
- Neural Networks for subtle nonlinear corrections.  

**Exploration vs. exploitation:**  
- Exploit strong regions identified in Weeks 2–3 (~70%).  
- Explore uncertain regions with hybrid scoring (~30%).  

**Uniqueness:**  
Hybrid scoring balanced error metrics and stability, preventing bias toward a single model and ensuring defensible selections.

### Week 5
- **Strategy:**
Bayesian optimisation was introduced in Week 5 to replace earlier heuristic methods. Using all historical data from Weeks 1–4, I trained multiple surrogate models (Random Forest, Gradient Boosting, SVR, kNN, MLP, and Gaussian Process Regression).
GPR was selected as the primary surrogate because it provides calibrated uncertainty, which is essential for Expected Improvement (EI).
For each function:
•	Generated 2000 random candidate points in ([0,1]^d)
•	Predicted mean and uncertainty using GPR
•	Computed EI for each candidate
•	Selected the EI maximiser as the Week 5 query
This ensured a principled exploration–exploitation balance.
- **Outcome:**
Week 5 outputs showed moderate improvements for several functions (e.g., F2, F4, F7, F8).
Function 5 dropped sharply, indicating a highly non linear landscape.
The EI driven approach successfully diversified exploration without drifting into unstable extremes.

### Week 6
- **Strategy:**
Week 6 introduced a multi model ensemble trained on Weeks 1–5 data:
•	Random Forest
•	Gradient Boosting
•	SVR
•	Multiple MLPs
•	GPR (for uncertainty aware scoring)
Predictions were averaged to reduce variance, and a stability penalty discouraged large deviations from prior outputs.
Candidate points were generated both globally (uniform sampling) and locally (perturbations around top historical points).
- **Outcome:**
Outputs stabilised across most functions.
Function 8 remained strong, Function 2 continued improving, and Function 5 began recovering from Week 5’s drop.
Ensemble averaging reduced noise and produced more reliable query points.

### Week 7
- **Strategy:**
Week 7 focused on boundary exploration to test whether extreme regions contained hidden optima.
This was motivated by ensemble disagreement and high uncertainty in sparsely sampled corners of the domain.
- **Outcome:**
•	Function 5 produced a dramatic spike (1084+), revealing a steep ridge.
•	Function 7 collapsed sharply, indicating high volatility.
•	Function 8 uncovered a new high value region near the upper boundary.
These emergent behaviours reshaped the search strategy for subsequent rounds.

### Week 8
- **Strategy:**
Week 8 concentrated on exploiting the newly discovered ridge in Function 5 and refining the high value cluster in Function 8.
Anisotropic perturbations were used to probe sensitive dimensions more finely, while ensemble scoring ensured stability.
- **Outcome:**
Function 8 reached ~8.0, confirming a stable basin.
Function 5 remained extremely high, and other functions showed incremental improvements.

### Week 9
- **Strategy:**
Week 9 introduced a rolling surrogate trained on Weeks 1–8.
This improved robustness and reduced overreaction to outliers.
Candidate generation became cluster aware: instead of uniform sampling, candidates were drawn around historical high value points.
- **Outcome:**
Function 8 reached ~8.80, and Function 5 remained extremely high.
Functions 2 and 4 showed gradual improvements.
The optimisation process became more stable and predictable.

### Week 10
- **Strategy:**
Week 10 used a combination of:
•	Global random sampling
•	Local perturbation sweeps
•	Ensemble scoring
•	Uncertainty aware selection
This round resembled a model based RL planner: thousands of hypothetical candidates were evaluated before selecting a single query.
- **Outcome:**
Function 5 reached 1616.64, the highest value observed across all weeks.
Function 8 remained strong, and Function 2 stabilised in a mid range band.

### Week 11
- **Strategy:**
Week 11 revisited boundary regions for Functions 1–4 to ensure no narrow optima were missed.
This was motivated by ensemble uncertainty and the need to validate whether earlier clusters were genuine or artefacts.
- **Outcome:**
Function 8 reached 9.16, a new high.
Function 5 remained at its peak.
Function 4 improved slightly, and Function 6 showed smoother behaviour.

### Week 12
- **Strategy:**
The final week used all historical data (Weeks 1–11) to train the ensemble.
Candidate points were generated using:
•	Local sampling around top clusters
•	Global random sampling
•	Uncertainty weighted scoring
This balanced exploitation of known basins with a final sweep for undiscovered optima.
- **Outcome:**
Function 8 reached 9.25, the highest value across all rounds.
Function 5 remained at 1616.64, confirming a stable optimum.
The optimisation process converged with clear clusters for Functions 2, 5, and 8, while Functions 1 and 3 remained relatively flat.

---

## 7. Query history (Rounds 1–12)
| Week   | Function 1 | Function 2 | Function 3 | Function 4 | Function 5 | Function 6 | Function 7 | Function 8 |
|--------|------------|------------|------------|------------|------------|------------|------------|------------|
| **1** | [0.333333, 0.666667] → 5.7248e 48 | [0.777778, 0.222222] → 0.1669 | [0.142857, 0.571429, 0.857143] → −0.0351 | [0.285714, 0.714286, 0.428571, 0.857143] → −16.1819 | [0.062500, 0.500000, 0.937500, 0.250000] → 94.6212 | [0.111111, 0.444444, 0.777778, 0.222222, 0.888889] → −1.7667 | [0.090909, 0.363636, 0.636364, 0.181818, 0.545455, 0.818182] → 1.0627 | [0.125000, 0.250000, 0.375000, 0.500000, 0.625000, 0.750000, 0.875000, 0.062500] → 8.6682 |
| **2** | [0.500000, 0.500000] → 2.6753e 9 | [0.700000, 0.300000] → 0.4380 | [0.200000, 0.600000, 0.800000] → −0.0651 | [0.200000, 0.800000, 0.300000, 0.700000] → −15.3005 | [0.080000, 0.520000, 0.920000, 0.270000] → 73.8487 | [0.200000, 0.500000, 0.800000, 0.300000, 0.900000] → −1.7217 | [0.120000, 0.380000, 0.660000, 0.220000, 0.580000, 0.840000] → 0.8381 | [0.150000, 0.275000, 0.400000, 0.525000, 0.650000, 0.775000, 0.900000, 0.100000] → 8.5282 |
| **3** | [0.450000, 0.550000] → 1.55e 13 | [0.725000, 0.275000] → 0.4116 | [0.800000, 0.200000, 0.400000] → −0.0390 | [0.250000, 0.750000, 0.350000, 0.650000] → −11.86 | [0.070000, 0.510000, 0.930000, 0.260000] → 85.55 | [0.210000, 0.490000, 0.810000, 0.310000, 0.910000] → −1.82 | [0.100000, 0.360000, 0.640000, 0.200000, 0.560000, 0.820000] → 1.01 | [0.130000, 0.260000, 0.380000, 0.510000, 0.630000, 0.760000, 0.880000, 0.070000] → 8.63 |
| **4** | [0.427778, 0.572222] → 1.41e‑16 | [0.734259, 0.265741] → 0.3110 | [0.380952, 0.457143, 0.685714] → −0.1185 | [0.245238, 0.754762, 0.359524, 0.735714] → −14.1099 | [0.070833, 0.510000, 0.929167, 0.260000] → 84.2661 | [0.173704, 0.478148, 0.795926, 0.277407, 0.899630]  → −1.8337 | [0.103636, 0.367879, 0.645455, 0.200606, 0.561818, 0.826061] → 0.9692 | [0.135000, 0.261667, 0.385000, 0.511667, 0.635000, 0.761667, 0.885000, 0.077500] → 8.6114 |
| **5**	| [0.420000, 0.580000] → 3.400e 18 | [0.630000, 0.370000] → 0.3658	| [0.310000, 0.520000, 0.740000] → −0.1188	| [0.270000, 0.610000, 0.440000, 0.830000] → −13.1643 |	[0.180000, 0.570000, 0.890000, 0.330000] → 54.3674 | [0.220000, 0.480000, 0.790000, 0.290000, 0.910000] → −1.7569	| [0.140000, 0.390000, 0.660000, 0.230000, 0.580000, 0.840000] → 0.8329	| [0.160000, 0.280000, 0.410000, 0.530000, 0.660000, 0.780000, 0.900000, 0.110000] → 8.5041 |
| **6** |	[0.436555, 0.561852] → 9.045e 15 | [0.643711, 0.350427] → 0.4067 | [0.290003, 0.526542, 0.748973] → −0.1086 |	[0.257349, 0.623142, 0.431400, 0.843698] → −13.8931 |	[0.164072, 0.559418, 0.909880, 0.318913] → 74.4239 | [0.203885, 0.477786, 0.796998, 0.271818, 0.890277] → −1.8021	| [0.133556, 0.371272, 0.667798, 0.244733, 0.565004, 0.824401] → 0.9452	| [0.140764, 0.294750, 0.399680, 0.531209, 0.640643, 0.763956, 0.880412, 0.096299] → 8.5786 |
| **7** |	[0.999509, 0.605025] → −7.41e 97 |	[0.709894, 0.999867] → 0.5846 |	[0.018005, 0.040702, 0.029470] → −0.1458 | [0.022476, 0.055750, 0.000980, 0.032298] → −23.1301 |	[0.990736, 0.060822, 0.932835, 0.009122] → 1084.1094 |	[0.565687, 0.022461, 0.000202, 0.168316, 0.021242] → −1.7200 |	[0.998542, 0.979690, 0.934707, 0.047897, 0.948514, 0.802608] → 8.71e 05 |	[0.731876, 0.927411, 0.970310, 0.985745, 0.915076, 0.597028, 0.907676, 0.859568] → 4.7559 |
| **8** |	[0.999509, 0.605025] → 1.049e 138 |	[0.709894, 0.999867] → 0.5197 |	[0.018005, 0.040702, 0.029470] → −0.0582 | [0.022476, 0.055750, 0.000980, 0.032298] → −7.2677 |	[0.990736, 0.060822, 0.932835, 0.009122] → 1084.1094 | [0.565687, 0.022461, 0.000202, 0.168316, 0.021242] → −1.9941 |	[0.998542, 0.979690, 0.934707, 0.047897, 0.948514, 0.802608] → 0.1521 |	[0.731876, 0.927411, 0.970310, 0.985745, 0.915076, 0.597028, 0.907676, 0.859568] → 7.9990 |
| **9** |	[0.990198, 0.819370] → −9.112e 118 |	[0.709894, 0.999867] → 0.6167 |	[0.037714, 0.991662, 0.857859] → −0.0727 |	[0.264095, 0.590310, 0.575603, 0.333135] → −6.2653 |	[0.990736, 0.060822, 0.932835, 0.009122] → 1084.1094 |	[0.051082, 0.041178, 0.031590, 0.136823, 0.343311] → −2.2079 |	[0.059048, 0.366885, 0.960575, 0.052238, 0.469479, 0.879831] → 0.5677 |	[0.049164, 0.319656, 0.074848, 0.584120, 0.404708, 0.887928, 0.792066, 0.003256] → 8.8032 |
| **10** | [0.993848, 0.880315] → 1.215e 137 |	[0.997454, 0.986872] → 0.0491 |	[0.954008, 0.568624, 0.969873] → −0.2946 | [0.262460, 0.588081, 0.564112, 0.366838] → −6.1277 |	[1.000000, 0.000000, 1.000000, 0.000000] → 1616.6425 |	[0.396780, 0.997315, 0.907430, 0.342969, 0.985318] → −2.1426 |	[0.021038, 0.747023, 0.003158, 0.084239, 0.965362, 0.695918] → 0.0018	| [0.023899, 0.345245, 0.094899, 0.085174, 0.772708, 0.901727, 0.981125, 0.216820] → 8.5456 |
| **11** | [0.728471, 0.000053] → −5.026e 190 |	[0.002253, 0.997017] → 0.0126 |	[0.956376, 0.013162, 0.028526] → −0.1215 |	[0.944788, 0.574188, 0.999906, 0.042979] → −34.0092 |	[1.000000, 0.000000, 1.000000, 0.000000] → 1616.6425 |	[0.408396, 0.080753, 0.998253, 0.146887, 0.232701] → −1.4489 |	[0.562278, 0.101469, 0.358616, 0.024459, 0.052806, 0.040439] → 0.0195 |	[0.008520, 0.265534, 0.122699, 0.540872, 0.423047, 0.742627, 0.690756, 0.000000] → 9.1694 |
| **12** | [0.972951, 0.000963] → 0.0	| [0.688477, 0.984237] → 0.5801 |	[0.003606, 0.980658, 0.362354] → −0.0640	| [0.012833, 0.998698, 0.563760, 0.006490] → −28.9450	| [1.000000, 0.000000, 1.000000, 0.000000] → 1616.6425	| [0.449374, 0.041588, 1.000000, 0.156186, 0.264819] → −1.4772	| [0.094247, 0.310016, 0.599245, 0.110477, 0.517195, 0.828528] → 1.1710	| [0.003004, 0.215680, 0.122410, 0.559561, 0.452147, 0.676872, 0.663216, 0.002333] → 9.2523 |

The full query history is stored in the /data/ directory.

---

## 8. Summary of Best So Far Outputs (Across All Weeks)
| Function |	Best Output |	Week | Achieved |	Notes |
|----------|--------------|------|----------|-------|
| **F1** |	~5.7e 48 |	Week 1 |	Flat function, no meaningful variation
| **F2** | ~0.6167 |	Week 9 |	Stable mid range optimum |
| **F3**	| ~−0.0351 |	Week 1 |	Mostly negative, weak structure |
| **F4**	| ~−6.12 |	Week 10 |	Improved gradually with boundary exploration |
| **F5**	| 1616.64 |	Week 10 |	Major ridge discovered in Weeks 7–10 |
| **F6**	| ~−1.4488 | Week 12 | Slight improvements with ensemble smoothing |
| **F7**	| ~1.0627 |	Week 1 | Volatile, collapsed in Week 7 |
| **F8** | 9.2523 |	Week 12 |	Strong basin near upper boundary |

---

## 9. Final conclusions

Across twelve rounds, the optimisation strategy evolved from naive exploration to a fully model‑based, uncertainty‑aware pipeline. Surrogate modelling, ensemble learning, and acquisition‑driven sampling enabled efficient navigation of complex landscapes.

**Key insights:**

- Function 5 contained a steep, high‑value ridge, discovered through boundary exploration.
- Function 8 exhibited a smooth, navigable basin near the upper boundary.
- Functions 1 and 3 remained flat, confirming limited structure.
- Rolling surrogates and ensemble averaging improved stability in later rounds.

The project demonstrates practical mastery of Bayesian optimisation under strict query constraints.

---

## 10. What I learned

- How to design experiments under uncertainty  
- How to build and update surrogate models  
- How to balance exploration and exploitation  
- How to interpret uncertainty for decision‑making  
- How to structure a multi‑week optimisation pipeline  
- How to document and communicate ML workflows professionally  

---

## 11. Reproducibility

To reproduce the workflow:

1. Load initial `.npy` inputs/outputs (not included in repo; link provided).  
2. Train surrogate models (GPR + ensemble).  
3. Generate candidate points in \([0,1]^d\).  
4. Compute acquisition scores (EI or uncertainty‑weighted).  
5. Select the next query.  
6. Append new data and repeat.

A full implementation is provided in the notebooks.

---

## 12. Documentation

### Key documentation

- `docs/datasheet.md` — Data description, limitations, context  
- `docs/model_card.md` — Model behaviour, assumptions, interpretability  
- `docs/weekly_strategy.md` — Consolidated week‑by‑week strategy notes and reflections  
- `docs/architecture_diagram.md` — High‑level system and pipeline architecture  

### Repository layout

. ├── README.md                     # This file ├── requirements.txt              # Python dependencies ├── function_description_and_details.md ├── scripts/                      # Python modules │   ├── bayesian_optimisation/    # BO (standard, exploitative, noise-aware) │   ├── logistic_regression/      # Linear and nonlinear logistic regression │   ├── neural_networks/          # MLP / NN-based surrogates │   └── utils/                    # Data loading and visualisation helpers ├── data/                         # Query data by week │   ├── initial_data/ │   ├── week1/ │   ├── ... │   └── week12/ ├── notebooks/                    # Full code for all functions and experiments ├── plots/                        # Optimisation trajectories and uncertainty visualisations ├── docs/ │   ├── datasheet.md │   ├── model_card.md │   ├── weekly_strategy.md │   └── architecture_diagram.md ├── PROJECT_PRESENTATION.md       # Final presentation-style project summary

---

## 13. Visualisations

All plots are stored in `/plots/`.

### Included:

- `best_so_far.png` — Best output per function across all weeks  
- `f1_trajectory.png` … `f8_trajectory.png` — Per‑function output trajectories  
- Additional plots for uncertainty, candidate distributions, and cluster‑aware sampling (where relevant)

---

## 14. Architecture diagram

The overall optimisation pipeline and data flow are documented in:

- `docs/architecture_diagram.md` — High‑level architecture of:
  - Data ingestion and storage  
  - Surrogate training and evaluation  
  - Candidate generation and acquisition scoring  
  - Query selection and logging  
  - Visualisation and reporting  

This diagram is intended for technical stakeholders and interviewers to quickly understand how the system is structured end‑to‑end.

---

## 15. Weekly strategy

A consolidated, narrative view of how the strategy evolved week‑by‑week (methods, visualisations, and rationale) is provided in:

- `docs/weekly_strategy.md`

This complements the raw query history and plots by explaining **why** each change was made and how it affected subsequent decisions.
