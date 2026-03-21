---
name: tercet
description: >
  Use when a user needs to think through a plan, decision, system change, or
  process design that involves tacit knowledge, competing forces, or tradeoffs
  that can't be resolved from documentation alone. Triggers include: "help me
  plan", "help me think through", "what am I missing", "what questions should
  I be asking", or any request where the user provides rich situational context
  and the path forward isn't obvious. Works for software features, organizational
  processes, strategic decisions, or any domain where understanding emerges from
  interaction rather than information. Do NOT use for straightforward tasks
  where the path is clear.
---

# Tercet — Planning Through Dialectic

## Core Idea

Most AI planning tools treat context as input and produce plans as output.
Tercet does the opposite: it takes an incomplete brief, runs a dialectical
loop that either strengthens the brief or identifies what CAN'T be
strengthened without the human — and returns exactly that.

The output is not a plan. It's the questions the system couldn't answer
on its own.

## Architecture

The brief is the living artifact. There is no conversation history to
manage. Each cycle either enriches the brief or surfaces an unresolvable
question that goes back to the human.

```
                    ┌─────────────┐
                    │    USER     │
                    │  provides   │
                    │   brief     │
                    └──────┬──────┘
                           │
                           ▼
               ┌───────────────────────┐
               │   CLAUDE (Driver)     │
               │                       │
               │  Holds the brief.     │
               │  Sends it through     │
               │  the loop. Collects   │◄──────────────────┐
               │  unresolvable         │                   │
               │  questions. Returns   │                   │
               │  to user when the     │                   │
               │  loop exhausts.       │                   │
               └───────────┬───────────┘                   │
                           │                               │
                           ▼                               │
               ┌───────────────────────┐                   │
               │    CONSTRUCTOR        │                   │
               │                       │                   │
               │  Receives brief.      │                   │
               │  Asks: "Is this       │                   │
               │  enough to act on?"   │                   │
               │                       │                   │
               │  If NO: searches      │                   │
               │  MCPs, enriches the   │                   │
               │  brief, re-evaluates. │                   │
               │                       │                   │
               │  If YES: declares     │                   │
               │  confidence and       │                   │
               │  passes enriched      │                   │
               │  brief forward.       │                   │
               └───────────┬───────────┘                   │
                           │                               │
                           ▼                               │
               ┌───────────────────────┐                   │
               │     EXAMINER          │                   │
               │                       │                   │
               │  Receives enriched    │                   │
               │  brief. Doesn't       │                   │
               │  trust it.            │                   │
               │                       │                   │
               │  Poses challenges:    │                   │
               │  counter-arguments,   │                   │
               │  questions about      │                   │
               │  evidence staleness,  │                   │
               │  hidden forces,       │                   │
               │  human factors.       │                   │
               └───────────┬───────────┘                   │
                           │                               │
                           ▼                               │
               ┌───────────────────────┐                   │
               │    CONSTRUCTOR        │                   │
               │    (responds)         │                   │
               │                       │                   │
               │  For each challenge:  │                   │
               │                       │                   │
               │  CAN defend:          │                   │
               │  → searches, finds    │                   │
               │    evidence, updates  │────► brief grows  │
               │    brief              │      loop continues
               │                       │                   │
               │  CANNOT defend:       │                   │
               │  → returns the        │                   │
               │    unresolvable       │────► to Driver ───┘
               │    question           │
               └───────────────────────┘

  When the loop exhausts (Constructor confident + Examiner satisfied,
  OR enough unresolvable questions accumulated):

               ┌───────────────────────┐
               │   CLAUDE (Driver)     │
               │                       │
               │  Presents to user:    │
               │  • The enriched brief │
               │  • Unresolvable Qs    │
               │  • Suggested actions  │
               └───────────────────────┘
```

## The Living Brief

The brief is the only artifact. It starts sparse and grows through the loop.
It has a consistent structure:

```markdown
## Intent
[What the user wants to do and why]

## Known Context
[What the user provided + what the Constructor discovered]
[Each addition is tagged with its source]

## Assumptions
[Things treated as true, tagged as:]
[  VERIFIED — confirmed by retrieved evidence (source cited)]
[  INFERRED — reasonable but unconfirmed]
[  CONTESTED — Examiner challenged, Constructor couldn't fully defend]

## Open Questions
[Things the Constructor searched for and couldn't resolve]

## Constraints
[Hard boundaries: time, budget, team, technical, political]

## Proposed Approach
[The Constructor's current best proposal, updated each cycle]
```

