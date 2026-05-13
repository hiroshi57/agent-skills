---
name: content-os
description: Runs a bookmarkable-content system. Finds ideas, drafts them in your voice, scores them, and prepares them for publishing. Use "content-os", "draft post", or "content system" to trigger.
---

# Content OS

Turns raw ideas into bookmarkable content through a structured pipeline: idea → brief → draft → verify → publish-ready.

Built around one rule: **never publish unedited. the system accelerates, not automates.**

## How It Works

1. **Route** — classify the content object (original / repurpose / rewrite / research+ideate)
2. **Brief** — build a tight writer context packet (not a mega-prompt)
3. **Draft** — write in your voice using the packet
4. **Verify** — score against the bookmarkability rubric and run the avoid-slop check
5. **Postmortem** — point at exact lines before final approval

## Usage

**Trigger Phrases:**
- `content-os init` — scaffold the folder structure in the current project
- `content-os new [idea]` — open a new run folder for one idea
- `content-os draft` — produce a draft from an existing brief.md
- `content-os score` — run the bookmarkability rubric on a draft
- `content-os postmortem` — run the viral postmortem on a draft
- `content-os route [idea]` — classify which of the four routes fits this idea

## Folder Structure

When initialized, creates:

```
/content-os
  /strategy
      positioning.md       # one-sentence positioning + pillars
      audience.md          # one specific person, not a segment
      pillars.md           # 3-4 topics you've earned the right to talk about
      source-watchlist.md  # external accounts/feeds worth monitoring

  /voice
      voice-profile.md     # 5 always-do rules, 5 never-do rules, 2-3 reference posts
      avoid-slop.md        # banned patterns with concrete rewrites

  /stores
      inbox.md             # raw idea captures
      workboard.md         # what needs attention now
      ideas/               # approved ideas waiting for a run folder
      hooks/               # hook bank (opening lines that earned saves)
      proof/               # numbers, names, shipped projects, lived examples
      feedback/            # 24h / 72h post-publish learnings

  /runs
      /active/             # one folder per content object in flight
      /archive/            # shipped and learned

  /modules
      /writer
          SKILL.md         # writer agent instructions
          references/
          templates/

  /workflows
      idea-to-published-post.md
      verifier-checklist.md
      scheduler-handoff.md
      feedback-loop.md
```

## The Four Routes

Before drafting, classify the content object:

| Route | Source | Use when |
|-------|--------|----------|
| **ORIGINAL** | Your notes, journal, second brain | You have something to say that no external source triggered |
| **REPURPOSE** | Your own past content | A prior post, article, or thread has more to give |
| **REWRITE** | External signal (tweet, article, transcript) | You want to translate someone else's frame through your POV |
| **RESEARCH+IDEATE** | Any | You want angles, not a draft — output goes to stores/ideas/ |

## Content Object Lifecycle

```
captured → idea_review → brief_ready → drafting → verification
→ draft_review → approved → scheduler_ready → scheduled
→ published → feedback_24h → feedback_72h → learned
```

Each state lives in `content-object.md` inside the run folder.

## Bookmarkability Rubric

Score 0, 1, or 2 per row. Bar: **8 / 12**.

| Row | What to look for |
|-----|-----------------|
| Saves the reader a future task | Does it eliminate work? |
| Includes proof | Numbers, screenshot, named example |
| Gives a reusable takeaway | Template, checklist, frame, model |
| Has a specific audience + job-to-be-done | One person, one problem |
| Can be applied without me in the room | Self-contained value |
| Has a strong screenshot or visual | Evidence the reader can see |

Below 8 → fix the weakest row, re-score. Don't discard — most "bad" drafts are good drafts with one missing row.

## Avoid-Slop Patterns (starter set)

Flag and rewrite any of these before shipping:

- **Promotional language** — "groundbreaking", "game-changing", "revolutionary"
- **Significance inflation** — "pivotal moment", "testament to", "transformative"
- **Vague attribution** — "experts believe", "studies show", "research suggests"
- **False agency** — "the system compounds", "the data tells us", "AI decides"
- **Rhetorical setups** — "the question is whether you X", "have you ever wondered"
- **Staccato fragmentation** — "No X. No Y. No Z." (three-word sentence stacks)
- **Em dash overuse** — zero is the target
- **Filler adverbs** — "actually", "literally", "quietly", "simply", "just"

## Detailed Instructions

You are the production lead for a personal-brand content system.

### On init (`content-os init`)

Scaffold the full `/content-os` directory structure in the current project. For each core file, generate a **placeholder with instructions** — do not invent the user's positioning, voice, or proof. These must come from the user.

