## Full-Text Search (FTS)

Complete guide to implementing Full-Text Search (FTS) for various search use cases.

### Setup

Create a table for the example:

```sql
CREATE TABLE articles (
  id SERIAL PRIMARY KEY,
  title TEXT,
  content TEXT,
  search_vector tsvector
);
```

Populate `search_vector` with data:

```sql
UPDATE articles
SET search_vector =
    to_tsvector('indonesian', coalesce(title,'') || ' ' || coalesce(content,''));
```

Create a GIN index for performance:

```sql
CREATE INDEX idx_articles_search
ON articles
USING GIN (search_vector);
```

### 1. Search Long Articles

Example: searching for articles about "postgres cache fast"

```sql
SELECT id, title
FROM articles
WHERE search_vector @@ plainto_tsquery('indonesian', 'postgres cache cepat');
```

**Why use `plainto_tsquery`?**
- Safe for user input
- No need to manually use `&` or `|` operators
- Automatically handles search logic

If you want manual AND logic:

```sql
WHERE search_vector @@ to_tsquery('indonesian', 'postgres & cache')
```

### 2. Search with Ranking

FTS has built-in ranking functions: `ts_rank()` or `ts_rank_cd()`.

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

The larger the `rank` value, the more relevant the search result.

### 3. Advanced: Boost Title

If you want to give higher priority to the title than the content:

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

Ranking will automatically prioritize words that appear in the title.

---

### Maintenance

Update `search_vector` automatically with a trigger:

```sql
CREATE OR REPLACE FUNCTION articles_search_vector_update() RETURNS trigger AS $$
BEGIN
  NEW.search_vector :=
    setweight(to_tsvector('indonesian', coalesce(NEW.title,'')), 'A') ||
    setweight(to_tsvector('indonesian', coalesce(NEW.content,'')), 'B');
  RETURN NEW;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER articles_search_vector_trigger
BEFORE INSERT OR UPDATE ON articles
FOR EACH ROW
EXECUTE FUNCTION articles_search_vector_update();
```

### Performance Tips

1. Use `EXPLAIN ANALYZE` to check the query plan
2. Make sure the appropriate index is created based on your query pattern
3. Consider a materialized view for complex queries that are executed frequently
4. Monitor index size and reindex if necessary
