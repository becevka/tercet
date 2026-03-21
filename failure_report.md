# Worked Example: Automated Daily Failure Report

*Software automation task. See `process_redesign.md` for a non-engineering example.*

## Initial Brief (from user)

> **Intent:** Automate the daily failure report for our claims processing platform.
> Currently a senior engineer spends 45 minutes each morning pulling data from
> Datadog, Splunk, and our internal status page, cross-referencing with Jira
> tickets, and writing a summary for stakeholders.
>
> **Known Context:** Services involved: ClaimsIngestion, ValidationEngine,
> AdjudicationService, PaymentGateway, NotificationHub. Kafka topics + shared
> PostgreSQL cluster. Monitoring: Datadog (infra), Splunk (app logs), PagerDuty
> (incidents), custom status page (Rails app).
>
> **Stakeholders:** ~50 business people who care about: which claims were
> affected, is it resolved, what's the business impact.
>
> **What I know that isn't documented:** The status page is kind of its own
> thing — I don't think it talks to Datadog or Splunk.
>
> **Worried about:** The engineer who writes this report does a lot of
> interpretation, not just data assembly. Not sure we can automate that.

## What a Single-Agent Approach Would Produce

- Build a Python script querying Datadog, Splunk, and PagerDuty APIs
- Correlate failures by timestamp
- Generate markdown report, send via Slack on a cron schedule

Technically correct. Tells you nothing you didn't already know.

## How the Tercet Loop Played Out

### Cycle 1: Constructor receives brief, declares SEARCHING

The Constructor reads the brief and identifies gaps. It searches:
- Datadog API docs → finds the service catalog. Discovers services are
  named differently than in the brief (e.g., `claims-ingestion-prod` vs.
  `ClaimsIngestion`). **Adds to Known Context with source.**
- Confluence → finds an architecture diagram from 18 months ago showing
  the Kafka topic layout. **Adds to Known Context.**
- Jira → finds an old epic "Unified Monitoring Dashboard" that was abandoned
  in Q2. **Adds to Known Context — notes it was abandoned.**
- Splunk → can't access. No MCP configured. **Flags as retrieval gap.**

Constructor updates the brief, adds a proposed approach (API-based correlation
engine), and declares **CONFIDENT**: "The brief has enough to build a data
aggregation pipeline. The service naming mismatch is solvable."

### Cycle 1: Examiner receives enriched brief, declares CHALLENGING

The Examiner raises:

**CRITICAL:** The Confluence architecture diagram is 18 months old and predates
the Q3 migration mentioned in the abandoned Jira epic. The Kafka topic layout
may have changed significantly. The Constructor is treating a stale document
as the current system topology.

**CRITICAL:** The brief says stakeholders care about "is it resolved" but the
Constructor's proposed approach only tracks service health, not claim-level
reprocessing status. These are different things. What does "resolved" actually
mean to the people reading this report?

**SIGNIFICANT:** The abandoned "Unified Monitoring Dashboard" epic suggests
someone already tried to solve a related problem and failed. Why did it fail?
The same forces may apply here.

**SIGNIFICANT:** The user said the engineer's 45 minutes contains "interpretation."
The Constructor's proposal automates data assembly but doesn't address the
editorial judgment. What specific interpretive decisions does the engineer make?

### Cycle 2: Constructor responds

Constructor searches again:
- Jira → finds comments on the abandoned epic. An engineer wrote: "Blocked
  because Datadog and Splunk use different service taxonomies and nobody wants
  to own the mapping." **This confirms the Examiner's naming concern and
  explains the prior failure. Moves from INFERRED to VERIFIED.**
- Confluence → searches for anything newer than the architecture diagram.
  Finds nothing. **Flags the retrieval gap explicitly: "No architecture
  documentation post-Q3 migration exists."**
- PagerDuty → pulls incident resolution data. Notes that resolution timestamps
  are when engineers close the incident, not when services recover.
  **Adds to Known Context.**

Constructor **cannot resolve** the definition of "resolved." Searches Slack
for discussions between engineering and business stakeholders about failure
reporting — finds nothing relevant. Returns this to the Driver:

> "STUCK: I cannot determine what 'resolved' means to business stakeholders.
> Engineering tracks service health; the business likely tracks claim
> reprocessing. These require different data sources. This is a requirements
> question that needs a human conversation."

