## pg_trgm Extension

### Setup

Aktifkan extension:

```sql
CREATE EXTENSION IF NOT EXISTS pg_trgm;
```

Buat tabel contoh:

```sql
CREATE TABLE products (
  id SERIAL PRIMARY KEY,
  name TEXT
);
```

Buat GIN trigram index:

```sql
CREATE INDEX idx_products_name_trgm
ON products
USING GIN (name gin_trgm_ops);
```

### 1. Typo Search (Fuzzy Search)

Contoh: user salah ketik "postgress" (seharusnya "postgres")

**Menggunakan fungsi `similarity()`:**

```sql
SELECT name,
       similarity(name, 'postgress') AS sim
FROM products
WHERE similarity(name, 'postgress') > 0.3
ORDER BY sim DESC
LIMIT 10;
```

**Menggunakan operator `%`:**

```sql
SELECT name
FROM products
WHERE name % 'postgress'
ORDER BY similarity(name, 'postgress') DESC;
```

**Atur threshold similarity:**

```sql
SET pg_trgm.similarity_threshold = 0.3;
```

### 2. Autocomplete

Contoh: user mengetik "pos"

```sql
SELECT name
FROM products
WHERE name ILIKE 'pos%'
ORDER BY name
LIMIT 10;
```

Dengan trigram index, pencarian tetap cepat meskipun menggunakan `ILIKE`.

### 3. Autocomplete + Ranking Similarity

Lebih pintar dengan menggabungkan autocomplete dan similarity ranking:

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

Update `search_vector` secara otomatis dengan trigger:

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

1. Gunakan `EXPLAIN ANALYZE` untuk memeriksa query plan
2. Pastikan index yang tepat dibuat berdasarkan pola query
3. Pertimbangkan materialized view untuk query kompleks yang sering dijalankan
4. Monitor ukuran index dan reindex jika diperlukan
