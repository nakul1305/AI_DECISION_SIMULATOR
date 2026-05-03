<div align="center">

<!-- Banner -->
<img src="https://capsule-render.vercel.app/api?type=waving&color=gradient&customColorList=6,11,20&height=200&section=header&text=AI%20Decision%20Simulator&fontSize=48&fontColor=fff&animation=fadeIn&fontAlignY=38&desc=Intelligent%20Multi-Domain%20Decision%20Intelligence%20Platform&descAlignY=60&descSize=18" width="100%"/>

<!-- Badges -->
<p align="center">
  <img src="https://img.shields.io/badge/Python-3.11+-3776AB?style=for-the-badge&logo=python&logoColor=white"/>
  <img src="https://img.shields.io/badge/Flask-3.x-000000?style=for-the-badge&logo=flask&logoColor=white"/>
  <img src="https://img.shields.io/badge/Ollama-Local%20LLM-FF6B35?style=for-the-badge&logo=llama&logoColor=white"/>
  <img src="https://img.shields.io/badge/Anthropic-Claude%20API-CC785C?style=for-the-badge&logo=anthropic&logoColor=white"/>
  <img src="https://img.shields.io/badge/SQLite-Database-003B57?style=for-the-badge&logo=sqlite&logoColor=white"/>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/License-MIT-green?style=flat-square"/>
  <img src="https://img.shields.io/badge/Status-Active-brightgreen?style=flat-square"/>
  <img src="https://img.shields.io/badge/PRs-Welcome-blue?style=flat-square"/>
  <img src="https://img.shields.io/badge/B.Tech%20Project-CSE-purple?style=flat-square"/>
</p>

<br/>

> **A hybrid AI decision-support system** that combines rule-based logic with Large Language Models (Ollama + Anthropic) to generate structured, multi-outcome decision reports with real-time streaming — across Finance, Healthcare, Technology, Business, Education, and Risk domains.

<br/>

