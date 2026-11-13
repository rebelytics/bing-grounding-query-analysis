# Presenting the cuts

Reference file for the `bing-grounding-query-analysis` skill (CC BY 4.0 — Eoghan Henn and contributors). Load before drafting the deliverable that presents the cuts.

## Presenting the cuts

When the cuts feed a stakeholder deliverable (deck, report, summary), four
rules prevent a correct number from being read as a wrong claim:

1. **Branded share is a composition finding, never a leading score.** Do not
   headline the branded percentage as if it were a performance metric —
   stakeholders anchor on the number and a caption explaining "high branded
   share is not good news" gets filtered out while the number survives. Frame
   it with risk labels instead (e.g. "X% of citations depend on the brand
   already being named — a concentration risk"), so the framing travels with
   the number.
2. **Segment rankings by market before aggregating.** An aggregate ranking
   across markets of very different sizes is dominated by the big markets and
   can invert findings — a domain absent from an aggregate top-30 can be #1
   within its own (smaller) market, so the aggregate would report a strength
   as a weakness. Report per-market rankings first; only aggregate with the
   market mix disclosed.
3. **Every named page or asset carries its clickable URL.** An action slide
   that names content the reader cannot open is not actionable.
4. **Every chart names its engine set explicitly.** Numbers from different
   engine pools (e.g. Bing/Copilot grounding data vs other AI-visibility
   sources) must never appear under one unlabelled umbrella — label the
   source engine set on each chart and number.

### Validating recommendations before they enter a deliverable

A performance gap licenses a diagnosis, not a prescription. Any
recommendation of the form **"fix X because performance is low" requires
verifying that X is actually deficient** before it enters a deliverable.
The checks are cheap and the failure mode is common — a plausible-sounding
action inferred from a gap, where the presumed deficiency turns out not to
exist. Two checks cover most cases:

- **Existence check.** Before recommending that missing content be created
  or translated, verify it is actually missing. On multilingual sites, a
  cross-domain join on a shared article or content identifier — run on data
  already in hand — answers "do the translations exist?" in minutes.
  *Illustrative: a "translate the citation winners" recommendation is
  falsified by exactly this join wherever the articles already exist in the
  other languages — a common finding.*
- **Parity check against the strongest sibling.** Before recommending that
  underperforming versions be "refreshed", fetch 2–3 strong/weak page pairs
  and compare word count, section structure, and publish/modified dates.
  *Illustrative: weak versions are often complete, current and
  structurally identical to their winning siblings, sometimes longer.*

For multilingual version gaps specifically, the order is: identifier
cross-domain join first, then the parity fetch of 2–3 strong/weak pairs;
only recommend content work the checks support.

Corollary: **falsified obvious causes strengthen the deliverable.** "We
tested translation coverage and content parity and ruled both out" reads
stronger than an unverified action item — the cheap check that kills a
recommendation is itself a finding that makes the analysis credible.

### Deliverable arc for snapshot analyses

When the input is a **single snapshot** — one extraction, no trend data —
do not structure the stakeholder deliverable as findings → actions. A
snapshot licenses pointers and questions, not prescriptions. Structure the
arc instead as:

1. **Pointers** — what the snapshot indicates, framed as first observations
   rather than conclusions.
2. **Open questions** — every would-be action whose premise the data cannot
   verify is converted into an open question, not shipped as a
   recommendation.
3. **Measurement case** — close with what ongoing measurement would answer:
   the open questions themselves are the argument for continued monitoring,
   and they make that case more persuasively than any recommendation slide.

Reserve action lists for analyses with trend data or verified premises
(see the validation rules above). Cap snapshot deliverables at **one or two
no-regret moves** — actions that make sense regardless of how the open
questions resolve.
