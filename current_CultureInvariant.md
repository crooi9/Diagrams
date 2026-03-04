```mermaid
graph TD
    A[Start Calculate] --> B{Any Related Value IsNotApplicable?};
    B -- Yes --> C[Return N/A];
    B -- No --> D{Any Related Value IsNullOrEmpty?};
    D -- Yes --> E[Return null];
    D -- No --> F{Any Related Value IsFailed?};
    F -- Yes --> G[Return FAIL];
    F -- No --> H{Any Related Value IsBroken?};
    H -- Yes --> I[Return Broken];
    H -- No --> J{Any Related Value IsNotNumber?};
    J -- Yes --> C;
    J -- No --> K[Format Expression String];
    K --> L{try...catch};
    L -- try --> M[Evaluate Expression];
    L -- catch --> N[Return string.Empty];
    M --> O[Get Fixed Points from Format];
    M --> P[Get Formatted Number];

subgraph Potential_Culture_Issue
        P
    end

    P --> Q[Return Formatted Value]

    NoteP["⚠️ GetFormattedNumber uses value.ToString()<br/>
    without CultureInfo.InvariantCulture.<br/>
    This may cause parsing errors when<br/>
    decimal separators differ by culture."]

    P -.-> NoteP

    style P fill:#add8e6,stroke:#333,stroke-width:2px
    style NoteP fill:#fff3cd,stroke:#856404,stroke-dasharray: 5 5
