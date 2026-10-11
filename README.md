# Lab 3 — The category table that came out short

**ECBS5294 — Working with Data · Session 2, Block 3**

## Start here

| | |
|---|---|
| **The question** | What were item sales by product category, with the category names in English? Does the table add up to total item sales? |
| **The files** | `data/raw/`: Olist's orders, items, customers, products and category names, one CSV each. The report is `notebooks/report.ipynb`. |
| **What is wrong** | In section D of the notebook, a colleague's report runs without an error, and its category table adds up to less than its own total. |
| **What you hand in** | The notebook, on the lab's Moodle checkpoint, before you leave: the keyboard uploads it; everyone else pastes its *Your team* line as their submission text. Nothing else. |
| **First thing to do** | Decide who is keyboard, who is AI and who is checker, and write your names in the notebook's *Your team* cell. Then the keyboard runs the three setup cells, and the team starts at section A. |

The syntax you need is on one page: `REFERENCE-sql.md`. The full reference, with a task index in plain English, is
on the course site: https://earino.github.io/ecbs5294-2026/site/reference.html

## Get the project

The keyboard person cloned it during the stretch. Otherwise, in your terminal (Git Bash on Windows, Terminal on
macOS), in the folder where you keep course work:

```bash
git clone https://github.com/earino/ecbs5294-lab03-joins.git
cd ecbs5294-lab03-joins
uv sync
```

Open **this folder** in VS Code (*File → Open Folder…*; trust the authors if asked), open `notebooks/report.ipynb`,
pick the `.venv` kernel, write your names in the *Your team* cell, and run the first three code cells. The first
prints the folder it is working in: it must be this project's folder. The second makes each file a table you can name; the third gets the checks ready. Then work top to bottom, running each cell as you
come to it. (After a kernel restart, run the cells again from the top down to the one you had reached, and no
further: *Run All* is for the finished notebook. If the clone or the sync failed, put a hand up at the start of the lab: a TA comes to you first. If you ran everything by accident: *Clear All Outputs*, restart the kernel, run the three setup cells, and
start again at section A; nothing is lost but the surprise. If a cell went missing, put a hand up: staff help you get it
back without losing your work.)

`notebooks/lecture.ipynb` holds the lecture's queries, as they were shown in class, for review. This lab is
`notebooks/report.ipynb`.

## How your team works

Three people, one team for the whole day, **one work laptop**. Teams are the people sitting next to you: three in
a row. If your row leaves two people, stay two; if it leaves one, join the team beside you. The work laptop is the
keyboard person's, so when the roles change for the second lab, a different laptop is the work laptop. Each of you
holds one role for this lab:

| Role | What you do |
|---|---|
| **Keyboard** | You type on the work laptop, and only what the team has agreed. Say what a cell will do before you run it. |
| **AI** | You may use an AI assistant on your own device. Nobody else sees that screen, and you never type on the work laptop: what the assistant says reaches the team by your voice. **For the first five minutes you share nothing from it.** Your own ideas you share from the first minute. |
| **Checker** | Before a query runs, you say which number must stay the same, go up or go down, and why. After it runs, you compare. When a check prints, you read its lines out, and you say what one row of the table is. |

- **Say it before you type it.** Where the notebook asks for a query or a blank, the two of you who are not at the
  keyboard say what goes there, out loud; then the keyboard types what the team agrees on. Nothing is written on paper.
- **What works in the AI role:** agree the question with the team before you ask it; tell the assistant what one row
  of each table is; ask it to explain, or to suggest a check, rather than to write the query; and say what it said in
  your own words. It cannot see the data: the team tests whatever it says.
- **In the second lab of the day you take a role you have not had today.** The simple way: keyboard becomes checker,
  checker becomes AI, AI becomes keyboard.
- A team of two has no separate checker: the AI person checks. In the second lab the two of you swap. A team of four
  has two checkers.
- **The only writing is in the notebook:** everyone's name and role in its first cell, your queries, one comment on
  what one row is, one comment on why the report was wrong. The checks print the rest. There is no note to keep.
- Nobody is graded as a team. The homework, the knowledge check and the exam are yours alone: make sure you could
  write every query in this notebook yourself.

## What is broken

In sections A, B and C you write queries that work, and a table that adds up. Section D is a colleague's report. It
says total item sales were **364,110.54 BRL**, and under that it has a table of item sales by product category, with
the category names in English. The table adds up to **351,299.08 BRL**.

A breakdown of a total must add up to that total. Nothing errors. Find out why, before you change anything.

## The files

One table each, in `data/raw/`:

| File | One row is | Columns this lab uses |
|---|---|---|
| `order_items.csv` | one item of an order | `order_id`, `order_item_id`, `product_id`, `price` |
| `products.csv` | one product | `product_id`, `product_category_name` (in Portuguese) |
| `product_category_name_translation.csv` | one category name | `product_category_name`, `product_category_name_english` |
| `orders.csv` | one order | `order_id`, `order_status` |
| `customers.csv` | the customer of one order (the lecture and section G use it) | |

