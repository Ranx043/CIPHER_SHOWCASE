# CIPHER

## Solana Pump.fun Sniper Bot | Creator Scoring System | Paper Trading

> **Intelligent token sniping with creator trust analysis and risk management**

---

## Overview

**CIPHER** is an advanced Solana trading bot that monitors Pump.fun token launches in real-time, evaluates creator credibility through a trust scoring system, and executes paper trades with configurable risk parameters.

---

## System Architecture

```mermaid
flowchart TB
    subgraph INPUT["📡 Data Sources"]
        WS[Pump.fun WebSocket]
        RPC[Solana RPC]
    end

    subgraph COLLECTOR["🔍 Data Collector"]
        CONNECT[WebSocket Connection]
        SUBSCRIBE[Event Subscription]
        PARSE[Message Parser]
    end

    subgraph ANALYSIS["🧠 Analysis Engine"]
        CREATOR[Creator Analyzer]
        SCORE[Trust Score Calculator]
        HISTORY[Historical Performance]
    end

    subgraph TRADER["💹 Paper Trader"]
        EVAL[Token Evaluator]
        POSITION[Position Manager]
        EXIT[Exit Logic]
    end

    subgraph STORAGE["💾 Database"]
        TOKENS[Tokens Table]
        CREATORS[Creators Table]
        TRADES[Trades Table]
        PORTFOLIO[Portfolio Table]
    end

    WS --> CONNECT --> SUBSCRIBE --> PARSE
    RPC --> ANALYSIS
    PARSE --> CREATOR --> SCORE
    HISTORY --> SCORE
    SCORE --> EVAL --> POSITION --> EXIT

    PARSE --> TOKENS
    CREATOR --> CREATORS
    POSITION --> TRADES
    EXIT --> PORTFOLIO

    style INPUT fill:#e3f2fd
    style COLLECTOR fill:#fff3e0
    style ANALYSIS fill:#e8f5e9
    style TRADER fill:#fce4ec
    style STORAGE fill:#f3e5f5
```

---

## Real-Time Data Collection Flow

```mermaid
sequenceDiagram
    participant PF as Pump.fun
    participant WS as WebSocket
    participant COL as Collector
    participant DB as Database
    participant PT as Paper Trader

    PF->>WS: New Token Event
    WS->>COL: JSON Message
    COL->>COL: Parse & Validate

    alt Valid Token
        COL->>DB: Save Token
        COL->>DB: Get/Create Creator
        DB-->>COL: Creator Info + Score
        COL->>PT: Token Data + Score

        alt Score >= Threshold
            PT->>PT: Evaluate Position Size
            PT->>DB: Open Paper Trade
            PT-->>COL: Trade Opened
        else Score < Threshold
            PT-->>COL: Skip Token
        end
    else Invalid Data
        COL->>COL: Log & Skip
    end

    Note over PF,PT: Continuous monitoring ~100 tokens/hour
```

---

## Creator Trust Scoring System

```mermaid
flowchart TB
    subgraph FACTORS["📊 Scoring Factors"]
        TOKENS_CREATED[Tokens Created]
        SUCCESS_RATE[Success Rate]
        AVG_GAIN[Average Gains]
        RUG_HISTORY[Rug History]
    end

    subgraph CALC["🔢 Score Calculation"]
        BASE[Base Score: 50]
        ADJUST[Adjustments]
        FINAL[Final Score: 0-100]
    end

    subgraph TIERS["🎯 Score Tiers"]
        ELITE["Elite (85-100)<br/>Large position"]
        HIGH["High (70-84)<br/>Medium position"]
        MEDIUM["Medium (55-69)<br/>Small position"]
        LOW["Low (40-54)<br/>Micro position"]
        SKIP["Skip (<40)<br/>No trade"]
    end

    TOKENS_CREATED --> ADJUST
    SUCCESS_RATE --> ADJUST
    AVG_GAIN --> ADJUST
    RUG_HISTORY --> ADJUST

    BASE --> ADJUST --> FINAL

    FINAL --> ELITE
    FINAL --> HIGH
    FINAL --> MEDIUM
    FINAL --> LOW
    FINAL --> SKIP

    style FACTORS fill:#e3f2fd
    style CALC fill:#fff3e0
    style TIERS fill:#e8f5e9
```

---

## Position Sizing Logic

