# vector-db — Reference

## Store Comparison

| | pgvector | Qdrant | Weaviate | Pinecone | Chroma | Milvus |
|---|---|---|---|---|---|---|
| Scale | <50M vectors | 1M–1B+ | 1M–100M | Any (managed) | <1M (dev) | Billions |
| Hybrid search | ✅ (with FTS) | ✅ | ✅ (BM25) | ❌ | ❌ | ✅ |
| Self-hosted | ✅ | ✅ | ✅ | ❌ | ✅ | ✅ |
| Managed cloud | Via Supabase | ✅ Qdrant Cloud | ✅ WCS | ✅ | ❌ | ✅ Zilliz |
| Cost vs Pinecone | 60–80% cheaper | Similar self-hosted | Similar | Baseline | Free | Enterprise |
| Production-ready | ✅ | ✅ | ✅ | ✅ | ❌ | ✅ |

---

## BUILD — pgvector (Node.js + pg)

```sql
-- Migration
CREATE EXTENSION IF NOT EXISTS vector;
CREATE TABLE documents (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  content TEXT NOT NULL,
  embedding vector(1536), -- match your model dimensions
  metadata JSONB,
  created_at TIMESTAMPTZ DEFAULT now()
);
-- HNSW index (better recall, slower write)
CREATE INDEX ON documents USING hnsw (embedding vector_cosine_ops);
-- IVFFlat index (faster build, write-heavy workloads)
-- CREATE INDEX ON documents USING ivfflat (embedding vector_cosine_ops) WITH (lists = 100);
```

```ts
import OpenAI from 'openai'
import { Pool } from 'pg'

const openai = new OpenAI()
const pool = new Pool({ connectionString: process.env.DATABASE_URL })

async function embed(text: string): Promise<number[]> {
  const res = await openai.embeddings.create({
    model: 'text-embedding-3-small',
    input: text,
  })
  return res.data[0].embedding
}

async function insert(content: string, metadata: object) {
  const embedding = await embed(content)
  await pool.query(
    'INSERT INTO documents (content, embedding, metadata) VALUES ($1, $2, $3)',
    [content, JSON.stringify(embedding), metadata]
  )
}

async function search(query: string, limit = 5) {
  const embedding = await embed(query)
  const { rows } = await pool.query(
    `SELECT id, content, metadata,
     1 - (embedding <=> $1) AS similarity
     FROM documents
     ORDER BY embedding <=> $1
     LIMIT $2`,
    [JSON.stringify(embedding), limit]
  )
  return rows
}
```

---

## BUILD — Qdrant (Node.js)

```bash
npm install @qdrant/js-client-rest
```

```ts
import { QdrantClient } from '@qdrant/js-client-rest'

const client = new QdrantClient({ url: 'http://localhost:6333' })

// Create collection
await client.createCollection('documents', {
  vectors: { size: 1536, distance: 'Cosine' },
})

// Insert
await client.upsert('documents', {
  points: [{ id: crypto.randomUUID(), vector: embedding, payload: { content, userId } }],
})

// Search with filter (permission-aware — always filter by userId)
const results = await client.search('documents', {
  vector: await embed(query),
  limit: 5,
  filter: { must: [{ key: 'userId', match: { value: currentUserId } }] },
  with_payload: true,
})
```

---

## RAG Pipeline — Production Pattern

```ts
// 1. Chunk documents (naive: 512 tokens, 50 overlap)
function chunkText(text: string, size = 512, overlap = 50): string[] {
  const words = text.split(' ')
  const chunks: string[] = []
  for (let i = 0; i < words.length; i += size - overlap) {
    chunks.push(words.slice(i, i + size).join(' '))
  }
  return chunks
}

// 2. Hybrid retrieval (vector + keyword)
async function retrieve(query: string, userId: string, k = 20) {
  const [vectorResults, keywordResults] = await Promise.all([
    vectorSearch(query, userId, k),
    keywordSearch(query, userId, k),
  ])
  return reciprocalRankFusion([vectorResults, keywordResults])
}

// 3. Re-ranking (required — never skip in production)
async function rerank(query: string, docs: Doc[], topK = 5) {
  // Use Cohere rerank or cross-encoder model
  const response = await cohere.rerank({
    model: 'rerank-english-v3.0',
    query,
    documents: docs.map(d => d.content),
    top_n: topK,
  })
  return response.results.map(r => docs[r.index])
}

// 4. Assemble context + generate
async function rag(query: string, userId: string) {
  const candidates = await retrieve(query, userId)
  const reranked = await rerank(query, candidates)
  const context = reranked.map(d => d.content).join('\n\n')
  // Pass to /claude-api or OpenAI — validate output before use
  return generateAnswer(query, context)
}
```

**HyDE pattern** (20–40% precision gain):
```ts
// Generate hypothetical answer first, embed it, then retrieve
const hypothetical = await llm.generate(`Answer this briefly: ${query}`)
const hydeEmbedding = await embed(hypothetical)
const results = await vectorSearch(hydeEmbedding, userId)
```

---

## Reciprocal Rank Fusion

```ts
function reciprocalRankFusion<T extends { id: string }>(
  rankings: T[][], k = 60
): T[] {
  const scores = new Map<string, number>()
  const items = new Map<string, T>()
  for (const ranking of rankings) {
    ranking.forEach((item, rank) => {
      scores.set(item.id, (scores.get(item.id) ?? 0) + 1 / (rank + k))
      items.set(item.id, item)
    })
  }
  return [...scores.entries()]
    .sort(([, a], [, b]) => b - a)
    .map(([id]) => items.get(id)!)
}
```

---

## REVIEW — Security Checklist

| Check | Pass condition |
|---|---|
| No PII in embeddings | Sensitive fields excluded or encrypted before embedding |
| Permission-aware retrieval | Filter by userId/tenantId on every search — never return all vectors |
| Input validation | Query text length bounded; untrusted input sanitised before embedding |
| RAG output sanitised | LLM output validated before returning to user; no direct passthrough |
| Document source validation | Only trusted/curated documents indexed; no open upload to vector store |
| Rate limiting on embed endpoints | Per-user embedding rate limited to prevent DoS / cost abuse |
| Multi-tenant isolation | Separate collections or mandatory filter per tenant — no cross-tenant leakage |
| HNSW index on write-heavy workload | If write rate is high, IVFFlat or streaming index preferred |