This sample was selected so that the situations this lab is about are present. It is not a representative sample of
Olist (`DATA.md` says how it was drawn).

## What you must produce

Work in the notebook, under **Your work starts here**, top to bottom:

1. **Look first** (section A). Five rows of each of the two tables you will join (supplied), and the column they
   share; then the number of items and what they add up to. Say each query out loud before the keyboard types it.
2. **Your first join** (section B). Every item with its product's category name, written by you from an empty cell,
   as a view. Its check is written for you: three lines must say `PASS`. Then one comment: what is one row of it?
3. **What does this marketplace sell most?** (section C). Item sales by category name, from your view, written by
   you. Guess the answer first. Its check is written for you, and your table adds up.
4. **Your colleague's report** (section D). Run its two cells and read the two numbers out. Then write the report's join
   yourself and count what it kept. Then two cells that run as they are, for you to finish. The first lists
   the items that found no partner, by category name, once you add two lines to it: the join, and the `WHERE`. Its
   own check is under it: all three lines must say `PASS`. The second is your colleague's query under a new name:
   change it so that the category table keeps every item, with the label `no English name` for the ones that have
   none. Its first line is a comment for you to finish: one sentence on why the report's table came out short.
5. **The check** (section E). It is supplied. Run it after every change to the cell above it. It prints the evidence,
   then five numbered lines. When the five say `PASS`, your table has passed every check written here. A check tests
   only what it was written to test: before you call it done, say what one row of your table is, and read its rows.
6. **The last ten minutes**, below, at minute 33, finished or not. Then, before you leave, **the checkpoint**: the
   keyboard saves the notebook and uploads it to Moodle; everyone else pastes the *Your team* line as their
   submission text.

Sections F and G are for after section E passes, if there is time: choose either.

## Rules

- **For the first five minutes, nothing from an AI assistant reaches the team.** The team meets the problem with its
  own heads first. After that, the AI person shares what they like, by voice: they never type on the work laptop,
  and nobody else sees their screen.
- **Never edit `data/raw/`.** The raw data is the evidence. Fix the queries.
- **Money is compared to the cent, never with `==`:** `abs(a - b) < 0.005`. Sums of decimal amounts are computed in
  floating point: in this file the three items of one order, 26.90 each, add up to `80.69999999999999`. Counts are
  whole numbers and must be exactly equal. Round when you display, never while you are adding up (Block 2): the views keep every decimal.
- Each of you must be able to explain every line your team hands in: at minute 33 the team next to you will ask
  why it was wrong.

## Hints, if stuck

Each hint is for the section it names. Staff point the room to them at minutes 5, 10 and 15; read one when your
team is in its section and stuck, not before.

**At minute 18, a team not yet in section D puts a hand up.** Staff help you say your query and type it: B and C
are your own join and what it found, and nobody skips them.

1. Section B: `order_items` and `products` share one column, and that is the `ON`. Lecture cell 1 has the shape:
   `FROM` one table `AS` a short name, `JOIN` the other `AS` a short name, `ON` the two columns that must be equal.
2. Section D, the report's join. Your table in C adds up; the report's does not, and it is your join plus one more `JOIN` line. Count what that
   join keeps: the rows, and the distinct items. Which kind of join keeps a row that found no partner? Lecture cell 7.
3. Section D, the items with no partner. Some category names have no row in the translation table, and some products have no category name at all, so
   `JOIN` leaves their items out. Which join keeps them, and what is in the translation table's columns for those
   rows? Lecture cells 7 to 9 are the shape; say your two lines out loud before you look.

## If there is time

After section E passes, choose either.

- **Section F:** the same table with the two kinds of missing name apart, so that an item whose category has no translation is
  told apart from an item whose product has no category at all. Its check is written for you.
- **Your own question** (section G): three questions no single table can answer, from what one state buys
  to what costs most. Pick one, guess, write it, and show your answer to the team next to you. Nothing checks it.

## The last ten minutes

At minute 33, finished or not, your team turns to **the team next to you**. Two teams, one laptop at a time. If no
team is left to pair with, join the pair beside you.

At your laptop, finished or not, start with one result you found interesting; then the last step you reached. If
you repaired the report, show why it was wrong and what the check printed. About two minutes, pointing at the screen.
The other team asks one question. If you reached the report: **why was it wrong, not just where?** If not: **what is
one row of what you showed?** Then swap: their laptop, their turn.

Then **the what-if question on the slide**, both teams together: commit to an answer before anyone runs a query; you
may run it after you have committed. If anyone is unsure, or the two teams disagree, put a hand up: staff come to
you first. Then the answer to the what-if, for everyone, and what happened in the report.

**The lab ends in the room, finished or not.** Before you leave: the lab's **checkpoint on Moodle**. The keyboard
saves the notebook and uploads `notebooks/report.ipynb` as it is; everyone else pastes the *Your team* line (the
names and roles) as their submission text. That is what "complete" means; nobody signs you off, and there is nothing
to finish at home. The solved notebook is on Moodle two days later.

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
