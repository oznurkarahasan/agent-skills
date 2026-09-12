---
name: intent-discovery
description: Extracts what the user actually wants before any code or plan is written. Uses a one-question-at-a-time interview with explicit hypotheses until ~95% confidence is reached.
---

# Intent Discovery (Interview Me)

The cheapest moment to find the gap between what people ask for and what they actually want is before any code or spec exists. Interview the user one question at a time until you can predict what they will say.

## 1. The Interview Process

### Fast-Track (Escape Hatch)
If the user's initial message already contains the Outcome, User, Success criteria, and Constraints clearly, skip the interview step. Present the 6-line confirmation text from Step 4 directly and wait for approval.

### Step 1: Hypothesize
Before asking anything, write down your current best read of what the user wants in one sentence, plus an honest confidence number (0–100%):
HYPOTHESIS: You want a way to answer "how are we doing?" in standup, and "dashboard" came to mind.
CONFIDENCE: ~30% — missing: who it's for, what "metrics" means, what success looks like.


### Step 2: Ask ONE Question at a time
Do not batch questions. The user's energy for thinking carefully is finite.
Format:
Q: 
GUESS: <your hypothesis for the answer, and why>

*Why attach a guess?* The user reacts faster to a wrong guess than generating an answer from scratch.

### Step 3: Probe "Should Want" vs "Actually Want"
If the user pattern-matches best-practice talk ("it must be scalable", "clean architecture") without specifics, ask:
> *"If you didn't have to justify this to anyone, what would you actually want?"*

### Step 4: Restate Intent
When confidence is ~95%, restate the intent strictly using this format:
Here is what I now think you want:

Outcome:

User:         <one line — who benefits>

Why now:      <one line — what changed>

Success:      <one line — how we know it worked>

Constraint:   <one line — the binding limit>

Out of scope: <one line — what we're explicitly NOT doing>

Yes / no / refine?

Including "Out of scope" is non-negotiable.

### Step 5: Confirm
You need an explicit "yes."
- "Whatever you think is best" → NOT yes (delegation). Re-ask with concrete options.
- "Sounds good" → Ambiguous. Ask what to refine.

## 2. Red Flags
- Three or more questions in a single message (Batching).
- A question without your hypothesis attached.
- Producing a spec, plan, or task list before the user has explicitly confirmed your restate.
- Saving an intent document before confirmation.