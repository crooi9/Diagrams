```mermaid
graph TD
    A[Start Calculate] --> B{Any Related Value IsNotApplicable?};
    B -- Yes --> C[Return "N/A"];
    B -- No --> D{Any Related Value IsNullOrEmpty?};
    D -- Yes --> E[Return null];
    D -- No --> F{Any Related Value IsFailed?};
    F -- Yes --> G[Return "FAIL"];
    F -- No --> H{Any Related Value IsBroken?};
    H -- Yes --> I[Return "Broken"];
    H -- No --> J{Any Related Value IsNotNumber?};
    J -- Yes --> C;
    J -- No --> K[Format Expression String];
    K --> L{try...catch};
    L -- try --> M[Evaluate Expression];
    L -- catch --> N[Return string.Empty];
    M --> O[Get Fixed Points from Format];
    M --> P[Get Formatted Number];
    subgraph "Potential Culture Issue"
        P
    end
    style P fill:#add8e6,stroke:#333,stroke-width:2px
    note right of P
        GetFormattedNumber uses value.ToString()
        without specifying CultureInfo.InvariantCulture.
        This can lead to parsing errors if the
        current culture's decimal separator
        differs from what the evaluator produced.
    end note
    P --> Q[Return Formatted Value];