# 07 — Agentic AI Engineer (6-Month Roadmap)

> Build production-grade **autonomous agents** that reason, use tools, retrieve knowledge, and complete real work — not just chatbots.
> The hottest engineering role of 2026.

**Time:** 6 months · 1.5–2 hours/day
**Prereqs:** Python · basics of REST APIs · Linux ([`01-Linux`](../01-Linux)) · helpful but not required: one cloud cert ([`02-AWS-SAA`](../02-AWS-SAA-C03) or [`03-AZ-104`](../03-Azure-AZ-104))

---

## 🤖 What is an Agentic AI Engineer?

> Engineer who designs systems where **LLMs + tools + memory + planning** complete multi-step tasks autonomously — with evaluation, safety, observability, and deployment baked in.

**Salary signal (2026):** ₹25–80+ LPA / $160k–280k+ — currently one of the highest-paid IC tracks.

---

## 🧭 The Agentic Stack (2026)

| Layer | Examples |
|-------|----------|
| **Foundation models** | GPT-5, Claude 4.x, Gemini 2.5, Llama 4, Mistral Large, open-weights (Qwen, DeepSeek) |
| **Model access** | OpenAI / Anthropic / Bedrock / Azure OpenAI / Vertex / Together / Groq / Ollama |
| **Orchestration** | LangGraph, LlamaIndex, AutoGen, CrewAI, Pydantic-AI, Semantic Kernel |
| **Tools & function calling** | OpenAI/Anthropic tool calling, MCP (Model Context Protocol) |
| **RAG storage** | pgvector, Qdrant, Weaviate, Pinecone, Milvus, LanceDB |
| **Memory** | Mem0, Zep, Letta, Redis, plain SQL |
| **Eval & observability** | LangSmith, LangFuse, Arize Phoenix, Helicone, Braintrust, OpenTelemetry GenAI |
| **Safety & guardrails** | NeMo Guardrails, Guardrails AI, Llama Guard, prompt injection detectors |
| **Serving** | vLLM, TGI, Ollama, Bedrock/Azure OpenAI hosted |
| **Deployment** | FastAPI + K8s, Modal, Replicate, Bedrock Agents, AzureML |

---

## 📅 6-Month Roadmap

### Month 1 — Foundations (LLMs, prompting, tool use)

- How LLMs work at 10,000 ft: tokens, context window, attention, sampling (temperature, top_p)
- Prompt engineering patterns: zero-shot, few-shot, **Chain-of-Thought**, ReAct, self-consistency, reflection
- Structured output: **JSON mode**, Pydantic schemas, function/tool calling
- The OpenAI / Anthropic / Bedrock API basics
- Cost & latency awareness: token counting, batching, streaming
- **Project:** Build a CLI assistant in Python (no framework) that uses tool calling to query the local file system + fetch web pages.

---

### Month 2 — RAG (Retrieval-Augmented Generation)

- Why RAG vs fine-tuning
- **Embeddings:** what they are, sentence-transformers vs OpenAI vs Cohere, dimensions, similarity (cosine)
- **Chunking** strategies: fixed, sentence, semantic, hierarchical
- Vector DBs: `pgvector` (start here, free), Qdrant, Weaviate, Pinecone
- **Hybrid search:** vector + BM25 (e.g. RRF reranking)
- **Reranking:** Cohere Rerank, ColBERT, BGE reranker
- Eval RAG: faithfulness, relevance, context precision/recall — **Ragas**
- **Project:** "Ask my company docs" — ingest 200+ markdown/PDF docs into pgvector, hybrid retrieval, reranker, answers with citations. Measure faithfulness with Ragas.

---

### Month 3 — Agents (the real shift)

- **Agent ≠ chatbot.** An agent has a goal, picks tools, observes results, and replans.
- ReAct loop in detail; cycle limits; cost budgets
- **LangGraph** (most popular in 2026) — state, nodes, edges, conditional routing, human-in-the-loop
- Alternatives: **AutoGen** (multi-agent conversation), **CrewAI** (role-based), **Pydantic-AI** (typed, lightweight), **Semantic Kernel** (Microsoft)
- Tool design: idempotency, error surfaces, schemas, security
- **Project:** A "research agent" that takes a topic, calls a web search tool + a scraping tool + a summarizer, then produces a markdown report with sources. Built with **LangGraph**.

---

### Month 4 — MCP, Multi-Agent & Memory

- **MCP — Model Context Protocol** (Anthropic, now an industry standard)
  - MCP servers (expose tools) & clients (LLM hosts)
  - Build your own MCP server in Python — file system, GitHub, Postgres
