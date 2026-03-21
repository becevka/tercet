# Tercet

**Planning through dialectic, not dictation.**

---

## Why "Tercet"

A tercet is a unit of three lines in poetry. Three voices that form a
complete thought.

In Tercet, those three voices are **the Constructor** (who builds),
**the Examiner** (who questions), and **you** (who knows what neither
of them can retrieve). No single voice produces the insight. The insight
lives in what the three of them produce together — in the tension between
confident proposal, rigorous skepticism, and situated human knowledge.

The name also carries a deeper structural resonance. In Dante's *terza rima*,
each tercet's middle line rhymes with the outer lines of the *next* tercet.
The stanzas interlock — each one sets up the tension that the next one
resolves, and in resolving it, creates new tension. That's how the loop
works: each cycle's unresolved questions become the next cycle's starting
point. The form mirrors the function.

And beneath both of these: the triad. Any binary framing — "build it /
challenge it," "documented / undocumented," "plan / risk" — is incomplete.
The third element, what emerges from the interaction that neither party
brought, is where genuine understanding lives. Tercet encodes that
principle as a tool.

---

## The Problem

When you plan something complex, the most valuable knowledge isn't in your
documents. It's in your head: how the system *actually* behaves, who *really*
makes decisions, what happened *last time* someone tried this.

AI planning tools assume more context produces better plans. So they ingest
everything and generate a confident proposal. The proposal looks complete.
It isn't. It's built on a foundation of retrieved documents that may be stale,
incomplete, or actively misleading — and it has no way of knowing which.

## How Tercet Works

Tercet doesn't generate plans. It takes your initial brief and runs it
through a dialectical loop that either **strengthens the brief with real
evidence** or **identifies exactly where your undocumented knowledge is
needed**.

### The Loop

```
You provide a brief
        │
        ▼
┌─ CONSTRUCTOR ──────────────────────────────────────┐
│  Reads the brief. Asks: "Is this enough to act on?"│
│  If not, searches every available tool — Jira,     │
│  Confluence, Slack, monitoring, code, web — to     │
│  fill gaps. Enriches the brief. Declares confidence.│
└────────────────────────┬───────────────────────────┘
                         │
                         ▼
┌─ EXAMINER ─────────────────────────────────────────┐
│  Receives the enriched brief. Has NO tools. Doesn't│
│  trust the evidence. Challenges: is this doc stale? │
│  Does the org chart reflect reality? What happened  │
│  last time? Who actually decides? What's not written│
│  down and why?                                      │
└────────────────────────┬───────────────────────────┘
                         │
                         ▼
            ┌────────────────────────┐
            │  Constructor responds  │
            │                        │
            │  CAN defend → enriches │──► loop continues
            │  brief with evidence   │
            │                        │
            │  CANNOT defend →       │──► question goes
            │  returns to you        │    back to you
            └────────────────────────┘
```

The loop runs until the Constructor is confident and the Examiner is
satisfied, or until enough unresolvable questions accumulate. Those
questions — the ones the system couldn't answer — are the output.

### What You Get Back

1. **An enriched brief** — your original request plus everything the
   Constructor discovered, with sources cited
2. **Unresolvable questions** — ranked by impact, with details on what
   was searched and why it couldn't be resolved
3. **Contested assumptions** — things the Examiner challenged that the
   Constructor couldn't fully defend
4. **Suggested conversations** — specific people to talk to, specific
   questions to ask

Answer what you can, feed it back in, and the loop continues. The
process is naturally iterative.

## The Core Asymmetry

The two agents have fundamentally different relationships to evidence:

| | The Constructor | The Examiner |
|---|---|---|
| **Trait** | Resourceful | Skeptical |
| **Tools** | All available MCPs + web search | None |
| **Premise** | "I have the brief + what I can find. I have what I need." | "The documented state and the actual state are never the same." |
| **Trust** | Trusts retrieved evidence | Questions retrieved evidence |
| **Failure mode** | Overconfidence in documentation | Paralysis from seeing risk everywhere |

This asymmetry is the engine. The Constructor's research is genuinely
useful — it surfaces real evidence. The Examiner's skepticism is genuinely
useful — it finds where evidence is stale, misleading, or missing. The
boundary between them is a map of where tacit knowledge lives.

## Domain Agnostic

The agents are epistemic stances, not engineering roles. They adapt:

| Domain | Constructor researches... | Examiner probes... |
|--------|--------------------------|-------------------|
| Software feature | Service maps, ticket history, monitoring baselines | Gaps between architecture docs and running systems |
| Process design | Existing workflows, stakeholder needs, precedent | Incentives, behavioral patterns, political dynamics |
| Strategic decision | Market data, competitive analysis, internal capacity | Assumption quality, framing bias, second-order effects |
| Organizational change | Org structure, role definitions, prior attempts | Power dynamics, cultural resistance, historical patterns |

## Theoretical Foundations

**Enactive cognition.** Understanding emerges from interaction, not
transmission. The dialogue generates context; the briefing document
doesn't. (Varela, Thompson & Rosch; 4E cognition framework.)

**Triadic resolution.** The binary "build / challenge" is incomplete.
The third vertex — what emerges that neither party brought — is
where insight lives. The Constructor and Examiner are two vertices;
the human completes the triangle. (TRIM Theory — Triadic Resolution
& Integration Model; Hegelian dialectic.)

**Mētis over legibility.** The gap between institutional legibility and
local tacit knowledge determines outcomes. Tercet is designed to surface
mētis — the knowledge that resists documentation but shapes what
actually happens. (James C. Scott, *Seeing Like a State*.)

## Project Structure

```
tercet/
├── SKILL.md              # Orchestration instructions for Claude
├── README.md             # This file
├── agents/
│   ├── constructor.py    # Resourceful, tool-using, trusts evidence
│   └── examiner.py       # Skeptical, tool-less, questions evidence
├── templates/
│   ├── intake.md         # Structured intake questionnaire
│   └── knowledge_gap.md  # Output template
└── examples/
    ├── failure_report.md # Software automation example
    └── process_redesign.md # Organizational process example
```

## Background

Created by Wolfgang Basyuk. Architecture draws on triadic models of
understanding (TRIM Theory), enactive cognitive science, and experience
spanning family systems therapy, software architecture, and engineering
management.

The core insight: planning value emerges from structured interaction, not
context accumulation. The same principle governs therapeutic dyads,
engineering teams, and human-AI collaboration. Tercet encodes that
principle as a tool.

## License

MIT
