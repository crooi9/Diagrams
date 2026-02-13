```mermaid
graph TD
    A[Start Calculate] --> B{Check RelatedMeasurementValues};
    B --> C{Any value 'N/A'?};
    C -- Yes --> D[Return 'N/A'];
    C -- No --> E{Any value empty?};
    E -- Yes --> F[Return null];
    E -- No --> G{Any value 'FAIL'?};
    G -- Yes --> H[Return 'FAIL'];
    G -- No --> I{Any value 'Broken'?};
    I -- Yes --> J[Return 'Broken'];
    I -- No --> K{Any value not a number?};
    K -- Yes --> D;
    K -- No --> L[Extract values into parameters];
    L --> M[Format Expression string with parameters];
    M --> N{Evaluate expression};
    N -- Exception --> O[Return empty string];
    N -- Success --> P[Get fixed points from ValueFormat];
    P --> Q["Format evaluated number via<br/>GetFormattedNumber(<b>Convert.ToString(value, CultureInfo.InvariantCulture)</b>, fixedPoints)"];
    Q --> R[Return formatted number];
    D --> Z[End];
    F --> Z[End];
    H --> Z[End];
    J --> Z[End];
    O --> Z[End];
    R --> Z[End];

    style Q fill:#f9f,stroke:#333,stroke-width:2px