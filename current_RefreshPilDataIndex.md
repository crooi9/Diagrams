```mermaid
graph TD
    subgraph "Start"
        A[TfsApplicationIndexUri] --> B{GetOriginalApplicationIndex};
        C[TfsPilDataIndexUri] --> D{GetOriginalApplicationIndex};
    end

    subgraph "Data Retrieval"
        B --> E[allAppIndex];
        D --> F[pilDataIndex];
    end

    subgraph "Initial Processing"
        E --> G[Select PartNumber & Get Distinct];
        G --> H[uniqueAppPartNumbers];
        I[new ApplicationIndex()] --> J[newPilDataIndex];
    end

    subgraph "Loop for each uniquePartNumber"
        H --> K{For each partNumber};
        K --> L[venusService.QueryDtoProcedure];
        L --> M{dtoProcedure != null?};

        M -- Yes --> N[venusService.GetProcedureStatus];
        N --> O{status == ReviewCompleted?};
        O -- Yes --> P[venusToPandaConverter.GetLatestIndexItemByPil];
        P --> Q[Add item to newPilDataIndex];
        Q --> K;

        O -- No --> R{Find item in pilDataIndex};
        M -- No --> R;

        R -- Found --> S[Add existing item to newPilDataIndex];
        S --> K;

        R -- Not Found --> T{dtoProcedure != null?};
        T -- Yes --> U[venusToPandaConverter.GetLatestIndexItemByPil];
        U --> V[Add item to newPilDataIndex];
        V --> K;
        T -- No --> K;
    end

    subgraph "Finalization"
        J --> W[applicationIndexAccess.SaveApplicationIndex];
        C --> W;
        W --> X[CommitFileToTfs];
        C --> X;
        X --> Y[End];
    end