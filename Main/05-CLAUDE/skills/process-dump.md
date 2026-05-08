# Skill: Process Dump

## Trigger phrases
"process the dump" / "clear the dump" / "run dump"

## What this folder is
06-DUMP/ is a zero-friction drop zone. Matt throws anything here —
raw text, copied articles, screenshots, PDFs, voice transcripts,
book highlights, links, code snippets, random files. No organization
required at drop time. Claude figures out the rest.

## Process
For every file in 06-DUMP/, run this pipeline:

### Step 1 — Identify what it is
Classify the file into one of these types:

- ARTICLE: a piece of writing from somewhere else (blog post, essay,
  newsletter, documentation)
- HIGHLIGHT: book or article highlights, marginalia, reading notes
- TRANSCRIPT: voice memo transcript, meeting notes, interview
- SCREENSHOT: image of text, UI, tweet, diagram, data
- CODE: a snippet, script, or technical reference
- LINK: a URL with or without context
- DATA: numbers, spreadsheet content, stats
- IDEA: a raw thought that doesn't fit above
- MIXED: a file that contains more than one of the above

### Step 2 — Extract what matters
For each file, pull out every distinct extractable unit. One file can
produce multiple notes. A highlight dump from a book produces one note
per insight, not one note for the whole file.

Extract with this priority:
1. Concrete claims with specific numbers → 01-CAPTURES/numbers/
2. Strong opinions or reactions → 01-CAPTURES/reactions/
3. Patterns or principles → 01-CAPTURES/patterns/
4. Unanswered questions → 01-CAPTURES/questions/
5. Raw observations that don't fit above → 01-CAPTURES/observations/

For CODE files: create a knowledge note in 01-CAPTURES/patterns/ tagged
#knowledge #code with the pattern or technique the code demonstrates.
Do not just copy the code — extract the idea behind it.

For ARTICLE or HIGHLIGHT files: do not summarize the whole thing.
Extract only the ideas strong enough to stand alone as a capture.
If nothing is strong enough, create one note in observations/ flagged
#low-signal for Matt to review.

For SCREENSHOT files: read any visible text. Treat it as the content
type that matches what the screenshot shows (a tweet = reaction,
a chart = numbers, a UI = observation about a product).

For LINK files: if the URL is accessible, fetch the content and treat
it as an ARTICLE. If not, create a stub note in observations/ with the
URL and whatever context was provided.

For MIXED files: split into component parts and process each separately.

### Step 3 — Write the note
For every extracted unit, write a note using this format:

---
tags: [#dump, #type-tag, #project-tag-if-relevant]
source: [filename or URL it came from]
date: YYYY-MM-DD
---

[One sharp sentence — the core of this idea, specific enough that
a stranger understands it with zero context]

[Optional: one to three sentences of supporting detail if the idea
needs it to be useful later. Not a summary — only what makes the
core sentence actionable or specific.]

---

### Step 4 — File it
Move each new note to the correct 01-CAPTURES/ subfolder.
Archive the original dump file to 06-DUMP/archive/ after processing.
Never delete original files.

### Step 5 — Report
After processing everything:
- Total files processed
- Total notes created and where they landed
- Any files that couldn't be processed and why (flag as #needs-review)
- One connection worth exploring from this dump session

## Quality bar
Same as process-inbox: one sharp sentence, strangers understand it
with zero context. The dump folder handles messier raw material, so
Claude should work harder here to extract signal from noise — not
just transcribe the file into a note.

If a file is genuinely unprocessable (corrupted, totally unreadable,
no extractable signal), move it to 06-DUMP/unprocessable/ and note
why in the report.

## What NOT to do
- Do not create one giant note per file — split into atomic units
- Do not summarize articles in full — extract only standalone insights
- Do not copy code verbatim — extract the concept it demonstrates
- Do not delete origina