When the Constructor enriches the brief, it adds to Known Context and may
move items from Assumptions:INFERRED to Assumptions:VERIFIED. When the
Examiner challenges something the Constructor can't defend, it moves to
Assumptions:CONTESTED or becomes an Open Question.

The brief at the end of the loop is itself a deliverable — it's a richer,
more honest version of what the user started with.

## The Core Asymmetry

**The Constructor** is RESOURCEFUL and OPTIMISTIC. Its premise: "Given
this brief and everything I can retrieve from available tools, I have
what I need to build a solution." It actively pulls from MCPs — Jira,
Confluence, Slack, monitoring, code repos, web search — to fill gaps
and strengthen its case. It trusts what it finds.

**The Examiner** is SKEPTICAL and has NO TOOLS. It works only from the
brief the Constructor presents. Its premise: "The documented state and
the actual state are never the same. Show me why I should believe this."
It questions the reliability of evidence, probes for hidden forces, and
asks questions that retrieval systems can't answer.

The Examiner has no inherent belief that the Constructor knows everything.
Documentation lies. Dashboards show what they're configured to show.
The map is never the territory.

## Your Role as Driver

You (Claude) hold the loop. You are not a passive relay — you make
judgment calls:

### Starting the Loop

1. Collect the user's initial brief using `templates/intake.md` as guide
2. Structure it into the Living Brief format
3. Send it to the Constructor

### During the Loop

- After each Constructor pass: check if the brief actually got richer
  or if the Constructor is spinning (searching the same sources,
  restating the same evidence). If spinning, push to the Examiner.
- After each Examiner pass: check if challenges are substantive or
  generic. If generic ("have you considered risks?"), push for
  specificity before sending back to Constructor.
- When the Constructor returns an unresolvable question: evaluate it.
  Is it genuinely unresolvable (requires human tacit knowledge), or
  is the Constructor giving up too easily? If the latter, push back.
  If the former, add it to your collection.

### Termination Conditions

The loop ends when ANY of these are true:

1. **Constructor confident + Examiner satisfied.** The brief is robust
   enough that the Examiner can't find substantive challenges. This is
   rare but possible for well-documented domains.

2. **Unresolvable questions accumulated.** The Constructor has hit enough
   walls that continuing without the human is unproductive. This is the
   most common termination — and the most valuable, because those
   questions ARE the output.

3. **Diminishing returns.** The brief isn't getting meaningfully richer
   and the Examiner's challenges aren't surfacing new angles. You (the
   Driver) make this call.

4. **Safety valve.** Cap at ~10 Constructor/Examiner exchanges to prevent
   infinite loops. If you hit this, synthesize what you have.

### Returning to the User

Present:
1. **The enriched brief** — what they started with plus everything the
   Constructor discovered. This alone has value.
2. **Unresolvable questions** — ranked by impact. For each:
   - Why the Constructor couldn't answer it (what it searched, what it found)
   - Why the Examiner insists it matters
   - Who or what might hold the answer
3. **Contested assumptions** — things in the brief that the Examiner
   challenged and the Constructor couldn't fully defend
4. **Suggested conversations** — specific people to talk to, specific
   questions to ask

If the user answers some questions, update the brief and run the loop
again. The process is naturally iterative.

## Setting Up API Calls

### Constructor Call

```
System: [constructor.py SYSTEM_PROMPT]
User: [The current Living Brief + phase instructions]
Tools: [All available MCPs — Jira, Confluence, Slack, etc. + web search]
```

The Constructor gets tool access. It should use them aggressively.

### Examiner Call

```
System: [examiner.py SYSTEM_PROMPT]
User: [The enriched Living Brief as returned by Constructor]
Tools: NONE
```

The Examiner gets NO tool access. It works only from what the Constructor
presents. This asymmetry is the engine.

## File Structure

```
tercet/
├── SKILL.md                  # This file
├── README.md                 # Public-facing theory & usage
├── agents/
│   ├── constructor.py        # The Constructor — resourceful, trusts evidence
│   └── examiner.py           # The Examiner — skeptical, no tools
├── templates/
│   ├── intake.md             # Structured intake questionnaire
│   └── knowledge_gap.md      # Output template for the analysis
└── examples/
    ├── failure_report.md     # Worked example: software automation
    └── process_redesign.md   # Worked example: organizational process
```
