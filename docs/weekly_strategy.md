## Introduction
This methodology tells the story of how the optimiser for the Imperial College BBO Capstone Challenge grew from a handful of random samples into a mature, ensemble driven system capable of navigating eight unknown landscapes.  
The challenge imposed a strict rhythm:

- One query per function per week  
- No gradients, no structure, no retries  
- Eight functions, 2D–8D  
- Thirteen weeks of learning  

Under these constraints, the optimiser became a kind of explorer — cautious at first, then increasingly confident as patterns emerged.

This document explains that journey, grounded directly in the week by week implementations.

## The Bayesian Optimisation Loop (The Constant Behind the Change)
Every week, regardless of the modelling strategy, the optimiser followed the same loop:

1. Gather all historical inputs and outputs  
2. Fit a surrogate model (or ensemble)  
3. Generate a large pool of candidate points  
4. Score candidates using the surrogate  
5. Select the most promising point  
6. Submit it to the portal  
7. Append the new data and repeat  

What changed week to week was how each step was implemented — the models, the sampling strategy, the acquisition logic, and the way the optimiser interpreted the landscape.

## Week by Week Evolution of the Optimiser

Below is a narrative reconstruction of your optimiser’s evolution, directly tied to the code provided.

### Week 1–3: Surrogate Guided Refinement (SVM Classifier + SVR)
**Reference:** Week3_SurrogateGuidedRefinement.txt  

The project began with a surprisingly creative approach:

- SVM classifier to separate “high” vs “low” outputs  
- SVR regressor to model the continuous landscape  
- StandardScaler for normalisation  
- Candidate generation via:
  - Gaussian perturbations  
  - Symmetry flips  
  - Random probes  
  - Mid range anchors  

This early pipeline was exploratory and eclectic — a reflection of the limited data (only 3 points per function).  
The SVM acted as a coarse filter, while the SVR ranked the survivors.  
It was messy, but it worked: it gave the optimiser its first sense of direction.

### Week 4: Hybrid Scoring Across Four ML Models
**Reference:** Week4_Hybrid_Scoring_All_ML.txt  

Week 4 introduced structure. The following four models were evaluated per function:

- Gradient Boosting  
- Random Forest  
- SVR  
- MLPRegressor  

Using:

- 2 fold CV (because only 3 samples existed)  
- MSE + MAE + stability penalty  
- Min max normalisation  
- Hybrid scoring to select the most reliable model  

This was the first week where the optimiser behaved like a scientist: testing hypotheses, comparing models, and choosing the most stable one.  
It also produced our first formal report — Excel sheets, charts, and a PDF summary.

### Week 5–6: Ensemble Consensus + Variance Minimisation
**Reference:** week6.txt  

Weeks 5 and 6 marked a shift toward ensemble thinking. The following techniques were introduced:

- Random Forest  
- Gradient Boosting  
- SVR  
- MLPRegressor  

Instead of picking one, the following procedure was followed:

- Trained all models  
- Generated candidates around Week 5 inputs  
- Selected points with the lowest ensemble variance  

This was a clever insight when the uncertainty estimates were not trustworthy.  
The optimiser became more stable and less prone to wild exploratory jumps.

### Week 7–8: Large Scale Random Search + Ensemble Mean
**References:** week7.ipynb, week8.ipynb  

This was a major turning point. The following were introduced:

- 20,000 random candidates per function  
- Ensemble prediction = mean across 6 models  
- Selection = argmax of ensemble mean  

This simple strategy was shockingly effective.  
It avoided the pitfalls of uncertainty based acquisition and instead relied on brute force sampling and ensemble smoothing.  
This phase produced several breakthroughs — especially for F1, F5, F7, and F8.  
The optimiser had found its identity — a stable, model agnostic ranking machine.

### Week 9: Continued Ensemble Search with More Data
**Reference:** week9.txt  

Week 9 extended the Week 7–8 strategy but with:

- Eight weeks of historical data  
- More stable ensemble fits  
- Better localisation of peaks  

The optimiser was now confident enough to exploit aggressively.

### Week 10: Cluster Aware Candidate Generation + UCB Acquisition
**Reference:** week10.ipynb  

This week introduced sophistication:

- Global candidates (8,000 uniform samples)  
- Local candidates around top 3 historical points  
- UCB style acquisition: `mean + λ * std`  

This hybrid strategy balanced:

- Broad exploration  
- Local refinement  
- Uncertainty aware scoring  

It was the first time the optimiser explicitly used trust region like behaviour.

### Week 11: PCA / Kernel PCA + Ensemble + UCB
**Reference:** week11.ipynb  

Week 11 added dimensionality reduction:

- Linear PCA for low dimensional functions  
- Kernel PCA for higher dimensional ones  
- Automatic component selection based on variance or eigenvalue energy  

Candidates were:

- Sampled globally + locally  
- Transformed into PCA space  
- Scored using ensemble mean + std  
- Selected via UCB  

This allowed the optimiser to capture nonlinear structure in functions like F7 and F8.

### Week 12–13: Final Round Exploitation (Tight Radius, Low Uncertainty Weight)
**Reference:** week12.ipynb  

The final optimisation week was pure exploitation:

- More global samples (10,000)  
- More local samples (2,500 per top point)  
- Tighter radius (0.03)  
- Lower uncertainty weight (λ = 0.2)  
- PCA/Kernel PCA still active  

This configuration was intentionally greedy — the optimiser had enough data to trust its instincts.

## Emergent Behaviour: Trust Regions Without Trust Regions
One of the most interesting aspects of the optimiser is that the trust region behaviour emerged naturally, even before it was explicitly engineered.

Across weeks:

- Candidate sampling narrowed  
- Ensemble predictions stabilised  
- Steps became smaller  
- Exploration faded  
- Exploitation dominated  

This emergent behaviour is a hallmark of a system that has internalised the landscape.

## What the Optimiser Learned About the Functions
Through its journey, the optimiser uncovered several truths:

- F1, F4, F6: extremely peaked and required tiny steps.  
- F2: noisy and repeated evaluations outperform exploration.  
- F5: boundary optimum, x₂ and x₃ must approach 1.0.  
- F7, F8: smooth, directional and momentum works.  

These insights shaped the final weeks of strategy.