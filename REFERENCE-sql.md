# SQL reference — one page

Everything a lab in this course asks you to write, in the order it was taught: each line is on a slide of the block
named beside it, or in the lab named beside it, and a block's section is for that block on (a sheet can carry a block
you have not had yet). The examples read the files of the project they were taught in (Lab 1's `owid_co2.csv`, Lab
2's `online_retail.parquet`, Lab 3's Olist files) and the views those labs make (`countries`, `sales_2010`); to run
one in another project, change the file, the key columns and the column you count. Keep it open in a second tab. Paths are always `'data/raw/<file>'`
from the project folder, and every query is `con.sql("""...""").df()`; a single number is
`con.sql("...").fetchone()[0]`. The full reference, with a task index in plain English ("I want the first
character of a code…"), joins, JSON, pipelines and checks, is on the course site:
https://earino.github.io/ecbs5294-2026/site/reference.html
## Block 1 — one table, one row at a time

**The four inspection recipes** (on Lab 1's file; in another project change the file, the key columns in the third, and the column counted in the fourth; the grammar is Block 2's).

```sql
DESCRIBE SELECT * FROM 'data/raw/owid_co2.csv';                      -- one row per column: its name and type
SUMMARIZE SELECT * FROM 'data/raw/owid_co2.csv';                      -- min, max, nulls, distinct, per column
SELECT COUNT(*), COUNT(DISTINCT (country, year)) FROM 'data/raw/owid_co2.csv';   -- rows against distinct keys
SELECT country, COUNT(*) AS n FROM 'data/raw/owid_co2.csv'
GROUP BY country ORDER BY n DESC;                                     -- the census: one row per value, read to the end
```

**The `SELECT` and its clauses.** They go in this order and no other.

```sql
SELECT country, co2              -- the columns you want, or * for all
FROM 'data/raw/owid_co2.csv'     -- the file
WHERE year = 2024                -- keep the rows where this is true
ORDER BY co2 DESC                -- DESC largest first; ASC (the default) smallest first
LIMIT 10;                        -- the first n rows of that order
```

**Comparisons in `WHERE`.** `=`, `!=`, `<`, `<=`, `>`, `>=`; text in single quotes, exactly as the file spells it
(`'International aviation'`, not `'international aviation'`). `IN ('A', 'B')` is "any of these".

**Missing values.** A missing value is `NULL`. It is not a value: `= NULL` and `!= NULL` are never true, so a
`WHERE` with either keeps nothing and says nothing. The only tests are `IS NULL` and `IS NOT NULL`.
pandas prints a `NULL` as `NaN` or `None`, neither of which is in the file: do not put those words in a query.

**Two conditions.** `AND` keeps a row when both are true; `OR` when either is.

```sql
WHERE year = 2024 AND country = 'World'
WHERE Description != 'Manual' OR Description IS NULL
```

**A view: a saved query with a name.** Write the filter once, read from it everywhere. It is not a copy of the
data; it re-runs its query each time it is read.

```sql
CREATE OR REPLACE VIEW products AS
SELECT * FROM 'data/raw/online_retail.parquet'
WHERE StockCode NOT IN ('POST', 'DOT', 'C2', 'BANK CHARGES');
-- then:  SELECT COUNT(*) FROM products;
```

**One number into Python.** `.fetchone()` returns the first row of the result; `[0]` takes its first column.

```python
world = con.sql("SELECT co2 FROM 'data/raw/owid_co2.csv' WHERE year = 2024 AND country = 'World'").fetchone()[0]
```

## Block 2 — one table, groups

**Aggregates** collapse many rows into one number. `COUNT(*)` counts rows; `COUNT(col)` counts the rows where
`col` is not `NULL`; `COUNT(DISTINCT col)` counts its different values. `SUM`, `AVG`, `MIN`, `MAX` skip `NULL`.
An average over a column with gaps is the average of the rows that have a value. (`countries`, below, is the view
you built in Lab 1.)


```sql
SELECT COUNT(*) AS n_rows, COUNT(co2) AS n_co2, COUNT(DISTINCT country) AS n_countries,
       SUM(co2) AS total, AVG(co2) AS mean, MIN(co2), MAX(co2)
FROM countries WHERE year = 2024;
```

**`GROUP BY`**: one output row per distinct value of the grouped columns. Every column in the `SELECT` that is
not inside an aggregate must be in the `GROUP BY`. A `NULL` in the grouped column is a group of its own.

```sql
SELECT year, SUM(co2) AS total
FROM countries
GROUP BY year
ORDER BY year;
```

**The order a query runs**: `FROM` → `WHERE` → `GROUP BY` → aggregates in `SELECT` → `ORDER BY` → `LIMIT`.
`WHERE` runs before any group exists, so it cannot test a count. A name you make with `AS` is born in
`SELECT`; this course repeats the expression in `WHERE` rather than the name.

**Calculated columns and aliases.** `Quantity * Price AS revenue`. `ROUND(x, 2)` is for display: round when you
print, never while you are still adding up.

**`COALESCE(col, 0)`**: the first of its values that is not `NULL`.

**Dates.** `year(InvoiceDate)`, `month(InvoiceDate)`, `date_trunc('month', InvoiceDate)` (the first day of that
month, so months group and sort correctly).

```sql
SELECT date_trunc('month', InvoiceDate) AS month, SUM(Quantity * Price) AS revenue
FROM sales_2010
GROUP BY month
ORDER BY month;
```

**Text.** `left(Invoice, 1)` is the first character.

**A query inside a query.** The inner `SELECT` makes a table; the outer one reads it.

```sql
SELECT SUM(revenue) FROM (
    SELECT year, SUM(co2) AS revenue FROM countries GROUP BY year
);
```

**Comparing two sums.** Never `==`: two correct sums of the same rows can differ in the last decimal.
`abs(a - b) < 0.005` is "equal to the penny"; the identity is that a breakdown adds up to its total, so the two
numbers tie out.
## Block 3 — two tables: how to join

**A join** puts columns from two tables in one row, matched on a key. Give each table a short name (`AS o`) and
say which table every column comes from (`o.order_id`).

```sql
SELECT o.order_id, o.order_status, c.customer_state
FROM orders AS o
JOIN customers AS c ON c.customer_id = o.customer_id;
```

**After every join, say what one row is now, and count:** the rows that went in, the rows that came out, and the
distinct keys that came out. With `JOIN`, all three equal means every row found exactly one partner.

```sql
SELECT (SELECT COUNT(*) FROM orders) AS rows_in,
       COUNT(*)                      AS rows_out,
       COUNT(DISTINCT o.order_id)    AS distinct_orders_out
FROM orders AS o
JOIN customers AS c ON c.customer_id = o.customer_id;
```

A key made of two columns is counted as a pair: `COUNT(DISTINCT (order_id, order_item_id))` (DuckDB's spelling; other
databases write it differently).

**Join, then group.** The result of a join is a table; `GROUP BY` works on it as on any other.

```sql
SELECT c.customer_state, COUNT(*) AS orders
FROM orders AS o
JOIN customers AS c ON c.customer_id = o.customer_id
GROUP BY c.customer_state
ORDER BY orders DESC;
```

**Two kinds of join.** `JOIN` (its full name is `INNER JOIN`) keeps only the rows that found a partner. `LEFT JOIN`
keeps every row of the table on the left; where a row has no partner, the right table's columns are `NULL`. So with
`LEFT JOIN`, three equal counts say only that every left row is there once: they do not say it found a partner.

```sql
SELECT o.order_id, i.price
FROM orders AS o
LEFT JOIN order_items AS i ON i.order_id = o.order_id;
```

**The rows with no partner:** a `LEFT JOIN`, then keep the rows whose right side is `NULL`. Test the side that may
be missing, the right table's key: it is `NULL` exactly when the row found no partner (a left-side key that is
itself `NULL` finds no partner either, and the right side is `NULL` for it too).

