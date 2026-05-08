# Grammario — complete feature reference

  

This document is the authoritative list of every feature in the product across the web app, backend NLP service, and database. It covers user-facing behavior, API contracts, admin tooling, marketing surfaces, and growth infrastructure.

  

Repo layout: `frontend/` (Next.js app router), `backend/` (FastAPI NLP service), `supabase/` (schema + migrations).

  

*Last updated: May 2026 — includes Stripe billing, paragraph analysis, Explore panel (sentence transformations + contextual chips), CEFR coverage view, paradigm tables, role-specific navbars, and all May 2026 fixes.*

  

---

  

## Table of contents

  

1. [Supported languages](#1-supported-languages)

2. [Marketing and public pages](#2-marketing-and-public-pages)

3. [Authentication](#3-authentication)

4. [Account, plan, and access rules](#4-account-plan-and-access-rules)

5. [App shell and navigation](#5-app-shell-and-navigation)

6. [App home / dashboard](#6-app-home--dashboard)

7. [Sentence analyzer](#7-sentence-analyzer)

8. [Guided grammar learning](#8-guided-grammar-learning)

9. [Grammar concept review (SRS)](#9-grammar-concept-review-srs)

10. [Sentence review (SRS)](#10-sentence-review-srs)

11. [Sentence practice quiz](#11-sentence-practice-quiz)

12. [Explore panel (alternatives + sentence transformations)](#12-explore-panel-alternatives--sentence-transformations)

13. [Italian grammar mastery map](#13-italian-grammar-mastery-map)

14. [Vocabulary system (legacy)](#14-vocabulary-system-legacy)

15. [Error notebook](#15-error-notebook)

16. [Settings](#16-settings)

17. [Gamification](#17-gamification)

18. [Teacher / educator dashboard](#18-teacher--educator-dashboard)

19. [Student academic flows](#19-student-academic-flows)

20. [Live quiz system](#20-live-quiz-system)

21. [Growth and marketing infrastructure](#21-growth-and-marketing-infrastructure)

22. [Admin console](#22-admin-console)

23. [Backend NLP service](#23-backend-nlp-service)

24. [Database capabilities](#24-database-capabilities)

25. [Client persistence and offline behavior](#25-client-persistence-and-offline-behavior)

26. [Stripe billing](#26-stripe-billing)

27. [Known gaps and partially wired features](#27-known-gaps-and-partially-wired-features)

  

---

  

## 1. Supported languages

  

- **Analyzer, Learn, Review, Vocabulary**: Italian (`it`), Spanish (`es`), German (`de`), Russian (`ru`), Turkish (`tr`), Japanese ('jp').

- Language metadata (name, native name, family, sample sentence): `GET /api/v1/languages` (proxies backend).

  

---

  

## 2. Marketing and public pages

  

All public routes are crawlable (listed in `sitemap.ts`, allowed in `robots.ts`). Auth routes, `/app/*`, `/admin/*`, and `/api/*` are disallowed.

  

### Core marketing

  

- **Home** (`/`): hero section with multi-language rotating headline (English, Italian, Spanish), stats, feature highlights, mobile CTA, why-choose-us, pricing comparison table, email capture section, FAQ, and CTA. Includes `AnnouncementBanner` at the top.

- **Demo** (`/demo`): fully interactive marketing demo — analyze any sentence across all six languages without an account.

- **Pricing** (`/pricing`): Free vs Pro comparison table, FAQ, educator callout linking to `/for-educators`. Upgrade buttons are wired to Stripe checkout.

- **Patch notes** (`/patch-notes`): public changelog with Markdown and Mermaid diagram rendering.

- **Blog** (`/blog`, `/blog/[slug]`): blog index and individual post pages.

- **Waitlist** (`/waitlist`): beta / educator waitlist application form.

- **Terms of Service** (`/terms`).

- **Privacy Policy** (`/privacy`).

  

### SEO language landing pages (`/languages/[lang]`)

  

Six statically generated pages — one per supported language — targeting high-intent search queries like "German grammar checker" or "Russian case analyzer".

  

Each page includes:

- Language-specific headline and subheadline.

- "Why this language is hard for English speakers" panel.

- Grid of four language-specific pain points with descriptions of how Grammario addresses each.

- Feature highlights list.

- Internal cross-links to other language pages.

- `generateMetadata` with language-targeted title, description, and Open Graph tags; added to `sitemap.ts`.

  

Languages: `italian`, `spanish`, `german`, `russian`, `turkish` (Japanese handled via `/languages/[lang]` catch-all).

  

### Deep grammar topic SEO pages (`/languages/[lang]/[topic]`)

  

Statically generated long-form articles per grammar topic (e.g. `/languages/italian/subjunctive`), targeting topic-specific search queries.

  

### Competitor comparison pages (`/vs/[competitor]`)

  

Statically generated comparison pages (e.g. `/vs/languagetool`) for competitive SEO.

  

### Grammar guide lead magnet pages (`/grammar-guide/[slug]`)

  

Three statically generated reference guides used as lead magnets (exchanged for email signup):

  

- `italian-grammar-guide`: gender agreement, definite articles, passato prossimo vs imperfetto, essere vs avere auxiliary, subjunctive triggers.

- `spanish-grammar-guide`: present tense conjugation, preterite vs imperfect, ser vs estar, subjunctive.

- `german-grammar-guide`: the four cases (Nominativ/Akkusativ/Dativ/Genitiv), definite article table, prepositions by case, dative verbs.

  

Each guide contains reference tables, worked examples with translations, and CTA sections linking back to the app.

  

### For educators (`/for-educators`)

  

Marketing landing page targeting language tutors searching for classroom grammar tools. Covers:

  

- Teacher-to-student acquisition framing (one tutor → multiple students).

- Feature grid: class management, assignments, student progress, live quizzes, error analytics, individual student view.

- Step-by-step getting-started flow.

- Educator pricing callout (Class plan subscription).

- CTA to start teaching and subscribe.

  

### Partners / affiliate program (`/partners`)

  

Marketing page for the affiliate / creator program:

  

- How-it-works steps (get link → share → earn recurring commission → commission stays recurring).

- "Who it's for" badges (YouTubers, TikTokers, podcasters, bloggers, tutors).

- Full application form: name, email, channel type (YouTube / TikTok / podcast / blog / tutoring / other), channel URL, notes about audience.

- FAQ: commission rate, tracking window (30 days), audience size requirement (none), payment.

- Application submitted via `POST /api/v1/affiliate-apply` to `affiliate_applications` table.

  

### Class join funnel (`/join`)

  

Public redirect page for teacher-generated class invite links:

  

- Accepts `?code=JOIN_CODE` query parameter.

- If user is already logged in: immediately redirects to `/app/classes?joinCode=CODE`.

- If not logged in: shows the join code and prompts to create a free account, with the code passed through the redirect.

- Student classes page reads `?joinCode=` from URL and auto-opens the join dialog with the code pre-filled.

  

### Invite redemption (`/invite/[token]`)

  

Direct student invite links generated by teachers (per-student tokens, separate from join codes). Handles authentication-gated redemption and class enrollment.

  

---

  

## 3. Authentication

  

- **Supabase Auth**: email + password sign-up and sign-in, sign-out, password reset.

- **Google OAuth**: available both as a redirect flow and directly inside the `AuthModal` (login and signup modes) via a "Continue with Google" button. `/auth/callback` handles the code exchange and redirects to `/app`; `/auth/error` for failures.

- **Auth modal**: used on the landing page and throughout the app; supports login mode, signup mode, and password reset. Google OAuth button with visual divider sits above the email/password form.

- **Post-login redirect**: after successful email/password login or signup via the auth modal on non-app pages, the user is automatically redirected to `/app`. Teachers are then redirected onward to `/app/teacher` by the app home guard. Google OAuth already handled this via `/auth/callback`.

- **Profile bootstrap**: on first sign-up, a Postgres trigger (`handle_new_user`) inserts a row in `public.users` with `display_name` from `full_name` user metadata, `avatar_url` from provider, and `referred_by` from the `ref` user metadata field (populated from the affiliate cookie at signup time).

- **Session usage**: JWT access token sent as `Authorization: Bearer …` on all API route calls via `authFetch`.

- **Auth guard**: the `/app` layout has a client-side guard that redirects unauthenticated visitors to `/login`. Sign-out performs a hard navigation to `/` so all open `/app` pages are torn down immediately.

- **Affiliate referral capture**: the `?ref=AFFILIATE_CODE` query parameter is captured by middleware and stored in a 30-day `grammario_ref` cookie (first-touch, httpOnly: false). On signup, the client reads the cookie and passes the code in `user_metadata.ref`; the database trigger writes it to `users.referred_by`.

  

---

  

## 4. Account, plan, and access rules

  

- **`is_pro`**: when true, user may analyze any supported language, has no language-switch cooldown, and gets 1000 daily analyses.

- **Free tier learning language**:

- Must set one `learn_language` (modal on first app load, Settings page, or `PATCH /api/v1/profile/learn-language`).

- After the first choice, switching is limited to once per 30 days (rolling; enforced server-side and client-side).

- Analyze, vocabulary save, and review are gated to that language for non-Pro users (`language_required` / `language_not_allowed` API errors).

- **Daily analysis quota** (enforced in `POST /api/v1/analyze`):

- Free: **3** analyses per calendar day.

- Pro: **1000** per day.

- `users.daily_sentence_limit` overrides both when set (admin-tunable).

- Visible to user via **usage counter** in the analyze page input bar: dot-progress indicator showing `used/limit` for free users, turns red at limit.

- **Account expiry**: if `account_expires_at` is in the past, analyze and usage endpoints return a blocked/expired state.

- **`account_type`**: values include `student`, `teacher`; used for access control on teacher routes.

  

---

  

## 5. App shell and navigation

  

- **App layout** (`/app/*`): wrapped in `AppShell` — syncs free-tier language preference, shows `LanguagePickModal` when a free user has no `learn_language` set. Renders a skeleton screen while auth initializes (no white flash).

- **Role-specific navbars** (`AppNavbar`): the navigation items adapt to account type:

- **Admin**: standard Pro nav (Analyze, Learn, Review, Settings) + Admin link; explicitly checked before any role branch so admin never falls into teacher or student nav.

- **Teacher**: Dashboard, Classes, Quizzes, Tests, Curriculum, Analyze.

- **Student**: Home, My Classes, Learn, Practice, Review.

- **Standard / Pro**: Analyze, Learn, Review, Settings.

- **Unauthenticated**: standard nav items (no role checks attempted).

- **Teacher redirect**: when a teacher navigates to `/app`, the app home immediately redirects them to `/app/teacher`.

- **Language pick modal**: blocks all interactions until a free-tier user selects their `learn_language`; not shown for Pro, teacher, or student accounts.

- **Onboarding modal**: shown to new standard accounts to guide language selection and account type; suppressed for existing `account_type === "student"` and `account_type === "teacher"` accounts.

- **Announcement banner**: rendered at the top of marketing and app pages; content fetched from `GET /api/v1/announcements`; dismissable.

- **TanStack Query caching**: teacher dashboard, app home, student classes page, and learn hub use `useQuery` with a 5-minute `staleTime` and 10-minute `gcTime`, so data cached during one visit is served instantly on the next.

  

---

  

## 6. App home / dashboard

  

- **Guest state**: feature intro, sign-in CTA via auth modal, locked state for Analyze/Learn/Review actions.

- **Signed-in state**:

- Welcome heading with display name and current streak.

- **Personalized recommendations** from `GET /api/v1/recommendations` — urgency-ranked cards surfacing: vocabulary due for review, weak grammar concepts (mastery ≤ 2), upcoming assignment due dates, pending live quizzes with room codes, and prompts to join a class if in no classes. Up to 6 recommendations shown; empty state when all caught up.

- Quick-access links to Analyze, Learn, Review, classes, error notebook.

  

---

  

## 7. Sentence analyzer (`/app/analyze`)

  

Requires sign-in (redirects guests to `/app`).

  

### Input and analysis request

  

- Text input with default sample sentence; **language selector** (Pro / teacher: any language; free: locked to `learn_language`; student: scoped to class language(s), deduplicated).

- **Analyze mode toggle**: switch between **Single sentence** and **Paragraph** (multi-sentence) modes.

- **Usage counter** (free users only): dot-progress pill in the input bar showing `used/limit today`, turns red when at limit. Hidden for student and teacher accounts.

- **Run analysis** → `POST /api/v1/analyze` → Python backend returns token graph + enrichment.

- **Language detection** (`franc` trigram library): server-side detection rejects submissions where the detected language clearly doesn't match the selected language (e.g. sending English text for an Italian analysis). Skipped for texts under 20 characters. Detection is also skipped when the selected and detected language are both in a confusable Romance group (Italian/Spanish/Portuguese), so Italian text doesn't incorrectly trigger a "you're writing Spanish" error. Mismatch surfaces as a descriptive toast error.

- Loading/error states: toast feedback; 403 handling for `language_required` and `language_not_allowed`; daily limit errors.

  

### Paragraph mode

  

Accepts up to 8 sentences (newline-separated). Each sentence is analyzed independently via `POST /api/v1/analyze`.

  

- Requests are **staggered 600 ms apart** so all sentences hit the NLP backend at different times, avoiding thread-pool saturation.

- **Auto-retry**: each sentence gets one automatic retry (after a 1.5 s pause) before being marked as failed.

- Results render as **collapsible sentence cards** (`ParagraphResultsView` component):

- Card header: truncated sentence text, CEFR badge (color-coded A1–C2), expand/collapse chevron.

- Expanded card body: English translation, key grammar concepts, grammar errors, individual "Save to Review" action.

- Failed card: error message with a **Retry** button that re-analyzes just that sentence on demand.

- Quota enforcement: free users' daily limit applies across both single and paragraph modes (each sentence counts as one analysis).

- Empty state example sentences are filtered to the student's class language(s); all 6 languages shown for standard / teacher accounts.

  

### Visualization (ReactFlow)

  

- **Linear layout**: tokens in sentence order; no dependency edges.

- **Tree layout**: Dagre-ranked dependency tree (top–bottom).

- **Toggle draggable nodes** for manual rearrangement.

- **Word nodes** show: surface form, lemma, UPOS tag (click → Universal POS explanation panel), morphological feature chips (click → feature glossary), optional morpheme segments (Turkish agglutination).

- **Rule-based grammar errors**: nodes render error styling with message when `grammar_errors[].word_id` matches.

  

### Pedagogy panel

  

When present in the backend response:

- English translation.

- Nuance note.

- Grammar concepts (name, description, related words) — each has a **Queue for review** button (calls `POST /api/v1/grammar-reviews`) and optionally a **Lesson** button linking to the matching Learn topic.

- Tips ("why this form?" Q&A with rules and examples).

- LLM-reported corrections (`pedagogical_data.errors`).

- CEFR difficulty level (A1–C2) with score bar.

- Rule-based error list with severity.

  

### Post-analysis side effects (signed-in)

  

- Inserts row in `analyses` (text, language, token graph JSON, pedagogical JSON, difficulty, embedding).

- Increments `total_analyses` on user profile.

- **+10 XP**; level recalculated; level-up toast if level increases.

- Daily goal `completed` counter incremented; **+50 XP** and toast on first time reaching target today.

- Refreshes usage counter and history list (last 20 analyses).

- Zustand stores current analysis (up to 10 recent session analyses).

  

### History sidebar

  

- Recent analyses with relative timestamps; open any entry to reload its tokens and pedagogy.

- Favorite toggle and delete per entry.

- All / Favorites filter.

  

### Semantic memory ("You've seen this before")

  

After a successful analysis (if backend returns an embedding):

- `getSimilarAnalyses()` calls `match_analyses()` Supabase RPC with the embedding, cosine similarity threshold 0.6, same language; returns up to 4 prior analyses.

- Strip below pedagogy panel shows cards: sentence text (truncated), similarity %, CEFR badge, relative date.

- Clicking a card loads that historical analysis into the canvas.

- Failures silently swallowed — never blocks main flow.

  

### Shareable analysis cards

  

- **Share card** button in the drawer header (shown after an analysis with pedagogical data).

- Opens an in-page preview of a branded card: sentence, language flag, CEFR level badge, top 2 grammar concepts, Grammario branding and URL.

- **Download as PNG** via `html-to-image` (2× pixel ratio). Card is rendered off-screen at full size and captured.

  

### Practice quiz

  

After a successful analysis, a **Practice** button appears in the drawer toolbar. It opens a modal quiz generated entirely from the stored analysis (no extra network call):

  

- **Fill-in-the-blank**: key verb or noun is blanked; user picks from 4 options (correct form + plausible distractors from other tokens/lemmas).

- **Conjugation recall**: given lemma + morphological description (e.g. "third person singular past"), identify the correct form.

- **Translation challenge**: given the sentence, select the correct English translation from 4 options.

- **Morphology identification**: given a highlighted token, pick the correct grammatical feature value (person, number, tense, etc.).

- Progress bar through 5 questions; green/red feedback flash with explanation after each answer.

- **Summary screen**: accuracy %, per-question breakdown of missed items, "Save missed concepts to Grammar Review" button.

  

See §11 for implementation detail.

  

### Review Later (sentence SRS)

  

**Review Later** button appears next to Practice in the drawer toolbar. Queues the current sentence for spaced-repetition review:

  

- Calls `POST /api/v1/sentence-reviews` with the current `analysis_id`.

- Button transitions to "Queued" (green check) or "In queue" if already added — idempotent.

- Queued sentences appear in the **Sentences** tab of `/app/review`.

  

See §10 for implementation detail.

  

### Explore panel

  

A standalone **Explore →** action button in the analysis action bar opens the Explore panel in **Sentence mode** directly. Individual token-level access is also available by hovering VERB/AUX tokens (contextual chip popover) or clicking the `GitBranch` icon on any VERB/AUX/NOUN/ADJ/DET/PRON token.

  

See §12 for full implementation detail.

  

### Feedback on an analysis

  

- **Rate this analysis** form: 1–5 stars, category (`accuracy` / `translation` / `explanation` / `difficulty` / `other`), optional comment. Writes to `sentence_feedback` linked to `analysis_id`.

  

### Vocabulary save from analyzer

  

- Per-token **Save for review** (non-punctuation): inserts into `vocabulary` table if lemma + language not already present; sets due today; links `analysis_id`.

- **+5 XP** on save; duplicate shows "Already saved" state.

  

---

  

## 8. Guided grammar learning (`/app/learn`)

  

- **Hub** (`/app/learn`): language selector; free users only see their `learn_language`; Pro users see all 6; students see only their class language(s); teachers see only the language(s) of their own classes.

- **Per language** (`/app/learn/[lang]`): three tabs:

- **List**: CEFR bands A1–C2 as navigable levels.

- **Map**: visual grammar concept dependency map (Italian only; see §13).

- **Coverage** (new): CEFR coverage view backed by `GET /api/v1/learn/coverage`. Pulls SRS mastery data from `grammar_concept_reviews` and shows a tri-segment progress bar per CEFR level (mastered / in-review / unseen), summary stats (topics mastered, in review, not started), and a gap list of topics not yet started with direct lesson links.

- **Per level** (`/app/learn/[lang]/[level]`): list of grammar topics by slug.

- **Topic page** (`/app/learn/[lang]/[level]/[slug]`): title, summary, multi-paragraph body with `**bold**` Markdown rendering.

- **Italian curriculum** (comprehensive, hand-authored): full A1–C2 topic bodies — conjugation tables, worked examples, usage rules. Covers nouns and gender, articles, essere/avere, regular/irregular verbs, all major tenses, pronouns, subjunctive, passive, discourse.

- **Spanish curriculum** (comprehensive): full A1–C2, 37 topics. Includes interactive **paradigm tables** for 9 topics (articles, subject pronouns, ser/estar, present tense, pretérito indefinido, imperfecto, futuro, condicional simple, subjuntivo presente, modal verbs). Each table has tabbed views and clickable rows that expand to show example sentences and translations.

- **German curriculum** (comprehensive): full A1–C2, 37 topics. Includes interactive **paradigm tables** for 8 topics (articles with all four cases, sein/haben, present tense with vowel-change verbs, nominative/accusative, dative case, Perfekt, modal verbs, Konjunktiv II, adjective declension).

- **Russian curriculum** (comprehensive): full A1–C2, 37 topics spanning the Cyrillic alphabet through stylistics, all six cases, verbal aspect, motion verbs, participles, and advanced discourse.

- **Turkish curriculum** (comprehensive): full A1–C2, 37 topics spanning vowel harmony and agglutination through information structure, the evidential -mIş past, participial relative clauses, the full case system, and pragmatic politeness strategies.

- **Japanese**: outline stubs.

- **Queue for practice** button: calls `POST /api/v1/grammar-reviews` with `topic_id`; if already queued, shows "Already added" with link to Review.

  

---

  

## 9. Grammar concept review (SRS) (`/app/review`)

  

SM-2 spaced-repetition flashcard system for grammar concepts (rebuilt April 2026).

  

### Data model

  

- `grammar_concept_reviews` table: `user_id`, `topic_id` (curriculum concept ID), `language`, SM-2 fields (`mastery`, `ease_factor`, `interval_days`, `next_review`, `last_reviewed`, `review_count`).

- Two concept sources: **curriculum concepts** (linked by `topic_id`) and **ad-hoc concepts** from analyzer (inline `concept_name`, `concept_description`, `concept_examples`, `level`, optional `analysis_id`).

  

### API

  

- `GET /api/v1/grammar-reviews`: concepts due today (`next_review <= today`), enriched with curriculum body/examples; returns `{ items, stats: { total, due, mastered } }`.

- `POST /api/v1/grammar-reviews`: queue a concept — accepts `{ topic_id }` or inline ad-hoc concept. Returns `{ already_added: true }` on duplicate.

- `PUT /api/v1/grammar-reviews`: submit SM-2 quality rating `{ id, quality: 0–5 }`; advances scheduling, updates mastery/ease/interval.

  

### Flashcard UI

  

- **Card front**: concept title, CEFR level badge (color-coded), language flag, mastery %, progress counter (N of total).

- **Card back** (after "Show Answer"): full explanation with Markdown rendering, up to 4 example sentences, optional rule list.

- **Rating buttons**: Wrong (1), Hard (3), Good (4) primary; full 0–5 scale expandable.

- **Session complete screen**: correct count, accuracy %, Again / Go to Learn actions.

- **Empty state**: "all caught up" with prompts to analyze or open Learn.

  

---

  

## 10. Sentence review (SRS) (`/app/review` — Sentences tab)

  

Spaced-repetition review of full sentences the user has analyzed. Complements the grammar concept SRS with whole-sentence recall.

  

### Data model

  

- `sentence_reviews` table: `user_id`, `analysis_id` (FK → `analyses`, cascade delete), `language`, SM-2 fields (`ease_factor`, `interval_days`, `next_review`, `mastery`, `last_reviewed`, `review_count`). Unique on `(user_id, analysis_id)`. RLS: owner-only. Index on `(user_id, next_review)` for efficient due-date queries.

  

### API

  

- `GET /api/v1/sentence-reviews`: sentences due today (`next_review <= today`), joined to `analyses` for text, translation, and difficulty; returns `{ reviews, stats: { total, due, mastered } }`. Only sentences with a stored translation are returned (untranslatable sentences are excluded).

- `POST /api/v1/sentence-reviews`: queue an analysis by `analysis_id`. Idempotent upsert; returns `{ already_added: true }` on duplicate. Returns `422` if the analysis has no translation.

- `PUT /api/v1/sentence-reviews`: submit SM-2 quality rating `{ review_id, quality: 0–5 }`; advances scheduling, updates mastery/ease/interval.

  

### Review UI

  

The **Review** page (`/app/review`) now shows a **Grammar / Sentences** tab switcher with due-count badges on each tab.

  

Sentence card flow:

- **Card front**: English translation prompt ("Translate into 🇮🇹") with CEFR badge and mastery %.

- **Card back** (after "Show original sentence"): native sentence displayed in large italic heading.

- **Rating buttons**: Again (1), Hard (3), Got it (4) — same SM-2 pattern as grammar concepts.

- **Session complete screen**: correct count, accuracy %, Again / Go to Learn actions.

- **Empty state**: "all caught up" with prompt to go to Analyze and click "Review Later".

  

---

  

## 11. Sentence practice quiz

  

On-demand quiz generated from a single analyzed sentence. Launched from the **Practice** button in the analyze page drawer toolbar.

  

### Question types

  

Generated by `frontend/src/lib/quiz-generator.ts` (pure TypeScript, no network calls):

  

| Type | Prompt | Answer source |

|------|--------|---------------|

| `fill_blank` | Sentence with one token blanked (`___`) | Token surface form; distractors from other tokens/lemmas |

| `conjugation` | "What is the [person] [tense] form of [lemma]?" | Token surface form |

| `translation` | "What is the English translation of this sentence?" | `pedagogical_data.translation`; distractors are scrambled/truncated variants |

| `morphology` | "What is the [feature name] of [word]?" | Feature value from UD feats string; distractors from valid feature-value pool |

  

- Target token selection prioritises VERB / AUX; falls back to NOUN, ADJ, PRON, DET.

- Up to 5 questions per session (shuffled mix of all available types).

- All 4 options shuffled before display; correct answer is always one of the options.

  

### UI (`frontend/src/components/app/sentence-quiz.tsx`)

  

- Modal overlay (no new route); launched by `showQuiz` state in the analyze page.

- One question at a time with progress bar.

- Picking an option locks in the answer immediately; green/red background + explanation shown.

- "Next question" / "See results" button advances flow.

- **Summary screen**: overall accuracy %, per-question breakdown (correct ✓ / missed ✗ with correct answer + explanation for each), "Save missed concepts to Grammar Review" button (queues `pedagogical_data.concepts` via `POST /api/v1/grammar-reviews`).

  

---

  

## 12. Explore panel (alternatives + sentence transformations)

  

The **Explore panel** is a unified right-side panel (and bottom-drawer tab in paragraph mode) with two modes: **Word** (token-level grammar alternatives) and **Sentence** (whole-sentence transformations). It replaces the earlier standalone "Alternatives Panel."

  

A standalone **Explore →** action button in the analysis action bar opens the panel in Sentence mode. Individual token access is also available via hover popovers and the `GitBranch` icon on tokens.

  

---

  

### Word mode — grammar alternatives

  

Allows users to see how changing a single grammatical dimension (person/number, tense, or mood) transforms a sentence across all six supported languages.

  

#### Backend (`POST /api/v1/alternatives`)

  

Request body: `{ text, language, token_text, token_feats, token_upos, change_type }` where `change_type` ∈ `["person_number", "tense", "mood"]`.

  

The LLM rewrites the sentence changing only the specified dimension. Language-specific prompts cover:

  

| Language | Person/Number | Tense | Mood |

|----------|---------------|-------|------|

| Italian | io/tu/lui-lei/noi/voi/loro | Presente, Passato Prossimo, Imperfetto, Futuro, Condizionale | Indicativo, Congiuntivo, Condizionale, Imperativo |

| Spanish | yo/tú/él-ella/nosotros/vosotros/ellos | Presente, Indefinido, Imperfecto, Futuro, Condicional | Indicativo, Subjuntivo, Condicional, Imperativo |

| German | ich/du/er-sie-es/wir/ihr/sie-Sie | Präsens, Perfekt, Präteritum, Futur I | — |

| Russian | я/ты/он-она/мы/вы/они | Present, Past imperfective, Past perfective, Future | — |

| Turkish | ben/sen/o/biz/siz/onlar | Geniş, Şimdiki, -di geçmiş, -miş geçmiş, Gelecek | — |

  

**Multi-word construction awareness**: the prompt engine detects AUX tokens (e.g. "stavo" in *stavo andando*), gerund tokens (`VerbForm=Ger`), and past participle tokens (`VerbForm=Part`) and adjusts the LLM instruction to vary the auxiliary while leaving the invariable form unchanged.

  

Response: `{ alternatives: [{ label, form, sentence, translation, explanation }] }`. Rate-limited: 10 requests/hour free, 60/hour Pro (in-memory per cold-start).

  

#### Word mode UI

  

Triggered by clicking any VERB/AUX/NOUN/ADJ/DET/PRON token and clicking the `GitBranch` icon, or by switching to Word mode inside the Explore panel.

  

- **Token badge** + POS tag + language.

- **Tab switcher**: Person/Number | Tense | Mood (tabs are filtered to those applicable to the selected POS).

- **Conjugation table**: label, form, truncated translation. Original form row is highlighted and tagged "orig.".

- Clicking any row expands a detail block: full rewritten sentence (changed word highlighted), full translation, one-sentence explanation.

- **Client-side cache**: results stored per `change_type`; switching tabs never re-fetches.

- Retry button on error/empty state.

  

---

  

### Sentence mode — contextual transformations

  

Applies seven named transformations to the whole analyzed sentence via the backend `POST /remix` → Next.js proxy `POST /api/v1/remix`.

  

#### Backend (`POST /api/v1/remix`)

  

Request body: `{ sentence, language, transformation }` where `transformation` ∈ `["past_tense", "present_tense", "negative", "plural", "formal", "first_person", "passive"]`.

  

The LLM transforms the sentence, then the result is **fully re-analyzed** through the NLP pipeline, returning a compact analysis response (tokens, CEFR, translation).

  

#### Contextual chip system (`sentence-context.ts`)

  

Rather than always showing all seven chips, the UI derives **directional chips** from the morphological features of the current `TokenNode` data:

  

- `parseSentenceContext` reads verb tense, person, and mood from existing token features.

- `getDynamicChips` returns 2–4 chips relevant to the current sentence (e.g. "→ Past Tense" only appears when the verb is currently present tense).

- `getTokenChips` returns 2–3 contextual chips for a specific VERB/AUX token.

  

#### VERB/AUX hover popovers

  

VERB and AUX tokens in the `SentenceView` show a **hover popover** with 2–3 contextual chips derived from that token's morphology. Clicking a chip immediately fires one `/api/v1/remix` call — no upfront LLM call required.

  

#### Inline diff strip

  

The result of a transformation renders as an **inline diff strip** at the bottom of the analysis canvas:

  

- Original and transformed sentence shown side by side.

- **Changed words highlighted in amber** (computed by `computeTokenDiff`, which aligns tokens by position and flags mismatches).

- CEFR badge and translation for the transformed sentence.

- The Explore panel's Sentence mode also renders a full result card with the same diff-highlighted tokens.

  

---

  

### Explore panel placement

  

- **Single sentence mode**: opens as a right-side slide-in panel; replaces the Grammar Reference panel when active.

- **Analysis details drawer** (bottom sheet on mobile / desktop toggle): two tabs — "Analysis details" and "Alternatives". The Alternatives tab appears only when a token is selected, shows the token name as a badge, and includes an X to dismiss.

  

---

  

## 13. Italian grammar mastery map (`/app/mastery-map`)

  

Requires sign-in. Italian only.

  

- **Concept taxonomy** (`it-grammar-taxonomy.ts`): curated list of Italian grammar concepts mapped to CEFR levels (A1–C2), each with `id`, `title`, `slug`.

- **Mastery scoring**: fetches all user Italian analyses, extracts `pedagogical_data.concepts[].name`, fuzzy-matches against taxonomy, increments encounter counts. Scores: Not yet seen (0), Spotted (1), Familiar (2–4), Well-practiced (5+).

- **Stats bar**: concepts encountered / total, total Italian analyses, coverage %.

- **List view** (default): CEFR level sections (collapsible, each with progress bar); concept cards in a grid; clicking a card opens a detail panel with matching analyses (text, date, difficulty) and Learn topic link.

- **Map view**: ReactFlow canvas with concept nodes in CEFR-level rows; prerequisite edges (smoothstep) connect related concepts; pan/zoom; clicking a node shows the detail panel.

- **Mobile**: detail panel slides up as bottom sheet.

  

---

  

## 14. Vocabulary system (legacy)

  

The Review page was rebuilt in April 2026 to grammar-concept SRS. Vocabulary review endpoints remain for backward-compatibility; the current Review UI uses the grammar-concept flow.

  

- **`GET /api/v1/vocabulary/review`**: up to 20 vocabulary items with `next_review <= today`; stats: total, due, mastered (mastery ≥ 80); scoped to free-tier `learn_language`.

- **`POST /api/v1/vocabulary/review`** with `{ vocab_id, quality }`: updates SM-2 scheduling.

- **403 `language_required`**: when free-tier user has no `learn_language` set.

- Vocabulary saved from Analyze: lemma, surface form, UPOS, optional translation, `analysis_id`; due immediately.

  

---

  

## 15. Error notebook (`/app/error-notebook`)

  

Requires sign-in.

  

Aggregates a student's persistent weak points in one view:

  

- **Weak grammar concepts**: grammar review items with mastery ≤ 2 that have been reviewed at least once; shows concept title, mastery %, review count, last reviewed date, link to Learn topic.

- **Weak vocabulary**: vocabulary items with low mastery across all languages; shows word, lemma, translation, mastery %, review count.

- **Wrong quiz answers**: recent incorrect answers from live quiz sessions; shows quiz title, question context.

- Sections are collapsible and empty states are friendly.

- Data fetched via `GET /api/v1/error-notebook`.

  

---

  

## 16. Settings (`/app/settings`)

  

Requires sign-in (unauthenticated users redirected to `/`).

  

- **Profile**: avatar (or initial fallback), display name, email, totals (analyses, streak, level), and "Member since" date.

- **Inline editable fields**: clicking the pencil icon next to display name or email opens an in-place edit field with save/cancel controls.

- **Display name**: saves directly to `users.display_name` via `updateDisplayName` in `db.ts`.

- **Email**: routes through Supabase Auth (`updateEmail`), which sends a confirmation link to the new address.

- **Plan**: Pro badge for Pro users; Class plan badge for teachers; upgrade/manage subscription links (Stripe Customer Portal for Pro users).

- **Learning language** (free users): dropdown to set/switch language with 30-day cooldown rules visible; syncs `defaultLanguage` in local preferences.

- **Preferences** (Zustand + `localStorage` under `grammario-storage`):

- **Daily goal target**: 3, 5, 10, 15, or 20 analyses.

- **Show translations** toggle.

- **Sign out**.

  

---

  

## 17. Gamification

  

### Active / wired

  

- **Streak** and **longest streak**: updated on profile on active use; shown in Settings and StatsPanel.

- **XP and level**: updated on analyze (+10 XP), vocabulary save (+5 XP), daily goal complete (+50 XP); level calculated from cumulative XP against fixed thresholds (10 levels).

- **Level-up toast** on level increase.

- **Daily goal progress**: per-day row in `daily_goals`; progress shown in StatsPanel.

- **XP gain toasts**: shown after each reward event.

  

### Schema-ready, not wired

  

- **Achievements**: `achievements` definitions + `user_achievements` junction table; seeds in schema (milestones for analyses, streaks, vocab, levels, polyglot); helper functions `unlockAchievement` / `getUserAchievements` exist in `db.ts`; `AchievementToast` component supports `"achievement"` type — but no automatic unlock trigger is wired in the hot path.

- **Extra XP constants**: `FIRST_ANALYSIS_OF_DAY`, `STREAK_BONUS`, `ACHIEVEMENT_UNLOCK` defined in `db.ts` but unused in the current analyze flow.

- **`enableSounds`**: Zustand default exists; no callers or audio files present.

  

---

  

## 18. Teacher / educator dashboard (`/app/teacher/*`)

  

Accessible only when `account_type === "teacher"` (or admin). Layout guard redirects non-teachers.

  

### Dashboard (`/app/teacher`)

  

- Overview: classes summary, quiz template count, quick links to classes and quizzes.

  

### Class management (`/app/teacher/classes`)

  

- **List classes**: shows name, language, join code, student count.

- **Create class**: name, description, language.

- **Class detail** (`/app/teacher/classes/[classId]`):

- **Members tab**: roster with per-student XP, level, streak, assignment completion rate. Remove student. Click a student to open the per-student detail page.

- **Assignments tab**: list with completion rates (N/total), due dates, overdue indicators. Create assignment (type: `analyze_text`, `vocabulary`, `grammar_topic`, `writing_prompt`; title, description, due date; optional linked resource). **Duplicate** copies an assignment to another class owned by the same teacher via `POST /api/v1/teacher/assignments/duplicate` (vocabulary assignments with a class list clone the list and items into the target class; due date is cleared on the copy). **Assignment detail expand**: clicking an assignment card reveals an inline panel showing the full content — target sentence (Analyze Text type), grammar topic slug (Grammar Topic), linked vocabulary list with word count and a shortcut to the Vocab Lists tab (Vocabulary), or the writing prompt text (Writing Prompt).

- **Gradebook tab**: student × assignment matrix (completion + writing scores); **Export CSV** (UTF-8 BOM) for spreadsheet / SIS-adjacent workflows.

- **Passages tab**: teacher-authored reading passages assigned to the class.

- **Vocab Lists tab**: teacher-curated vocabulary lists for the class. Clicking any vocab list expands an **inline word table** showing each word, translation, part of speech, and example sentence (fetched from the existing vocab list API).

- **Announcements tab**: post and view class-scoped announcements visible to enrolled students.

- **Messages tab**: class messaging thread; teachers can post; students can read and reply (via student classes page).

- **Class join code** displayed and copyable.

- **Error patterns tab** (`/api/v1/teacher/classes/[id]/error-patterns`): aggregate grammar error types across all student analyses in the class, with human-readable topic names (raw IDs like `es-b1-subjunctive` are formatted as "Subjunctive").

  

### Quiz templates (`/app/teacher/quizzes`)

  

- List of quiz templates; create new; edit existing.

- **Quiz editor** (`/app/teacher/quizzes/[quizId]`): add/remove/reorder questions; question types: multiple choice, fill-in-the-blank, translation; set point values.

- **Launch session**: creates a live quiz session with a room code; navigates to host view.

  

### Quiz host view (`/app/teacher/quizzes/[quizId]/host`)

  

- **Lobby**: shows room code; waits for students to join; list of joined participants.

- **Run quiz**: broadcasts questions to students via Supabase Realtime (`quiz:{sessionId}` channel); advances through questions; shows live answer counts.

- **Results**: final leaderboard with scores and streaks.

  

### Tests (`/app/teacher/tests`)

  

- Create tests with questions (MCQ, fill-in-the-blank, translation); set time limits and due dates per class.

- View per-test attempts and scores from students.

  

### Vocabulary lists (`/api/v1/teacher/vocab-lists`)

  

- Create curated vocabulary lists per class; students can import lists into their personal vocabulary deck.

  

### Passages

  

- Create reading passages (title, body text, language) and assign to a class.

- Students see annotated passage with per-sentence token breakdowns.

  

### Teacher assignment API (`/api/v1/teacher/assignments`)

  

- **`POST` `/api/v1/teacher/assignments`** — create assignment (`class_id`, `title`, `type`, optional `config`, `due_date`, `description`).

- **`GET` `/api/v1/teacher/assignments?class_id=`** — list assignments for a class (with `completion_count`).

- **`DELETE` `/api/v1/teacher/assignments?id=`** — delete assignment.

- **`POST` `/api/v1/teacher/assignments/duplicate`** — body `{ assignment_id, target_class_id }`. Copies the assignment into another class **for the same teacher**; `due_date` is set to `null` on the copy. If `type` is `vocabulary` and `config.list_id` is set, a new **`vocabulary_lists` row and all `vocabulary_list_items`** are created in the target class and the new assignment references the new list.

  

### Teacher & district strategy (doc only)

  

- Product-facing priorities for LMS, SSO, and district rollout: [`docs/product/teacher-district-adoption.md`](./teacher-district-adoption.md) (not a runtime feature; see [CHANGELOG educator section](../CHANGELOG.md#educator-product--documentation-april-2026)).

  

---

  

## 19. Student academic flows (`/app/classes`, `/app/passages/[id]`, `/app/tests/[id]`)

  

### Classes page (`/app/classes`)

  

- **Join class**: enter teacher's join code (supports `?joinCode=` URL parameter from `/join?code=` redirect — code auto-fills and dialog opens).

- **My classes**: list of enrolled classes with teacher name, language, join code.

- **Pending assignments**: list with type icons, due dates (overdue highlighted), completion status. Assignment actions:

- `analyze_text`: navigates to Analyze with pre-filled sentence.

- `vocabulary`: imports class vocab list into personal deck.

- `grammar_topic`: navigates to Learn topic page.

- `writing_prompt`: opens in-page writing submission modal with AI feedback.

- **Writing submission**: textarea for free-form writing; submits to `POST /api/v1/student/assignments/[id]/submit`; receives AI feedback (grade, overall comment, error list with corrections, style suggestions).

- **Vocab lists**: lists shared by teacher; import into personal vocabulary deck with one tap.

- **Tests**: list of assigned tests with due dates; navigate to test-taking page.

- **Live quiz sessions**: pending sessions surfaced with room code and "Join" button.

  

### Passage reader (`/app/passages/[id]`)

  

- Renders passage body text.

- Sentence-by-sentence token annotation: word, lemma, part of speech, dependency relation, optional translation.

- Vocabulary save per token; navigates to Analyze for deeper breakdown.

  

### Test taking (`/app/tests/[id]`)

  

- Time-limited test flow (countdown timer from `time_limit_min`; auto-submits on expiry).

- Question types: multiple choice (click answer), fill-in-the-blank (text input), translation (text input).

- Submit → score revealed with per-question correct/incorrect review and point breakdown.

- Results persist via `POST /api/v1/student/tests/[id]/attempt`.

  

---

  

## 20. Live quiz system (Supabase Realtime)

  

Both teacher host and student participant views communicate via a Supabase Realtime broadcast channel named `quiz:{sessionId}`.

  

### Student flow (`/app/quiz/join`, `/app/quiz/[sessionId]`)

  

- **Join**: enter 6-digit room code → `POST /api/v1/student/quiz/join` → navigate to session.

- **Lobby phase**: waiting screen; sees participant list update in real time.

- **Question phase**: question text and answer options displayed; tap to select; answer locked in; timer countdown.

- **Results phase**: per-question correct/incorrect reveal with scores.

- **Final leaderboard**: ranked by score; name, score, streak.

  

### Teacher host flow (`/app/teacher/quizzes/[quizId]/host`)

  

- Broadcasts question events to all connected students.

- Controls pace: advance question, skip, end session.

- Live dashboard: answer counts per option, response rate.

- Final leaderboard with export-ready results.

  

---

  

## 21. Growth and marketing infrastructure

  

### Freemium usage visibility

  

- `GET /api/v1/usage` returns: `{ used_today, limit, remaining, reset_at, is_pro }`.

- Analyze page fetches on mount and after each analysis; shows a dot-progress pill (`used/limit`) for free users in the input bar; turns red at limit.

- `reset_at` is a Unix timestamp (midnight UTC) used for "resets in Xh" messaging.

  

### Email capture with lead magnet

  

- `EmailCaptureSection` component on the home page (between Pricing and FAQ): email input, language guide selector (Italian / Spanish / German), submit → `POST /api/v1/email-capture`.

- `POST /api/v1/email-capture`: inserts into `email_subscribers` (`email`, `source`, `lead_magnet` slug); duplicate emails silently return `{ already_subscribed: true }`.

- After signup: redirect to `/grammar-guide/[slug]` with the selected guide content.

- Grammar guide pages are full reference articles with tables and examples (see §2).

  

### Shareable analysis cards

  

- Branded PNG card generated client-side from analysis data via `html-to-image`.

- Card contents: sentence (italic), language flag and name, CEFR level badge (color-coded), top 2 grammar concepts with descriptions, Grammario logo and URL.

- 2× pixel ratio; downloaded as `grammario-analysis.png`.

- Share card button appears in the analyze page drawer after any analysis that includes pedagogical data.

  

### Affiliate / referral program

  

- **Referral tracking**: `?ref=AFFILIATE_CODE` query parameter captured in Next.js middleware; validated regex (`/^[a-zA-Z0-9_-]{2,32}$/`); stored as `grammario_ref` cookie (30-day expiry, first-touch attribution).

- **Signup attribution**: client reads cookie on signup, passes code in `user_metadata.ref`; `handle_new_user` DB trigger writes to `users.referred_by`.

- **Application API**: `POST /api/v1/affiliate-apply` inserts into `affiliate_applications` (name, email, channel_type, channel_url, notes, status `pending`).

- **Partners page** (`/partners`): how-it-works, who-it's-for, application form, FAQ (see §2).

  

### SEO infrastructure

  

- `sitemap.ts`: includes `/languages/[lang]` (5 pages), `/grammar-guide/[slug]` (3 pages), `/for-educators`, `/partners`, plus all core marketing pages.

- `robots.ts`: explicitly allows all marketing, language, grammar-guide, for-educators, and partners routes.

- Per-page `generateMetadata` with targeted `title`, `description`, `keywords`, `openGraph`, and `alternates.canonical` on all new SEO pages.

  

### Class join funnel

  

- Public `/join?code=` page as a link target for tutor-to-student sharing.

- Handles unauthenticated visitors with a sign-up CTA; auto-redirects logged-in users into the classes join flow.

- Student classes page reads `?joinCode=` and pre-fills + opens the join dialog automatically.

  

---

  

## 22. Admin console (`/admin/*`)

  

Restricted to `NEXT_PUBLIC_ADMIN_USER_ID`. Sidebar: Overview, Users, Requests & Data, Vocabulary, Feedback, Announcements, Backend.

  

- **Overview** (`/admin`): KPI stats via `GET /api/v1/admin/stats` — user counts, analyses, vocabulary, feedback aggregates, language breakdown, recent activity.

- **Users** (`/admin/users`): search and filter by account type; edit fields (display name, type, limits, notes); create / delete; create calls admin Supabase client (service role).

- **Requests & Data** (`/admin/requests`): browse analyses, filter by user/language, view raw token/pedagogy JSON, delete by ID.

- **Vocabulary** (`/admin/vocabulary`): paginated cross-user vocabulary table.

- **Feedback** (`/admin/feedback`): filter by category/status; resolve feedback; add admin notes.

- **Announcements** (`/admin/announcements`): CRUD for site-wide announcement banners surfaced in `AnnouncementBanner`.

- **Backend** (`/admin/backend`): proxy to Python `/health`; shows model status, cache stats, service health JSON.

  

Admin API routes under `/api/v1/admin/` (stats, users, analyses, vocabulary, feedback, health, announcements CRUD).

  

---

  

## 23. Backend NLP service (FastAPI)

  

### Endpoints

  

- `POST /analyze`: full sentence analysis pipeline (see below).

- `POST /alternatives`: grammar alternatives for a single token — person/number, tense, or mood conjugation variants with full rewritten sentences and English translations. Auth + rate-limiting enforced by the Next.js proxy route.

- `POST /writing-feedback`: LLM evaluation of student free-form writing; returns grade, overall feedback, per-error corrections, style suggestions. Used for assignment writing submissions.

- `POST /embed`: sentence embedding only.

- `GET /languages`: supported language metadata.

- `GET /health`: service + model health JSON.

- `GET/POST /cache/stats`, `/cache/flush`: analysis cache management.

  

### Analysis pipeline (`POST /analyze`)

  

1. Input sanitized, language validated.

2. **Redis cache** checked by SHA-256 hash of `(language, text)`; returns cached payload (without embedding) on hit.

3. **NLP**: spaCy preferred, Stanza fallback; Turkish and Italian always via Stanza. Italian is excluded from the spaCy path because `it_core_news_md` misidentifies capitalized sentence-initial verb forms (e.g. "Sto") as proper nouns (`PROPN`), producing null morphological features. Stanza's Italian model yields accurate POS tags and full `feats` for all sentence positions. Language-specific strategies (Romance, inflection, agglutinative) process tokens.

4. Per-token output: `id`, `text`, `lemma`, `upos`, `xpos`, `feats`, `head_id`, `deprel`, optional `segments`.

5. **Parallel enrichment**: LLM pedagogical JSON (translation, nuance, concepts, tips, errors) + sentence embedding.

6. **CEFR difficulty heuristic**: score and level (A1–C2) computed from token features.

7. **Rule-based grammar errors**: agreement and vowel-harmony checks produce `grammar_errors[{ word_id, word, error_type, message, rule }]`.

8. **Lemma frequency bands** (1–5): computed per token.

9. Cached result returned (embedding stripped from cache to save space; embedding included in live response).

  

### Services

  

| Module | Role |

|--------|------|

| `services/nlp.py` | Orchestrates all analysis stages |

| `services/spacy_manager.py` / `stanza_manager.py` | Model loading and lifecycle |

| `services/strategies/*` | Language-specific token processing (Romance, inflection, agglutinative) |

| `services/llm_service.py` | LLM calls for pedagogical data + writing feedback |

| `services/error_detector.py` | Rule-based grammar error detection |

| `services/difficulty_scorer.py` | CEFR readability heuristic |

| `services/embeddings.py` | Sentence encoding for semantic memory |

| `services/cache.py` | Redis result cache |

| `services/frequency.py` | Lemma frequency lookups |

  

---

  

## 24. Database capabilities

  

### Core tables

  

| Table | Key use |

|-------|---------|

| `users` | Profile, plan flags (`is_pro`), gamification fields, `learn_language`, `referred_by`, Stripe columns (future) |

| `analyses` | Text, language, token graph JSON, pedagogical JSON, difficulty, `embedding` (pgvector), `is_favorite` |

| `vocabulary` | Per-user words with SM-2 fields |

| `grammar_concept_reviews` | Per-user grammar concept SRS with SM-2 fields |

| `sentence_reviews` | Per-user sentence SRS — SM-2 scheduling for whole analyzed sentences |

| `daily_goals` | Per-user per-day progress rows |

| `sentence_feedback` | User ratings and comments on analyses |

| `achievements` / `user_achievements` | Achievement definitions and unlocks |

| `classes` | Teacher classes with join codes |

| `class_memberships` | Student↔class enrollment |

| `assignments` | Teacher-created tasks per class |

| `assignment_submissions` | Student writing submissions with AI feedback |

| `quiz_templates` / `quiz_questions` | Reusable quiz content |

| `quiz_sessions` | Live session state and room codes |

| `quiz_participants` / `quiz_answers` | Per-participant session data |

| `vocabulary_lists` / `vocabulary_list_items` | Teacher-curated vocab lists |

| `passages` | Teacher reading passages |

| `tests` / `test_questions` / `test_attempts` | Formal assessments |

| `email_subscribers` | Email captures from lead magnet form |

| `affiliate_applications` | Partner program applications |

| `announcements` | Site-wide banner content |

  

### Notable SQL functions

  

- **`match_analyses(embedding, user_id, language, exclude_id, threshold)`**: pgvector cosine similarity search over `analyses.embedding`; returns up to 4 similar prior sentences. Wired to "You've seen this before" strip in the Analyzer.

- **`handle_new_user()`**: trigger function that bootstraps `public.users` on auth signup; reads `full_name`, `avatar_url`, and `ref` from `raw_user_meta_data`.

  

### Security

  

- RLS enabled on all exposed tables.

- `email_subscribers` and `affiliate_applications`: service-role only (no authenticated-user reads).

- `analyses`: owner-only (`user_id = auth.uid()`).

- Teacher and class data: scoped to teacher ID or membership.

  

---

  

## 25. Client persistence and offline behavior

  

- **Zustand** (`grammario-storage` in `localStorage`): preferences (default language, daily goal target, show translations), onboarding flag, recent analyses (up to 10 session analyses kept in memory).

- **No offline analyzer**: all NLP runs on the Python backend; no local model in the web client.

  

---

  

## 26. Stripe billing

  

Stripe is fully integrated for both the Pro (individual) and Class (teacher) plans.

  

### Plans

  

| Plan | Stripe Price ID env var | Who can subscribe |

|------|------------------------|-------------------|

| Pro | `STRIPE_PRO_PRICE_ID` | Standard users (upgrades `is_pro = true`) |

| Class | `STRIPE_CLASS_PRICE_ID` | Educators (sets `account_type = "teacher"`, `is_pro = true`) |

  

### Checkout flow

  

- **`POST /api/v1/billing/checkout`**: creates a Stripe Checkout Session (subscription mode). Accepts `{ plan: "pro" | "class" }`. Uses `stripe_customer_id` from `users` if the user has previously subscribed; otherwise creates a new Stripe Customer. Returns `{ url }` for client-side redirect to Stripe-hosted checkout.

- On success, Stripe redirects back to `/app` (or configurable success URL).

  

### Customer portal

  

- **`POST /api/v1/billing/portal`**: creates a Stripe Customer Portal Session for existing subscribers. Returns `{ url }` for redirect. Used in Settings to let Pro/Class users manage their subscription (cancel, update payment, view invoices).

  

### Webhook sync (`POST /api/webhooks/stripe`)

  

Handles Stripe webhook events to keep `users` in sync:

  

- `checkout.session.completed` / `customer.subscription.updated`: sets `is_pro = true`, writes `stripe_customer_id`, `stripe_subscription_id`, `stripe_subscription_status`.

- `customer.subscription.deleted` / `customer.subscription.paused`: sets `is_pro = false`, `stripe_subscription_status = "canceled"`.

- Webhook signature verified with `STRIPE_WEBHOOK_SECRET`.

  

### Database columns (`users`)

  

`stripe_customer_id`, `stripe_subscription_id`, `stripe_subscription_status`, `stripe_price_id`, `stripe_current_period_end`.

  

---

  

## 27. Known gaps and partially wired features

  

1. **Achievements**: schema, seed data, and helpers complete; no end-to-end unlock trigger or achievement list UI in the current web app.

2. **Extra XP constants**: `FIRST_ANALYSIS_OF_DAY`, `STREAK_BONUS`, `ACHIEVEMENT_UNLOCK` defined in `db.ts` but not called in the analyze hot path.

3. **`enableSounds`**: Zustand default defined; no audio files or callers.

4. **Vocabulary `translation` field**: not populated on save-from-analyze (the backend doesn't return translations per token by default); shows blank in legacy vocabulary review unless filled by another path.

5. **Grammar Concept Review — no language gate**: review queue returns all queued concepts regardless of free-tier `learn_language`; behavior is intentional (review is not gated) but should be documented for mobile parity.

6. **Mastery map**: Italian-only; taxonomy and concept bodies complete only for `it`. Other language topics use outline stubs.

7. **Affiliate commission tracking**: `affiliate_applications` table stores applications; `users.referred_by` tracks attribution; no commission calculation, payout, or dashboard is implemented yet.

8. **Writing feedback for assignments**: backend `POST /writing-feedback` and student submission route are wired; teacher-side review of submissions shows text and AI feedback but no grade override UI.