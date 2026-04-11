# Grammario -- Observability & Monitoring
> Related: [[Plans for Grammario 1-1-2026]] | [[Grammario Upgrade Interview Summarization]] | [[Grammario Performance and ML-NLP Feature Upgrade Implementation Details]]

## Instrumented Metrics

| Metric | Source | Purpose |
|--------|--------|---------|
| NLP pipeline latency (ms) | Structured logs | Detect model degradation |
| LLM response time (ms) | Structured logs | Catch API slowdowns |
| Embedding encode time (ms) | Structured logs | Baseline for scaling |
| Cache hit/miss/rate (%) | `/health` + `/cache/stats` | Validate caching effectiveness |
| Loaded models per engine | `/health` | Confirm expected models in memory |
| RSS/VMS memory (MB) | `/health` via `psutil` | Prevent OOM on constrained VM |
| Feature availability flags | `/health` | At-a-glance capability check |
| Total analysis time (ms) | Structured logs | End-to-end latency tracking |

---

## Admin Dashboard (`/admin/backend`)

- Green/red/yellow health status per service (LLM, Redis, Embeddings)
- Per-language model badges showing loaded vs. unloaded state
- Cache hit/miss/rate stats
- Memory usage progress bar against container limit
- Expandable raw health JSON

---

## Graceful Degradation

- **spaCy fails** → falls back to Stanza transparently
- **Redis unreachable** → caching silently disables, pipeline continues
- **Embedding model down** → pipeline returns results without embeddings
- **LLM timeout** → NLP parse still returned via `return_exceptions=True`

Every failure is logged and reflected in the `/health` endpoint.

---

## How to Say It in the Interview

> "I built observability into the ML pipeline from the ground up. Every algorithm stage -- NLP parsing, LLM inference, embedding generation, difficulty scoring -- is instrumented with millisecond-level timing, and those metrics are surfaced through a structured health endpoint and an admin dashboard. I track cache hit rates to validate that the caching layer is effective, monitor which models are loaded per language, and report real-time memory usage since the ML models are the heaviest consumers on a constrained VM. The system also has graceful degradation -- if the embedding model or Redis goes down, the pipeline continues with reduced functionality rather than failing, and the health endpoint reflects the degraded state. If I were scaling this further, the next step would be pushing these metrics to something like Prometheus with Grafana dashboards, but the instrumentation layer is already there."
