
```mermaid
flowchart TD
    Start --> Decision{Is condition true?}
    Decision -->|Yes| Process
    Decision -->|No| End
    Process --> End