```mermaid
flowchart LR
    subgraph SCORE["Creator Score"]
        S85["85-100"]
        S70["70-84"]
        S55["55-69"]
        S40["40-54"]
    end

    subgraph SIZE["Position Size"]
        P1["0.1 SOL<br/>Max Confidence"]
        P2["0.05 SOL<br/>High Confidence"]
        P3["0.02 SOL<br/>Medium Confidence"]
        P4["0.01 SOL<br/>Low Confidence"]
    end

    subgraph RISK["Risk Level"]
        R1["5% Portfolio"]
        R2["2.5% Portfolio"]
        R3["1% Portfolio"]
        R4["0.5% Portfolio"]
    end

    S85 --> P1 --> R1
    S70 --> P2 --> R2
    S55 --> P3 --> R3
    S40 --> P4 --> R4

    style SCORE fill:#e3f2fd
    style SIZE fill:#fff3e0
    style RISK fill:#e8f5e9
```

---

## Exit Strategy System

```mermaid
flowchart TB
    subgraph POSITION["📈 Open Position"]
        ENTRY[Entry Price]
        CURRENT[Current Price]
    end

    subgraph CALC2["🔢 P/L Calculation"]
        CHANGE["Price Change %"]
    end

    subgraph EXITS["🚪 Exit Conditions"]
        SL["Stop Loss<br/>-30% default"]
        TP1["Take Profit 1<br/>+50% (partial)"]
        TP2["Take Profit 2<br/>+100% (full)"]
        EMERGENCY["Emergency Close<br/>Control file trigger"]
    end

    subgraph ACTIONS["⚡ Actions"]
        CLOSE_SL["Close + Log Loss"]
        CLOSE_TP["Close + Log Profit"]
        CLOSE_EM["Close All Positions"]
    end

    ENTRY --> CHANGE
    CURRENT --> CHANGE

    CHANGE --> SL --> CLOSE_SL
    CHANGE --> TP1 --> CLOSE_TP
    CHANGE --> TP2 --> CLOSE_TP
    EMERGENCY --> CLOSE_EM

    style POSITION fill:#e3f2fd
    style CALC2 fill:#fff3e0
    style EXITS fill:#fce4ec
    style ACTIONS fill:#e8f5e9
```

---

## Hot Reload Control System

```mermaid
flowchart TB
    subgraph CONTROL["🎛️ control.json"]
        TRADING["trading_enabled"]
        PAUSE["pause_new_trades"]
        BLACKLIST["blacklist_creators"]
        WHITELIST["whitelist_creators"]
        PARAMS["Trading Parameters"]
        CLOSE_ALL["close_all_positions"]
    end

    subgraph TRADER2["💹 Paper Trader"]
        CHECK["Check on each evaluation"]
        APPLY["Apply settings"]
    end

    subgraph EFFECTS["📋 Effects"]
        E1["Enable/Disable trading"]
        E2["Pause without closing"]
        E3["Filter by creator"]
        E4["Adjust SL/TP/Size"]
        E5["Emergency exit"]
    end

    CONTROL --> CHECK --> APPLY

    TRADING --> E1
    PAUSE --> E2
    BLACKLIST --> E3
    WHITELIST --> E3
    PARAMS --> E4
    CLOSE_ALL --> E5

    style CONTROL fill:#e3f2fd
    style TRADER2 fill:#fff3e0
    style EFFECTS fill:#e8f5e9
```

---

## Database Schema

```mermaid
erDiagram
    TOKENS {
        string mint PK
        string name
        string symbol
        string creator FK
        string uri
        float last_price
        float last_mcap
        datetime created_at
    }

    CREATORS {
        string address PK
        int tokens_created
        int successful_tokens
        float avg_gain
        int trust_score
        datetime first_seen
    }

    PAPER_TRADES {
        int id PK
        string mint FK
        string creator FK
        float entry_price
        float entry_mcap
        float amount_sol
        int creator_score
        float exit_price
        string exit_reason
        float profit_sol
        float profit_percent
        datetime opened_at
        datetime closed_at
    }

    PORTFOLIO {
        int id PK
        float balance_sol
        float total_profit
        int total_trades
        int wins
        int losses
    }

    CREATORS ||--o{ TOKENS : creates
    CREATORS ||--o{ PAPER_TRADES : "traded by"
    TOKENS ||--o{ PAPER_TRADES : "traded"
```

---

## Module Architecture

