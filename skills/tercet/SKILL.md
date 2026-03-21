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

# Tercet - Planning Through Dialectic

## Core Idea

Most AI planning tools treat context as input and produce plans as output.
Tercet does the opposite: it takes an incomplete brief, runs a dialectical
loop that either strengthens the brief or identifies what CAN'T be
strengthened without the human - and returns exactly that.

The output is not a plan. It's the questions the system couldn't answer
on its own.

## The TERCET.md File

`TERCET.md` is the living artifact. It lives in the project root. Both
agents read from it and write to it directly. There is no context passing
between agents - the file is the shared state.

### On Invocation

1. Look for `TERCET.md` or `tercet.md` in the project root.
2. **If found:** Read it. It's the brief. Start the loop immediately.
3. **If not found:** Ask the user for their brief using the intake
   template as a guide. Structure their input into the Living Brief
   format and write it to `TERCET.md`. Then start the loop.

### Brief Structure

When creating `TERCET.md` from user input, use this structure:

```markdown
# Tercet Brief

> Status: IN PROGRESS
> Cycle: 0
> Last updated by: Driver

## Intent
[What the user wants to do and why]

## Known Context
[What the user provided]

## Assumptions
[Things treated as true, tagged as:]
- VERIFIED - confirmed by retrieved evidence (source cited)
- INFERRED - reasonable but unconfirmed
- CONTESTED - Examiner challenged, Constructor couldn't fully defend

## Open Questions
[Empty initially - populated by the loop]

## Constraints
[Hard boundaries: time, budget, team, technical, political]

## Proposed Approach
[Empty initially - Constructor fills this in]

## Examiner Challenges
[Empty initially - Examiner writes challenges here]

## Unresolvable Questions
[Empty initially - questions neither agent could resolve]
```

## The Core Asymmetry

**The Constructor** is RESOURCEFUL and OPTIMISTIC. It reads `TERCET.md`,
searches every available tool for evidence, and writes an enriched version
back. It trusts what it finds.

**The Examiner** is SKEPTICAL and has NO TOOLS except reading `TERCET.md`.
It reads the Constructor's enriched brief, writes challenges and contested
assumptions back into the file. The documented state and the actual state
are never the same.

## Your Role as Driver

You (Claude) hold the loop. You are not a passive relay - you make
judgment calls.

### Running the Loop

1. **Spawn the Constructor agent.** Tell it to read `TERCET.md`, enrich
   the brief using all available tools, and write the updated brief back
   to `TERCET.md`.

2. **Read `TERCET.md` after the Constructor finishes.** Check: did the
   brief actually get richer, or is the Constructor spinning? Check the
   confidence declaration.

3. **Spawn the Examiner agent.** Tell it to read `TERCET.md` and write
   its challenges directly into the Examiner Challenges section. The
   Examiner should also move any assumptions it contests to CONTESTED
   and add items to Open Questions.

4. **Read `TERCET.md` after the Examiner finishes.** Check: are the
   challenges substantive or generic? If generic, push for specificity.

5. **If CRITICAL challenges exist:** Spawn the Constructor again to
   address them. It reads the updated `TERCET.md` (which now contains
   the Examiner's challenges), searches for evidence, and updates the
   file again.

6. **Repeat until termination.**

### Termination Conditions

The loop ends when ANY of these are true:

1. **Constructor confident + Examiner satisfied.** The brief is robust
   enough that the Examiner can't find substantive challenges.

2. **Unresolvable questions accumulated.** The Constructor has hit enough
   walls that continuing without the human is unproductive. This is the
   most common and most valuable termination.

3. **Diminishing returns.** The brief isn't getting meaningfully richer.
   You (the Driver) make this call.

4. **Safety valve.** Cap at ~10 Constructor/Examiner exchanges.

### Returning to the User

Before presenting results, update `TERCET.md` one final time:
- Set Status to `NEEDS INPUT` (if unresolvable questions exist) or
  `READY` (if the brief is robust)
- Update the cycle count
- Set "Last updated by: Driver"

Then present to the user:
1. **The enriched brief** - summarize what grew from their initial input
2. **Unresolvable questions** - ranked by impact, with who might know
3. **Contested assumptions** - things the Examiner challenged successfully
4. **Suggested conversations** - specific people to talk to

Point the user to `TERCET.md` for the full artifact. If they answer
questions and update the file, they can invoke Tercet again and the
loop continues from where it left off.

## Agent Instructions

### Constructor

Spawn the **constructor** agent with this instruction:

> Read TERCET.md in the project root. This is a Tercet planning brief.
> Your job is to enrich it. Search every available tool for evidence.
> Write the updated brief back to TERCET.md. Update the cycle count,
> set "Last updated by: Constructor", and append your confidence
> declaration. If responding to Examiner challenges, address each one
> in the Examiner Challenges section - either defend with evidence or
> move the question to Unresolvable Questions.

Give it full tool access.

### Examiner

Spawn the **examiner** agent with this instruction:

> Read TERCET.md in the project root. This is a Tercet planning brief
> enriched by the Constructor. Your job is to challenge it. Write your
> challenges into the Examiner Challenges section. Move any assumptions
> you contest to CONTESTED. Add items to Open Questions where evidence
> is missing. Update "Last updated by: Examiner" and append your
> termination declaration (SATISFIED, CHALLENGING, or ESCALATE).

Give it NO tools except Read (to read the file) and Edit/Write (to
update it). No search, no web, no MCPs. This constraint is the engine.
