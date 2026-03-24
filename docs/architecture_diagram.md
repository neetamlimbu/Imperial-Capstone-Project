flowchart TD

    A[Raw Input Data] --> B[Historical Outputs]
    B --> C[Data Preprocessing]
    C --> D[Data Storage]

    D --> E[Train Surrogate Models]
    E --> F[Surrogate Models]
    F --> G[Cross Validation]

    G --> H[Generate Candidates]
    H --> I[Random and Clustered Sampling]
    I --> J[Compute Acquisition Scores]
    J --> K[EI or Uncertainty Scoring]
    K --> L[Select Next Query]

    L --> M[Execute Query]
    M --> N[Store Results]

    N --> O[Performance Charts]
    N --> P[Uncertainty Plots]
    N --> Q[Reports and Insights]