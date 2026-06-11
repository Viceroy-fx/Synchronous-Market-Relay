# Synchronous-Market-Relay
A high-performance, multithreaded Python pipeline for real-time financial telemetry. Engineered with a zero-lag GUI, O(1) memory caching to bypass rate limits, and a high-availability dual-AI failover system for uninterrupted data analysis.


```mermaid
graph TD
    classDef core fill:#1E1E1E,stroke:#4CAF50,stroke-width:2px,color:#fff;
    classDef ui fill:#2D2D2D,stroke:#2196F3,stroke-width:2px,color:#fff;
    classDef module fill:#333333,stroke:#FF9800,stroke-width:1px,color:#fff;
    classDef api fill:#1A1A1A,stroke:#E91E63,stroke-width:2px,color:#fff,stroke-dasharray: 5 5;

    subgraph Main_Thread ["Main Thread (Synchronous)"]
        UI[Tkinter Desktop GUI Dashboard]:::ui
    end

    subgraph Background_Thread ["Background Worker Thread (Asynchronous)"]
        Loop[1-Minute Polling Engine]:::core

        subgraph Risk_Layer ["1. Institutional Risk Layer"]
            News[FairEconomy XML Scraper]:::module
            Cache["O(1) Memory Cache"]:::module
            News -->|Bypass Cloudflare| Cache
        end

        subgraph Data_Layer ["2. Market Data Layer"]
            MT5[MetaTrader 5 Local Terminal]:::module
            SMC[Deterministic SMC Math Parser]:::module
            MT5 -->|Tick Data| SMC
        end

        subgraph Compute_Layer ["3. Dual-AI Orchestration Layer"]
            Groq[Primary: Groq Llama-3.3]:::api
            Circuit[Exception Circuit Breaker]:::module
            Qwen[Fallback: Alibaba Qwen-Turbo]:::api

            Groq -- "Timeout / Rate Limit" --> Circuit
            Circuit -- "Auto-Route" --> Qwen
        end

        Loop --> Risk_Layer
        Risk_Layer -- "News Cleared" --> Data_Layer
        Data_Layer -- "System Payload Built" --> Compute_Layer
    end

    Compute_Layer -- "JSON Decision & Latency Telemetry" --> UI
