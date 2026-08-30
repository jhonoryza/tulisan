## pg_trgm Extension

### Setup

Enable the extension:

```sql
CREATE EXTENSION IF NOT EXISTS pg_trgm;
```

Create an example table:

```sql
CREATE TABLE products (
  id SERIAL PRIMARY KEY,
  name TEXT
);
```

Create a GIN trigram index:

```sql
CREATE INDEX idx_products_name_trgm
ON products
USING GIN (name gin_trgm_ops);
```

### 1. Typo Search (Fuzzy Search)

Example: user mistypes "postgress" (should be "postgres")

**Using the `similarity()` function:**

```sql
SELECT name,
       similarity(name, 'postgress') AS sim
FROM products
WHERE similarity(name, 'postgress') > 0.3
ORDER BY sim DESC
LIMIT 10;
```

**Using the `%` operator:**

```sql
SELECT name
FROM products
WHERE name % 'postgress'
ORDER BY similarity(name, 'postgress') DESC;
```

**Set the similarity threshold:**

```sql
SET pg_trgm.similarity_threshold = 0.3;
```

### 2. Autocomplete

Example: user types "pos"

```sql
SELECT name
FROM products
WHERE name ILIKE 'pos%'
ORDER BY name
LIMIT 10;
```

With a trigram index, the search remains fast even when using `ILIKE`.

### 3. Autocomplete + Similarity Ranking

Smarter by combining autocomplete and similarity ranking:

```sql
SELECT name,
       similarity(name, 'pos') AS sim
FROM products
WHERE name ILIKE 'pos%'
ORDER BY sim DESC
LIMIT 10;
```

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
