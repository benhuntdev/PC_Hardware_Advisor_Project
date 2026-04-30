# 🖥️ PC Hardware Advisor - AI Agent

> **Ironhack AI Engineering Bootcamp 2026 - Final Project**

An AI-powered PC hardware advisor that replaces hours of YouTube research with a single conversation. Built with LangChain, ChromaDB, OpenAI, and a React frontend.

![Python](https://img.shields.io/badge/Python-3.12-blue?logo=python&logoColor=white)
![LangChain](https://img.shields.io/badge/LangChain-1.2-green?logo=chainlink&logoColor=white)
![OpenAI](https://img.shields.io/badge/OpenAI-GPT--4o--mini-412991?logo=openai&logoColor=white)
![ChromaDB](https://img.shields.io/badge/ChromaDB-0.5-orange)
![React](https://img.shields.io/badge/Frontend-React-61DAFB?logo=react&logoColor=black)
![FastAPI](https://img.shields.io/badge/API-FastAPI-009688?logo=fastapi&logoColor=white)

> ⚠️ **Work in Progress** - This is a functional prototype built as a bootcamp final project. Known bugs exist and the experience is actively being refined. See [Roadmap](#roadmap) for planned improvements.

---

## 📋 Table of Contents

- [Overview](#overview)
- [Demo](#demo)
- [Features](#features)
- [Architecture](#architecture)
- [Tech Stack](#tech-stack)
- [Project Structure](#project-structure)
- [Setup & Installation](#setup--installation)
- [Usage](#usage)
- [Evaluation](#evaluation)
- [Key Technical Decisions](#key-technical-decisions)
- [Roadmap](#roadmap)
- [Author](#author)

---

## Overview

**The Problem:** Buying PC hardware is overwhelming. Hundreds of YouTube videos, conflicting opinions, and no single place that gives you a personalised answer.

**The Solution:** PC Hardware Advisor is a RAG-based AI agent that has indexed **1,400+ YouTube hardware reviews** (36,489 searchable chunks) and can answer any PC hardware question with timestamped evidence from real reviewers.

It doesn't just recommend - it **builds with you**. A guided PC configurator asks 9 questions, then generates 1 to 4 complete builds with full specs, pricing, compatibility checks, and direct buy links.

The advisor covers a wide range of hardware needs:

| ![PCs](https://drive.google.com/uc?export=view&id=1P1TX_SDHMHwppCMpvSgHRdAEQG0TowL4) | ![MacBooks](https://drive.google.com/uc?export=view&id=1Y5qjwe4aCfmIAqe29dyWVglDiRt9k-e2) | ![Monitors](https://drive.google.com/uc?export=view&id=1a30LPrw-qrczhC50gnNNt19KvncTpGJJ) |
|:---:|:---:|:---:|
| Custom PC Builds | MacBooks | Monitors |

![Chatbot Home](https://drive.google.com/uc?export=view&id=1xcRIELXHW2wEugjPumZbLRPA2OpOcoHT)

---

## Demo

### Full Walkthrough Video

[![PC Hardware Advisor - Full Demo](https://cdn.loom.com/sessions/thumbnails/8293d333ed014db49a561494f451f396-with-play.gif)](https://www.loom.com/share/8293d333ed014db49a561494f451f396)

> Click the image above to watch the full demo on Loom.

---

The following clips walk through the core user journey, from telling the advisor what you want through to placing your order.

> ⚠️ These recordings are from an early prototype. Some rough edges and bugs are visible and are being addressed. See [Roadmap](#roadmap).

---

### 1. Tell It What You Want

Users simply describe what they are looking for in natural language - budget, use case, preferences. No forms, no dropdowns. The advisor handles the rest.

![User defines hardware build](https://drive.google.com/uc?export=view&id=1o1jfFSNZUAQVNQa39XBFTa7nQRYA_X4n)

---

### 2. Compare AI-Generated Builds

The advisor returns one to four complete builds tailored to the user's input. Every build has been automatically checked for component compatibility (socket, RAM type, GPU clearance, PSU wattage) and benchmarked against best-fit options for the stated use case.

![Compare builds](https://drive.google.com/uc?export=view&id=1GduCbelZHWrTNPb9KohLnBevdvHmcCT2)

---

### 3. Customise Any Part

Not happy with a specific component? Users can swap out any part in any build. The advisor presents curated alternatives with full specs and compatibility already verified.

![Pick your PC parts](https://drive.google.com/uc?export=view&id=1dNLJpgowhNyfakvsWVtrmZrrPHQQaum8)

---

### 4. Buy Your Build

When the user is happy with their chosen build, they simply type **"buy my build"** (or select the option when prompted). Direct purchase links to trusted retailers are generated on the spot.

![Buy your build](https://drive.google.com/uc?export=view&id=1FXZCWjF3pv_c_cyjS6kfRz5G9obS1A3a)

---

## Features

### 💬 Intelligent Chat
- Ask anything about CPUs, GPUs, cases, RAM, storage, PSUs, monitors
- Covers MacBooks, laptops, pre-builts, and emulation hardware
- Every answer backed by YouTube reviewer evidence with timestamp links
- Scope-aware: politely refuses off-topic questions

### 🔧 PC Configurator (Two Modes)
- **"Build it for me"** - 9 guided questions leading to 1 to 4 complete builds generated
- **"I'll build it myself"** - Step-by-step component selection with 2 to 4 options per slot
- Automatic compatibility checking (socket, RAM type, GPU clearance, PSU wattage)
- Full build tables with 10 components, key specs, and estimated pricing

### 🎬 YouTube Integration
- Timestamp links jump directly to the relevant moment in a review
- Corpus spans 5 years of content from major hardware channels
- Semantic search finds relevant reviews even when wording differs

### 📊 Structured Output
- Performance metric bars (Gaming, Productivity, Noise Level)
- Side-by-side build comparison
- Option cards with Quick Specs, Best For, and At a Glance sections
- Hidden tags parsed by frontend for dynamic UI rendering

### 🛒 Vendor Links
- Amazon.de, Alternate.de, Mindfactory.de, Geizhals.de search links
- Price estimates from SerpAPI web search

---

## Architecture

```
┌─────────────────┐     ┌──────────┐     ┌──────────────────────────────┐
│  Lovable React  │────▶│  ngrok   │────▶│  FastAPI (Google Colab)      │
│  Frontend       │◀────│  tunnel  │◀────│                              │
└─────────────────┘     └──────────┘     │  ┌────────────────────────┐  │
                                         │  │  LangChain Agent       │  │
                                         │  │  (GPT-4o-mini)         │  │
                                         │  │                        │  │
                                         │  │  Tools:                │  │
                                         │  │  ├─ pc_transcript_search│  │
                                         │  │  │  (ChromaDB + OpenAI │  │
                                         │  │  │   embeddings)       │  │
                                         │  │  └─ web_search_product │  │
                                         │  │     (SerpAPI)          │  │
                                         │  └────────────────────────┘  │
                                         │                              │
                                         │  ┌────────────────────────┐  │
                                         │  │  ChromaDB              │  │
                                         │  │  36,489 chunks         │  │
                                         │  │  ~1,400 videos         │  │
                                         │  └────────────────────────┘  │
                                         └──────────────────────────────┘
```

---

## Tech Stack

| Layer | Technology | Purpose |
|-------|-----------|---------|
| **LLM** | GPT-4o-mini | Agent reasoning and response generation |
| **Framework** | LangChain 1.2 | Agent orchestration, tool calling, memory |
| **Vector DB** | ChromaDB 0.5 | Semantic search over YouTube transcripts |
| **Embeddings** | OpenAI text-embedding-3-small | Chunk and query vectorisation |
| **Web Search** | SerpAPI | Live pricing, availability, product images |
| **Backend** | FastAPI + Uvicorn | REST API with session management |
| **Tunnel** | ngrok | Expose Colab backend to the internet |
| **Frontend** | React (Lovable) | Chat UI, build cards, configurator sidebar |
| **Evaluation** | LangSmith | Tracing, experiments, automated evaluators |
| **Memory** | ConversationBufferWindowMemory (k=10) | Chat history across turns |
| **Environment** | Google Colab (T4 GPU) | Development and runtime |

---

## Project Structure

```
pc-hardware-advisor/
│
├── PCHardwareAgent_v2.ipynb    # Main notebook (agent, API, evaluation)
│
├── data/                        # Chunked transcript CSVs
│   ├── gpu_reviews_chunked.csv
│   ├── cpu_reviews_chunked.csv
│   ├── case_reviews_chunked.csv
│   └── ...
│
├── frontend/                    # Lovable React app (separate repo)
│   ├── src/
│   │   ├── components/
│   │   │   ├── BuildCard.tsx
│   │   │   ├── OptionCard.tsx
│   │   │   ├── ChatArea.tsx
│   │   │   ├── ChatMessage.tsx
│   │   │   └── ...
│   │   ├── lib/
│   │   │   └── api.ts
│   │   └── hooks/
│   │       └── useChat.ts
│   └── ...
│
├── presentations/               # Slide decks
│   ├── langsmith_eval_chart.html
│   └── pc_agent_slides_light.pptx
│
├── requirements.txt             # Python dependencies
├── .gitignore
├── .env.example                 # API key template
├── LICENSE
└── README.md
```

---

## Setup & Installation

### Prerequisites

- Google Colab account (with GPU runtime recommended)
- OpenAI API key
- SerpAPI key (free tier: 100 searches/month)
- LangSmith account (EU endpoint) - optional, for evaluation
- ngrok account + auth token (free tier)

### 1. Clone the Repository

```bash
git clone https://github.com/YOUR_USERNAME/pc-hardware-advisor.git
cd pc-hardware-advisor
```

### 2. Upload to Google Colab

1. Upload `PCHardwareAgent_v2.ipynb` to Google Colab
2. Upload all chunked CSV files from `data/` folder
3. Set runtime to **GPU (T4)** for faster embedding

### 3. Configure API Keys

In Colab, go to **Secrets** (key icon in left sidebar) and add:

| Secret Name | Value |
|------------|-------|
| `OPENAI_API_KEY` | Your OpenAI API key |
| `SERPAPI_KEY` | Your SerpAPI key |
| `LANGSMITH_API_KEY` | Your LangSmith API key |
| `NGROK_AUTH_TOKEN` | Your ngrok auth token |

### 4. Run Cells in Order

```
Cell 02 → Install packages (restart runtime after)
Cell 04 → Verify imports
Cell 06 → Upload CSV files
Cell 08 → Load OpenAI key
Cell 10 → Load LangSmith key
Cell 12 → Load SerpAPI key
Cell 14 → Imports
Cell 16 → Config
Cell 18 → Load chunked data
Cell 24 → Build ChromaDB index (~15-20 min first time)
Cell 26 → Retrieval function
Cell 28 → Conversation memory
Cell 30 → PC Configurator state
Cell 32 → Agent setup
Cell 92 → FastAPI backend + ngrok tunnel
```

### 5. Connect Frontend

Copy the ngrok public URL from cell 92 output and paste it into `frontend/src/lib/api.ts`:

```typescript
const API_BASE_URL = "https://your-ngrok-url.ngrok-free.dev";
```

---

## Usage

### Chat Endpoint

```bash
curl -X POST https://your-ngrok-url.ngrok-free.dev/chat \
  -H "Content-Type: application/json" \
  -d '{"message": "What GPU should I get for 1440p gaming under €400?"}'
```

### Configurator Flow

```bash
# Start a build
curl -X POST .../chat -d '{"message": "I want to build a gaming PC", "session_id": "demo"}'

# Answer guided questions
curl -X POST .../chat -d '{"message": "Build it for me", "session_id": "demo"}'
curl -X POST .../chat -d '{"message": "€1200", "session_id": "demo"}'
# ... continue through 9 questions
```

### Health Check

```bash
curl https://your-ngrok-url.ngrok-free.dev/health
```

---

## Evaluation

Evaluated using **LangSmith** with 25 curated test cases and 7 automated evaluators:

| Metric | Score | Description |
|--------|-------|-------------|
| **Configurator Mention** | 1.00 | Always offers build tool when relevant |
| **Emulation Notice** | 0.98 | Legal disclaimer for ROM/emulation queries |
| **Scope Handling** | 0.96 | Correctly refuses off-topic questions |
| **Quality Score** | 0.83 | GPT-4o-mini judge rating (baseline) |
| **Has Timestamps** | 0.56 | Includes YouTube evidence links |
| **Has Specs** | 0.76 | Structured spec formatting |
| **Non-Empty Response** | 1.00 | Produces substantive answers |

**Cost:** < $0.05 per full 25-query evaluation run | < $0.01 per conversation

---

## Key Technical Decisions

### 1. Tool Choice: `auto`
The agent can call `pc_transcript_search` (YouTube corpus) and `web_search_product` (live web) as needed. Setting `tool_choice="auto"` lets it answer simple questions directly while searching for hardware queries.

### 2. 877-Line System Prompt
The entire configurator flow - two modes, 9 guided questions, build table format, compatibility rules, vendor links - is controlled by a single system prompt. No additional backend state machine code required.

### 3. Two-Layer Memory
- **Layer 1:** `ConversationBufferWindowMemory(k=10)` keeps chat history
- **Layer 2:** Configurator state injected into the system prompt on every turn via `rebuild_agent_with_state()`

### 4. Semantic Search over Keyword Search
ChromaDB with OpenAI embeddings finds relevant content even when wording differs. "Best GPU for 1440p" matches chunks saying "this card handles 2K beautifully."

---

## Roadmap

This project is a working prototype with several known areas for improvement. The following developments are planned:

### 🐛 Stability & Performance
- Fix known bugs across the configurator and chat flows
- Refine the system prompt for more consistent, higher-quality output
- Improve agent response speed and reliability

### 📚 Corpus Overhaul
- Remove all YouTube creator content from the knowledge base
- Replace with clean, factual PC hardware documentation and specifications
- Integrate curated, free-to-use customer service and support scripts for a legally sound data foundation

### 🤖 Architecture Upgrade
- Explore a **multi-agent model** to replace the current single-agent approach, separating concerns such as retrieval, build generation, compatibility checking, and purchasing into dedicated agents for improved efficiency, maintainability, and safety

### 🎨 UI/UX Improvements
- Redesign the chat interface for a more polished, user-friendly experience
- Improve build card layout, part picker clarity, and mobile responsiveness

### 🔩 Step-by-Step Build Guide
- Add a post-purchase assembly companion: once a user has their parts, the advisor walks them through the physical build process step by step
- No prior PC building experience required - the advisor handles the technical guidance throughout

---

## Author

**Benjamin Hunt**
Ironhack AI Engineering Bootcamp - March 2026

- GitHub: [github.com/benhuntdev](https://github.com/benhuntdev)
- Project: [PC Hardware Advisor](https://github.com/benhuntdev/PC_Hardware_Advisor_Project)

---

## License

This project is for educational purposes as part of the Ironhack AI Engineering Bootcamp 2026.
**Benjamin Hunt**
Ironhack AI Engineering Bootcamp — March 2026

---

## License

This project is for educational purposes as part of the Ironhack AI Engineering Bootcamp 2026.
