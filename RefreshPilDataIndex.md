```mermaid
graph TD
    subgraph RefreshPilDataIndex
        A[Start] --> B{Get TfsApplicationIndexUri};
        B --> C[applicationIndexAccess.GetOriginalApplicationIndex];
        C --> D{Get uniqueAppPartNumbers};
        D --> E{Get TfsPilDataIndexUri};
        E --> F[applicationIndexAccess.GetOriginalApplicationIndex];
        F --> G[Initialize newPilDataIndex];
        G --> H{Loop through uniqueAppPartNumbers};

        subgraph "For each uniquePartNumber"
            H --> I(try);
            I --> J[venusService.QueryDtoProcedure];
            J -- TimeoutException --> Log1[Log 'QueryDtoProcedure' error];
            Log1 --> SetError1[Set error variable];
            SetError1 --> LoopEnd;
            J -- Success --> K{dtoProcedure != null?};

            K -- Yes --> L(try);
            L --> M[venusService.GetProcedureStatus];
            M -- TimeoutException --> Log2[Log 'GetProcedureStatus' error];
            Log2 --> SetError2[Set error variable];
            SetError2 --> FindOldItem;
            M -- Success --> N{status == ReviewCompleted?};

            N -- Yes --> O(try);
            O --> P[venusToPandaConverter.GetLatestIndexItemByPil];
            P -- TimeoutException --> Log3[Log 'venusToPandaConverter' error];
            Log3 --> SetError3[Set error variable];
            SetError3 --> LoopEnd;
            P -- Success --> Q[Add newIndexItem to newPilDataIndex];
            Q --> LoopEnd;

            N -- No --> FindOldItem;
            K -- No --> FindOldItem;

            FindOldItem{Find matchedOldIndexItem in pilDataIndex};
            FindOldItem -- Found --> AddOldItem[Add matchedOldIndexItem to newPilDataIndex];
            AddOldItem --> LoopEnd;
            FindOldItem -- Not Found --> CheckDtoAgain{dtoProcedure != null?};

            CheckDtoAgain -- Yes --> R(try);
            R --> S[venusToPandaConverter.GetLatestIndexItemByPil];
            S -- TimeoutException --> Log4[Log 'venusToPandaConverter' error];
            Log4 --> SetError4[Set error variable];
            SetError4 --> LoopEnd;
            S -- Success --> T[Add newIndexItem to newPilDataIndex];
            T --> LoopEnd;

            CheckDtoAgain -- No --> LoopEnd;
        end

        H -- All processed --> U[applicationIndexAccess.SaveApplicationIndex];
        U --> V[CommitFileToTfs];
        V --> W{error != null?};
        W -- Yes --> X(throw error);
        W -- No --> Y[End];
        X --> Z[Error End];
        LoopEnd --> H;
    end

    style Log1 fill:#f9f,stroke:#333,stroke-width:2px
    style Log2 fill:#f9f,stroke:#333,stroke-width:2px
    style Log3 fill:#f9f,stroke:#333,stroke-width:2px
    style Log4 fill:#f9f,stroke:#333,stroke-width:2px
    style SetError1 fill:#f9f,stroke:#333,stroke-width:2px
    style SetError2 fill:#f9f,stroke:#333,stroke-width:2px
    style SetError3 fill:#f9f,stroke:#333,stroke-width:2px
    style SetError4 fill:#f9f,stroke:#333,stroke-width:2px
    style X fill:#ff0000,stroke:#333,stroke-width:4px
    style Z fill:#ff0000,stroke:#333,stroke-width:4px