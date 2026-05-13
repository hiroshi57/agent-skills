---
name: autoresearch
description: Auto-improves any skill prompt in a loop using Karpathy's autoresearch method. Runs, scores, tweaks, and repeats until quality hits 95%+. Use "autoresearch", "auto-improve skill", or "run autoresearch on [skill]" to trigger.
---

# Autoresearch

Auto-improves any skill prompt on autopilot. You write a 3–6 item yes/no checklist. The agent does the rest.

> **Observed: your Claude skills probably fail ~30% of the time and you don't even notice.**
> Autoresearch finds and fixes those failures autonomously.

Based on Andrej Karpathy's autoresearch method (github.com/karpathy/autoresearch) — adapted for Claude skills by Ole Lehmann.

**Karpathy's core insight:** give an agent a small but real optimization setup and let it experiment autonomously. Each experiment: modify one thing → measure with a fixed budget → keep or revert → repeat. The agent that ran overnight beats the engineer who tweaked manually for a week.

Applied to skills: the "training run" is one skill execution. The "val_bpb metric" is your binary eval score. The "train.py" is your SKILL.md. The loop is the same.

## System Architecture

```
┌─────────────────────┐
│       Inputs        │          Observed: skill fails
│  Target skill prompt│          ~30% unnoticed
│  Test inputs        │
│  Checklist          │
│  (3-6 yes/no checks)│
└──────────┬──────────┘
           │
           ▼
    ┌─────────────────┐     ┌──────────────────┐     ┌──────────────────┐
    │  Execute skill  │────▶│ Checklist scoring │────▶│ Failure analysis │
    │  on test set    │     │   (yes / no)      │     │                  │
    └─────────────────┘     └──────────────────┘     └────────┬─────────┘
           ▲                                                   │
           │              Autonomous                           ▼
           │         Improvement Loop              ┌──────────────────────┐
           │                                       │  Single prompt       │
    ┌──────┴───────────────────┐                   │  mutation            │
    │  Accept if score improves│◀──────────────────┘
    │  Rollback if not         │
    └──────────────────────────┘

Case study: Landing page skill — 56% → 92% (4 rounds; 3 kept; 1 undone)

Operational details:
  Baseline score shown first → Live dashboard in browser → Auto-refresh ~10s
  → Stop: 95%+ ×3 (or manual stop)

Outputs:
  Improved skill (saved separately) | Results log (scores per round) | Changelog + backup

"If you can score it, you can autoresearch it."
  e.g. Website speed: 1100ms → 67ms (67 rounds) | Cold outreach quality | Newsletter intros
```

## How It Works

1. **Baseline** — run the target skill once, score it against your binary evals
2. **Loop** — execute skill → checklist scoring → failure analysis → single mutation → accept or rollback
3. **Keep or revert** — score improves? keep it. Same or worse? rollback exactly.
4. **Repeat** — until the skill scores 95%+ three times in a row, or you stop it

Your only job: write the eval checklist. The agent handles everything else.

```
Your only job          Run skill once        The loop
Write a 3–6 item  →   Get baseline score  →  runs on its own
checklist              e.g. 56%               until 95%+ x3
                                              ↓
                       Real result        ←  Change one thing
                       56% → 92%             in the skill prompt
                       in 4 rounds           ↓
                                             Run + score
                                             against your checklist
                                             ↓
                                             Better? Keep it.
                                             Worse? Revert.
                                             ↓
                                             Improved skill
                                             + changelog saved
```

## Usage

**Trigger Phrases:**
- `autoresearch on my [skill name] skill`
- `run autoresearch on [skill]`
- `auto-improve [skill]`

**Commands (`/ar:` namespace):**

| Command | Description |
|---------|-------------|
| `/ar:setup [skill]` | Initialize a new autoresearch experiment for the target skill |
| `/ar:run` | Execute one single iteration (modify → score → keep/revert) |
| `/ar:loop` | Start the autonomous loop (runs until 95%+ ×3 or manual stop) |
| `/ar:status` | Show progress dashboard: current score, round count, recent changes |
| `/ar:resume` | Resume an interrupted experiment from the last saved state |

**Design principle: one change per experiment. Measure everything. Accumulate improvements.**
The agent must never modify the eval checklist during a run — only the skill prompt.