```mermaid
flowchart TB
    subgraph CORE["🔧 Core Modules"]
        CONFIG[config.py<br/>Parameters & Constants]
        DATABASE[database.py<br/>SQLite Operations]
    end

    subgraph COLLECTORS["📡 Data Collection"]
        PUMPFUN[collector.py<br/>WebSocket Client]
        DEXSCREEN[dexscreener.py<br/>Price Data]
    end

    subgraph TRADING["💹 Trading Logic"]
        PAPER[paper_trader.py<br/>Paper Trading Engine]
        EVALUATOR[Token Evaluator<br/>Score-based decisions]
    end

    subgraph ENTRY["🚀 Entry Points"]
        MAIN[main.py<br/>Bot Orchestration]
        TEST[Test Runners<br/>Individual modules]
    end

    CONFIG --> DATABASE
    CONFIG --> PUMPFUN
    CONFIG --> PAPER

    DATABASE --> PUMPFUN
    DATABASE --> PAPER

    PUMPFUN --> PAPER
    DEXSCREEN --> PAPER

    MAIN --> PUMPFUN
    MAIN --> PAPER

    style CORE fill:#e3f2fd
    style COLLECTORS fill:#fff3e0
    style TRADING fill:#e8f5e9
    style ENTRY fill:#fce4ec
```

---

## Configuration Parameters

| Category | Parameters | Description |
|----------|------------|-------------|
| **Portfolio** | 3 | Initial balance, max positions, max size |
| **Risk Management** | 4 | Stop loss, take profit levels |
| **Creator Scoring** | 5 | Min score, min tokens, thresholds |
| **Position Sizing** | 4 | Size per score tier |
| **Control** | 6 | Hot reload parameters |
| **Total** | **22** | Full customization |

---

## Technology Stack

```mermaid
mindmap
  root((CIPHER))
    Platform
      Solana Blockchain
      Pump.fun DEX
      DexScreener API
    Language
      Python 3.11+
      Asyncio
      Type Hints
    Data
      SQLite
      WebSocket
      JSON Control
    Libraries
      websockets
      aiohttp
      aiosqlite
    Features
      Paper Trading
      Creator Scoring
      Hot Reload Config
      Risk Management
```

| Component | Technology |
|-----------|------------|
| **Blockchain** | Solana |
| **DEX** | Pump.fun |
| **Language** | Python 3.11+ |
| **Async** | asyncio, aiohttp |
| **WebSocket** | websockets |
| **Database** | SQLite (aiosqlite) |
| **Config** | JSON hot reload |

---

## Key Features

- **Real-Time Monitoring** - WebSocket connection to Pump.fun for instant token detection
- **Creator Trust Scoring** - Historical performance analysis for risk assessment
- **Dynamic Position Sizing** - Score-based allocation protects capital
- **Paper Trading Mode** - Validate strategy without risking real funds
- **Hot Reload Config** - Adjust parameters without restarting
- **Emergency Controls** - One-click close all positions
- **Comprehensive Logging** - Track every decision for analysis

---

## Trading Flow Summary

```mermaid
flowchart LR
    NEW[New Token] --> PARSE[Parse Data]
    PARSE --> SCORE[Get Creator Score]
    SCORE --> EVAL{Score >= Min?}

    EVAL -->|Yes| SIZE[Calculate Size]
    EVAL -->|No| SKIP[Skip Token]

    SIZE --> BALANCE{Balance OK?}
    BALANCE -->|Yes| OPEN[Open Position]
    BALANCE -->|No| SKIP

    OPEN --> MONITOR[Monitor Price]
    MONITOR --> EXIT{Exit Trigger?}

    EXIT -->|Stop Loss| CLOSE_L[Close - Loss]
    EXIT -->|Take Profit| CLOSE_W[Close - Win]
    EXIT -->|No| MONITOR

    style NEW fill:#e3f2fd
    style EVAL fill:#fff3e0
    style OPEN fill:#e8f5e9
    style EXIT fill:#fce4ec
```

---

## Capabilities Demonstrated

- **Blockchain Integration** - Solana ecosystem, DEX protocols
- **Real-Time Systems** - WebSocket streaming, async processing
- **Risk Management** - Position sizing, stop losses, take profits
- **Data Analysis** - Creator scoring, historical performance
- **System Design** - Modular architecture, hot reload configuration
- **Python Async** - High-performance concurrent operations

---

**Technologies:** Python | Solana | Pump.fun | WebSocket | SQLite | Asyncio

**Category:** Cryptocurrency | Trading Systems | Blockchain | DeFi

---

*This project demonstrates capability to build sophisticated crypto trading systems with real-time data processing, risk management, and intelligent decision-making algorithms.*
