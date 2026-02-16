```mermaid
graph TD
    subgraph FillApplicationTests
        A[Application Object] --> B{Iterate Procedures};
        B --> C{Iterate Sections};
        C --> D{Iterate MeasurementPoints};
        D --> E{Point};
        E --> F{Filter MeasurementValues where m.IsVisible == false};
        F --> G{Check if column titles are new};
        G -- Yes --> H[AddPointTitleRow];
        G -- No --> I[Call AddPointRow];
        H --> I;
    end

    subgraph AddPointRow
        J[Point from FillApplicationTests] --> K{Filter MeasurementValues};
        K -- "m.IsVisible == true" --> L[Visible Measurement Values];
        style K fill:#f9f,stroke:#333,stroke-width:2px
        L --> M{Find and re-order 'Point Index' value};
        M --> N[Final list of values to write];
        N --> O{Iterate through final list};
        O --> P[Get/Create Cell];
        P --> Q[Set Cell Value & Style];
        O -- Next Value --> P;
    end

    I --> J;