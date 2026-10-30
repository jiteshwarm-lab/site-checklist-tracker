# Site Deployment Checks

A lightweight tracker for running **pre-deployment** and **post-deployment** checks at GreyOrange sites. Pick a site, choose what is being deployed, and work through one combined checklist tied to a CRN number.

---

## How it works

### A typical deployment

```mermaid
sequenceDiagram
    actor E as Engineer
    participant T as Tracker
    participant S as Browser storage
    E->>T: Open the tracker and pick a site
    E->>T: Select the deployment type(s)
    T-->>E: Combined pre-checks and post-checks
    E->>T: Enter the CRN number
    T->>S: Save the CRN
    loop Every pre-check
        E->>T: Tick the check
        T->>S: Save the tick
    end
    Note over E: Run the deployment (outside the tracker)
    loop Every post-check
        E->>T: Tick the check
        T->>S: Save the tick
    end
    E->>T: Reset completed checklist
    T-->>E: Allowed only if the CRN is filled and every check is ticked
    T->>S: Clear the ticks and the CRN
```

### Moving between screens

```mermaid
flowchart LR
    A(["Open the tracker"]) --> B["Landing page<br/>site cards + search"]
    B -- "Click a site card" --> C["Checklist screen"]
    C -- "All sites button" --> B
```

### Choosing what is being deployed

Each selected type or sub-type is called a **part**. You can select any mix of parts.

```mermaid
flowchart TD
    A["Open a site"] --> B["No type selected"]
    B --> C["GreyMatter"]
    B --> D["JSON"]
    B --> E["Infra"]
    D --> F["JSON sub-types"]
    F --> G["Map"]
    F --> H["PPS"]
    F --> I["Zigbee"]
    C --> J["Selected parts"]
    E --> J
    G --> J
    H --> J
    I --> J
    J --> K["One combined checklist"]
```

JSON itself is not a part. Pick at least one of Map, PPS or Zigbee to see its checks. Until you do, the page asks you to choose a JSON type.

### How the combined checklist is built

```mermaid
flowchart LR
    P1["GreyMatter checks"] --> M
    P2["JSON · Map checks"] --> M
    P3["JSON · PPS checks"] --> M
    M{"Same wording in<br/>more than one part?"}
    M -- Yes --> C["Common checks<br/>(listed once)"]
    M -- No --> S["Listed under<br/>its own part"]
    C --> OUT["Pre-check and<br/>post-check cards"]
    S --> OUT
```

- A merged check is a single tick. Ticking it covers every part that shares it, and it counts once in the progress total.
- Wording is compared ignoring capital letters and punctuation. "Backup current JSON files" and "Backup current JSON files." are the same check, but "Back up the JSON files" is a different one.
- With only one part selected there are no section headings.

### When Reset is allowed

```mermaid
flowchart TD
    A(["Click Reset completed checklist"]) --> B{"CRN number filled in?"}
    B -- No --> X1["Popup: CRN number is not filled"]
    B -- Yes --> C{"Every pre-check and<br/>post-check ticked?"}
    C -- No --> X2["Popup: All checks are not complete"]
    C -- Yes --> D["Clear the ticks and the CRN<br/>for this selection"]
    D --> E(["Ready for the next deployment"])
```

Reset only affects the current selection of parts on the current site. It never removes checks you added.

---

## Where data is stored

Everything is saved in your own browser (`localStorage`). There is no server and no account.

```mermaid
flowchart LR
    subgraph Browser
        H["index.html<br/>page, check lists, logic"]
        C["style.css<br/>light and dark themes"]
        L[("localStorage")]
    end
    H --- C
    H <--> L
    F["Google Fonts<br/>Outfit typeface"] -.-> H
```
