```mermaid
graph TD
    subgraph "Start"
        A["config.TfsApplicationIndexUri"] --> B{"applicationIndexAccess.GetOriginalApplicationIndex"};
        C["config.TfsPilDataIndexUri"] --> D{"applicationIndexAccess.GetOriginalApplicationIndex"};
    end

    subgraph "Data Retrieval"
        B --> E["allAppIndex"];
        D --> F["pilDataIndex"];
    end

    subgraph "Initial Processing"
        E --> G["Select(item => item.PartNumber).Distinct()"];
        G --> H["uniqueAppPartNumbers"];
        I["new ApplicationIndex()"] --> J["newPilDataIndex"];
    end

    subgraph "Loop for each uniquePartNumber"
        H --> K{"For each uniquePartNumber"};
        K --> L["venusService.QueryDtoProcedure(partNumber)"];
        L --> M{"dtoProcedure != null?"};

        M -- Yes --> N["venusService.GetProcedureStatus(dtoProcedure)"];
        N --> O{"status == VenusApproveStatus.ReviewCompleted?"};
        O -- Yes --> P["venusToPandaConverter.GetLatestIndexItemByPil(dtoProcedure)"];
        P --> Q["newPilDataIndex.Items.Add(newIndexItem)"];
        Q -- continue --> K;

        O -- No --> R{"pilDataIndex.Items.FirstOrDefault(...)"};
        M -- No --> R;

        R -- "Found (matchedOldIndexItem != null)" --> S["newPilDataIndex.Items.Add(matchedOldIndexItem)"];
        S --> K;

        R -- "Not Found" --> T{"dtoProcedure != null?"};
        T -- Yes --> U["venusToPandaConverter.GetLatestIndexItemByPil(dtoProcedure)"];
        U --> V["newPilDataIndex.Items.Add(newIndexItem)"];
        V --> K;
        T -- No --> K;
    end

    subgraph "Finalization"
        J --> W["applicationIndexAccess.SaveApplicationIndex(newPilDataIndex, ...)"];
        C --> W;
        W --> X["CommitFileToTfs(...)"];
        C --> X;
        X --> Y((End));
    end