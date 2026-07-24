# 🤖 ProcureAI — Agentic Procurement Intelligence Platform

> **An Agentic AI Platform where 6 specialized AI agents collaborate to automate vendor evaluation, risk detection, negotiation strategy, and procurement decisions.**

---

## 📌 Table of Contents

- [Business Problem](#-business-problem)
- [Solution](#-solution)
- [AI Agents](#-ai-agents)
- [Tech Stack](#-tech-stack)
- [Project Structure](#-project-structure)
- [Setup Instructions](#-setup-instructions)
- [API Endpoints](#-api-endpoints)
- [Features](#-features)
- [How It Works](#-how-it-works)

---

## 💼 Business Problem

Every company faces this challenge:

- Procurement teams receive **multiple vendor quotations** for every purchase
- They spend **days manually reading**, comparing prices, checking risks, and negotiating terms
- Human errors lead to **poor vendor selection** and financial losses
- There is **no systematic way** to evaluate vendors consistently

**Result:** Slow decisions, missed savings, and hidden contractual risks.

## ✅ Solution

**ProcureAI** acts as a **virtual procurement team**. Upload vendor PDFs → 6 AI agents automatically analyze, compare, assess risk, suggest negotiation tactics, and recommend the best vendor — all in seconds.

A **Human-in-the-Loop** approval step ensures the procurement manager stays in control of the final decision.

## 🤖 AI Agents

ProcureAI uses **6 specialized AI agents** orchestrated by LangGraph:

| # | Agent | Role | Input | Output |
|---|---|---|---|---|
| 1 | 📋 Planner Agent | Creates the analysis plan and strategy | Vendor names | Structured plan |
| 2 | 📄 Document Agent | Extracts key info from each vendor PDF | Raw PDF text | Structured vendor data |
| 3 | 🔍 Comparison Agent | Side-by-side vendor comparison | Extracted data | Comparison table |
| 4 | ⚠️ Risk Agent | Identifies hidden risks and red flags | Extracted data | Risk report |
| 5 | 🤝 Negotiation Agent | Suggests negotiation tactics per vendor | Comparison + Risks | Negotiation strategies |
| 6 | ✅ Recommendation Agent | Makes the final vendor recommendation | All above outputs | Final decision |

### Shared Memory (LangGraph State)

All agents communicate through a shared state (`ProcureAIState`), a typed dictionary acting as a shared workspace:

```python
class ProcureAIState(TypedDict):
    vendor_names: List[str]        # Input: vendor names
    vendor_texts: List[str]        # Input: raw PDF text
    plan: Optional[str]            # Planner agent output
    extracted_data: Optional[str]  # Document agent output
    comparison: Optional[str]      # Comparison agent output
    risks: Optional[str]           # Risk agent output
    negotiation: Optional[str]     # Negotiation agent output
    recommendation: Optional[str]  # Recommendation agent output
    human_approved: Optional[bool] # Human approval decision
```

Each agent reads from this state and writes its output back — no agent talks directly to another, keeping the pipeline clean and extensible.

## 🛠️ Tech Stack

| Technology | Version | Purpose |
|---|---|---|
| Python | 3.13 | Core language |
| LangGraph | 0.4.8 | Multi-agent orchestration & state management |
| LangChain | 0.3.25 | LLM integration layer |
| LangChain-Groq | latest | Groq API connector |
| LLaMA 3.3 70B | via Groq | AI model (free tier) |
| FastAPI | 0.115 | REST API backend |
| Streamlit | 1.45 | Frontend UI |
| PyMuPDF | 1.25.5 | PDF text extraction |
| Pydantic | 2.11.4 | Data validation |
| Uvicorn | 0.34.3 | ASGI server |

## 📁 Project Structure

```
Procure_AI/
├── agents/                      # All AI agents
│   ├── state.py                 # Shared memory (ProcureAIState)
│   ├── graph.py                 # LangGraph pipeline wiring
│   ├── planner_agent.py
│   ├── document_agent.py
│   ├── comparison_agent.py
│   ├── risk_agent.py
│   ├── negotiation_agent.py
│   └── recommendation_agent.py
├── backend/                     # FastAPI backend
│   ├── main.py                  # App entry point + CORS
│   ├── models.py                # Request/Response schemas
│   ├── routes/
│   │   └── procurement.py
│   └── services/
│       └── pdf_service.py
├── frontend/                    # Streamlit UI
│   ├── app.py
│   └── components/
│       ├── upload_section.py
│       ├── results_section.py
│       └── approval_section.py
├── config/
│   └── settings.py
├── tests/
│   └── test_agents.py
├── docs/
│   └── architecture.md
├── requirements.txt
├── .env                          # API keys (not committed)
├── .gitignore
└── README.md
```

## 🚀 Setup Instructions

### Prerequisites
- Python 3.13
- A free [Groq API key](https://console.groq.com)

### 1. Clone the repository
```bash
git clone https://github.com/kashif030905/Procure_AI.git
cd Procure_AI
```

### 2. Create virtual environment
```bash
python3.13 -m venv venv
source venv/bin/activate       # Mac/Linux
# venv\Scripts\activate        # Windows
```

### 3. Install dependencies
```bash
pip install -r requirements.txt
pip install langchain-groq
```

### 4. Configure environment variables
```bash
cp .env.example .env
# Add your Groq API key
GROQ_API_KEY=your-groq-api-key-here
```

### 5. Run the backend (Terminal 1)
```bash
uvicorn backend.main:app --reload --port 8000
```

### 6. Run the frontend (Terminal 2)
```bash
streamlit run frontend/app.py
```

### 7. Open the app
```
http://localhost:8501
```

## 🔌 API Endpoints

| Method | Endpoint | Description |
|---|---|---|
| GET | `/` | Health check |
| POST | `/api/analyze` | Upload PDFs and run full agent pipeline |
| POST | `/api/approve` | Human approval decision (true/false) |
| POST | `/api/report` | Generate and download PDF report |

### Example: Analyze vendors
```bash
curl -X POST "http://localhost:8000/api/analyze" \
  -F "files=@vendor_a.pdf" \
  -F "files=@vendor_b.pdf"
```

### Example response
```json
{
  "status": "success",
  "comparison": "...",
  "risks": "...",
  "negotiation": "...",
  "recommendation": "...",
  "message": "Analysis complete for 2 vendors"
}
```

## ✨ Features

- 📤 **Multi-PDF Upload** — Upload 2 to 15 vendor quotation PDFs simultaneously
- 🤖 **6 Specialized AI Agents** — Each with a distinct role and expertise
- 🧠 **Shared Memory** — LangGraph state connects all agents seamlessly
- 🔍 **Automatic Comparison** — Side-by-side vendor evaluation across price, delivery, warranty
- ⚠️ **Risk Detection** — Identifies hidden costs, unfavorable clauses, delivery risks
- 🤝 **Negotiation Strategies** — Specific tactics and leverage points per vendor
- ✅ **AI Recommendation** — Clear, reasoned final vendor selection
- 👤 **Human-in-the-Loop** — Procurement manager approves or rejects before finalizing
- 📥 **Report Download** — Full analysis exportable as PDF or text
- 🔄 **Extensible Architecture** — New agents can be added in minutes

## ⚙️ How It Works

1. **Upload** — Procurement manager uploads vendor PDF quotations via the Streamlit UI
2. **Extract** — PyMuPDF extracts raw text from each PDF
3. **Plan** — Planner Agent creates a structured analysis strategy
4. **Analyze** — Document Agent extracts key fields (price, terms, delivery, warranty)
5. **Compare** — Comparison Agent builds a side-by-side vendor table
6. **Risk Check** — Risk Agent flags financial, delivery, quality, and legal risks
7. **Negotiate** — Negotiation Agent suggests leverage points and specific asks per vendor
8. **Recommend** — Recommendation Agent picks the best vendor with clear reasoning
9. **Approve** — Human procurement manager reviews and approves or rejects
10. **Report** — Full analysis downloaded as a PDF report

## 📄 License

MIT License — feel free to use, modify, and build on this project.

## 👤 Author

**Syed Kashif Uddin**
B.Tech CSE | VNRVJIET, Hyderabad