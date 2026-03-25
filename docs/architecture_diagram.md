```mermaid
flowchart TD

    %% --- Start of Weekly Cycle ---
    A[Start Week N] --> B[Load All Historical Inputs & Outputs]

    %% --- Surrogate Training ---
    B --> C[Train Surrogates\n(GPR, RF, GBM, SVR, MLP)]
    C --> D[Compute Uncertainty\n& Ensemble Disagreement]

    %% --- Candidate Generation ---
    D --> E1[Global Sampling\n(Random Uniform)]
    D --> E2[Local Sampling\n(Perturb Top Points)]
    D --> E3[Cluster-Aware Sampling\n(Around High-Value Regions)]
    D --> E4[Boundary Sampling\n(If High Uncertainty)]

    %% --- Merge Candidates ---
    E1 --> F[Merge Candidate Pool]
    E2 --> F
    E3 --> F
    E4 --> F

    %% --- Scoring ---
    F --> G[Predict Mean & Variance\nUsing GPR]
    G --> H[Compute Acquisition Scores\n(EI, UCB, Stability Penalty)]

    %% --- Selection ---
    H --> I[Select Best Candidate\n(Argmax Acquisition)]
    I --> J[Submit Query for Week N]

    %% --- Feedback Loop ---
    J --> K[Receive Output]
    K --> L[Append to Historical Data]
    L --> A
    
```