## Writing Good Evals

Evals must be **binary yes/no** — not scales, not ratings. Binary evals give a reliable signal every time.

**The validation test for each eval question:**
1. Can two agents answer it the same way independently?
2. Can a skill game it by memorizing the pattern? (if yes, rephrase)
3. Does it actually matter to output quality? (if no, cut it)

**Good eval examples by domain:**

Landing page copy:
- Does the headline include a specific number or result? (no vague promises)
- Is the copy free of buzzwords: revolutionary, synergy, game-changing, next-level, cutting-edge?
- Does the CTA use a specific verb phrase? (not "Learn More" or "Click Here")
- Does the first line call out a specific pain point? (not a generic opener)
- Is the total copy under 150 words?

Cold outreach:
- Does it mention the prospect's company by name?
- Is it under 75 words?
- Does it end with a specific question?

Code review:
- Does it identify at least one concrete security concern?
- Does it include a suggested fix for every flagged issue?
- Is every suggestion tied to a specific line number?

**Pitfalls to avoid:**
- More than 6 evals — skill starts gaming the checklist
- Overlapping evals — "is it clear?" and "is it easy to read?" measure the same thing
- Unmeasurable standards — "is it engaging?" has no consistent answer
- Scales disguised as binary — "is quality above 7/10?" is still a scale

**Sweet spot: 3–6 evals. Fewer is usually better.**

## Output Files

After the run completes:

| File | Contents |
|------|----------|
| `[skill]-improved.md` | The improved skill prompt (original stays untouched) |
| `[skill]-backup.md` | Backup of the original |
| `[skill]-results.log` | Score for every round |
| `[skill]-changelog.md` | Every change tried, why, and whether it helped |

The changelog is the most valuable output. It's a complete record of what works for that specific skill. When smarter models arrive, hand them the changelog and they pick up exactly where this run left off.

## Detailed Instructions

You are an autonomous skill optimizer. Improve a target skill prompt through iterative testing — one change at a time.

### Step 1 — Gather context

Ask the user three things:
1. Which skill to optimize (path to its `SKILL.md`)
2. What test input(s) to use (e.g. "write landing page copy for an AI productivity tool")
3. Their eval checklist — or help them write one

**If they need help with the checklist:**
- Ask: what does a bad output look like? (get specific examples)
- Ask: what does a good output look like?
- Ask: do you have a style guide or reference?
- Convert answers into 3–6 binary yes/no questions
- Apply the three validation tests to each question
- Confirm the final list before proceeding

### Step 2 — Read the skill

Read the target skill's `SKILL.md` in full. Note:
- Its current instructions and rules
- Sections most likely responsible for the failing eval items
- Sections to preserve (don't touch what's working)

### Step 3 — Build the eval suite

For each eval question, write a sub-prompt that checks exactly that one thing.

Example:
```
eval: "Does the headline include a specific number or result?"
check: Read the headline only. Does it contain a number (e.g. "10x", "56%", "3 steps") or a specific measurable result? Answer: YES or NO.
```

Run each check independently. Total score = sum of YES answers / total evals.

### Step 4 — Generate a dashboard (optional but recommended)

Create a simple HTML file at `autoresearch-dashboard.html` that shows:
- Current round number
- Score chart (score per round, plotted as a line)
- Pass/fail breakdown for each eval item
- Log of every change tried
- Auto-refreshes every 10 seconds from a `autoresearch-state.json` sidecar file

Update `autoresearch-state.json` after each round. This lets the user watch progress without you reporting every round manually.

### Step 5 — Establish baseline

1. Run the skill on the test input(s)
2. Score against every eval (record YES/NO for each)
3. Calculate total score: X/Y (XX%)
4. Record: `round 0 | score: X/Y | [pass/fail per item]`
5. Report baseline to the user before entering the loop

### Step 6 — Run the experiment loop

Repeat until stopping conditions are met:

**Analyze failures**
Look at which eval items failed. Pick the one that failed most consistently or is most fixable with a prompt change.

**Make one change**
One change only per round. Candidates (in order of typical impact):
- Add a specific rule targeting the failure: `"Your headline MUST include a specific number or result. Never use vague promises."`
- Add a banned word/pattern list: `"NEVER use: revolutionary, cutting-edge, synergy, next-level, game-changing"`
- Insert a worked example showing the correct behavior with the failure pattern highlighted
- Tighten a structural constraint (word count, format, required sections)
- Remove a rule that may be causing the failure mode