Constructor **cannot resolve** what editorial judgment the engineer applies.
No documentation of the report-writing process exists. Returns this too:

> "STUCK: The engineer's interpretive process isn't documented anywhere.
> I can automate the data pull but can't determine what the engineer
> highlights, downplays, or escalates without talking to them."

Updates the brief. Some assumptions move to CONTESTED. Declares **CONFIDENT**
on the technical approach but **STUCK** on the requirements.

### Cycle 3: Examiner receives updated brief, declares ESCALATE

The Examiner acknowledges the Constructor's research was thorough — the Jira
comment finding was valuable. But escalates:

**CRITICAL (ESCALATE):** Two of the most important questions — what "resolved"
means and what editorial judgment matters — can't be answered by any tool.
These are requirements questions, not implementation questions. Building
the automation without answering them risks producing a report that's
technically accurate and operationally useless.

**SIGNIFICANT:** The user mentioned the status page is "kind of its own thing."
The Constructor hasn't investigated this. If the automated report contradicts
the manually-updated status page, stakeholders will lose trust in both.
Is anyone going to deprecate the status page, or does it need to be integrated?

### Loop terminates: Driver collects output

## What the User Received

### Enriched Brief (excerpt)

**Known Context (additions by Constructor):**
- Datadog service names don't match internal names. Full mapping needed.
  *(source: Datadog service catalog)*
- Kafka topic layout documented 18 months ago; no update post-Q3 migration.
  *(source: Confluence/arch-diagram-v2, flagged as potentially stale)*
- Previous attempt at unified monitoring dashboard failed specifically
  because of cross-tool taxonomy mismatch. No one wanted to own the mapping.
  *(source: Jira/MON-4521 comments)*
- PagerDuty "resolved" timestamp = when engineer closes incident, not when
  service recovers. *(source: PagerDuty API data)*

**Retrieval Boundary:**
- No architecture documentation exists post-Q3 migration
- No documentation of the manual report-writing process
- No Splunk access (MCP not configured)
- No Slack history connecting engineering and business definitions of failure

### Unresolvable Questions

1. **What does "resolved" mean to business stakeholders?**
   Constructor searched Slack, Confluence, and Jira. No shared definition
   exists. Engineering tracks service health; stakeholders likely track
   claim-level reprocessing. The automation must know which.
   *Talk to: claims ops lead who currently consumes the report.*

2. **What editorial judgment does the reporting engineer apply?**
   No documentation exists. The 45-minute process contains decisions about
   what to highlight, downplay, and escalate that encode institutional
   knowledge.
   *Talk to: the engineer who writes the report. Shadow them for one morning.*

3. **What is the current Kafka topic layout post-Q3 migration?**
   The only architecture doc is 18 months stale. The Constructor confirmed
   via Jira that the migration changed the topology but no updated doc exists.
   *Talk to: the engineer who ran the Q3 migration, or inspect the running config.*

4. **What happens to the status page?**
   It's manually updated, disconnected from monitoring tools. If the
   automated report contradicts it, stakeholders distrust both.
   *Decision needed: integrate, deprecate, or accept divergence.*

### Suggested Conversations

| Priority | Talk to | Ask | To learn |
|----------|---------|-----|----------|
| High | Claims ops lead | "When you read the failure report and it says 'resolved,' what does that mean to you? Service is healthy, or claims were reprocessed?" | The actual requirements |
| High | Reporting engineer | "Can I watch you write tomorrow's report? I want to see what decisions you make." | The editorial judgment that can't be documented in advance |
| Medium | Q3 migration engineer | "Do you have the current Kafka topic layout, or can we pull it from config?" | The real system topology |
| Medium | Status page owner | "Is there a plan for the status page once we automate reporting?" | Whether to integrate or deprecate |

---

## Why This Matters

The Constructor pulled real evidence that enriched the brief — the Jira
comment about the failed monitoring dashboard was a finding no human had
thought to look for. But the Examiner correctly identified that the
two most critical questions (what "resolved" means, what editorial
judgment matters) are requirements questions that live in people's heads,
not in any tool.

The user now has a brief that's twice as rich as what they started with,
AND knows exactly which four conversations to have before writing a
single line of code.
