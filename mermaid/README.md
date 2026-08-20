# Mermaid

This folder demonstrates rendered [Mermaid](https://mermaid.js.org/) diagrams
inside provisioned folder documentation. Each tab shows a different diagram
type.

## Git Sync flow

```mermaid
flowchart LR
    Dev[Developer] -->|git push| Repo[(Git repository)]
    Repo -->|webhook / poll| Sync[Grafana Git Sync]
    Sync -->|provision| Grafana[Grafana instance]
    Grafana -->|render tabs| User[Folder docs]
```

## More diagram types

A gallery of different Mermaid diagram types rendered from a single doc.

### Pie chart

```mermaid
pie title Dashboards by folder
    "monitoring" : 5
    "applications" : 5
    "infrastructure" : 5
    "business" : 4
    "security" : 3
```

### Class diagram

```mermaid
classDiagram
    class Repository {
        +String url
        +String branch
        +sync()
    }
    class Folder {
        +String title
        +listDocs()
    }
    class Document {
        +String path
        +String content
        +render()
    }
    Repository "1" o-- "many" Folder
    Folder "1" o-- "many" Document
```

### Entity relationship diagram

```mermaid
erDiagram
    REPOSITORY ||--o{ FOLDER : contains
    FOLDER ||--o{ DASHBOARD : contains
    FOLDER ||--o{ DOC : contains
    DOC }o--|| DOCTYPE : "is a"
```

### Gantt chart

```mermaid
gantt
    title Git Sync rollout
    dateFormat YYYY-MM-DD
    section Setup
    Connect repository      :done,    a1, 2026-08-01, 3d
    Provision folders       :done,    a2, after a1, 2d
    section Docs
    Author folder docs      :active,  b1, 2026-08-18, 4d
    Demo doc tabs           :         b2, after b1, 2d
```

### User journey

```mermaid
journey
    title Editing a provisioned dashboard
    section Discover
      Open folder: 5: User
      Read docs in tabs: 4: User
    section Change
      Edit dashboard JSON: 3: User
      Open pull request: 4: User
    section Ship
      Merge & sync: 5: User, Grafana
```

### Timeline

```mermaid
timeline
    title Folder docs feature
    2026-07 : README rendering behind toggle
    2026-08 : GitHub-style doc tabs
            : More overflow dropdown
            : Mermaid diagram rendering
```

### Git graph

```mermaid
gitGraph
    commit id: "init"
    branch demo/folder-doc-tabs
    checkout demo/folder-doc-tabs
    commit id: "folder docs"
    commit id: "mermaid folder"
    commit id: "overflow docs"
    commit id: "diagram gallery"
```

### Invalid diagram (error handling)

This block is intentionally malformed to check how the renderer handles a
diagram it cannot parse — it should surface an error rather than break the page.

```mermaid
flowchart LR
    A[Start] --> B{Decision
    B -->|yes| C((End)
    B ==> not-a-real-syntax >>> D
    C --| dangling
```

## Tabs in this folder

| Tab | Diagram type |
|-----|--------------|
| README | Flowchart + gallery (pie, class, ER, gantt, journey, timeline, git) |
| Architecture | Component / flowchart |
| Sequence | Sequence diagram |
| States | State diagram |