Record what you changed and why **before** running.

**Run and score**
Run the skill on the same test input(s) with the modified prompt. Score against all evals.

**Keep or revert**
- Score went up → keep the change
- Score same or lower → revert exactly (restore original text)
- Change helped one eval but hurt another → revert (net neutral is a regression)

**Log the round**
```
round N | score: X/Y (XX%) | change: [what] | result: kept/reverted | reason: [score delta + why]
```

Update `autoresearch-state.json` if dashboard is running.

### Step 7 — Deliver results

**Stopping conditions:**
- Score hits 95%+ three consecutive rounds → success
- User explicitly stops → stop immediately
- 20+ rounds with no score improvement → stop, report stall

**On completion:**

1. Save improved skill as `[skill-name]-improved.md`
2. Save original as `[skill-name]-backup.md`
3. Write `[skill-name]-results.log`:
   ```
   round 0 | score: 4/7 (56%)
   round 1 | score: 5/7 (71%) | kept: added headline number rule
   round 2 | score: 6/7 (85%) | kept: added buzzword ban list
   ...
   ```
4. Write `[skill-name]-changelog.md`:
   ```markdown
   # Autoresearch Changelog — [skill name]

   ## Summary
   - Starting score: X/Y (XX%)
   - Final score: X/Y (XX%)
   - Rounds run: N
   - Changes kept: N | Changes reverted: N

   ## Changes Kept

   ### Round N — [change description]
   **What changed:** [exact text added/removed/modified]
   **Why tried:** [which eval was failing and how often]
   **Score impact:** XX% → XX%
   **Why it worked:** [brief explanation]

   ## Changes Reverted

   ### Round N — [change description]
   **What tried:** [exact change]
   **Score impact:** XX% → XX% (no improvement / regression)
   **Why reverted:** [explanation]
   ```
5. Report to user: starting score → final score, rounds run, changes kept, file paths

### On `/ar:resume`

Save experiment state to `autoresearch-state.json` after every round so the experiment can be resumed:

```json
{
  "skill": "path/to/SKILL.md",
  "test_input": "...",
  "evals": ["eval 1", "eval 2", "..."],
  "round": 12,
  "best_score": "6/7",
  "current_score": "5/7",
  "consecutive_95": 0,
  "history": [
    { "round": 1, "score": "4/7", "change": "...", "result": "kept" }
  ]
}
```

On resume: read state file, restore skill to `current_skill_prompt`, report where the experiment left off, then continue the loop.

### Edge cases

**Non-deterministic skill (outputs vary wildly each run):**
Warn the user. Suggest running each test 3x and averaging, or fixing the skill's temperature/structure before autoresearching.

**All evals pass from round 0:**
The evals are too easy. Tell the user and ask for harder questions.

**Same eval fails every round after 5 attempts:**
Flag it. The fix may require a structural change (e.g. adding a worked example vs. adding a rule). Or the eval itself may be unmeasurable — validate it.

**Skill file is very long (>200 lines):**
Only modify sections relevant to failing evals. Preserve everything else exactly.

## Anti-patterns

- Making more than one change per round — you can't isolate which change helped
- Changing the test input between rounds — scores become incomparable
- Adding checklist-specific language to the skill prompt (gaming the evals)
- Keeping a change that helped one eval but hurt another
- Vague eval questions that two agents would answer differently

## Red Flags

- Fewer than 3 or more than 6 eval items
- Eval items use words like "good", "clear", "engaging" without a specific measurable criterion
- Score jumps more than 40% in a single round — likely a fluke, verify manually
- 10+ rounds with score stuck at the same level
- The improved skill passes evals but produces worse real-world output (the evals are gaming you — revise them)

## Verification

After autoresearch completes:

- [ ] Backup of original skill saved
- [ ] Improved skill saved as separate file (original untouched)
- [ ] Results log shows every round's score with kept/reverted label
- [ ] Changelog explains every change tried, why, and outcome
- [ ] Final score verified by running the improved skill manually on a fresh input
- [ ] User reviewed changelog and confirmed changes make sense
- [ ] Evals validated: two agents would answer each one the same way
