# 🤖 Autonomous AI Lead Scoring & Qualification Agent

![n8n](https://img.shields.io/badge/Orchestrator-n8n-orange) ![AI](https://img.shields.io/badge/AI-Llama3%20%2F%20Groq-blue) ![Self-Healing](https://img.shields.io/badge/Architecture-Self--Healing-green)

A resilient autonomous agent designed to research, analyze, and qualify B2B leads automatically. This system implements a **"Self-Healing"** architecture to handle web scraping failures by falling back to a simulated search strategy, ensuring continuous operation.

## 🧠 Workflow Architecture

The system follows an advanced design pattern to ensure operational continuity:

```mermaid
graph TD
    %% Styles
    classDef trigger fill:#2E8B57,stroke:#333,stroke-width:2px,color:white;
    classDef ai fill:#6A5ACD,stroke:#333,stroke-width:2px,color:white;
    classDef logic fill:#FFD700,stroke:#333,stroke-width:2px,color:black;
    classDef action fill:#4682B4,stroke:#333,stroke-width:2px,color:white;
    classDef db fill:#20B2AA,stroke:#333,stroke-width:2px,color:white;
    classDef fallback fill:#FF6347,stroke:#333,stroke-width:2px,color:white;

    subgraph "1. Ingestion & Validation"
        A[Mock Lead Data] -->|JSON| B(Guardrail: Data Cleaning)
        B --> C{Filter: Is Valid?}
        C -- No --> D[Discard End]
    end

    subgraph "2. Research & Resilience"
        C -- Yes --> E[Agent Planner: Generates Questions]
        E --> F[Web Scraper: HTTP Request]
        F --> G{Check: Success?}
        
        %% Happy Path
        G -- Yes --> H[HTML Parser]
        
        %% Self-Healing Path
        G -- No / Error --> I[Fallback: Google Search Simulation]
        class I fallback
    end

    subgraph "3. Analysis & Decision"
        H --> J[Context Normalizer]
        I --> J
        J --> K[The Analyst: Scoring LLM]
        K --> L[JSON Parser]
    end

    subgraph "4. Routing & Storage"
        L --> M{Router: Score Rules}
        M -- Score > 70 --> N[(DB: Hot Leads)]
        M -- Score 40-70 --> O[(DB: Warm Leads)]
        M -- Score < 40 --> P[(DB: Discard)]
    end

    %% Class Assignment
    class A trigger;
    class E,K ai;
    class C,G,M logic;
    class F,H,J,L action;
    class N,O,P db;
