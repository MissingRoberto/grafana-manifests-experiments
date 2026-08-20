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

## Tabs in this folder

| Tab | Diagram type |
|-----|--------------|
| README | Flowchart |
| Architecture | Component / flowchart |
| Sequence | Sequence diagram |
| States | State diagram |
