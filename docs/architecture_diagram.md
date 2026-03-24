flowchart TD
    A[Raw Input Data] --> B[Historical Outputs]
    B --> C[Data Preprocessing]
    C --> D[Data Storage]

    D --> E[Train Surrogate Models]
    E --> F[GPR / Ensemble Models]
    F --> G[Cross-Validation]

    G --> H[Generate Candidates]
    H --> I[Random & Clustered Sampling]
    I --> J[Compute Acquisition Scores]
    J --> K[EI / Uncertainty-Based Scoring]
    K --> L[Select Next Query]

    L --> M[Execute Query]
    M --> N[Store Results]

    N --> O[Performance Charts]
    N --> P[Uncertainty Plots]
    N --> Q[Reports & Insights]

    subgraph Data Management
        A --> B --> C --> D
    end

    subgraph Surrogate Modelling
        E --> F --> G
    end

    subgraph Acquisition & Selection
        H --> I --> J --> K --> L
    end

    subgraph Evaluation & Logging
        M --> N
    end

    subgraph Visualisation & Reporting
        O
        P
        Q
    end