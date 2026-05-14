<div align="center">

# Justin Arndt

### Building AI infrastructure that runs on your hardware, not theirs.

[![LinkedIn](https://img.shields.io/badge/LinkedIn-Connect-0A66C2?style=flat&logo=linkedin)](https://linkedin.com/in/qualityai/)
[![HuggingFace](https://img.shields.io/badge/HuggingFace-Spaces-FFD21E?style=flat&logo=huggingface)](https://huggingface.co/justinarndt)
[![GitHub](https://img.shields.io/badge/GitHub-j--arndt-181717?style=flat&logo=github)](https://github.com/j-arndt)

</div>

---

I'm an engineer focused on one problem: **making AI systems production-grade without giving your data to an API.**

That means local inference at the edge, deterministic memory compression, and enterprise-grade guardrails on agentic behavior. All four repositories below are standalone systems that solve real infrastructure problems. They are also a portfolio — a record of how I think about building AI that is auditable, private, and permanent.

---

## Projects

### 🧠 [Project Engram — Agent Long-Term Memory](https://github.com/j-arndt/agent-long-term-memory)
**The problem:** Local LLMs forget everything the moment their context fills up. A 7B model on an 8GB GPU has roughly 4 hours before it's back to zero. Every architectural decision, every bug traced, every library rejected — gone.

**What it does:** Engram watches your VRAM in real time. When it hits 90%, a background process triggers the *DEPA Crucible* — a three-strategy compression loop that encodes your entire session (files modified, bugs encountered, architectural decisions) into a **4,096-byte Latent Seed** using Bloom filters, Merkle trees, and zlib. That seed is pushed to Cloudflare R2 at zero egress cost. Six months later, a single natural-language question pulls it back in under 500ms and hydrates it into a fresh context.

**At night**, when the machine is idle, Unsloth QLoRA fine-tunes the base model on sessions it hasn't seen yet — baking the memory into the weights permanently. The model wakes up knowing things it was never directly told.

**Stack:** `llama-cpp-python` · `Qwen2.5-Coder-7B Q4_K_M` · `Cloudflare R2` · `SQLite FTS5` · `Unsloth QLoRA` · `Python 3.12` · `WSL2`

**Key numbers:**
| Metric | Result |
|---|---|
| Seed size | 1,360 bytes (fits in a single TCP packet) |
| Compression drift | 0.000 (lossless reconstruction) |
| First token after hydration | 401ms |
| R2 storage cost | $0.00 (zero egress) |
| DEPA probes passed | 6/6 |

[![Space](https://img.shields.io/badge/Live%20Demo-HF%20Space-FFD21E?style=flat&logo=huggingface)](https://huggingface.co/spaces/justinarndt/agent-long-term-memory)
[![Repo](https://img.shields.io/badge/Code-GitHub-181717?style=flat&logo=github)](https://github.com/j-arndt/agent-long-term-memory)

---

### ♻️ [Agent Resurrection](https://github.com/j-arndt/agent-resurrection)
**The problem:** Stateless AI agents lose all context between sessions. Every restart is amnesia. The naive solution — dump everything to a text file — explodes token budgets and leaks private data.

**What it does:** Agent Resurrection is the mathematical foundation that Engram is built on. It defines the **SEED_POLY** encoding protocol — a deterministic, binary-efficient serialization format that compresses agent state using Bloom filters, Merkle trees, and polynomial hash vectors. The **DEPA (Deterministic Evolutionary Proof of Agency)** framework validates resurrection fidelity through six weighted probes, scoring information drift to three decimal places.

The key insight: natural language is the worst possible format for agent memory. A file path takes 60 bytes as text. As a Bloom filter slot it takes 3 bytes and leaks no plaintext. The DEPA Slap-Back Loop exploits this — it forces the LLM toward mathematical encoding by rejecting prose until drift drops below 0.10.

**Stack:** `Python 3.12` · `Pydantic` · `Bloom filters` · `Merkle trees` · `zlib` · `base85`

[![Space](https://img.shields.io/badge/Live%20Demo-HF%20Space-FFD21E?style=flat&logo=huggingface)](https://huggingface.co/spaces/justinarndt/agent-resurrection)
[![Repo](https://img.shields.io/badge/Code-GitHub-181717?style=flat&logo=github)](https://github.com/j-arndt/agent-resurrection)

---

### 🔒 [Certified Agentic Firewall](https://github.com/j-arndt/certified-agentic-firewall)
**The problem:** Enterprise AI deployments fail audits not because the model is wrong, but because no one can prove *why* it made a decision or *what* data it acted on. Agentic systems — ones that browse, read files, call APIs — are invisible to compliance teams.

**What it does:** A production-grade input/output firewall for agentic AI systems. Every tool call, every prompt, every model output is intercepted, classified, and logged with a cryptographic audit trail before it reaches the model or the downstream system. Policy rules are declarative YAML; enforcement is deterministic. The system is designed to satisfy enterprise audit requirements (SOC 2, ISO 27001) without modifying the underlying model or agent framework.

**Stack:** `Python` · `FastAPI` · `YAML policy engine` · Pluggable classifier backends

[![Repo](https://img.shields.io/badge/Code-GitHub-181717?style=flat&logo=github)](https://github.com/j-arndt/certified-agentic-firewall)

---

### 🏥 [Certified PHI Egress Firewall](https://github.com/j-arndt/certified-phi-egress-firewall)
**The problem:** The fastest way to violate HIPAA with an AI system is to let a prompt containing patient data reach a cloud API. Most teams don't know it's happening until the audit.

**What it does:** A HIPAA-aware egress firewall that intercepts all outbound AI traffic and scans for Protected Health Information (PHI) before it leaves the network boundary. Uses a lightweight local classifier to detect 18 HIPAA PHI categories (names, DOBs, MRNs, diagnoses) with sub-50ms latency. Violations are blocked and logged. Clean traffic passes through. The system is designed for healthcare organizations that want to use commercial LLMs without compromising patient data.

**Stack:** `Python` · Local PHI classifier · Policy engine · Audit logger

[![Repo](https://img.shields.io/badge/Code-GitHub-181717?style=flat&logo=github)](https://github.com/j-arndt/certified-phi-egress-firewall)

---

## How These Fit Together

These aren't four unrelated experiments. They are four layers of the same infrastructure problem:

```
┌─────────────────────────────────────────────────────┐
│  PHI Egress Firewall — Data never leaves the edge   │
│  Agentic Firewall    — Every action is auditable     │
│  Agent Resurrection  — State survives across resets  │
│  Project Engram      — Memory survives across time   │
└─────────────────────────────────────────────────────┘
```

The thesis: **AI systems should be private, persistent, and provable.** Not one of those. All three.

---

## Background

My background is in quality systems and validation for regulated industries — the world where "it works" is never enough and you need to prove *how* it works, *why* it works, and *what* it would take for it to fail.

That lens shapes everything I build. The DEPA Crucible isn't just a compression algorithm — it's a validation protocol with quantified fidelity probes. The PHI Egress Firewall isn't just a content filter — it's an audit trail with a defined scope and documented exclusions. The Certified Agentic Firewall produces logs that a QA auditor can actually read.

I'm now applying that instinct to AI infrastructure: building systems that are private, persistent, and provable — the three properties that regulated industries require and that most AI tooling ignores entirely.

Open to infrastructure, platform, and applied AI roles where the problem is making AI production-grade in environments that can't afford to get it wrong.

[![LinkedIn](https://img.shields.io/badge/LinkedIn-Let's%20Connect-0A66C2?style=flat&logo=linkedin)](https://linkedin.com/in/qualityai/)

---

<div align="center">
<sub>All projects built on an RTX 4060 laptop. No cloud GPUs were harmed in the making of this portfolio.</sub>
</div>
