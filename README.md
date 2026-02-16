# ContextAgent

[![Python 3.11+](https://img.shields.io/badge/Python-3.11%2B-blue?style=flat-square&logo=python)](https://www.python.org/)
[![LangChain](https://img.shields.io/badge/LangChain-Framework-green?style=flat-square)](https://github.com/langchain-ai)
[![OpenAI](https://img.shields.io/badge/OpenAI-GPT--4-orange?style=flat-square&logo=openai)](https://openai.com)
[![FastAPI](https://img.shields.io/badge/FastAPI-REST-teal?style=flat-square)](https://fastapi.tiangolo.com/)
[![Docker](https://img.shields.io/badge/Docker-Ready-2496ED?style=flat-square&logo=docker)](https://www.docker.com)

> **AI assistant backend that answers questions from your documents using RAG (Retrieval-Augmented Generation)**

Upload PDFs/docs, ask natural language questions, get accurate answers with source citations.

---

## 🚀 Quick Start

```bash
git clone https://github.com/NitinAutam/ContextAgent.git
cd ContextAgent
pip install -r requirements.txt
cp env.example .env  # Add your OPENAI_API_KEY
python start.py
```

**🔗 Access:** http://localhost:8000/docs

---

## ✨ What It Does

```
📄 Upload Documents (PDF, DOCX, MD, TXT)
         ↓
🔍 Ask Questions in Natural Language
         ↓
🤖 Get AI Answers with Source Citations
```

Three modes:

- **RAG Mode** - Answers from your documents
- **Agent Mode** - Uses tools (calculator, web search)
- **Simple Mode** - Direct GPT-4 responses

---

## 🎯 RAG Explained

```
Your Documents → Embeddings → Vector DB
                                  ↓
Question → Search Similar → Top 4 Chunks → GPT-4 → Answer
```

**Why?** Eliminates AI hallucinations by grounding responses in real documents.

---

## 📚 Usage

### Upload & Query

```python
import requests

# 1. Upload document
files = {"file": open("policy.pdf", "rb")}
requests.post("http://localhost:8000/ingest/upload", files=files)

# 2. Ask question
response = requests.post("http://localhost:8000/chat/", json={
    "question": "What's the refund policy?",
    "use_rag": True
})
print(response.json()["answer"])
# Output: "Full refund within 30 days per company policy."
# Sources: ["policy.pdf"]
```

### Agent with Tools

```python
response = requests.post("http://localhost:8000/chat/", json={
    "question": "Calculate 15 * 23",
    "use_agent": True
})
# Output: "345"
```

---

## 🔌 API Endpoints

| Endpoint                | Description                      |
| ----------------------- | -------------------------------- |
| `POST /chat/`           | Ask questions (RAG/Agent/Simple) |
| `POST /ingest/upload`   | Upload document                  |
| `GET /chat/memory/{id}` | Get conversation history         |
| `GET /chat/tools`       | List available tools             |
| `GET /health`           | Health check                     |
| `GET /docs`             | Interactive API docs             |

---

## ⚙️ Configuration

**`.env` file:**

```bash
OPENAI_API_KEY=sk-...                    # Required
OPENAI_MODEL=gpt-4                       # Optional
CHROMA_PERSIST_DIRECTORY=./chroma_db     # Optional
SERP_API_KEY=your_key                    # Optional (for web search)
```

**Supported formats:** PDF, DOCX, Markdown, TXT (max 10MB)

---

## 🐳 Docker

```bash
docker-compose up -d
```

Or:

```bash
docker build -t contextagent .
docker run -p 8000:8000 -e OPENAI_API_KEY=sk-... contextagent
```

---

## 🏗️ Architecture

```
FastAPI
 ├── Routes (HTTP endpoints)
 ├── Chains
 │   ├── QA Chain (RAG)
 │   └── Agent Chain (Tools)
 ├── Memory (Conversation)
 ├── Tools (Calculator, Search)
 └── Vector Store (ChromaDB)
```

**Tech Stack:** FastAPI • LangChain • OpenAI GPT-4 • ChromaDB • Python 3.11+

---

## 🛠️ Project Structure

```
app/
├── chains/          # QA & Agent chains
├── ingest/          # Document processing
├── memory/          # Conversation state
├── routes/          # API endpoints
├── tools/           # Calculator, Search
└── utils/           # Config, loaders
```

---

## 🎯 Use Cases

- **Enterprise KB** - Company policies, manuals, SOPs
- **Research** - Search across papers and notes
- **Support** - Chatbots with product docs
- **Legal** - Contract analysis with citations
- **Personal** - Your own knowledge base

---

## 🔧 Customization

### Add a Tool

```python
# app/tools/weather.py
from langchain.tools import BaseTool

class WeatherTool(BaseTool):
    name = "weather"
    description = "Get weather for a city"

    def _run(self, location: str) -> str:
        return f"Weather in {location}: Sunny, 72°F"

# Register in app/chains/agent_chain.py
from app.tools.weather import WeatherTool
tools = [calculator_tool, WeatherTool()]
```

### Adjust Chunk Size

Edit `app/ingest/embedder.py`:

```python
chunk_size=1000      # Smaller = precise, Larger = context
chunk_overlap=200
```

---

## 📊 Performance

| Operation      | Time  |
| -------------- | ----- |
| Upload 1MB PDF | ~2s   |
| RAG query      | ~1-2s |
| Agent query    | ~2-4s |

---

## 🐛 Troubleshooting

**Missing API key?** → Add `OPENAI_API_KEY` to `.env`  
**No documents?** → Upload via `/ingest/upload` first  
**ChromaDB error?** → Delete `./chroma_db` and restart  
**Agent not working?** → Be explicit: "calculate 5+5"

---

## 🤝 Contributing

```bash
git checkout -b feature/new-feature
# Make changes
python test_api.py
git commit -m "Add feature"
git push origin feature/new-feature
```

---

## 📚 Resources

- [RAG Paper](https://arxiv.org/abs/2005.11401)
- [LangChain Docs](https://python.langchain.com/)
- [OpenAI Embeddings](https://platform.openai.com/docs/guides/embeddings)
- [FastAPI Tutorial](https://fastapi.tiangolo.com/tutorial/)

---
