# Worked Example: Redesigning the Quarterly Planning Process

*Organizational process design. See `failure_report.md` for a software example.*

## Initial Brief (from user)

> **Intent:** Redesign quarterly planning. Current process takes 6 weeks,
> produces plans nobody follows. Directors spend two weeks on slide decks
> that get approved and immediately diverge from reality.
>
> **Known Context:** ~200 person product org, 4 VPs, 12 directors, ~30 teams.
> We use OKRs but they've become compliance artifacts. Real prioritization
> happens in Slack DMs between directors. Last year's big strategic bet
> (Platform Modernization) quietly lost resources to customer escalations
> by week 3 of every quarter.
>
> **What I know that isn't documented:** The real decisions happen in DMs,
> not in planning meetings. A few directors are really good at informal
> negotiation and they basically run the actual resource allocation.
>
> **Worried about:** That making it "lighter" might just make it less
> visible without fixing the underlying problem. Also worried that some
> directors use the elaborate process as political cover.
>
> **Constraints:** Need VP buy-in. Can't just skip planning — finance
> and the board expect quarterly commitments.

## How the Tercet Loop Played Out

### Cycle 1: Constructor receives brief, declares SEARCHING

The Constructor looks for evidence:
- Google Drive → finds the last 3 quarterly planning templates and the
  final slide decks from each director. Notes that the template itself
  is 47 slides with mandatory sections including "strategic alignment
  matrix" and "cross-team dependency map." **Adds to Known Context:
  "The template alone takes significant effort to complete."**
- Google Calendar → finds that Q1 planning involved 14 meetings over
  6 weeks totaling ~40 hours per director. **Adds specific data.**
- Jira → pulls OKR completion rates. Finds that across 4 quarters,
  average OKR completion is 34%. But specific OKRs around "keep the
  lights on" work hit 90%+, while strategic OKRs average 18%.
  **Significant finding. Adds to Known Context with source.**
- Slack → searches #leadership and #planning channels. Finds a thread
  from last cycle where a director wrote: "Can we please simplify this?
  I spent 3 days on slides that were approved in 10 minutes."
  **Adds to Known Context.**

Constructor proposes: Compress to 3 weeks, replace slide decks with a
lightweight brief format, add mid-quarter checkpoints with explicit
resource reallocation windows, ring-fence strategic bets with protected
headcount.

Declares **CONFIDENT**: "The data clearly shows the current process is
high-overhead and low-compliance. The 34% OKR completion rate, the 18%
strategic OKR rate, and the director complaints all point to the same
diagnosis. A lighter, more adaptive process with protected strategic
investment is the obvious move."

### Cycle 1: Examiner receives enriched brief, declares CHALLENGING

**CRITICAL:** The Constructor found that strategic OKRs complete at 18%
and proposed ring-fencing resources to fix this. But WHY do strategic
OKRs fail? If they fail because customer escalations are genuinely more
valuable in the short term, ring-fencing is fighting rational behavior.
If they fail because there's no real consequence for abandoning them,
the problem is accountability, not process. The 18% number is a symptom.
The Constructor is proposing a treatment without a diagnosis.

**CRITICAL:** The user said real prioritization happens in Slack DMs.
The Constructor found a complaint about the slide process but didn't
investigate the informal system. Redesigning the formal process without
understanding the informal one guarantees the new process gets routed
around the same way. What actually happens in those DMs? What makes
the informal system work?

**SIGNIFICANT:** The user mentioned directors use the process as
political cover. The Constructor's "lighter process" removes that cover.
How will directors who feel politically vulnerable react? They may
resist or sabotage a redesign that increases their personal exposure.
This is not retrievable from any tool.

**SIGNIFICANT:** The 47-slide template and 40 hours of meetings exist
for a reason. They may be bad at planning, but they might be the only
time 12 directors are in the same room. The meetings might serve a
coordination function that has nothing to do with planning.

### Cycle 2: Constructor responds

Constructor searches again:
- Jira → tries to correlate strategic OKR failure with specific events.
  Finds that in 3 of 4 quarters, Platform Modernization lost engineers
  to customer escalations tagged P1. The P1s came from the same 3
  enterprise clients. **Adds to Known Context: "Strategic resource drain
  is concentrated — 3 enterprise clients generate the P1s that pull
  people off strategic work."**
- Slack → searches for DM-like public threads between directors about
  resource moves. Finds some in #engineering-leads but they're cryptic —
  "can I borrow [engineer] for a sprint?" type messages. **Adds as
  partial evidence but flags: "The actual negotiation isn't visible
  in public channels."**
- Google Calendar → checks the Q4 planning review meeting. It was
  scheduled for 4 hours, ran 2.5 hours, with 40 minutes spent on the
  first director's presentation and 8-10 minutes each for the rest.
  **Adds to Known Context: "Review meetings front-load attention
  and rush remaining presentations."**

Constructor **cannot resolve** why strategic OKRs fail. Returns to Driver:

> "STUCK: I found that 3 enterprise clients generate most P1s that drain
> strategic resources, but I can't determine whether those P1s are
> genuinely more valuable than strategic work. That's a business judgment
> about the cost of losing those clients vs. the cost of delayed
> platform modernization. Finance or the VP of Sales would know."

