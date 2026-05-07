## Full-Text Search (FTS)

Panduan lengkap implementasi Full-Text Search (FTS) untuk berbagai use case pencarian.

### Setup

Buat tabel untuk contoh:

```sql
CREATE TABLE articles (
  id SERIAL PRIMARY KEY,
  title TEXT,
  content TEXT,
  search_vector tsvector
);
```

Populate `search_vector` dengan data:

```sql
UPDATE articles
SET search_vector =
    to_tsvector('indonesian', coalesce(title,'') || ' ' || coalesce(content,''));
```

Buat GIN index untuk performa:

```sql
CREATE INDEX idx_articles_search
ON articles
USING GIN (search_vector);
```

### 1. Search Artikel Panjang

Contoh: mencari artikel tentang "postgres cache cepat"

```sql
SELECT id, title
FROM articles
WHERE search_vector @@ plainto_tsquery('indonesian', 'postgres cache cepat');
```

**Kenapa pakai `plainto_tsquery`?**
- Aman untuk input user
- Tidak perlu manual pakai operator `&` atau `|`
- Otomatis menangani logika pencarian

Kalau mau AND logic manual:

```sql
WHERE search_vector @@ to_tsquery('indonesian', 'postgres & cache')
```

### 2. Search dengan Ranking

FTS memiliki fungsi ranking bawaan: `ts_rank()` atau `ts_rank_cd()`.

```sql
SELECT
  id,
  title,
  ts_rank(search_vector,
          plainto_tsquery('indonesian', 'postgres cache cepat')
  ) AS rank
FROM articles
WHERE search_vector @@ plainto_tsquery('indonesian', 'postgres cache cepat')
ORDER BY rank DESC
LIMIT 10;
```

Semakin besar nilai `rank`, semakin relevan hasil pencarian.

### 3. Advanced: Boost Title

Jika ingin memberikan prioritas lebih tinggi pada title daripada content:

```sql
UPDATE articles
SET search_vector =
      setweight(to_tsvector('indonesian', coalesce(title,'')), 'A') ||
      setweight(to_tsvector('indonesian', coalesce(content,'')), 'B');
```

**Weight levels:**
- `A` - Highest priority
- `B` - High priority
- `C` - Medium priority
- `D` - Low priority

Ranking otomatis akan memprioritaskan kata-kata yang muncul di title.

---