```sql
SELECT o.order_id, o.order_status
FROM orders AS o
LEFT JOIN order_items AS i ON i.order_id = o.order_id
WHERE i.order_id IS NULL;
```

**Joining texts** (Lab 3, section F; not on a slide). `'a' || 'b'` is `'ab'`; a text joined to `NULL` is `NULL`,
so `COALESCE(t.name_english, 'untranslated: ' || c.name, 'no category')` labels three kinds of row apart.

## Block 4 — when one row has several partners

**After `orders JOIN order_items`, one row of the result is one item, not one order**, because an order can have
several items. So `COUNT(*)` counts items and `AVG` averages over items, whatever the column is called.

**`HAVING`**: a `WHERE` on groups. It runs after `GROUP BY`, so it can test a count. This finds the keys that repeat.

```sql
SELECT order_id, COUNT(*) AS n FROM order_items
GROUP BY order_id
HAVING COUNT(*) > 1;
```

**One row per order first, then join.** `WITH name AS (...)` names a query you write first and read below.

```sql
WITH order_size AS (
    SELECT order_id, COUNT(*) AS n_items
    FROM order_items
    GROUP BY order_id
)
SELECT c.customer_state, COUNT(*) AS orders, AVG(s.n_items) AS items_per_order
FROM order_size AS s
JOIN orders AS o    ON o.order_id = s.order_id
JOIN customers AS c ON c.customer_id = o.customer_id
GROUP BY c.customer_state;
```