- **Multi-agent patterns:** supervisor/worker, hierarchical, swarm, debate
- **Memory:** short-term (window), long-term (vector + summarization), episodic (Mem0/Letta/Zep)
- **Project:** Multi-agent dev assistant — Planner agent + Coder agent + Reviewer agent, all talking to MCP servers for filesystem + GitHub + tests. Persistent memory of prior runs.

---

### Month 5 — Evaluation, Observability, Safety

- **Why eval matters:** without it, you're flying blind
- **Offline eval:** golden datasets, LLM-as-a-judge, deterministic checks
- **Online eval:** **LangFuse / LangSmith / Phoenix** — traces, scores, A/B
- **Latency & cost** tracking; OpenTelemetry GenAI semantic conventions
- **Safety:**
  - **OWASP Top 10 for LLM Applications** (memorize this)
  - **Prompt injection** (direct/indirect) & defenses
  - **PII redaction** before sending to LLM
  - Output guardrails (toxicity, jailbreak, schema)
  - Tool sandboxing (never give a shell tool to a public agent)
- **Project:** Add LangFuse traces + an eval set of 50 questions with LLM-as-judge to the Month-3 agent. Add prompt-injection tests with Garak or Promptfoo.

---

### Month 6 — Production: Deploy, Scale, Optimize

- Serve a custom agent as a **FastAPI** service, streaming + SSE
- Containerize → deploy on **EKS/AKS/GKE** (you already know Kubernetes 😉)
- **Async + queues** for long-running agents (Celery / Arq / Temporal.io)
- Cost optimization: model routing (cheap → expensive), **prompt caching** (Anthropic / OpenAI), KV caches, response caching
- Local/edge: **Ollama** + small models (Llama 3.2, Phi-4, Qwen) for sensitive workloads
- **Fine-tuning** — only when you actually need it: LoRA basics, when SFT vs DPO vs RAG
- **Project:** Capstone — see below

---

## 📚 Official / Trusted Free Resources

| Source | Why |
|--------|-----|
| **[OpenAI Cookbook](https://github.com/openai/openai-cookbook)** | Reference recipes from the source |
| **[Anthropic — Building Agents](https://docs.anthropic.com/)** | "Building effective agents" essay is a must |
| **[Model Context Protocol](https://modelcontextprotocol.io/)** | The MCP spec & SDKs |
| **[LangChain Academy](https://academy.langchain.com/)** | Free LangGraph courses |
| **[Hugging Face Course](https://huggingface.co/learn)** | Free NLP, agents, RL courses |
| **[OWASP Top 10 for LLMs](https://owasp.org/www-project-top-10-for-large-language-model-applications/)** | Security must-read |
| **[arXiv (cs.CL, cs.AI)](https://arxiv.org/)** | Original papers — read 1/week |
| **[Andrej Karpathy's videos](https://www.youtube.com/@AndrejKarpathy)** | Free, deep, no fluff |
| **[Eugene Yan's blog](https://eugeneyan.com/)** | Best applied-ML/LLM engineering writing |
| **[Chip Huyen's blog/book](https://huyenchip.com/)** | "Designing ML Systems" / "AI Engineering" — required reading |

---

## 🧪 Capstone — `agentic-platform` GitHub repo

Build a real, multi-component agentic platform:

1. **Frontend:** chat UI (Next.js) with streaming
2. **Backend:** FastAPI gateway → LangGraph orchestrator
3. **Agents:** Planner, Researcher (web + RAG), Coder, Reviewer
4. **MCP servers:** filesystem, GitHub, Postgres, web-search
5. **RAG:** pgvector with hybrid search + reranker
6. **Memory:** per-user long-term via Mem0
7. **Eval:** LangFuse + 100-question golden set + LLM judge
8. **Safety:** Llama Guard + prompt-injection detector + output schema
9. **Infra:** Dockerized, Helm-charted, deployed on EKS/AKS via GitHub Actions
10. **Observability:** Prometheus + Grafana + LangFuse + OTel GenAI

> This single project demonstrates **everything** an agentic AI engineer needs.

---

## 🧠 Interview Topics

- "RAG vs fine-tuning — when?"
- "How do you evaluate an agent without ground truth?"
- "What's prompt injection and how do you defend against it?"
- "Walk me through how LangGraph state works."
- "What's MCP and why does it matter?"
- "How do you reduce LLM cost in production?"
- "Multi-agent vs single agent with tools — when each?"
- "How do you handle hallucination in a RAG system?"
- "What's the difference between embeddings cosine and dot product?"
- "How would you detect that a model has regressed in production?"

---

## ▶️ Where to go from here

- **AI infra deep dive:** vLLM, distributed inference, GPU scheduling
- **Research-adjacent:** read 1 paper/week, reproduce results
- **Specialize:** voice agents, computer-use agents, autonomous-coding agents
- **Combine tracks:** **MLOps + DevSecOps + Agentic** = the highest-paid profile of 2026
