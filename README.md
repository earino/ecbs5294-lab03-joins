# Lab 3 — The revenue that grew when we joined

**ECBS5294 — Working with Data · Session 2, Block 3**

## Start here

| | |
|---|---|
| **The question** | What was revenue by payment type, and by customer state? Does every table add up to its own total? |
| **The files** | `data/raw/` — Olist's orders, items, payments, customers, and products, one CSV each. The report is `notebooks/report.ipynb`. |
| **What is wrong** | The report runs without an error, and its tables do not add up to its own total. |
| **What you hand in** | `DIAGNOSIS.md` on Moodle, before you leave. |
| **First thing to do** | Close every AI assistant: the first 20 minutes are AI-off. Then run the notebook top to bottom and read the numbers. |

## Get the project

If you cloned it during the stretch, you already have it. Otherwise, in your terminal (Git Bash on Windows,
Terminal on macOS), in the folder where you keep course work:

```bash
git clone https://github.com/earino/ecbs5294-lab03-joins.git
cd ecbs5294-lab03-joins
uv sync
```

Open **this folder** in VS Code (*File → Open Folder…*; trust the authors if asked), open `notebooks/report.ipynb`,
pick the `.venv` kernel, and run all cells. The first code cell prints the folder it is working in: it must be this
project's folder.

`notebooks/lecture.ipynb` holds the lecture's queries, as they were run in class, for review. This lab is
`notebooks/report.ipynb`.

## What is broken

Your colleague's report says total revenue was **364,110.54 BRL**. Under it are two breakdowns of revenue:

- by payment type, which adds up to **383,270.23 BRL** — more than the total;
- by product category, which adds up to **351,299.08 BRL** — less than the total.

A breakdown of a total must add up to that total. Nothing errors. Find out why, before you change anything.

**Today's core is the table that comes out above the total.** The one below is the stretch.

## The brief

The board pack uses two measures of revenue. Each table breaks down one of them.

| Measure | Where it is | Its total | The tables that break it down |
|---|---|---|---|
| What the items sold for | `price` in `order_items` | `SUM(price)` over `order_items` | revenue by product category |
| What customers paid | `payment_value` in `order_payments` | `SUM(payment_value)` over `order_payments` | revenue by payment type; revenue by customer state |

Revenue by payment type is a breakdown of what customers paid. An item price has no payment type of its own, so the
board pack never breaks item prices down by payment type.

The two totals are different numbers. Section E, after the note if there is time, reports the difference as a number.
It does not have to explain it.

**Money matches when it agrees to the cent:** `abs(a - b) < 0.005`. Never compare money with `==`. Sums of decimal
amounts are computed in floating point and are rarely exact in their last digits: in this file, three items at 26.90
add up to `80.69999999999999`. Counts are whole numbers, and must be exactly equal.

The files, one table each:

| File | What it holds |
|---|---|
| `orders.csv` | the orders: which customer, when, the order's status |
| `order_items.csv` | the items in each order: product, seller, price, freight |
| `order_payments.csv` | the payments for each order: type, installments, value |
| `customers.csv` | the customer of each order: city, state |
| `products.csv` | the products: category name (in Portuguese), size, weight |
| `product_category_name_translation.csv` | category names, Portuguese to English |
| `sellers.csv` | the sellers (not needed today) |

This sample was selected so that the situations this lab is about are present. It is not a representative sample of
Olist (`DATA.md` says how it was drawn).

## What you must produce

Work in the notebook, under **Your work starts here**, top to bottom:

1. **Diagnose** (section A). The three counts for the join in the report's section 2. The orders that the join
   repeats, and one of them in `order_payments`. Where `payment_type` lives, and which of the lecture's two patterns
   revenue by payment type is. Paste what you found into `DIAGNOSIS.md`, part 3, **before you change anything**.
2. **Revenue by payment type** (section B). Write this query yourself, from the empty cell.
3. **Revenue by customer state** (section C). For each state: the number of orders and the revenue. Then the three
   counts for your join.
4. **The check** (section D). Your tables B and C each against the payments total, to the cent; your orders against
   the orders that were paid for.