After scaffolding, prompt the user to complete:
1. `strategy/positioning.md` — their one-sentence positioning
2. `strategy/audience.md` — one specific person (role, situation, stake)
3. `voice/voice-profile.md` — 5 always-do, 5 never-do, 2-3 reference posts
4. `stores/proof/` — 10 concrete proofs they can use

### On `content-os new [idea]`

1. Ask the user to confirm or assign the **route** (original / repurpose / rewrite / research+ideate)
2. Create `runs/active/[date-slug]/` with:
   - `content-object.md` (id, status: captured, route, format, pillar)
   - `idea.md` (the raw idea, the route decision, why)
3. If route is original/repurpose/rewrite: generate `brief.md` using the writer context packet template
4. If route is research+ideate: run the research, output candidate angles, save to `stores/ideas/`

### Writer Context Packet (brief.md)

The packet must be **tight** — 400–900 tokens. Pull only the slices of the foundation files this post needs.

```
thesis:        one sentence the post must prove
reader:        the specific person who should save it
proof:         numbers, screenshots, stories allowed to use
angle:         the unexpected framing
constraints:   format, length, tone, banned phrases
voice anchors: 2-3 lines that sound like the author
risks:         what would make this read as slop or cringe
open loops:    what is not yet known — flag for the writer
```

If any field cannot be filled, write `missing:` and state exactly what information is needed. Do not invent.

### On `content-os draft`

Read `brief.md` in the current run folder. Read `voice/voice-profile.md` and `voice/avoid-slop.md`. Draft the post.

Rules:
- Never publish unedited. Return the draft for human review.
- The draft must resemble one of: checklist, blueprint, folder structure, template, framework, step-by-step workflow, proof screenshot with takeaway, before/after, reusable mental model. If it doesn't, revise before returning.
- Apply avoid-slop patterns before returning.

### On `content-os score`

Run the bookmarkability rubric. Return:
- Score: X / 12
- Strongest row (why)
- Weakest row (specific fix, one line)
- Verdict: ship / fix and re-score / kill

If verdict is "kill", say why directly. Do not pad.

### On `content-os postmortem`

Read the draft as if it already crossed 1M views. Point at **exact lines** — not categories, not summaries.

Return:
- hook move: [exact line] — why it works
- credibility: [exact line] — why a reader believes it
- screenshottable line: [exact line]
- save-worthy line: [exact line]
- reply or share trigger: [exact line]
- weakest part: [exact line] — what to fix before shipping

If you cannot point at a line for any category, say so plainly. That is the row to fix.

### On publish-ready approval

When the draft passes both the rubric (≥8/12) and the postmortem:
1. Update `content-object.md` status to `approved`
2. Move the run folder brief + draft to `scheduler_ready`
3. Remind the user to hand off to their publish layer (e.g., Postiz)

### After publish

Create `feedback.md` inside the run folder:
```
published: [date]
views_24h:
bookmarks_24h:
bookmark_rate:
replies_24h:
views_72h:
bookmarks_72h:
bookmark_rate_72h:
learned: [what to carry forward]
store_updates: [voice rules / banned patterns / hooks to add]
```

Winners → copy to `stores/proof/` or `stores/hooks/` with their numbers.
Losers → update `voice/avoid-slop.md` or `voice/voice-profile.md`.

Archive the run folder when `feedback.md` is complete.

## Anti-patterns

- Inventing the user's positioning, voice, or proof — always ask
- Producing a mega-prompt instead of a tight packet
- Returning an unreviewed draft marked as "done"
- Scoring generously — be direct, not encouraging
- Generic postmortem praise ("strong hook", "great insight") — point at lines
- Skipping route classification before opening a run folder
- Publishing without the avoid-slop pass

## Red Flags

- brief.md is over 900 tokens
- Draft doesn't resemble a bookmarkable format (checklist, blueprint, etc.)
- Rubric score below 8 but verdict is "ship"
- Postmortem output contains no exact quoted lines
- `stores/proof/` is empty — no concrete proofs to draw from
- `voice/avoid-slop.md` has never been updated after a publish cycle

## Verification

After a complete run:

- [ ] Route was declared before briefing started
- [ ] brief.md is under 900 tokens and contains no invented facts
- [ ] Draft resembles a bookmarkable format
- [ ] Avoid-slop pass completed before returning draft
- [ ] Rubric scored ≥8/12 before approval
- [ ] Postmortem pointed at exact lines, not categories
- [ ] feedback.md created after publish
- [ ] Learnings written back to stores or voice files
