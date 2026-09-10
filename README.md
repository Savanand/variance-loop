<div align="center">

# Variance Loop

**Continuous Discovery.**

*A four-product AI publishing ecosystem — architected, built, and deployed solo.*

[varianceloop.com](https://varianceloop.com)

</div>

<br>

<div align="center">

![Python](https://img.shields.io/badge/Python-3.11-000000?style=flat-square&logo=python&logoColor=white)
![FastAPI](https://img.shields.io/badge/FastAPI-000000?style=flat-square&logo=fastapi&logoColor=white)
![Astro](https://img.shields.io/badge/Astro-000000?style=flat-square&logo=astro&logoColor=white)
![Ollama](https://img.shields.io/badge/Ollama-Local%20LLM-000000?style=flat-square)
![Cloudflare](https://img.shields.io/badge/Cloudflare-Pages-000000?style=flat-square&logo=cloudflare&logoColor=white)

</div>

<br>

Variance Loop generates and continuously refreshes editorial content across four verticals — institutional markets, macro & geopolitics, film, and automotive design — using agentic AI workflows running against locally hosted models.

It's a self-directed project, not a commercial product: built to explore current agentic AI and systems design patterns in a real, live deployment rather than a demo.

This repository holds architecture notes only. Application source lives in private repositories.

<br>

---

<br>

## The Ecosystem

<br>

<table>
<tr>
<td width="50%" valign="top">

### Terminal
`terminal.varianceloop.com`

Institutional-markets analysis comparing retail sentiment against institutional capital flow, built on aggregated SEC filings and positioning data.

</td>
<td width="50%" valign="top">

### News
`news.varianceloop.com`

Macro and geopolitical synthesis over SEC Parquet datasets, surfacing structural narrative shifts.

</td>
</tr>
<tr>
<td width="50%" valign="top">

### Cinema
`cinema.varianceloop.com`

Audits rating variance — comparing LLM-generated reviews against public rating aggregates to flag rating inflation and review-bombing.

</td>
<td width="50%" valign="top">

### Cars
`cars.varianceloop.com`

Automotive design analysis using vector embeddings to score proportions, aesthetic continuity, and design language.

</td>
</tr>
</table>

<br>

---

<br>

## Architecture

<br>

A single principle governs the whole system: **keep heavy compute and large datasets on local hardware, ship only compiled static output to the edge.**

```mermaid
graph TD
    subgraph "Local Hardware — Data & AI Engine"
        A[(Source Data<br/>SEC Parquet · TMDB · OMDb)] --> B[DuckDB]
        B --> C[Python — Agent State Machine]
        D[Ollama — Local LLM<br/>Qwen · Llama Vision] --> C
        C --> E[Streamlit — Human-in-the-Loop]
    end

    subgraph "Local Filesystem — Serialization"
        E --> F[YAML Frontmatter]
        E --> G[Markdown Content]
    end

    subgraph "Node.js — Build"
        F & G --> H[Astro]
        H --> I[Tailwind CSS v4]
        H --> J[React 19]
        H --> K[/Static Output/]
        K --> L[Pagefind — WASM Search]
    end

    subgraph "Cloudflare — Edge"
        K & L --> M[Cloudflare Pages]
        M --> N[Browser]
    end

    style A fill:#f5f5f7,stroke:#1d1d1f,color:#1d1d1f
    style D fill:#f5f5f7,stroke:#1d1d1f,color:#1d1d1f
    style M fill:#1d1d1f,stroke:#1d1d1f,color:#ffffff
    style N fill:#1d1d1f,stroke:#1d1d1f,color:#ffffff
```

Local inference avoids ongoing cloud GPU cost and keeps proprietary prompts and multi-gigabyte datasets off the network. Everything that reaches the public internet is pre-compiled static output — no live database exposure, no backend attack surface.

<br>

---

<br>

## Cinema — Agentic Pipeline

<br>

The clearest example of the pattern used across the ecosystem: a resumable, checkpointed state machine implementing an **LLM-as-a-judge** design, deliberately isolating review generation from review auditing.

```mermaid
stateDiagram-v2
    [*] --> pending
    pending --> ratings_fetched: Agent 1 · Data Gathering
    ratings_fetched --> review_drafted: Agent 2 · Editorial Critic
    review_drafted --> completed: Agent 3 · Variance Judge
    ratings_fetched --> insufficient_data: no public rating data
    completed --> [*]
    insufficient_data --> [*]

    classDef done fill:#1d1d1f,stroke:#1d1d1f,color:#ffffff
    class completed,insufficient_data done
```

<br>

<details>
<summary><strong>Design details</strong></summary>

<br>

- **Checkpointed after every agent.** An interrupted run resumes from the last completed state instead of redoing work.
- **Immutable audit trail.** Every fetch and prompt execution appends to a per-title log — a transparent record of what the system did.
- **Guardrail against hallucination.** If Agent 1 can't find enough public rating data, the pipeline halts at `insufficient_data` rather than letting the model invent a report.
- **Blinded review generation.** Agent 2 drafts from neutral plot and cast facts only, with no visibility into public sentiment — keeping Agent 3's judgment non-circular.

</details>

<br>

---

<br>

## Stack

<br>

| | |
|---|---|
| **Backend / AI** | Python 3.11 · FastAPI · Ollama (local inference) · DuckDB · Pydantic · Streamlit |
| **Frontend** | Astro · Tailwind CSS v4 · React 19 · Framer Motion · Pagefind |
| **Data** | Postgres / pgvector · SEC EDGAR filings · Parquet |
| **Infra** | Cloudflare Pages · Wrangler CLI · Oracle Cloud (Ampere A1) · Docker Compose |

<br>

---

<br>

<div align="center">

Application source is private. Happy to walk through it directly as part of a hiring conversation.

[varianceloop.com](https://varianceloop.com) · [github.com/Savanand](https://github.com/Savanand)

</div>
