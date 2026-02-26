Abstract
The optimisation system uses a heterogeneous ensemble surrogate to approximate eight hidden black box functions under a strict evaluation budget. Each function is modelled independently using Random Forests, Gradient Boosting, Support Vector Regression and multiple neural networks, with predictions aggregated through simple averaging. The surrogate is retrained weekly on all accumulated query–response pairs, enabling adaptation to sparse and unevenly distributed data. Candidate points are generated through large scale uniform sampling in ([0,1]^d), and the next query is selected by maximising the ensemble’s predicted output. This approach provides a stable, model agnostic ranking mechanism that avoids degeneracy issues observed in earlier pipelines while remaining computationally lightweight. Although the ensemble lacks uncertainty quantification and may over exploit historically promising regions, it offers a practical balance of robustness, flexibility and simplicity for black box optimisation in synthetic domains.

Model Card: Ensemble Surrogate Optimisation Approach
Overview
Name: Ensemble Surrogate Optimiser
Type: Regression based surrogate model with random search acquisition
Version: Week 13 (final iteration)
The optimiser approximates eight unknown black box functions in the Imperial College BBO Capstone Challenge. It predicts function values for candidate points and selects the maximiser. Each function is treated as an independent regression task with dimensionalities from 2D to 8D.

Intended Use
Suitable for
•	Surrogate based optimisation with limited evaluations
•	Ranking candidate points in ([0,1]^d)
•	Small sample regression
•	Synthetic black box optimisation tasks

Not suitable for
•	Real world deployment
•	Safety critical or fairness critical applications
•	Interpreting global function structure
•	High dimensional optimisation without sampling augmentation

Strategy Across 13 Weeks
Weeks 1–3: Baseline Exploration
•	Structured mid range sampling
•	Goal: establish baseline behaviour and avoid premature exploitation
Week 4: Hybrid Scoring
•	Models tested: RF, GB, SVR, MLP
•	2 fold CV due to tiny datasets
•	Hybrid score combining MSE, MAE, and prediction stability
•	Week 4 inputs generated via element wise averaging of Weeks 1–3
Week 5: Gaussian Process Regression + EI
•	GPR surrogate with Matern kernel
•	Expected Improvement acquisition
•	2,000 random candidates per function
•	Balanced exploration and exploitation
Weeks 6–7: Ensemble Surrogate + Large Scale Random Search
•	Ensemble of six models
•	20,000 uniform candidates per function
•	Next query = argmax of ensemble prediction
•	Adapted well to Week 7 anomalies
Weeks 8–10: Refinement and Trust Region Behaviour
•	Ensemble retained
•	Sampling restricted to neighbourhoods of promising regions
•	Improved stability and reduced variance
Weeks 11–13: Stability Driven Exploitation
•	Focus on consistent improvements
•	Controlled exploration to avoid local traps
•	Final queries reflect accumulated surrogate confidence

Performance Summary
Performance was evaluated indirectly through:
•	Improvement in weekly outputs
•	Stability of predictions
•	Adaptation to sudden behaviour changes
•	Avoidance of degenerate solutions
Observed behaviour
•	Functions 1–4: smooth surfaces, steady improvements
•	Functions 5 & 8: sharp peaks, large jumps in later weeks
•	Functions 6 & 7: moderate nonlinearity, ensemble handled them well
•	Ensemble remained stable across all 13 weeks
Metrics used:
•	CV MSE and MAE (Week 4)
•	Relative improvement vs previous week
•	Surrogate stability (prediction deviation from last known value)

Assumptions and Limitations
Assumptions
•	Functions are continuous enough for surrogate modelling
•	Uniform random sampling provides adequate exploration
•	Ensemble averaging reduces overfitting risk
Limitations
•	No uncertainty quantification
•	Ensemble may smooth out narrow optima
•	Random search becomes less effective as dimensionality increases
•	Bias toward previously explored regions
•	Surrogate disagreement not explicitly measured
Failure modes
•	Over exploitation in later rounds
•	Overconfidence in sparsely sampled regions
•	Sensitivity of SVR and MLPs to scaling

Ethical Considerations
The model operates exclusively on synthetic mathematical functions.
There are no fairness concerns, no personal data, and no societal risks.
Transparency through this model card supports reproducibility and adaptation to real world optimisation tasks.

