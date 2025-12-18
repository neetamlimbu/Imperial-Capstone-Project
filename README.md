# Black-Box Optimisation (BBO) Capstone Project

## 1. Project overview
The BBO capstone project simulates real‑world machine learning scenarios where the underlying function is unknown and direct analytical solutions are unavailable. The challenge is to query these black‑box functions strategically, learn from the responses, and move closer to optimal solutions with limited information.

**Goal:**  
The overall goal is to minimise or maximise the outputs of eight hidden functions while using as few queries as possible. This mirrors practical ML problems such as hyperparameter tuning, drug discovery, or financial modelling, where evaluation is costly and interpretability is limited.

**Career Impact:**  
Completing this project strengthens skills in experimental design, surrogate modelling, and decision‑making under uncertainty. These are critical abilities for data scientists working in domains like fintech, healthcare, or AI systems engineering.

## 2. Inputs and outputs
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
  
## 3. Challenge objectives
The objective is to identify query points that optimise each function (maximisation in most cases). Key constraints include:

- Limited queries: Each round allows only one query per function, so efficiency is critical.  
- Unknown structure: Functions may be non‑linear, multi‑modal, or noisy, requiring flexible modelling.  
- Response delay: Outputs are only revealed after submission, so strategies must be planned.  

Success means balancing exploration (searching new regions) with exploitation (refining known good areas), while building interpretable models that guide decision‑making.

## 4. Technical approach
### Round 1
- **Strategy:** Pure exploration. I spread queries evenly across the domain to gain coverage and establish baseline outputs.  
- **Outcome:** Identified initial regions of interest, but many outputs were near zero or negative.

### Round 2
- **Strategy:** Early exploitation. I shifted queries toward symmetry and balanced coordinates, guided by Round 1 results.  
- **Outcome:** Several functions improved (e.g., Function 2 rose from ~0.17 to ~0.44). Others declined, showing the need for more nuanced modelling.

### Round 3
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

## Query history (Rounds 1–3)
| Week   | Function 1 | Function 2 | Function 3 | Function 4 | Function 5 | Function 6 | Function 7 | Function 8 |
|---------|------------|------------|------------|------------|------------|------------|------------|------------|
| **1** | [0.333333, 0.666667] → 5.7248e‑48 | [0.777778, 0.222222] → 0.1669 | [0.142857, 0.571429, 0.857143] → −0.0351 | [0.285714, 0.714286, 0.428571, 0.857143] → −16.1819 | [0.062500, 0.500000, 0.937500, 0.250000] → 94.6212 | [0.111111, 0.444444, 0.777778, 0.222222, 0.888889] → −1.7667 | [0.090909, 0.363636, 0.636364, 0.181818, 0.545455, 0.818182] → 1.0627 | [0.125000, 0.250000, 0.375000, 0.500000, 0.625000, 0.750000, 0.875000, 0.062500] → 8.6682 |
| **2** | [0.500000, 0.500000] → 2.6753e‑9 | [0.700000, 0.300000] → 0.4380 | [0.200000, 0.600000, 0.800000] → −0.0651 | [0.200000, 0.800000, 0.300000, 0.700000] → −15.3005 | [0.080000, 0.520000, 0.920000, 0.270000] → 73.8487 | [0.200000, 0.500000, 0.800000, 0.300000, 0.900000] → −1.7217 | [0.120000, 0.380000, 0.660000, 0.220000, 0.580000, 0.840000] → 0.8381 | [0.150000, 0.275000, 0.400000, 0.525000, 0.650000, 0.775000, 0.900000, 0.100000] → 8.5282 |
| **3** | [0.450000, 0.550000] | [0.725000, 0.275000] | [0.800000, 0.200000, 0.400000] | [0.250000, 0.750000, 0.350000, 0.650000] | [0.070000, 0.510000, 0.930000, 0.260000] | [0.210000, 0.490000, 0.810000, 0.310000, 0.910000] | [0.100000, 0.360000, 0.640000, 0.200000, 0.560000, 0.820000] | [0.130000, 0.260000, 0.380000, 0.510000, 0.630000, 0.760000, 0.880000, 0.070000] |
