# Tercet — Knowledge Gap Analysis

> Generated: {{ timestamp }}
> Loop cycles: {{ cycles }} | Termination: {{ reason }}

---

## The Enriched Brief

What the Constructor built from your initial input plus everything it
discovered through available tools.

### Intent
{{ intent — unchanged from user unless reframed by evidence }}

### Known Context (enriched)
{{ Original context + Constructor discoveries, each tagged with source }}

### Proposed Approach
{{ The Constructor's best proposal, grounded in retrieved evidence }}

---

## Evidence Inventory

What the Constructor found, where, and how the Examiner assessed it.

| Evidence | Source | Constructor's Use | Examiner's Assessment |
|----------|--------|-------------------|----------------------|
{{ for e in evidence }}
| {{ e.what }} | {{ e.source }} | {{ e.how_used }} | {{ e.examiner_trust }} |
{{ endfor }}

---

## The Retrieval Boundary

Where the Constructor searched and came up empty. These gaps in the
documented record are often where the most critical tacit knowledge lives.

{{ for gap in retrieval_gaps }}
- **Searched for:** {{ gap.what }}
  **Looked in:** {{ gap.where }}
  **Why it matters:** {{ gap.significance }}
{{ endfor }}

---

## Contested Assumptions

Things the Examiner challenged that the Constructor couldn't fully defend.
Ordered by severity.

{{ for a in contested_assumptions }}
### {{ a.severity }}: {{ a.statement }}

- **Constructor's evidence:** {{ a.evidence_presented }}
- **Examiner's challenge:** {{ a.challenge }}
- **Why it couldn't be resolved:** {{ a.why_stuck }}
- **What would resolve it:** {{ a.resolution_path }}
{{ endfor }}

---

## Unresolvable Questions

Ranked by impact. These are the questions the loop couldn't answer —
the Constructor searched, the Examiner pressed, and neither could resolve
them. They require your situated knowledge.

{{ for q in unresolvable_questions }}
### {{ loop.index }}. {{ q.question }}

- **Why it matters:** {{ q.impact }}
- **What was searched:** {{ q.constructor_searched }}
- **Why the Examiner insists:** {{ q.examiner_reasoning }}
- **Who might know:** {{ q.who_to_ask }}
{{ endfor }}

---

## Hidden Forces

Dynamics surfaced by the Examiner that wouldn't appear in any
retrievable document.

{{ for f in hidden_forces }}
- **{{ f.description }}**
  {{ f.implication }}
{{ endfor }}

---

## Suggested Conversations

Specific people to talk to, specific questions to ask. These come
directly from the unresolvable questions and contested assumptions above.

| Priority | Talk to | Ask about | To learn |
|----------|---------|-----------|----------|
{{ for c in conversations }}
| {{ c.priority }} | {{ c.who }} | {{ c.topic }} | {{ c.unlock }} |
{{ endfor }}

---

## What to Do Next

1. **Answer what you can.** You likely know the answers to several
   unresolvable questions right now. Feed them back in — the loop
   will continue with an enriched brief.
2. **Verify stale evidence.** If the Examiner flagged sources as
   potentially outdated, have someone confirm.
3. **Have the conversations.** The suggested conversations list is
   your 1:1 agenda for the next week.
4. **Decide the contested assumptions.** These are judgment calls
   that require your context, not more research.
