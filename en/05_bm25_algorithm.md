# BM25 Algorithm

## Table of Contents

1. [The Need for Better Keyword Search in the AI Era](#the-need-for-better-keyword-search-in-the-ai-era)
2. [What Is the BM25 Algorithm?](#what-is-the-bm25-algorithm)
3. [How BM25 Works](#how-bm25-works)
4. [BM25 vs TF-IDF](#bm25-vs-tf-idf)
5. [BM25 in PostgreSQL](#bm25-in-postgresql)
6. [Practical Applications](#practical-applications)
7. [Summary](#summary)

---

## The Need for Better Keyword Search in the AI Era

### The Resurgence of "Boring" PostgreSQL

We've been hearing a lot about a resurgence of interest in "boring," reliable PostgreSQL, especially since AI has taken off. While initially all the talk seemed to be about vector databases, an emerging pattern is merging vector and keyword search.

### Why AI Changes the Game

Traditional search engines like Apache Lucene, Elasticsearch, and native PostgreSQL have offered keyword search for years. However, AI has hastened the need to improve the relevance of the output they provide.

**The Shift in Use Cases:**

| Traditional Search | AI-Native Search |
|-------------------|------------------|
| Humans browsing catalogs | LLMs retrieving context |
| Engineers querying logs | RAG systems |
| Simple keyword matching | Hybrid search (vector + keyword) |
| Result speed prioritized | Result quality paramount |

### The Challenge of AI-Native Applications

> "AI-native applications, RAG [retrieval-augmented generation] systems, chat agents, and agentic workflows need search not for humans browsing catalogs or engineers querying logs, but for LLMs [large language models] retrieving context." — TJ (Todd) Green, Senior Software Engineer

**Key Requirements:**

- ✅ **Semantic understanding** from vector search
- ✅ **Precision of keyword matching** for exact terms
- ✅ **High result quality** for accurate context retrieval
- ✅ **Consistent ranking** for reliable LLM outputs

### The Complementary Nature of Search Approaches

The two approaches are deeply complementary:

```
┌─────────────────────────────────────────────────────────┐
│                    Hybrid Search                         │
├─────────────────────────────────────────────────────────┤
│                                                          │
│  Vector Search          Keyword Search                  │
│  ──────────────         ───────────────                 │
│  • Conceptual           • Exact term matching           │
│    similarity                                            │
│  • Semantic             • Prevents missed terms         │
│    understanding                                          │
│  • Contextual            • Precision                    │
│    relevance                                            │
│                                                          │
│  ─────────────────────────────────────────────────────  │
│                    Combined Results                      │
│  ─────────────────────────────────────────────────────  │
│                                                          │
└─────────────────────────────────────────────────────────┘
```

### The PostgreSQL Limitation

> "The challenge is that Postgres native full-text search lacks the ranking signals needed to consistently surface the most relevant results."

This is where BM25 comes in.

---

## What Is the BM25 Algorithm?

### Overview

**BM25 (Best Matching 25)** is a ranking algorithm used in information retrieval systems to determine the relevance of documents to a search query. It's considered an improvement over the traditional TF-IDF (Term Frequency–Inverse Document Frequency) approach.

### History

- **Origin**: Developed in the 1980s as part of the Okapi information retrieval system
- **Evolution**: BM25 refers to the 25th iteration of the algorithm
- **Adoption**: Now the de facto standard in modern search engines
- **Implementation**: Used by Elasticsearch, Apache Solr, and many others

### Why "BM25"?

The "25" doesn't refer to a version number but rather to the specific parameter tuning that makes this variant particularly effective for most use cases.

---

## How BM25 Works

### The BM25 Formula

BM25 calculates a relevance score for each document based on:

```
score(D, Q) = Σ IDF(qi) × (f(qi, D) × (k1 + 1)) / (f(qi, D) + k1 × (1 - b + b × |D| / avgdl))
```

**Where:**

| Variable | Description |
|----------|-------------|
| `D` | Document being scored |
| `Q` | Query containing terms q₁, q₂, ..., qₙ |
| `f(qi, D)` | Frequency of term qi in document D |
| `|D|` | Length of document D (in terms) |
| `avgdl` | Average document length in the collection |
| `k1` | Term frequency saturation parameter (typically 1.2-2.0) |
| `b` | Length normalization parameter (typically 0.75) |
| `IDF(qi)` | Inverse document frequency of term qi |

### Key Components

#### 1. Inverse Document Frequency (IDF)

Weights rare terms higher than common terms:

```
IDF(qi) = log((N - df(qi) + 0.5) / (df(qi) + 0.5))
```

**Example:**
- "database" appears in 1000 documents → lower IDF
- "PostgreSQL" appears in 50 documents → higher IDF
- "pg_bm25" appears in 5 documents → highest IDF

#### 2. Term Frequency Saturation

Prevents terms used repeatedly from dominating results:

- Without saturation: A document with "database" appearing 100 times would score 100x higher than one with it appearing once
- With saturation: Diminishing returns as term frequency increases

**Visualization:**

```
Score
  ↑
  │    ┌───── k1 = 1.2 (low saturation)
  │   ╱
  │  ╱
  │ ╱      k1 = 2.0 (high saturation)
  │╱───────
  └──────────────→ Term Frequency
```

#### 3. Length Normalization

Prevents long documents from dominating:

- Longer documents naturally have more term matches
- BM25 normalizes by document length
- Parameter `b` controls the strength of normalization

**Impact:**

| Document Length | Without Normalization | With Normalization |
|-----------------|----------------------|-------------------|
| Short (100 words) | May be under-ranked | Fairly scored |
| Medium (500 words) | Fairly scored | Fairly scored |
| Long (2000 words) | Over-ranked | Fairly scored |

#### 4. Relative Ranking

Focuses on rank order rather than absolute score values:

- ✅ **Consistent ordering**: Document A > Document B > Document C
- ✅ **Cross-query comparison**: Same ranking logic across different queries
- ✅ **Threshold filtering**: Can filter out low-relevance results

### Parameter Tuning

| Parameter | Default | Range | Effect |
|-----------|---------|-------|--------|
| `k1` | 1.2-2.0 | 0 → ∞ | Controls term frequency saturation |
| `b` | 0.75 | 0 → 1 | Controls length normalization strength |

**Practical Guidelines:**

- **k1 = 0**: No term frequency consideration (binary)
- **k1 = 1.2**: Good for most general search (default)
- **k1 = 2.0+**: For short queries where term frequency matters more
- **b = 0**: No length normalization
- **b = 0.75**: Balanced approach (default)
- **b = 1.0**: Strong length normalization

---

## BM25 vs TF-IDF

### Comparison

| Aspect | TF-IDF | BM25 |
|--------|--------|------|
| **Term Frequency** | Linear | Saturated |
| **Length Normalization** | None | Configurable |
| **Document Length Impact** | Problematic | Controlled |
| **Parameter Tuning** | None | k1, b |
| **Relevance Quality** | Basic | Advanced |
| **Modern Adoption** | Limited | Widespread |

### Why BM25 is Better

#### 1. Term Frequency Saturation

**TF-IDF Problem:**
```
Document A: "database" appears 100 times → Score: 100
Document B: "database" appears 1 time → Score: 1
Ratio: 100:1 (unrealistic)
```

**BM25 Solution:**
```
Document A: "database" appears 100 times → Score: 8.5
Document B: "database" appears 1 time → Score: 1.0
Ratio: 8.5:1 (realistic)
```

#### 2. Length Normalization

**TF-IDF Problem:**
- Long documents naturally score higher
- Short documents are disadvantaged

**BM25 Solution:**
- Normalizes by average document length
- Fair comparison across document lengths

#### 3. Practical Performance

| Metric | TF-IDF | BM25 |
|--------|--------|------|
| **Relevance Accuracy** | 65-75% | 80-90% |
| **Long Document Bias** | High | Low |
| **Short Query Handling** | Poor | Good |
| **Parameter Flexibility** | None | High |

---

## BM25 in PostgreSQL

### Native PostgreSQL Limitations

PostgreSQL's native full-text search uses basic ranking:

```sql
-- Native PostgreSQL (basic ranking)
SELECT 
  title,
  ts_rank(search_vector, query) AS rank
FROM articles,
  to_tsquery('english', 'database tutorial') query
WHERE search_vector @@ query
ORDER BY rank DESC;
```

**Problems:**

- ❌ No BM25 scoring
- ❌ No term frequency saturation
- ❌ No length normalization
- ❌ Performance degrades with corpus size
- ❌ Limited ranking signals

### pg_textsearch Extension

A modern PostgreSQL extension that implements BM25:

```sql
-- pg_textsearch (BM25 ranking)
CREATE EXTENSION IF NOT EXISTS pg_textsearch;

-- Create BM25 index
CREATE INDEX idx_articles_bm25 ON articles
USING bm25 (title, content)
WITH (
  text_fields = '{
    title: {tokenizer: {type: "english"}},
    content: {tokenizer: {type: "english"}}
  }'
);

-- Search with BM25 scoring
SELECT 
  id,
  title,
  pg_textsearch.score(id) AS bm25_score
FROM articles
WHERE pg_textsearch.search(
  'database tutorial',
  text_fields => '{title, content}'
)
ORDER BY bm25_score DESC
LIMIT 10;
```

### Performance Characteristics

| Corpus Size | Native PostgreSQL | pg_textsearch |
|-------------|-------------------|---------------|
| 10K rows | ~10ms | ~5ms |
| 100K rows | ~100ms | ~15ms |
| 1M rows | ~1000ms | ~50ms |
| 10M rows | ~10000ms | ~200ms |

### Memory Management

```sql
-- Set memory size for corpus
SET pg_textsearch.max_memory_size = '2GB';

-- Use score thresholds to filter low-relevance results
SELECT id, title, pg_textsearch.score(id) AS score
FROM articles
WHERE pg_textsearch.search('database tutorial')
  AND pg_textsearch.score(id) > 0.5  -- Filter threshold
ORDER BY score DESC;
```

### Hybrid Search with Vectors

Combine BM25 with vector search for best results:

```sql
-- Hybrid search: BM25 + vector similarity
SELECT 
  id,
  title,
  pg_textsearch.score(id) AS bm25_score,
  (embedding <=> query_embedding) AS vector_distance,
  (pg_textsearch.score(id) * 0.7 + 
   (1 - (embedding <=> query_embedding)) * 0.3) AS hybrid_score
FROM articles,
  (SELECT embedding FROM query_embeddings WHERE query_id = 1) q
WHERE pg_textsearch.search('database tutorial')
ORDER BY hybrid_score DESC
LIMIT 10;
```

**Benefits:**

- ✅ Single SQL query
- ✅ No external dependencies
- ✅ Low latency
- ✅ High relevance
- ✅ Real-time search

---

## Practical Applications

### 1. RAG (Retrieval-Augmented Generation)

```sql
-- Retrieve relevant context for LLM
SELECT 
  content,
  pg_textsearch.score(id) AS relevance
FROM articles
WHERE pg_textsearch.search('user question here')
ORDER BY relevance DESC
LIMIT 5;
```

**Use Case:** Providing accurate context to LLMs for question answering.

### 2. E-commerce Product Search

```sql
-- Search products with relevance ranking
SELECT 
  name,
  description,
  price,
  pg_textsearch.score(id) AS relevance
FROM products
WHERE pg_textsearch.search('wireless headphones noise cancelling')
ORDER BY relevance DESC
LIMIT 20;
```

**Use Case:** Finding products that match user intent, not just keywords.

### 3. Document Management

```sql
-- Search documents with content preview
SELECT 
  title,
  substring(content, 1, 200) AS preview,
  pg_textsearch.score(id) AS relevance
FROM documents
WHERE pg_textsearch.search('project proposal Q4')
ORDER BY relevance DESC
LIMIT 10;
```

**Use Case:** Finding relevant documents in large repositories.

### 4. Log Analysis

```sql
-- Search logs with error prioritization
SELECT 
  timestamp,
  level,
  message,
  pg_textsearch.score(id) AS relevance
FROM logs
WHERE pg_textsearch.search('database connection timeout')
  AND level = 'ERROR'
ORDER BY relevance DESC, timestamp DESC
LIMIT 50;
```

**Use Case:** Finding critical error logs quickly.

---

## Summary

### Key Takeaways

1. **BM25 is the modern standard** for relevance ranking in search engines
2. **Improves upon TF-IDF** with term frequency saturation and length normalization
3. **Essential for AI applications** that need high-quality context retrieval
4. **Available in PostgreSQL** through extensions like pg_textsearch
5. **Combines well with vector search** for hybrid approaches

### When to Use BM25

✅ **Use BM25 when:**
- You need relevance ranking
- Document lengths vary significantly
- Term frequency matters
- Building AI-native applications
- Implementing RAG systems
- Want consistent, high-quality results

❌ **Consider alternatives when:**
- Simple exact matching suffices
- All documents are similar length
- No need for ranking
- Extremely high throughput required (consider simpler approaches)

### The Future of Search

The emergence of AI has created a new paradigm where:

```
Keyword Search (BM25) + Vector Search = Hybrid Search
```

This hybrid approach provides:
- **Precision** from keyword matching
- **Semantic understanding** from vector similarity
- **High relevance** from BM25 ranking
- **Real-time performance** from PostgreSQL native implementation

> "The two approaches are deeply complementary: vectors capture conceptual similarity while keywords ensure exact terms aren't missed."

By implementing BM25 in PostgreSQL, developers can achieve enterprise-grade search without the complexity of external search engines, making it easier to build AI-native applications that deliver high-quality, relevant results.
