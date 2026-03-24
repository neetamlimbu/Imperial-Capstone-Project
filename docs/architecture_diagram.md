flowchart TD

    %% --- Data Management ---
    A[Raw Input Data] --> B[Historical Outputs]
    B --> C[Data Preprocessing]
    C --> D[(Data Storage)]

    %% --- Surrogate Modelling ---
    D --> E[Train Surrogate Models]
    E --> F[GPR / Ensemble Models]
    F --> G[Cross-Validation]

    %% --- Acquisition & Selection ---
    G --> H[Generate Candidates]
    H --> I[Random & Clustered Sampling]
    I --> J[Compute Acquisition Scores]
    J --> K[EI / Uncertainty-Based Scoring]
    K --> L[Select Next Query]

    %% --- Evaluation & Logging ---
    L --> M[Execute Query]
    M --> N[Store Results]

    %% --- Visualisation & Reporting ---
    N --> O[Performance Charts]
    N --> P[Uncertainty Plots]
    N --> Q[Reports & Insights]

    %% --- Section Labels (non-breaking) ---
    classDef section fill:#f0f0f0,stroke:#999,stroke-width:1px,color:#333;

    subgraph DM[Data Management]
        A --> B --> C --> D
    end
    class DM section;

    subgraph SM[Surrogate Modelling]
        E --> F --> G
    end
    class SM section;

    subgraph AS[Acquisition & Selection]
        H --> I --> J --> K --> L
    end
    class AS section;

    subgraph EL[Evaluation & Logging]
        M --> N
    end
    class EL section;

    subgraph VR[Visualisation & Reporting]
        O
        P
        Q
    end
    class VR section;