[🚀 Quick Start](#-quick-start) · [🏗 Architecture](#-architecture) · [⚙️ Configuration](#️-configuration) · [📡 API Reference](#-api-reference) · [🎯 Features](#-features)

</div>

---

## 📸 Preview

```
┌─────────────────────────────────────────────────────────┐
│  🧠 AI Decision Simulator                    ● LIVE      │
├─────────────────────────────────────────────────────────┤
│  Scenario: Expand to Southeast Asian markets            │
│  Domain: Business  │  Budget: ₹50L  │  Risk: 6/10      │
│  Time: 12 weeks    │  Priority: Growth                  │
├─────────────────────────────────────────────────────────┤
│  Risk Level: MEDIUM    Confidence: 82%    ROI: 14–22%  │
│                                                         │
│  ① Phased digital-first entry       ████████░░  74%    │
│  ② Partnership via local distrib.   ████░░░░░░  19%    │
│  ③ Conservative single-market pilot █░░░░░░░░░   7%    │
│                                                         │
│  ◈ Radar: Feasibility·ROI·Risk·Time·Alignment          │
│  ⚡ Insight: Allocate 20% as contingency buffer...      │
└─────────────────────────────────────────────────────────┘
```

---

## ✨ Features

| Feature | Description |
|---|---|
| 🔀 **Tri-Engine Architecture** | Local rule-based · Ollama (local LLM) · Anthropic API — auto-routed by availability |
| ⚡ **Real-Time Streaming** | Server-Sent Events (SSE) stream LLM output token-by-token to the browser |
| 🛡️ **Robust Fallback Chain** | Anthropic → Ollama → Local — the system *never* fails to produce output |
| 📊 **Structured JSON Output** | Guaranteed schema: risk level, confidence, ROI, 3 outcomes, radar scores, risks, insight |
| 🗄️ **Simulation History** | Full SQLite persistence with REST API for retrieve, browse, and delete |
| 🌐 **6 Decision Domains** | Finance, Healthcare, Technology, Business, Education, Risk Management |
| 🎨 **Professional Dashboard** | Dark-themed UI with Chart.js radar visualization and streaming output panel |
| ✅ **Input Validation** | Strict type, range, and enum validation — rejects invalid inputs at the boundary |

---

## 🏗 Architecture

```
┌──────────────────────────────────────────────────────────────────┐
│                        USER BROWSER                              │
│              index.html  ←→  AI.html (SSE consumer)             │
└───────────────────────────┬──────────────────────────────────────┘
                            │ HTTP POST /api/simulate
                            ▼
┌──────────────────────────────────────────────────────────────────┐
│                        app.py  (Flask)                           │
│  ┌──────────────┐   ┌─────────────────┐   ┌──────────────────┐  │
│  │data_processor│   │  select_engine()│   │  build_prompt()  │  │
│  │  .py         │   │  auto-router    │   │  context inject  │  │
│  └──────┬───────┘   └────────┬────────┘   └──────────────────┘  │
│         │ validated          │                                   │
│         ▼ params             ▼                                   │
│  ┌──────────────┐   ┌────────────────────────────────────────┐  │
│  │decision_     │   │           ENGINE SELECTOR              │  │
│  │engine.py     │   │  ┌──────────┐ ┌────────┐ ┌─────────┐  │  │
│  │(preflight)   │   │  │Anthropic │ │ Ollama │ │  Local  │  │  │
│  └──────┬───────┘   │  │   API    │ │  LLM   │ │  Rules  │  │  │
│         │           │  └────┬─────┘ └───┬────┘ └────┬────┘  │  │
│         └───────────┘       │           │            │       │  │
│                             └─────SSE Stream─────────┘       │  │
└─────────────────────────────────┬────────────────────────────┘
                                  │ insert_simulation()
                                  ▼
                         ┌─────────────────┐
                         │  database.py    │
                         │  (SQLite)       │
                         └─────────────────┘
```

### Engine Selection Logic

```python
# Priority: Anthropic (best) → Ollama (local LLM) → Rule-based (always available)
def select_engine(requested):
    if requested in ("local", "ollama", "anthropic"):
        return requested          # honour explicit user choice
    if ANTHROPIC_KEY:             return "anthropic"   # cloud LLM
    if ollama.is_available():     return "ollama"      # local LLM
    return "local"                                     # always works
```

---

## 📁 Project Structure

```
ai-decision-simulator/
│
├── app.py                  # Flask backend — routes, engine orchestration, SSE streaming
├── decision_engine.py      # Rule-based decision logic — always-available fallback engine
├── data_processor.py       # Input validation & sanitization — strict type/range enforcement
├── ollama_engine.py        # Ollama (local LLM) wrapper — streaming + non-streaming modes
├── database.py             # SQLite CRUD — schema init, insert, fetch, delete simulations
│
├── templates/
│   ├── index.html          # Input form — 6 parameter fields, engine selector
│   └── AI.html             # Results dashboard — streaming panel, outcomes, radar chart
│
├── static/
│   └── style.css           # Dark theme UI — DM Serif Display, Instrument Sans, DM Mono
│
├── database.db             # SQLite database (auto-created on first run)
├── .env                    # Environment variables (ANTHROPIC_API_KEY)
├── requirements.txt        # Python dependencies
└── README.md
```

---

## 🚀 Quick Start

### Prerequisites

- Python 3.11+
- Node.js (optional, for frontend tooling)
- [Ollama](https://ollama.ai) (optional, for local LLM)

### 1. Clone the repository

```bash
git clone https://github.com/your-username/ai-decision-simulator.git
cd ai-decision-simulator
```

### 2. Install Python dependencies

```bash
pip install -r requirements.txt
```

**requirements.txt:**
```
flask>=3.0.0
python-dotenv>=1.0.0
requests>=2.31.0
```

### 3. Configure environment variables

```bash
cp .env.example .env
```

Edit `.env`:
```env
# Optional — enables Anthropic Claude engine (highest quality)
ANTHROPIC_API_KEY=sk-ant-your-key-here
```

### 4. (Optional) Set up Ollama for local LLM

```bash
# Install Ollama
brew install ollama          # macOS
# or visit https://ollama.ai for Windows/Linux

# Pull a model
ollama pull llama3

# Start the server
ollama serve
```

### 5. Run the application

```bash
python app.py
```

Open your browser at **http://localhost:8000** 🎉

---

## ⚙️ Configuration

| Parameter | Environment Variable | Default | Description |
|---|---|---|---|
| Anthropic API Key | `ANTHROPIC_API_KEY` | `""` | Enables Claude cloud engine |
| Database Path | `DATABASE` (auto) | `./database.db` | SQLite database file location |
| Ollama URL | Hardcoded in `ollama_engine.py` | `http://localhost:11434` | Local Ollama server address |
| Ollama Model | Per-request (`model` field) | `llama3` | Model to use for local inference |
| Flask Host | `app.run()` | `0.0.0.0` | Bind address |
| Flask Port | `app.run()` | `8000` | Server port |

---

## 📡 API Reference

### `POST /api/simulate`

Run a decision simulation. Returns a **Server-Sent Events (SSE)** stream.

**Request Body:**
```json
{
  "scenario": "We are considering expanding our SaaS product to Southeast Asia.",
  "domain": "business",
  "budget": 50,
  "risk": 6,
  "time": 12,
  "priority": "growth",
  "engine": "anthropic"
}
```

| Field | Type | Required | Constraints |
|---|---|---|---|
| `scenario` | string | ✅ | Non-empty, max 1000 chars |
| `domain` | string | ✅ | `finance` \| `healthcare` \| `technology` \| `business` \| `education` \| `risk` |
| `budget` | integer | ✅ | 1 – 1000 (₹ Lakhs) |
| `risk` | integer | ✅ | 1 – 10 |
| `time` | integer | ✅ | 1 – 104 (weeks) |
| `priority` | string | ✅ | `growth` \| `stability` \| `innovation` \| `cost-reduction` |
| `engine` | string | ❌ | `local` \| `ollama` \| `anthropic` (auto-selected if omitted) |

**SSE Stream Events:**

```
data: {"delta": "Based on your scenario..."}       ← streaming token (LLM engines)
data: {"delta": " the recommended strategy is..."}
data: {"done": true, "result": {...}, "engine": "anthropic", "latency_ms": 2341}
```

**Final Result Schema:**
```json
{
  "summary": "Two-sentence executive summary of the decision context.",
  "riskLevel": "Medium",
  "confidenceScore": 82,
  "expectedROI": "14–22%",
  "outcomes": [
    {
      "rank": 1,
      "title": "Primary Recommendation",
      "description": "Detailed strategy description...",
      "probability": 74,
      "color": "green",
      "badge": "Recommended"
    },
    { "rank": 2, "title": "Alternative Strategy", "probability": 19, "color": "amber", "badge": "Alternative" },
    { "rank": 3, "title": "Conservative Fallback", "probability": 7,  "color": "red",   "badge": "Fallback"     }
  ],
  "radarData": {
    "labels": ["Feasibility", "ROI Potential", "Risk Control", "Time Efficiency", "Alignment"],
    "values": [78, 84, 65, 72, 88]
  },
  "insight": "Allocate 20% of budget as a contingency reserve before committing to any single outcome.",
  "keyRisks": [
    "Competitor response may erode first-mover advantage faster than modelled.",
    "Budget constraints may force mid-project scope cuts.",
    "High volatility exposure in target market."
  ]
}
```

---

### `GET /api/history`

Returns the 50 most recent simulations.

```bash
curl http://localhost:8000/api/history
```

### `GET /api/history/<id>`

Returns a single simulation by ID.

```bash
curl http://localhost:8000/api/history/42
```

### `DELETE /api/history/<id>`

Deletes a simulation by ID.

```bash
curl -X DELETE http://localhost:8000/api/history/42
```

---

## 🔌 Supported Engines

### 🏠 Local Rule-Based Engine
- **Always available** — zero external dependencies
- Deterministic output with explicit formulas for each field
- Response time: **< 10 ms**
- Best for: development, testing, offline use

### 🦙 Ollama (Local LLM)
- Requires [Ollama](https://ollama.ai) running locally
- Supports any Ollama-compatible model (`llama3`, `mistral`, `phi3`, etc.)
- Streaming output via HTTP SSE
- Response time: **8–30 seconds** (hardware-dependent)
- Best for: privacy-sensitive use cases, offline production

### ☁️ Anthropic (Claude API)
- Requires `ANTHROPIC_API_KEY` in environment
- Uses `claude-sonnet-4-20250514` — highest reasoning quality
- Streaming output via SSE
- Response time: **1–4 seconds**
- Best for: highest-quality production output

---

## 🗄️ Database Schema

```sql
CREATE TABLE simulations (
    id          INTEGER PRIMARY KEY AUTOINCREMENT,
    params_json TEXT    NOT NULL,   -- validated input parameters (JSON)
    result_json TEXT    NOT NULL,   -- full structured output (JSON)
    engine      TEXT    NOT NULL,   -- 'local' | 'ollama' | 'anthropic' | *_fallback
    latency_ms  INTEGER DEFAULT 0,  -- end-to-end inference time
    created_at  DATETIME DEFAULT CURRENT_TIMESTAMP
);
```

---

## 🧩 Module Reference

### `data_processor.py`

```python
from data_processor import process_input, ValidationError, summarise

params = process_input(raw_json)   # raises ValidationError on invalid input
log    = summarise(params)         # "business | ₹50L | risk=6 | 12wk | growth"
```

### `decision_engine.py`

```python
from decision_engine import make_decision

result = make_decision(params)     # returns DecisionResult dataclass
data   = result.to_dict()          # serializable dict for JSON response
```

### `ollama_engine.py`

```python
from ollama_engine import stream_ollama, run_ollama, is_available

# Check availability
if is_available():
    # Streaming (for Flask SSE)
    gen = stream_ollama(prompt, model="llama3")
    try:
        while True:
            delta = next(gen)      # yields string tokens
    except StopIteration as e:
        result = e.value           # final parsed dict | None

    # Non-streaming (for scripts)
    result = run_ollama(prompt)
```

### `database.py`

```python
from database import get_connection, init_schema, insert_simulation, fetch_history

conn = get_connection("database.db")
init_schema(conn)
insert_simulation(conn, params, result, engine="anthropic", latency_ms=2341)
rows = fetch_history(conn)         # list of dicts, newest first
```

---

## 🎯 Decision Domains

| Domain | Example Scenarios |
|---|---|
| 💰 **Finance** | Portfolio rebalancing, investment strategy, cost optimization |
| 🏥 **Healthcare** | App launch, clinical process improvement, resource allocation |
| 💻 **Technology** | Infrastructure upgrade, platform migration, build vs. buy |
| 🏢 **Business** | Market entry, product launch, team scaling, M&A evaluation |
| 📚 **Education** | Curriculum design, EdTech platform launch, institutional planning |
| ⚠️ **Risk** | Vendor risk assessment, supply chain audit, contingency planning |

---

## 📊 Performance Benchmarks

| Engine | Avg Latency | Output Quality | Availability | Fallback |
|---|---|---|---|---|
| Local (Rule-Based) | < 10 ms | ⭐⭐⭐ Structured & reliable | 100% always | — |
| Ollama (LLaMA 3) | 8–25 s | ⭐⭐⭐⭐ Context-aware reasoning | Conditional | → Local |
| Anthropic (Claude) | 1–4 s | ⭐⭐⭐⭐⭐ Nuanced, scenario-specific | Requires API key | → Local |

---

## 🔮 Roadmap

- [ ] Multi-agent decision debates (risk analyst + strategist + financial advisor)
- [ ] Live external data integration (market feeds, healthcare APIs)
- [ ] Voice-based scenario input (Web Speech API / Whisper)
- [ ] Domain-specific fine-tuned models
- [ ] Explainable AI — step-by-step reasoning traces
- [ ] User authentication + multi-user history
- [ ] PDF/Excel export of decision reports
- [ ] Multi-language support

---

## 👥 Team

| Name | Roll No. | GitHub |
|---|---|---|
| Nakul | 23-CSE-5305 | [@anuchouhan](https://github.com) |
| Ankush | 23-CSE-5267 | [@snehalshaswat](https://github.com) |
| Deepanshu | 24-CSE-5351 | [@rohitraj](https://github.com) |

**Supervisor:** Ms. Jyoti, Assistant Professor — Department of CSE  
**Institution:** BRCM College of Engineering & Technology, Bahal  
**University:** Maharishi Dayanand University, Rohtak, Haryana

---

## 📄 License

```
MIT License — Copyright (c) 2026 AI Decision Simulator Team

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software.
```

---

<div align="center">

**Built with 🧠 AI · ⚡ Flask · 🦙 Ollama · ☁️ Anthropic**

*Department of Computer Science & Engineering*  
*BRCM College of Engineering & Technology, Bahal — May 2026*

<img src="https://capsule-render.vercel.app/api?type=waving&color=gradient&customColorList=6,11,20&height=100&section=footer" width="100%"/>

</div>
