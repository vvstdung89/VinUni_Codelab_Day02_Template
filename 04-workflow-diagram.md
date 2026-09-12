# 📊 04-workflow-diagram.md — Workflow Diagram (Mermaid)

```mermaid
flowchart TD
    A[Resident (authenticated) opens Vinhomes Residents app] --> B{Select request type}
    B -->|Submit complaint| C[Submit ticket (text + optional image)]
    C --> D{AI Router}
    D -->|Low confidence (<0.7)| E[Manual Review Queue]
    D -->|High confidence| F[LLM Classification]
    F --> G{Priority?}
    G -->|High| H[Escalate to Hotline]
    G -->|Medium/Low| I[Route to appropriate department queue]
    H --> J[Hotline number displayed to resident]
    I --> K[Management UI (staff view tickets)]
    K --> L[Staff Approve / Edit classification]
    L --> M[Ticket moves to execution (maintenance, cleaning, …)]
    style A fill:#e3f2fd,stroke:#2196f3,stroke-width:2px
    style K fill:#fff3e0,stroke:#ff9800,stroke-width:2px
```

---

*Diagram mô tả quy trình tự động phân loại và định tuyến phản ánh cư dân, với các ranh giới an toàn (fallback, Human‑in‑the‑Loop) được tích hợp.*
