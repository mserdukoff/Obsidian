# Grammario -- Interview-Ready Technical Summary
> Related: [[Plans for Grammario 1-1-2026]] | [[Grammario Observability]] | [[Grammario Performance and ML-NLP Feature Upgrade Implementation Details]]

## The Big Picture

**Grammario** is a language-learning web app. You type a sentence in a foreign language (Italian, Spanish, German, Russian, or Turkish) and it breaks it down -- parses the grammar, explains vocabulary, and helps you learn.

The problem: **it was painfully slow** (10-15 seconds per query). The project had two goals: make it fast, and make it smarter.

---

## Phase 1: Performance (from ~10s to ~4s, or ~5ms on repeat queries)

**Three changes made it fast:**

1. **Parallelization** -- The app was doing two expensive things *one after the other*: parsing the sentence with an NLP model (~4s) and calling an LLM for explanations (~5s). But these two tasks don't depend on each other, so they were restructured to run *at the same time* using Python's `asyncio.gather()`. That alone cuts the time roughly in half.

2. **Swapped the NLP engine** -- The original tool (Stanza) uses heavy neural networks. It was replaced with spaCy, which does the same job (tokenization, POS tagging, dependency parsing) but is 10-50x faster on CPU. Stanza is kept as a fallback for languages spaCy doesn't support (Turkish).

3. **Added Redis caching** -- If someone submits the same sentence twice, why re-analyze it? A Redis cache stores results keyed by a hash of the sentence text. Cache hits return in ~5ms. The cache uses LRU eviction (least recently used gets dropped) and has a 24-hour TTL.

There was also a **subtle async bug fix**: the FastAPI route handler was declared `async` but was calling blocking (synchronous) code inside, which froze the entire web server during each request. Making it truly async let the server handle multiple users concurrently.

---

## Phase 2: New ML/NLP Features

Four intelligent features were added on top of the core analysis:

1. **CEFR Difficulty Scoring** -- Each sentence gets rated A1 through C2 (the European language proficiency scale). Instead of training a model, this uses *feature engineering*: it extracts 10 measurable properties from the parse tree (sentence length, dependency tree depth, number of subordinate clauses, morphological complexity, rare word ratio, etc.) and combines them with a weighted formula. It's essentially a hand-tuned scoring rubric based on linguistics research. The design is swap-ready for a trained scikit-learn model later.

2. **Word Frequency Bands** -- Every word gets tagged 1-5 based on how common it is in that language (from corpus data). "The" would be band 1 (very common), an obscure literary word would be band 5 (rare). This helps learners focus on high-value vocabulary first. The frontend shows color-coded dots on each word (green = common, red = rare).

3. **Sentence Embeddings** -- Each sentence gets converted to a 384-dimensional vector using a multilingual sentence-transformer model. These vectors are stored in Supabase using the pgvector extension, enabling *semantic similarity search* -- "find me sentences I've studied before that are similar to this one." An IVFFlat index makes this search efficient at scale.

4. **Grammar Error Detection** -- A dual approach: *rule-based* checks analyze the parse tree for agreement errors (e.g., a feminine article with a masculine noun in Italian), and the LLM is prompted to return structured error data (spelling, conjugation, word order issues) alongside its existing explanations. Both types surface in the UI with corrections.

---

## Phase 3: Spaced Repetition System (Data Engineering)

This is essentially **building Anki into the app**. When you save vocabulary words, the app schedules when you should review them using the **SM-2 algorithm** (the same algorithm Anki uses):

- Rate your recall 0-5 after seeing a word
- If you got it right, the review interval grows (1 day, then 6 days, then multiplied by an "ease factor")
- If you got it wrong, it resets to 1 day
- The ease factor adjusts over time -- words you consistently struggle with show up more often

The frontend has a full flashcard review page with progress tracking, mastery percentages, and session summaries.

---

## Phase 4: Observability

The `/health` endpoint was turned into a real operational dashboard -- it reports which NLP engines are loaded, cache hit rates, which features are active, and real-time memory usage via `psutil`. Every service logs its execution time in milliseconds so you can trace exactly where time is spent.

---

## Phase 5: Admin Dashboard

A standalone admin console at `/admin` with its own layout, protected by a simple user-ID check. It gives full visibility into:

- **Platform stats** (users, analyses per day/week, language breakdown)
- **User management** (edit/delete users, search, pagination)
- **Raw data inspection** (every analysis with expandable JSON for the parse tree and LLM response)
- **Vocabulary** across all users
- **Live backend health** (service statuses, loaded models, memory usage)

The key database challenge was that Supabase uses Row Level Security (each user can only see their own rows). This was solved by adding RLS policies that grant the admin user ID access to all rows across every table.

---

## One-Paragraph Summary

> "I had a language learning app where sentence analysis took 10-15 seconds. I got it down to ~4 seconds (or 5ms on cache hits) by parallelizing independent tasks with asyncio, switching from Stanza to spaCy for NLP, and adding a Redis cache. On top of that, I built four ML/NLP features: CEFR difficulty scoring using feature engineering on the parse tree, word frequency analysis from corpus data, sentence embeddings with pgvector for similarity search, and grammar error detection combining rule-based parse tree analysis with LLM output. I also implemented a spaced repetition system using the SM-2 algorithm for vocabulary review, and built an admin dashboard with full data inspection capabilities. The whole thing runs in Docker with a Python/FastAPI backend and a Next.js frontend on Supabase."

---

## Tech Stack

| Layer | Technology |
|-------|-----------|
| Backend | Python, FastAPI, uvicorn |
| NLP | spaCy (primary), Stanza (fallback) |
| LLM | OpenRouter / OpenAI API |
| Embeddings | sentence-transformers (MiniLM-L12-v2) |
| Caching | Redis |
| Frontend | Next.js, React, TypeScript, Tailwind CSS |
| Database | Supabase (PostgreSQL + pgvector) |
| Infrastructure | Docker, Docker Compose, DigitalOcean |
