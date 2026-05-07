# PostgreSQL pg_search Extension

## Overview

`pg_search` is a Rust-based PostgreSQL extension that significantly enhances PostgreSQL's native full-text search capabilities. Named after the BM25 algorithm—the same ranking algorithm used by modern search engines like Elasticsearch—`pg_search` bridges the gap between PostgreSQL's built-in search functionality and specialized search engines.

---

## Table of Contents

1. [Problems with Native PostgreSQL FTS](#problems-with-native-postgresql-fts)
2. [Key Features](#key-features)
3. [How It Works](#how-it-works)
4. [Performance Comparison](#performance-comparison)
5. [When to Use pg_search](#when-to-use-pg_search)

---

## Problems with Native PostgreSQL FTS

PostgreSQL's native full-text search, which uses the `tsvector` type, has two significant limitations:

### 1. Performance Issues

- **Slow on large datasets**: Searching and ranking over tables with millions of rows can be sluggish
- **Long query times**: A single full-text search can take several minutes on large tables
- **Inefficient ranking**: The built-in `ts_rank()` function doesn't scale well

### 2. Limited Functionality

Missing features that are standard in modern search engines:

- ❌ No fuzzy search capability
- ❌ No relevance tuning options
- ❌ No BM25 relevance scoring
- ❌ Limited aggregation support
- ❌ No highlighting functionality

---

## Key Features

`pg_search` addresses these limitations with the following features:

| Feature | Description |
|---------|-------------|
| **100% Native** | Zero dependencies on external search engines |
| **Built on Tantivy** | Rust-based alternative to Apache Lucene |
| **20x Faster** | Query times over 1M rows compared to `tsquery` and `ts_rank` |
| **Fuzzy Search** | Support for typo-tolerant searches |
| **BM25 Scoring** | Same relevance algorithm as Elasticsearch |
| **Real-time Search** | New data is immediately searchable |
| **Aggregations** | Built-in support for search aggregations |
| **Highlighting** | Automatic result highlighting |
| **Relevance Tuning** | Fine-tune search relevance scores |

### Architecture Highlights

```sql
-- pg_search introduces a new index type: BM25
CREATE INDEX idx_bm25 ON articles
USING bm25 (title, content)
WITH (key_field = 'id');
```

---

## How It Works

### Index Storage

`pg_search` stores the index inside PostgreSQL as a new, PostgreSQL-native index type called the **BM25 index**. This is made possible through PostgreSQL's index access method API.

### Automatic Updates

When a BM25 index is created:

1. PostgreSQL automatically updates it as new data arrives
2. Changes are reflected immediately when data is deleted
3. No manual reindexing is required
4. Enables true real-time search

### Integration Flow

```mermaid
graph LR
    A[Data Insert/Update] --> B[PostgreSQL Table]
    B --> C[BM25 Index]
    C --> D[Real-time Search]
    D --> E[Ranked Results]
```

---

## Performance Comparison

### Benchmark Results (1 Million Rows)

| Operation | PostgreSQL Native | pg_search | Elasticsearch |
|-----------|-------------------|-----------|---------------|
| **Indexing Time** | Baseline | **50s faster** | Similar |
| **Query Time** | Baseline | **20x faster** | Similar |
| **Ranking** | Slow | Fast | Fast |

### Key Performance Metrics

- **Indexing**: 50 seconds faster than `tsvector`
- **Query Speed**: 20x faster than built-in `tsquery` and `ts_rank`
- **Scalability**: Nearly identical performance to dedicated Elasticsearch
- **Future Goals**: Aiming for 2x faster than Elasticsearch with further optimizations

> 📊 **Detailed benchmarks**: [View full benchmark results](https://github.com/paradedb/paradedb/blob/dev/benchmarks/README.md)

---

## When to Use pg_search

### Ideal Use Cases

✅ **Large datasets** (millions of rows)  
✅ **Need for fuzzy search**  
✅ **Relevance tuning requirements**  
✅ **Real-time search critical**  
✅ **Want to avoid external search engines**  
✅ **BM25 scoring needed**  

### Migration Path

From PostgreSQL Native FTS:

```sql
-- Before: Native GIN index
CREATE INDEX idx_gin ON articles
USING GIN (to_tsvector('english', content));

-- After: pg_search BM25 index
CREATE INDEX idx_bm25 ON articles
USING bm25 (content)
WITH (text_fields = '{content: {tokenizer: {type: "english"}}}');
```

---

## Summary

`pg_search` eliminates the need to bring a cumbersome service like Elasticsearch into your data stack by providing:

- 🚀 **Performance**: 20x faster queries on large datasets
- 🎯 **Features**: Modern search capabilities (fuzzy, BM25, highlighting)
- 🔧 **Simplicity**: 100% native PostgreSQL, no external dependencies
- ⚡ **Real-time**: Immediate searchability without reindexing

Perfect for applications that need enterprise-grade search without the complexity of managing separate search infrastructure.
