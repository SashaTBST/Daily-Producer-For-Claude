---
name: vector-db
description: Vector database skill covering pgvector, Qdrant, Weaviate, Pinecone, Chroma, and Milvus — store selection, embedding model choice, semantic search, RAG pipelines (with re-ranking), duplicate detection, and recommendation systems. Use when adding vector search, similarity search, or AI-powered retrieval to any application. Embedding inversion is OWASP LLM08:2025 — treat vector stores with the same rigour as secrets storage.
argument-hint: "[mode: guide|build|rag|review] [store: pgvector|qdrant|weaviate|pinecone|chroma|milvus] [description]"
---

## Scope

/vector-db owns: store setup, schema/collection design, embedding generation (routes to `/claude-api` or `/python` for model inference), vector search queries, RAG pipeline assembly, re-ranking, duplicate detection, recommendation patterns. General Postgres schema → `/sql`. General Node.js server → `/node`.

⚠ Chroma = prototype/dev only. Not for production. Migrate to Qdrant or Milvus when vector count exceeds 1M or QPS rises.
⚠ Embedding inversion is a known attack (OWASP LLM08:2025). Never store sensitive PII in embeddings without access controls.

## Store Selection

| Signal | Store |
|---|---|
| Existing Postgres stack, <50M vectors, hybrid (vector + SQL) | pgvector (default) |
| Highest query performance, 1M+ vectors, self-hosted | Qdrant |
| Hybrid search (BM25 + vector) required | Weaviate |
| Managed, simplest onboarding, startup scale | Pinecone |
| Rapid prototyping, dev only | Chroma |
| Billions of vectors, GPU acceleration, enterprise | Milvus |

pgvector is 60–80% cheaper than Pinecone and sufficient for 90% of RAG workloads. Switch to Qdrant when pgvector hits scale limits (~50M vectors or sub-20ms latency required).

## Embedding Model Selection

| Signal | Model |
|---|---|
| Node.js, managed API, best cost/accuracy | `text-embedding-3-small` (OpenAI) — default |
| Long-context documents (>2K tokens) | `nomic-embed-text` (open-source, runs via Ollama) |
| Offline / local inference required | `mxbai-embed-large` or `nomic-embed-text` |
| Maximum semantic precision, cost not a concern | `text-embedding-3-large` (OpenAI) |

## Modes

**GUIDE** — Store or use case unspecified. Ask two questions:
1. "Existing Postgres stack, or greenfield vector-only store?"
2. "Semantic search only, or full RAG pipeline with retrieval + generation?"

**BUILD** — Store named. Generate: schema/collection setup + embedding pipeline + query function. Always include hybrid search if store supports it. Full patterns → REFERENCE.md.

**RAG** — Build or improve a RAG pipeline. Always include: chunking strategy, hybrid retrieval, re-ranking (required — not optional). HyDE and multi-query expansion → REFERENCE.md.

**REVIEW** — Audit existing vector DB setup. Full checklist → REFERENCE.md. Report: PASS / WARN / FAIL.

## Security Gate

Every BUILD response ends with this line — never skip:
`Security pass: ✓ no PII/secrets in embeddings without access controls ✓ permission-aware retrieval (users only fetch their own vectors) ✓ input validated before embedding ✓ RAG output sanitised before passing to LLM ✓ document source validated (no untrusted injection) ✓ rate limiting on embedding endpoints`

Flag any item that cannot be confirmed. Stop and fix.

## Cross-Skill Integration

- `/sql` — pgvector lives in Postgres; /sql owns schema and migration layer
- `/nosql` — Qdrant/Weaviate/Pinecone client setup and caching layer
- `/claude-api` — embedding generation via Anthropic; propose alongside BUILD
- `/python` — local embedding inference (nomic-embed, mxbai via sentence-transformers)
- `/node` — HTTP server wiring for embedding endpoints and search APIs
- `/backend` — routes here when vector search is the task layer

## Anti-patterns

✗ Never use Chroma in production — no concurrency, no horizontal scaling, dev tool only
✗ Never store raw PII in embeddings — embedding inversion can recover source text (OWASP LLM08)
✗ Never skip re-ranking in production RAG — naive top-k retrieval is a red flag in 2025+
✗ Never embed without chunking strategy — full documents as single vectors destroy recall
✗ Never share a vector store across tenants without namespace/partition isolation
✗ Never use HNSW for write-heavy workloads — slow indexing; use IVFFlat or streaming index
✗ Never skip hybrid search if the store supports it — pure vector search misses exact matches


Every response ends with NEXT MOVE.