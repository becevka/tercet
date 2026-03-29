# Tercet Brief

> Status: IN PROGRESS
> Cycle: 1
> Last updated by: Examiner

## Intent

Build a system that automatically generates daily piano practice plans.
One hour per day, for fun. Includes improvisation and learning pieces.
Sends a daily plan with 1-2 pages of music to learn. Assesses current
level and adapts so it stays slightly challenging but enjoyable.

## Known Context

- Daily practice, ~1 hour
- Mix of improvisation and learning pieces
- Needs actual sheet music: 1-2 pages per day
- Must assess skill level and adapt over time
- Should be fun, not a grind

### Sheet Music Sources with Programmatic Access

- **IMSLP** (International Music Score Library Project): The largest free sheet music library. Has a basic API for listing composers and works (JSON format). A Python package (`imslp` on PyPI, v0.2.3, alpha) wraps the MediaWiki API and IMSLP's custom API for programmatic access to metadata and PDF URLs. Scores are primarily PDF. Some include MusicXML attachments, but coverage is inconsistent. Important: mass downloading is against site rules. Source: [IMSLP API docs](https://imslp.org/wiki/IMSLP:API), [imslp PyPI](https://pypi.org/project/imslp/)

- **Mutopia Project**: 2,124 pieces of public domain music available in PDF, MIDI, and editable LilyPond source files. Browsable by instrument, composer, and style. No API, but LilyPond source files could be scraped or mirrored. No difficulty grading. Source: [mutopiaproject.org](https://www.mutopiaproject.org/)

- **Musopen**: Free public domain recordings and sheet music. No documented API for programmatic access.

- **Open Opus API**: Free REST API returning JSON for classical music metadata (composers, works, performers). Public domain data, no registration needed. Does not serve sheet music files directly, but useful for building a catalogue of works. Source: [github.com/openopus-org/openopus_api](https://github.com/openopus-org/openopus_api)

### Music Notation Generation Libraries

- **music21** (Python, MIT): Toolkit for computational musicology. Can parse MusicXML, MIDI, ABC, Humdrum. Can generate notation and export to MusicXML, LilyPond, and MIDI. To render PDF output, it delegates to either MuseScore or LilyPond as a backend. Can programmatically construct scales, arpeggios, chord progressions, and custom exercises. Source: [music21.org](https://music21.org/music21docs/about/what.html)

- **LilyPond**: Text-based music engraver producing high-quality PDF scores. Input files are plain text and can be generated programmatically. Supports Scheme scripting for algorithmic composition. Includes `abc2ly` converter for ABC notation input. Source: [lilypond.org](https://lilypond.org/)

- **ABC notation**: Minimal text-based music notation using ASCII characters. Very compact, making it suitable for LLM generation or storage. Can be converted to LilyPond via `abc2ly`, or rendered directly by tools like `abcjs`. Source: [Wikipedia](https://en.wikipedia.org/wiki/ABC_notation)

### Piano Difficulty Grading Systems

- **Henle Verlag**: 9-level system (1-3 easy, 4-6 medium, 7-9 difficult). Widely respected and covers most of the classical piano repertoire. Browsable on henle.de but no public API. Source: [henle.de](https://www.henle.de/Levels-of-Difficulty/)

- **Piano Syllabus** (pianosyllabus.com): Database of 27,000+ graded piano pieces from 2,000+ composers. Maps pieces across multiple grading systems. Web-only, no known API.

- **ABRSM / RCM exam grades**: Standardized grading from Grade 1-8 plus diploma levels. Published syllabi list specific pieces per grade. These provide ground-truth difficulty labels for a large body of repertoire.

### Piano Skill Level Assessment Criteria

There is no universal standard, but common frameworks break skills into: technique (scales, arpeggios, hand independence), sight-reading ability, repertoire complexity handled, and musicality (dynamics, phrasing). A practical self-assessment approach: ask the user what pieces they can currently play, map those pieces to a known difficulty grade (Henle, ABRSM), and use that as the starting level. Source: [Piano Marvel](https://pianomarvel.com/en/article/what-is-my-piano-skill-level), [Pianote](https://www.pianote.com/blog/piano-ability-levels/)

### Practice Structure (Pedagogy Research)

Research on effective practice suggests:
- **20-minute attention blocks**: Psychologists identify ~20 minutes as the average focused attention span before fatigue sets in. An hour session maps well to three focused blocks. Source: [Practising the Piano](https://practisingthepiano.com/20-minute-practice-session/)
- **Deliberate practice**: Ericsson's research emphasizes focused, goal-directed practice over passive repetition. Source: [ERIC research paper](https://files.eric.ed.gov/fulltext/EJ912405.pdf)
- **Warm-up, work, review**: Common pedagogical structure is: warm-up/technique, repertoire work, then review/sight-reading or improvisation.
- **Consistency over duration**: Daily short practice is more effective than infrequent long sessions. The user's daily habit aligns with this.
- **Flow state**: Csikszentmihalyi's flow theory - the "slightly challenging but enjoyable" goal in the brief directly maps to the flow condition of challenge matching skill level.

### Delivery Mechanisms

- **Email with PDF attachment**: Python's `smtplib` + `email.mime` can send emails with PDF attachments. Schedule via cron or the `schedule` library. Well-understood, reliable, works on any device.
- **Messaging APIs** (Telegram bot, Slack, etc.): Could send plans as images or PDF links. More interactive but adds complexity.

## Assumptions

- INFERRED: User has a piano or keyboard available
- INFERRED: User has some existing piano ability (improvisation and piece-learning imply not a complete beginner, likely Henle 2-5 range)
- INFERRED: "Send me a plan" implies a push delivery mechanism (email, message, etc.)
- INFERRED: "Adapts" means the system needs some feedback loop - either explicit (user reports difficulty) or implicit (user marks pieces complete/incomplete)
- CONTESTED: The system can start with a curated subset of public domain pieces rather than indexing all of IMSLP (see Challenge #1 - Catalogue Depth)
- CONTESTED: Sheet music means rendered notation, not just text instructions (see Challenge #3 - LilyPond Extraction)
- CONTESTED: Improvisation segments need prompts or frameworks (key, chord progression, style), not full notation (see Challenge #6 - Improvisation Value)

## Open Questions

1. **What is the user's current skill level?** The system needs a starting point. Options: ask the user to name pieces they can play (then map to Henle/ABRSM grade), or provide a short self-assessment questionnaire.

2. **What delivery channel does the user want?** Email is the simplest. Telegram or another messenger would allow richer interaction (e.g., "too easy" / "too hard" buttons). The brief says "sends" but does not specify how.

3. **How should the system get feedback for adaptation?** Options range from simple (user rates each session easy/medium/hard) to complex (tracking which pieces are completed, how long they took). The brief implies adaptation but not the mechanism.

4. **What genres/styles does the user want?** The brief mentions "pieces" and "improvisation" but does not specify classical-only, jazz, pop, etc. This affects which music sources to use.

5. **Should the system generate original exercises or only serve existing sheet music?** For technique work (scales, arpeggios, patterns), generating exercises with music21 is straightforward. For repertoire, pulling from IMSLP/Mutopia is more practical. The brief implies both are needed.

6. **Where should this system run?** A local cron job on a personal machine, a cloud function, or a lightweight server? This affects reliability of daily delivery.

7. **How many Mutopia pieces are actually solo piano at an appropriate level?** 2,124 total includes all instruments, ensembles, and vocal works. The usable subset for a single pianist may be much smaller.

8. **What happens when the user doesn't practice for a few days?** The system delivers daily but the brief doesn't address gaps in practice. Does the plan queue up? Reset? Pick up where things left off?

9. **How will difficulty grading of ungraded pieces actually work?** The Constructor proposes "heuristic analysis" as a fallback but does not describe what heuristics would be used or how accurate they are.

10. **Is email reply parsing for feedback realistic?** Parsing free-text email replies ("easy", "good", "hard") requires handling varied email clients, reply formatting, quoting, and encoding. This is a known pain point.

## Constraints

- ~1 hour daily
- 1-2 pages of music per session
- Must stay fun and slightly challenging

## Proposed Approach

### Architecture

A Python script that runs daily (via cron or cloud scheduler), generates a practice plan, renders 1-2 pages of sheet music as PDF, and delivers it to the user.

### Components

**1. Skill Profile**
- On first run, a short intake asks the user to name 2-3 pieces they can play comfortably. Map those to Henle difficulty levels (maintain a lookup table of common pieces to Henle grades, sourced from henle.de and pianosyllabus.com).
- Store current level as a number (1-9 Henle scale). Target pieces at current level and one notch above.
- After each session, user provides simple feedback (completed / partially done / too hard). Adjust level gradually: move up after 3-5 consecutive "completed" ratings, move down after 2 "too hard" ratings.

**2. Music Catalogue**
- Build a curated index of piano pieces with difficulty ratings. Start with Mutopia Project's 2,124 pieces (available as LilyPond source, so re-renderable and editable). Supplement with selected IMSLP PDFs for broader repertoire.
- Store metadata per piece: title, composer, Henle level, style, length in pages, which segments are good excerpts (since the user needs 1-2 pages, not a full sonata).
- For technique exercises (scales, arpeggios, chord progressions), generate them programmatically with music21 in the appropriate key/difficulty.

**3. Daily Plan Generator**
- Structure each 1-hour session into three ~20-minute blocks, following pedagogy research:
  - **Block 1 - Warm-up/Technique** (~15 min): Scales, arpeggios, or a short technical exercise in a key related to the day's piece. Generated by music21.
  - **Block 2 - Repertoire** (~30 min): 1-2 pages from the current learning piece. If a piece spans multiple days, track progress through it. Select pieces at or slightly above current level.
  - **Block 3 - Improvisation/Exploration** (~15 min): Provide a prompt - a chord progression, a key and style suggestion, or a "play along" framework. Not full notation, just a lead sheet or text prompt.
- The plan is a single PDF document containing the technique exercise notation, the repertoire excerpt, and the improvisation prompt.

**4. Sheet Music Rendering**
- Use music21 to construct technique exercises and export to LilyPond format.
- Use LilyPond to render all notation to high-quality PDF.
- For Mutopia pieces, the LilyPond source is already available - extract the relevant pages/measures and re-render.
- For IMSLP pieces, serve the original PDF pages (extract specific pages with a PDF library like PyMuPDF).

**5. Delivery**
- Email with the daily plan PDF attached, sent at a configured time each morning.
- Include a brief text summary in the email body: what today's session covers, estimated difficulty, and a link/reply mechanism for feedback.
- Feedback can be a simple reply ("easy", "good", "hard") parsed from the reply email, or a minimal web form linked in the email.

**6. Adaptation Engine**
- Track: pieces assigned, feedback received, current Henle level, days on current piece.
- Rules: vary keys for technique work (cycle through all 12 major and 12 minor keys over time). Alternate between new pieces and review of recently learned ones. Increase difficulty only after consistent positive feedback.
- Keep a "fun budget" - if difficulty crept up and user reported "hard" twice, next session includes an easier, familiar piece to restore enjoyment.

### Tech Stack

- Python 3.10+
- music21 for music construction and analysis
- LilyPond for PDF rendering
- PyMuPDF (fitz) for PDF page extraction from existing scores
- smtplib for email delivery
- SQLite or JSON file for state (skill level, history, catalogue)
- cron or systemd timer for scheduling

### What This Approach Does NOT Include

- No audio analysis or recording-based assessment (the brief does not mention recording or listening)
- No mobile app or web dashboard (the brief says "send me a plan", not "build me an app")
- No social or sharing features
- No real-time interaction during practice

## Examiner Challenges

### CRITICAL: The music catalogue is thinner than it looks

**Target:** Music Catalogue (Component 2), Known Context (Mutopia, IMSLP)
**Reasoning:** The Constructor cites Mutopia's 2,124 pieces as the starting set, but that number covers all instruments, ensembles, choral works, and organ music. Solo keyboard works are a subset. Of those, many are advanced (Bach fugues, Liszt etudes) or trivially simple (children's pieces). The number of pieces in, say, Henle 2-5 range that are also 1-2 pages long could be in the low hundreds or fewer. At one piece every few days, a catalogue of 200 pieces lasts roughly a year before the system starts recycling. That is survivable, but it means the system's daily variety depends on a catalogue that might need manual curation effort measured in weeks, not hours. The Constructor describes this effort as "build a curated index" but does not estimate the work involved, and does not confirm that Mutopia's piano-specific coverage at the right difficulty level is sufficient.
**What would resolve it:** A concrete count of Mutopia pieces that are (a) solo piano, (b) 1-4 pages, and (c) plausibly in an intermediate difficulty range. Even an approximate number would calibrate whether Mutopia alone can seed the system or whether IMSLP integration is required from day one.
**Retrievable:** YES - Mutopia's catalogue is browsable and filterable by instrument.

---

### CRITICAL: Difficulty grading has no credible automation path

**Target:** Skill Profile (Component 1), Music Catalogue (Component 2), Adaptation Engine (Component 6)
**Reasoning:** The entire adaptation mechanism rests on knowing how hard a piece is. The Constructor proposes mapping pieces to Henle grades, but Henle covers a specific classical repertoire and their data is not available via API. For Mutopia pieces that don't appear in Henle's catalogue, the Constructor mentions "heuristic analysis" without specifying what that means. Algorithmically grading piano difficulty is an open research problem. Factors include hand span required, tempo, polyphonic complexity, key signature, rhythmic patterns, and musical idiom. music21 can parse notes, but turning a parsed score into a reliable difficulty number is a significant research and engineering task, not a feature you bolt on. If the grading is wrong, the adaptation engine is worse than useless: it will serve pieces that are too hard or too easy, which directly undermines the "fun, slightly challenging" goal.
**What would resolve it:** Either (a) a plan to manually grade the initial catalogue (viable if the catalogue is small, see Challenge #1), or (b) a concrete description of difficulty heuristics with some evidence they correlate with real difficulty, or (c) acknowledgment that the initial version will require manual difficulty tagging and that automated grading is a future goal.
**Retrievable:** MAYBE - research papers on computational difficulty estimation for piano music exist, but their practical accuracy is uncertain.

---

### SIGNIFICANT: Extracting 1-2 page excerpts from LilyPond source is not trivial

**Target:** Sheet Music Rendering (Component 4), assumption that Mutopia LilyPond sources are "re-renderable and editable"
**Reasoning:** The Constructor states "extract the relevant pages/measures and re-render" from LilyPond sources as though this is a straightforward operation. It is not. LilyPond files have complex structure: they define variables, use includes, set up page layouts, define multiple voices and staves. Cutting a LilyPond file at measure 17-32 to get a 1-2 page excerpt requires understanding the file's structure, maintaining all necessary preamble and variable definitions, and re-setting page/line breaks. Mutopia files are contributed by different engravers with no consistent file structure. Some use multiple files, custom macros, or older LilyPond syntax that may not compile with current versions. This is the kind of work that looks easy in a design document and turns into the project's biggest time sink.
**What would resolve it:** A proof-of-concept: take 5 representative Mutopia LilyPond files, attempt to extract a 1-2 page excerpt from each, and report how much manual intervention was needed. Alternatively, abandon excerpt extraction and only serve complete short pieces (1-2 pages total), which narrows the catalogue further (see Challenge #1).
**Retrievable:** YES - this is a testable engineering question.

---

### SIGNIFICANT: The feedback loop has a cold start problem and a friction problem

**Target:** Adaptation Engine (Component 6), Delivery (Component 5)
**Reasoning:** The system proposes "move up after 3-5 consecutive 'completed' ratings, move down after 2 'too hard' ratings." This means the system needs 3-5 sessions of feedback before it can make its first upward adjustment. During those first sessions it is flying on the initial assessment alone. If the initial assessment is off by even one Henle level (very likely given it is based on the user naming 2-3 pieces), the user gets a week of poorly calibrated material before the system corrects. More importantly, requiring the user to reply to an email after every practice session is a friction point that will degrade over time. People forget, get busy, or feel the feedback is not worth the effort. If the user stops providing feedback for a week, the system stalls. The Constructor does not address what happens when feedback is absent.
**What would resolve it:** (a) A default behavior when no feedback is received (e.g., assume "completed" and inch forward, or hold steady). (b) Consider whether the feedback mechanism should be lower-friction: a single tap in a Telegram bot, or a link in the email that registers feedback with one click, rather than composing a reply. (c) Acknowledge that the initial calibration period is inherently rough and design a more nuanced intake (e.g., ask the user to play a short sight-reading test, or provide more diagnostic questions).
**Retrievable:** NO for the friction question (this is a UX design choice). MAYBE for the default-when-silent behavior (design decision, not research).

---

### SIGNIFICANT: The 1-hour session structure is rigid in a way that undermines "fun"

**Target:** Daily Plan Generator (Component 3), Constraints
**Reasoning:** The Constructor maps the hour to three blocks: 15 min warm-up, 30 min repertoire, 15 min improvisation. This is pedagogically sound but assumes the user wants a structured lesson every day. The brief says "for fun." Many recreational pianists would bristle at being told to spend 15 minutes on scales before they can play the music they want. The "fun" framing suggests the user might want some days that are all improvisation, or all repertoire, or a short 20-minute session when time is tight. The rigid structure also conflicts with the flow-state research the Constructor cites: flow requires autonomy, and a prescriptive block schedule reduces autonomy. The plan should probably offer the structure as a suggestion, not a requirement, or vary the structure day to day.
**What would resolve it:** Clarification from the user on how structured they want the plans to be. Alternatively, design the plan with a suggested structure but present it as flexible (e.g., "Today's session: warm up with C major arpeggios if you want, then dig into this Chopin waltz, and if you have time, try improvising over this progression").
**Retrievable:** NO - this requires user input on their preference for structure vs. freedom.

---

### SIGNIFICANT: No plan for piece continuity and multi-day learning

**Target:** Daily Plan Generator (Component 3)
**Reasoning:** The Constructor mentions "if a piece spans multiple days, track progress through it" but does not elaborate on how this works. Learning a 1-2 page piece to a reasonable standard typically takes a recreational pianist 1-4 weeks, not 1-2 days. If the system serves a new excerpt every day, the user never finishes anything. If it repeats the same excerpt for a week, the daily plan feels stale. This tension between novelty and mastery is central to the system's value and is not addressed. How does the system decide when the user is "done" with a piece? How does it balance review of old pieces with introduction of new ones? The brief says "learning pieces," which implies finishing them, not just sight-reading new ones every day.
**What would resolve it:** A concrete policy for piece lifecycle: how long a piece stays in rotation, how "completion" is defined (user says so? a fixed number of days?), and how the daily plan balances current learning pieces, review pieces, and new introductions.
**Retrievable:** NO - this is a design decision, though pedagogical sources on spaced repetition for music practice could inform it.

---

### MINOR: IMSLP's terms of service may limit practical use

**Target:** Music Catalogue (Component 2), Known Context (IMSLP)
**Reasoning:** The Constructor notes "mass downloading is against site rules" but still lists IMSLP as a supplement to Mutopia. If the system pre-downloads and caches IMSLP PDFs to serve as daily practice material, this could be considered mass downloading depending on volume. If it fetches on demand, there is a daily dependency on IMSLP's availability and speed. The legal and practical constraints on IMSLP usage need to be clearer before it is treated as a reliable source.
**What would resolve it:** Clarify the intended usage pattern: is the system pre-downloading a curated set (and if so, how many, and does that violate terms?), or fetching on demand? If on demand, what is the fallback when IMSLP is slow or down?
**Retrievable:** YES - IMSLP's terms of use are public.

---

### MINOR: The tech stack assumes a developer-user

**Target:** Tech Stack, Delivery (Component 5)
**Reasoning:** The proposed system is a Python script run via cron, with SQLite for state and LilyPond as a rendering dependency. Setting this up requires installing Python, LilyPond, configuring cron, setting up email credentials, and possibly managing a server. The brief does not say who the user is, but "sends me a daily plan" and "for fun" suggests someone who wants a finished product, not a DIY project. If the user is the developer building this system, the tech stack is fine. If it is meant for someone else, the deployment story is missing.
**What would resolve it:** Clarification on whether the user is also the developer. If yes, the tech stack is appropriate. If no, a deployment plan (containerized, hosted, managed) is needed.
**Retrievable:** NO - requires user clarification.

## Unresolvable Questions

- **User's tolerance for structure vs. freedom.** The system can be designed with flexibility, but the right balance between "here is your lesson plan" and "here are some ideas, do what you feel like" can only come from the user's personality and practice habits. No amount of research resolves this. (See Challenge #5.)
- **Long-term engagement sustainability.** Whether the user will continue providing feedback, following plans, and finding the system valuable after the novelty wears off (month 3+) is fundamentally unknowable in advance. The system should be designed to degrade gracefully when engagement drops, but predicting engagement is not possible.

---

**Constructor Confidence: SEARCHING**

Rationale: The technical path is clear - music21 + LilyPond + email delivery is well-supported and proven. The main uncertainty is in the music catalogue: building a curated, difficulty-graded index of piano pieces with LilyPond sources is a significant data curation effort. Mutopia's 2,124 pieces are a solid starting set but are ungraded by difficulty, so mapping them to Henle levels requires either manual curation or heuristic analysis. The adaptation/feedback loop design also needs user input to validate - the brief implies adaptation but leaves the mechanism open. I am confident in the architecture but searching for the right scope of the initial catalogue and the right feedback mechanism.

---

**Examiner Determination: CHALLENGING**

Rationale: The Constructor has done thorough research and proposed a coherent architecture. The tech stack is sound and the pedagogical framing is well-grounded. However, I have identified two critical gaps and four significant concerns that the Constructor should address before this brief can proceed to implementation.

The two critical issues are (1) the music catalogue may be far thinner than presented once filtered to solo piano at appropriate difficulty levels, and (2) the difficulty grading problem - which the entire adaptation mechanism depends on - has no credible automation path and the manual curation effort has not been estimated. These are not theoretical objections. If the catalogue has 80 usable pieces instead of 2,124, the system runs dry in months. If difficulty grading is unreliable, the core promise of "adapts so it stays slightly challenging but enjoyable" fails.

The significant concerns around LilyPond excerpt extraction, feedback loop friction, session rigidity, and piece continuity are solvable but need to be addressed in the design rather than discovered during implementation.

I recommend the Constructor address the two critical challenges with concrete evidence (a Mutopia piano piece count and a difficulty grading strategy) before proceeding. The significant challenges can be resolved through design iteration.
