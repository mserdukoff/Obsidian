# How to Use This Vault With AI

## Setup
Open your vault as the project root in Cursor or run `claude` from:
~/Desktop/Obsidian/Main

The AI reads CLAUDE.md automatically when the vault is open.
Every skill is a markdown file in 05-CLAUDE/skills/ — you trigger
them by saying their trigger phrase in plain English.

---

## Daily Workflow (20 min)

### Morning
1. Dump anything from yesterday into 00-INBOX/ or 06-DUMP/
   - 00-INBOX: quick text thoughts, one idea per file
   - 06-DUMP: anything else — articles, screenshots, links,
     transcripts, code snippets, PDFs
2. Say: `process my inbox`
3. Say: `process the dump`
4. Say: `are there any connections between today's captures
   and anything recent in my vault?`
5. If a connection surprises you, say: `generate a brief for [topic]`

### Weekly (Sunday)
1. Say: `run connection session`
2. Review what lands in 02-CONNECTIONS/
3. Pick the two strongest, say: `generate a brief for [topic]`
4. You start Monday with two content pieces ready to write

### Monthly
1. Say: `run monthly analysis`
2. Say: `run vault health`
3. Review proposed vault changes, approve what makes sense

---

## Skill Reference

### `process my inbox`
Reads 00-INBOX/, sharpens each note to one punchy sentence,
adds three tags, files in correct 01-CAPTURES/ subfolder.
Use for: quick text captures you typed yourself.

### `process the dump`
Reads 06-DUMP/, classifies every file, extracts atomic ideas,
writes notes, archives originals. Never deletes source files.
Use for: articles, screenshots, PDFs, links, voice transcripts,
code snippets, anything messy.

### `run connection session` / `weekly connections`
Reads all captures from the last 7 days across all subfolders,
finds non-obvious relationships, creates notes in 02-CONNECTIONS/.
Minimum 3 connections, maximum 5. Obvious connections don't qualify.

### `generate a brief for [topic]`
Creates a structured content brief with: one thing, proof,
reader transformation, three hooks (ranked), three closers (ranked).
Saves to 03-BRIEFS/ tagged #ready-to-write.

### `write the brief for [topic]`
Takes an approved brief from 03-BRIEFS/ and writes the full piece
in your exact voice. Saves draft next to the source brief
tagged #written.

### `knowledge session`
Scans all notes tagged #knowledge, creates stubs for concepts
that appear across notes but have no dedicated note, proposes
links between related concepts.

### `what do I know about [topic]`
Searches the entire vault, synthesizes what your notes
collectively say, identifies gaps, suggests whether a new
knowledge note should be created.

### `run vault health`
Five checks: misplaced notes, orphaned notes, near-duplicates,
stale inbox, brief graveyard. Always shows proposed changes
and waits for your approval before touching anything.

### `run monthly analysis`
Reads everything in 04-PUBLISHED/, tells you which topics and
hook formats performed best, suggests untried angles.
Only uses your actual data — no generic advice.

---

## Folder Reference

| Folder | What goes here |
|--------|---------------|
| 00-INBOX/ | Quick text captures, one idea per file |
| 01-CAPTURES/observations/ | Things you noticed, raw |
| 01-CAPTURES/reactions/ | Gut responses to things you read or experienced |
| 01-CAPTURES/patterns/ | Same principle across domains + all #knowledge notes |
| 01-CAPTURES/questions/ | Things you genuinely don't know yet |
| 01-CAPTURES/numbers/ | Real data points with specific figures |
| 02-CONNECTIONS/ | Synthesized insights from two or more notes |
| 03-BRIEFS/ | Content ready to write |
| 04-PUBLISHED/ | Archived content with performance data |
| 05-CLAUDE/ | AI config files — don't dump notes here |
| 06-DUMP/ | Drop zone for anything unstructured |
| 06-DUMP/archive/ | Originals after processing — don't touch |
| Grammario/ | All Grammario project notes |
| Grammario/Marketing/ | Four-phase marketing playbook |
| Hime/ | Company-level notes |
| Wheelbase/ | Wheelbase product notes |
| Career and Personal Work/ | Portfolio, personal growth |
| Archive/ | Old documents — do not modify |

---

## Rules the AI Follows
- Never touches .env files
- Never modifies Archive/, Assets/, Home.md, or Recurring Tasks.md
  without explicit instruction
- Never reorganizes existing project folders without approval
- Never turns engineering/personal notes into content without asking
- Always shows proposed vault changes before executing them
- Splits dump files into atomic notes — never summarizes into one blob

---

## Adding a New Project
1. Create a top-level folder: /ProjectName/
2. Tell the AI: "I have a new project called [name], add it to
   your context"
3. The AI will suggest an update to CLAUDE.md — approve it and
   it will update the file itself

---

## Tips
- The inbox is for text you write yourself. The dump is for
  everything else. Don't mix them.
- One idea per inbox note. Atomic captures connect better than
  dense ones.
- The monthly analysis only gets smarter if you actually log
  performance data in 04-PUBLISHED/. Spend two minutes doing
  this after anything performs well.
- If a vault health session surfaces orphaned notes, don't just
  archive them — ask the AI what they might connect to first.