Constructor **cannot resolve** what the informal system looks like:

> "STUCK: The actual resource negotiation between directors happens in
> private channels or DMs I can't access. Public channels show only
> the surface — polite requests to borrow engineers. The real dynamics,
> power relationships, and negotiation patterns are invisible to me."

Constructor **cannot resolve** the political cover question:

> "STUCK: Whether directors use the process as protection is a question
> about individual psychology and organizational culture. No tool can
> answer this. The user's instinct is probably right — but which
> directors, and how would they react to losing that cover?"

Updates the brief. Revises proposed approach to acknowledge that ring-
fencing may not be the right intervention and that the informal system
must be accounted for. Declares **CONFIDENT** on the problem diagnosis
but **STUCK** on three key dimensions of the solution.

### Cycle 2: Examiner receives updated brief, declares ESCALATE

Acknowledges: the Constructor's finding about 3 enterprise clients
causing most P1s is genuinely valuable. That's specific and actionable.
The calendar data about front-loaded review meetings is also useful.

But escalates:

**CRITICAL (ESCALATE):** Three of the most important questions — why
strategic bets fail, how the informal system works, and how directors
will react — all require human knowledge. The Constructor has hit the
boundary of what's retrievable. Continuing the loop won't surface
anything new.

Adds one final challenge: "The board and finance expect quarterly
commitments. Has anyone investigated what 'commitment' actually means
to the board? If the board tracks quarterly OKRs at the same granularity
as the internal process, then simplifying the internal process may
create a gap that finance has to fill with their own reporting overhead.
The overhead might just move, not disappear."

### Loop terminates: Driver collects output

## What the User Received

### Enriched Brief (key additions)

- Strategic OKRs complete at 18% vs. 90%+ for operational OKRs.
  *(source: Jira OKR tracking)*
- Resource drain is concentrated: 3 enterprise clients generate most
  P1s that pull engineers off strategic work.
  *(source: Jira P1 tickets cross-referenced with Platform Mod sprint history)*
- The planning template is 47 slides. Directors spend ~40 hours in
  planning meetings per cycle. Reviews front-load attention on the first
  presenter. *(source: Google Drive, Google Calendar)*
- At least one director has complained publicly about process overhead.
  *(source: Slack #planning)*
- The actual resource negotiation between directors is invisible in
  tooling. Public channels show only polite surface requests.
  *(source: Slack search — notable for what WASN'T found)*

### Retrieval Boundary

- No visibility into director-to-director private negotiations
- No data on the business value of P1 escalations vs. strategic work
- No documentation of the informal governance system
- No information on how the board actually uses quarterly commitments

### Unresolvable Questions

1. **Are the P1 customer escalations genuinely more valuable than
   Platform Modernization, or is the org just defaulting to urgency?**
   The Constructor found the pattern but can't evaluate the tradeoff.
   *Talk to: Finance partner who tracks revenue risk from those 3
   enterprise clients, AND the VP who sponsors Platform Modernization.
   Compare their numbers.*

2. **How does the informal resource allocation system actually work?**
   The Constructor found evidence it exists but can't see inside it.
   *Talk to: the 2-3 directors the user identified as effective informal
   negotiators. Ask them to walk through their last major resource move.
   Not "how should it work" — "how did you actually do it last time?"*

3. **Which directors rely on the process for political protection,
   and how would they react to losing it?**
   Not retrievable. Requires reading the room.
   *No single conversation will answer this. The user needs to sense
   this across multiple 1:1s. Consider asking indirectly: "If we
   simplified planning, what would you lose?"*

4. **What does the board actually do with quarterly commitments?**
   If the board tracks at the same granularity, simplifying internal
   planning may just shift the overhead to finance.
   *Talk to: CFO or finance partner. Ask to see what the board
   actually receives and how they use it.*

### Suggested Conversations

| Priority | Talk to | Ask | To learn |
|----------|---------|-----|----------|
| High | Finance partner | "What's the actual revenue at risk from those 3 enterprise clients? And what's the projected cost of delaying Platform Mod another year?" | Whether P1 escalations are rational or reflexive |
| High | An effective informal negotiator (director) | "Walk me through the last time you moved an engineer between teams. What actually happened?" | How the real system works so the new formal system can account for it |
| High | CFO or finance lead | "Can I see what the board actually receives from our quarterly planning? How do they use it?" | Whether simplifying creates a gap downstream |
| Medium | Directors in 1:1s | "If we simplified planning, what would you personally lose?" | Who relies on the process for protection |

---

## Why This Matters

A single agent would have proposed a lighter planning process. Tercet
produced the enriched finding that 3 specific enterprise clients drive
the resource drain (actionable and not previously connected) — AND
identified that the three most important questions (are escalations
rational, how does the informal system work, who needs political cover)
live entirely in people's heads.

The user's brief went from "planning takes too long, make it shorter"
to a specific, evidence-backed understanding of the structural forces
at play — with exactly four conversations that would unlock the
information needed to design something that actually works.