5. **`DIAGNOSIS.md`, all five parts, short. Start it by minute 22**, finished or not: it needs five minutes. Then the
   last ten minutes, below, and the Moodle checkpoint.

**After the note, if there is time:** section E, the two totals side by side and the difference between them, as a
number. Sections F and G are the stretch.

## Rules

- **The first 20 minutes are AI-off.** Chat windows and assistant panels closed; VS Code stays open. Search engines
  are fine. Why: counting the rows before and after a join has to become a reflex, and a reflex comes from doing it,
  not from asking for it. After minute 20, the course's usual AI policy applies.
- **Never edit `data/raw/`.** The raw data is the evidence. Fix the queries.
- **Fix the cause, with a query.** Changing one word until the number looks right is not a fix. Each table must be
  built from the right table, at the right grain, and the check in section D must pass.
- You must be able to explain every line you hand in: your neighbour will ask, at minute 33, without notes.

## Hints, if stuck

Staff will say these over the room at minutes 5, 10, and 15. Read them earlier if you want.

1. Count the rows of `order_items`. Now count the rows of the join in section 2.
2. Which `order_id` appears more than once in the result, and how many payment rows does it have?
3. Every item is being counted once per payment row. Which table does `payment_type` live on — and does revenue by
   payment type need `order_items` at all?

## Diagnosis note

In `DIAGNOSIS.md`: the template is there. Part 3 is the three counts and the order the join repeats. Part 5 is the
check from section D: paste its output.

## Stretch task

Two parts, in order, after section E. They are sections F and G of the notebook.

1. **The category table.** It adds up to less than the total. Find the items its join lost, with an anti-join: each
   lost category, its number of items, and their revenue. Then write a labelled table that keeps every item, names
   the ones with no English category name, and adds up to the items total, to the cent. Changing one word of
   section 3 is not the repair: the board pack has to say which items have no English name.
2. **Is the difference freight?** For each order that has both items and payments, compare price plus freight with
   what was paid. How many orders agree to the cent (`abs(residual) < 0.005`)? How many do not? What about the orders
   on only one side?

## Git thread

Commit after section B works, and again after section C. **Any commit that changes a join carries its three counts
in the message.** A second `-m` becomes a second paragraph of the message; the `\` at the end of a line
continues the command on the next. The lecture's example:

```bash
git add notebooks/report.ipynb
git commit -m "Items per order by state: count items per order first" \
           -m "order_size 2,610 rows -> joined 2,610 rows, 2,610 distinct orders"
```

Yours carries your own join's three counts.

## The last ten minutes

At minute 33, finished or not, turn to the person next to you (three if the row is odd). One of you explains, about a
minute: what was wrong, why, the query that proved it, what you changed, how you know it is right. Point at the
screen; do not read the note. The other asks:

1. **Show me the query that proves it.**
2. **Why was it wrong, not just where?**
3. **The what-if question on the slide.**

Then swap. If either of you is unsure, or you disagree, put a hand up: staff come to you first. Then the answer to
the what-if, for everyone. An unfinished repair is explained the same way: what you found so far.

Before you leave: the lab's **checkpoint on Moodle**. Upload `DIAGNOSIS.md` with its first line filled in. That is
what "complete" means; nobody signs you off.

## If you got lost: how to reset

Both of these **destroy work**. Read before running.

**Discard uncommitted changes (destructive)** — throw away edits and new files; keep your commits:

```bash
git restore --staged --worktree .    # every tracked file back to the last commit, staged or not
git clean -fd                        # and remove new, untracked files
```

> ⚠️ Permanently deletes uncommitted changes, staged or not, and any new untracked files.

**Full reset to the starter state (destructive)** — back to exactly what you cloned; throws away your commits too:

```bash
git reset --hard origin/main
git clean -fdx
```

> ⚠️ Discards your local commits and uncommitted changes. The `-x` also removes ignored files — the `.venv/`
> environment among them — so the folder matches a fresh clone. `uv sync` rebuilds the environment in a minute.
