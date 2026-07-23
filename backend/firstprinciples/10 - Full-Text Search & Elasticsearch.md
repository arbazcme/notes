# LEC 10 - Full-Text Search & Elasticsearch

# The Problem

A relational database can search text using queries like

```sql
SELECT *
FROM products
WHERE name ILIKE '%laptop%'
   OR description ILIKE '%laptop%';
```

This works well for small datasets.

As data grows to millions of rows,

the search becomes slow because the database must scan large amounts of text.

---

# Why Normal Database Search Isn't Enough

Problems with simple text search

- Slow on large datasets
- No typo tolerance
- No relevance ranking
- Poor search experience

Example

Searching

```text
laptop
```

may return

```text
Laptop Bag
Gaming Laptop
Laptop Sticker
MacBook Pro
```

in no meaningful order.

Users expect

```text
MacBook Pro
Gaming Laptop
Laptop Bag
```

because they are more relevant.

---

# What is Full-Text Search?

Full-text search is a search system optimized for searching text.

Goals

- Very fast
- Relevance ranking
- Typo tolerance
- Autocomplete
- Better user experience

---

# What is Elasticsearch?

Elasticsearch is a distributed search engine built specifically for full-text search.

It is built on top of

**Apache Lucene**.

Think of it as

```text
Database

↓

Stores Data

Elasticsearch

↓

Searches Data
```

Usually,

your database remains the source of truth,

while Elasticsearch maintains a searchable copy.

---

# Core Idea - Inverted Index

Instead of searching every document,

Elasticsearch builds an index beforehand.

Normal Search

```text
Document

↓

Find Word
```

Inverted Index

```text
Word

↓

Find Documents
```

Example

```text
machine

↓

Book A
Book C
Book D

learning

↓

Book A
Book E
```

Searching now becomes

```text
Word

↓

Direct Lookup

↓

Results
```

instead of scanning every document.

---

# Why It's Called an Inverted Index

Traditional database

```text
Document

↓

Words
```

Inverted index

```text
Word

↓

Documents
```

The lookup direction is reversed.

---

# Relevance Scoring

Not every matching document is equally useful.

Elasticsearch assigns a score to every result.

Documents with higher scores appear first.

---

# Factors Affecting Relevance

## 1. Term Frequency (TF)

How many times the search word appears.

Example

```text
Machine Learning

(30 occurrences)

↓

Higher Score
```

---

## 2. Document Frequency

How common the word is across all documents.

Rare words are generally more valuable.

---

## 3. Document Length

Very long documents are normalized so they don't dominate simply because they contain more words.

---

## 4. Field Boosting

Some fields matter more than others.

Example

```text
Title

↓

Highest Priority

Description

↓

Medium

Content

↓

Lowest
```

If the keyword appears in the title,

it receives a higher score.

---

# BM25

Elasticsearch uses the **BM25** ranking algorithm.

It combines

- Term frequency
- Document frequency
- Document length
- Field boosting

to rank search results.

For interviews,

know **what BM25 does**, not its mathematics.

---

# Typo Tolerance

Users often make spelling mistakes.

Example

```text
lapotp
```

should still return

```text
laptop
```

This is called

**Fuzzy Search**.

---

# Autocomplete

As the user types,

suggestions appear immediately.

Example

```text
lap

↓

Laptop

Laptop Stand

Laptop Bag
```

This is often called

**Typeahead Search**.

---

# Typical Architecture

```text
User

↓

Backend

↓

Database
(Source of Truth)

↓

Sync

↓

Elasticsearch

↓

Search Results
```

Application writes data to the database,

then synchronizes searchable fields to Elasticsearch.

---

# Elasticsearch Data Model

Instead of

Tables

↓

Rows

Elasticsearch uses

Indexes

↓

Documents (JSON)

Example

```json
{
  "title": "MacBook Pro",
  "description": "Apple Laptop"
}
```

---

# Text vs Keyword Fields

## Text

Used for searchable content.

Supports

- Tokenization
- Full-text search
- Relevance scoring

Examples

- Product title
- Description
- Article content

---

## Keyword

Used for exact matching.

Examples

- Category
- Country
- Status
- Tags
- IDs

---

# Common Use Cases

- Product search
- Blog search
- Documentation search
- Chat search
- User search
- Job portals
- Movie search

---

# Another Major Use Case

## Log Search

Large companies generate millions of logs.

Searching them efficiently is difficult.

Elasticsearch powers

**ELK Stack**

- Elasticsearch
- Logstash
- Kibana

Used for

- Log search
- Monitoring
- Dashboards
- Analytics

---

# PostgreSQL vs Elasticsearch

| PostgreSQL | Elasticsearch |
|------------|---------------|
| Source of truth | Search engine |
| Strong consistency | Optimized for searching |
| CRUD operations | Full-text search |
| Transactions | Relevance ranking |
| Relational data | Text indexing |

---

# PostgreSQL Full-Text Search

Modern PostgreSQL also supports full-text search.

Use PostgreSQL when

- Search requirements are simple
- Small/medium applications
- Avoiding extra infrastructure

Move to Elasticsearch when you need

- Massive datasets
- Fast search
- Autocomplete
- Fuzzy search
- Advanced ranking

---

# When Should You Use Elasticsearch?

Use it when your application needs

- Fast text search
- Millions of searchable records
- Autocomplete
- Typo tolerance
- Relevance ranking
- Complex search filters

Do **not** use Elasticsearch as your primary database.

---

# Interview Takeaways

Know

- What full-text search is
- Why SQL LIKE/ILIKE becomes slow
- What an inverted index is
- Why Elasticsearch is fast
- What relevance scoring means
- BM25 (high level)
- Text vs Keyword fields
- PostgreSQL Full-Text Search vs Elasticsearch
- Common Elasticsearch use cases
- ELK Stack basics

---

# Mental Model

```text
User Search

↓

Elasticsearch

↓

Inverted Index

↓

Rank Results

↓

Return Most Relevant Matches
```

**Rule of Thumb**

- **Database** → Store data.
- **Elasticsearch** → Search data.
