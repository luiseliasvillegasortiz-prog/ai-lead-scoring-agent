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


´´

```
⚙️ How It Works
Guardrails: Initial data sanitization and validation (URL and email cleaning, competitor filtering).

Agent Planner: Generates dynamic research questions based on the prospect's role (e.g., specific questions for a CTO vs. a CEO).

Resilient Scraper:

Attempts to extract direct HTML content from the company website.

Self-Healing Mechanism: If the direct scrape fails (Error 403/404), the system activates a Google Search Simulation to recover context and prevent workflow failure.

The Analyst (RAG): An LLM analyzes the recovered information against the strategic questions to determine "Product-Market Fit".

Router: Classifies the lead (Hot, Warm, Discard) based on the calculated score and updates the CRM (Google Sheets).

🛠️ Tech Stack
Orchestrator: n8n (Self-hosted/Desktop)

LLM Engine: Groq (Llama 3 70B / 8B)

Database: Google Sheets (simulating a CRM)

Languages: JavaScript (for control logic and error handling)

🚀 Installation & Usage
Prerequisites:

n8n installed.

Groq Cloud API Key.

Google Cloud Credentials (for Sheets API).

Import:

Download the AI_Lead_Scoring_Agent.json file from this repository.

In n8n: Menu > Import > Select file.

Configuration:

Update credentials in the Google Sheets and HTTP Request nodes.

Configure your Sheet ID in the final database nodes.

Developed as a Proof of Concept for Autonomous AI Agents.

