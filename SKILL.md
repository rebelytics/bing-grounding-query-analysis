---
name: bing-grounding-query-analysis
description: 'Apply a repeatable analysis layer to Bing Webmaster Tools AI grounding-query data once it has been extracted into a spreadsheet. Load this skill whenever the user wants to analyse Bing grounding queries, do AI citation analysis or Copilot citation analysis, split branded vs non-branded citations, detect GEO entity confusion, or run an AI visibility analysis of Bing data. It is the analysis companion to the extraction skill `bing-wmt-ai-grounding-export`: that skill produces the per-page grounding-query spreadsheet, this skill turns it into decisions. Trigger on mentions of "branded vs non-branded citations", "grounding query analysis", "AI citation analysis", "entity confusion", "synthetic query share", or "what''s driving our AI citations" in a Bing / Copilot context.'
license: CC-BY-4.0
metadata:
  author: Eoghan Henn and contributors
  source: https://github.com/rebelytics/bing-grounding-query-analysis
  version: "1.1.1"
---

# Bing Grounding-Query Analysis

A repeatable analysis recipe for Bing Webmaster Tools AI grounding-query data
— the eight analytical cuts that turn a raw per-page citation export into
decisions. This is the analysis companion to the extraction skill
`bing-wmt-ai-grounding-export` (a published open-source companion,
https://github.com/rebelytics/bing-wmt-ai-grounding-export), which produces
the spreadsheet this skill consumes.

---

## Reference files in this skill — load on demand

- `references/presenting-the-cuts.md` — Presenting the cuts. **Load before drafting the deliverable that presents the cuts.**

## Why this skill exists

An extraction skill stops at the spreadsheet. The value lands when the
spreadsheet is read through a consistent analytical lens. The single most
important framing: **an extraction earns far more when it is paired with a
documented analysis recipe.** Of the eight cuts below, two matter most — the
**branded vs non-branded split** (the first cut) and the **matched
winner-vs-control content test** (the cut that actually isolates cause). Run
the others, but if time is short, those two carry the decisions.

## Input

A per-page grounding-query export with, at minimum: the cited page URL, the
grounding query text, a citation count, and (where present) the engine's own
Intent and Topic labels. This is exactly what `bing-wmt-ai-grounding-export`
produces. Shares described in this skill are **illustrative shapes, not
benchmarks** — never present a dataset's figure as a universal rate.
Three inputs need their own handling:

- **Citation Share** (`CitationRate`), where the export carries it. Bing
  publishes no definition, and its denominator is per property. Use it to
  rank queries within one property; never compare or sum it across
  properties.
- **Grounding queries are engine-rewritten lookups, not user prompts.** Never
  read them as what users asked. To compare them with a tracked prompt set,
  map both to the subject they name and join on the subject, not the string.
- **Opportunity queries**, where available: queries for which other sites are
  cited and this one is not, from the report's Topic Portfolio tab. They are
  the absence view this presence-only data otherwise lacks. Three caveats
  travel with them. The top-level list is capped, while the per-topic lists
  run far longer. The lists are personalised and can be filtered by the site
  owner's suppression rules. And they are topic-scoped, so run a relevance
  gate against what the brand actually offers before counting anything.

---

## Before Cut 1 — coverage and concentration

Run both checks before any cut, and carry their results into every share
reported.

- **Coverage.** Divide the query list's summed citations by the site's total
  citations for the same period (the export's daily-series total). State
  that coverage beside every share. A share in Cuts 1, 2, 4 or 8 is a share
  of the sampled, query-attributed subset, not of the site's citations.
  Coverage varies by site, so a cross-site comparison of shares is
  confounded unless both coverages are stated.
- **Concentration.** List the top five queries and the top five pages by
  share of citations. For any above ~20%, pull its daily series before
  reporting a distribution or a trend. Report distributions and trends with
  and without it. An invented example: one seasonal timetable lookup
  carrying half of a ferry operator's quarterly citations, all within six
  weeks, decides that quarter's intent mix on its own.

---

## The eight analytical cuts

### 1. Branded vs non-branded split — do this FIRST

Classify every grounding query by whether it contains the brand token (the
brand name or an unambiguous variant). This is the first cut because it
separates two different things the data measures:

- **Branded citations** measure **entity authority** and the canonical-page
  effect — the engine already knows the brand and is pulling its pages when
  the brand is named.
- **Non-branded citations** measure **content / topic discoverability** —
  the engine reached the brand's pages for a query that did *not* name the
  brand, which is the harder and usually more valuable signal.

Report the split as a share of citations, not just a share of distinct
queries (one query can carry many citations). *Illustrative: a clear majority of
citations branded is a common shape — an example of what the split can
look like, not a target.* A high branded share means
entity authority is doing the work and content discoverability is the gap; a
high non-branded share means the content is being found on its own merits.

**Mandatory second pass — validate the split before reporting it.** A single
brand regex (full brand name plus acronym) systematically under-counts
branded queries. After the initial split, search the "non-branded" remainder
for **each brand token individually**, plus common typo and token-scramble
patterns (word-order swaps like `<token2> <descriptor> <token1>`,
partial-token variants, misspellings), and reclassify the hits before
reporting. Grounding queries are often engine-rewritten, so brand mentions
appear in forms no first-pass regex anticipates; the validation pass
commonly moves the split by a visible margin and surfaces a whole class of
scrambled variants (see Cut 4). When product lines or product
names carry brand tokens, consider a **three-way split** — company-branded /
product-branded / non-branded — so product-name queries are not silently
counted as generic demand. Then check the inverse error: the first-pass
regex over-counts too. Read the Topic label of every query in the branded
set, and drop matches where the brand token carries its generic meaning (an
acronym brand matching the acronym's everyday sense, filed under an
unrelated Topic). Never report a branded/non-branded split whose excluded
set has not been fragment-searched and whose branded set has not been
Topic-checked.

The split shows only where the brand is cited. For where it is absent, use
the opportunity queries (Input), never an inference from the split.

### 2. Intent + Topic distribution, weighted by citations

Aggregate the engine's Intent and Topic labels, weighting each row by its
citation count rather than counting distinct queries. Citation-weighting
matters because a handful of high-citation queries can dominate the picture
that a raw query count would hide. The output is "where the citations
actually concentrate by intent and topic," which is the map for where content
investment pays back.

### 3. Entity-confusion detection via the Topic classifier

The engine's own Topic labels are a free entity-resolution probe. When an
ambiguous brand name gets mislabelled into an unrelated category, that is a
concrete GEO / entity-SEO signal that **the brand entity is not resolving
cleanly** in the engine's knowledge graph.

*Illustrative generic case:* consider a brand whose name collides with
unrelated common terms in other fields. The Topic classifier may file some
of the brand's citations under those unrelated categories — none of which
is the brand's actual business. That scatter is the symptom: the engine has
not bound the brand token to the correct entity. (This is a generic
illustration of the pattern, not a specific brand or its specific
misclassifications.)

What to do with it: flag the confused categories, then feed the finding into
entity-disambiguation work (clearer entity signals, sameAs/knowledge-panel
hygiene, on-page disambiguation). Detection here is cheap and the signal is
otherwise hard to get.

### 4. Synthetic-variant share

Copilot auto-generates research-style query variants — patterns like
`<brand> <topic> evaluation`, `<brand> <topic> review`, `<brand> <topic>
overview`. Measuring the share of citations attributable to these
engine-synthesised patterns reveals **how much citation volume is generated
by the engine itself versus organic user demand.** A high synthetic share
means the apparent visibility is partly an artefact of the engine's own
query expansion, not of real-world demand — which changes how much weight to
put on the raw citation totals. Detect synthetic variants with **three
signatures**, and report their combined share of citations:

1. **Research suffixes** — the brand token plus a research-suffix pattern
   (`evaluation`, `review`, `overview`, and similar).
2. **Token-scrambled brand variants** — queries where the brand's tokens
   appear in an order or combination no human would type (word-order swaps,
   brand tokens interleaved with descriptors, partial-token recombinations).
   These surface during the Cut 1 validation pass and are evidence of
   engine-side query synthesis, not user demand — count them as synthetic,
   not as organic branded volume.
3. **Near-total Citation Share** — a query where the site's share is about
   90% or more is one no other site is cited for, a strong sign the engine
   generated it around the brand. This signature is measured rather than
   pattern-matched, so use it to confirm the other two. Where the export has
   no share column, say this signature could not run.

*Illustrative: a synthetic share around a fifth of citations is a plausible
shape — an example, not a norm.*

### 5. Winner-vs-matched-control content test

This is the cut that isolates cause. To test whether content depth drives
citations, do **not** compare cited pages to the site average. Compare cited
pages ("winners") against **zero-grounding pages of the same template and
topic** ("matched controls"). Matching on template and topic removes the
confounds that a naive comparison leaves in.

**Check what the controls are before comparing.** A template's uncited set
is enriched with pages that never had a chance: stubs, near-duplicates and
duplicate slugs (a `-2` suffix, a slug that repeats its own path). Exclude
pages below a stub threshold and duplicate slugs from the controls, or
report them as a separate group. Otherwise the test compares full pages
with stubs, and depth separates the groups for the wrong reason.

When topic matching is impossible — one page per topic — say so. Fall back
to a rank correlation of the content attributes with citations within the
cited pages only, and label it as confounded by topic demand.

Then compare the content attributes you suspect matter (word count, depth,
structure) across winners and controls. Record the result as that
dataset's finding: depth may or may not separate the groups, and neither
outcome generalises. Where it does not separate matched groups, something
other than depth (demand, entity authority, page subject specificity)
drove citations. The design is the durable part; the result will vary.

### 6. Ranking-correlation with an explicit demand confound

It is tempting to correlate citations against a third-party directory or
ranking position and conclude that ranking prestige drives citations. **Do
not report that correlation without separating demand from rank first.** The
usual trap: pages in recognised, well-populated categories tend to *both*
rank in the relevant directories *and* attract high-demand queries.
High-demand queries produce more citations regardless of ranking prestige —
so the apparent rank-citation correlation is often really a **query-demand**
correlation wearing a ranking costume.

**Check rank coverage first.** Compute the share of cited queries, and of
their citations, for which the site has any ranking in the chosen index.
When coverage is low, that is the finding: citations on this dataset are
not explained by rankings in that index. Stop there, because a correlation
over the covered few is precise and irrelevant. Name the index behind every
rank (engine and country). Copilot grounds on Bing's own web results, so a
Google-based rank tool measures a different variable. Within one property,
`Citations ÷ CitationRate` can serve as a demand proxy; never use it across
properties.

Always model or at least control for query demand explicitly before
attributing citations to ranking. If demand is not separated, state plainly
that the correlation is confounded and cannot support a ranking-prestige
claim.

### 7. Query-language composition — multilingual datasets only

When the dataset spans multiple language or country versions of a site,
classify each grounding query by language before comparing versions.
Per-version citation gaps that look like page-quality problems can instead
be **retrieval-language routing**: if the engine's grounding layer skews
towards English regardless of the language the user prompted in, localised
versions end up competing against their own English sibling for the same
grounding queries — producing large per-version gaps with no page-level
defect. The existing cuts attribute version gaps to indexation, hreflang,
freshness or demand; this cut tests the routing mechanism they miss.

How to run it:

1. **Detect query language per row.** Automatic language detection (e.g.
   langdetect) is noisy on 2–4-word queries — do not trust it blindly at
   that length. Classify branded queries and language-neutral vocabulary
   into their own buckets rather than forcing them into a language.
2. **Report per-domain citation share by query language** — local-language
   vs English vs neutral — for every language version. *Illustrative: on a
   site with several language versions, cleanly local-language queries can
   carry only a minority of citation volume on every localised version,
   with the share varying widely between versions — an example, not a
   norm.*
3. **Flag same-query citations split across language versions** as sibling
   competition: when a single grounding query's citations land on both the
   primary (usually English) version and one or more localised versions,
   those versions are competing for the same grounding query rather than
   serving distinct demand.

Know the **data ceiling**: the export exposes the grounding query, not the
language the user prompted in, so grounding-language skew cannot be proven
from this data alone. The cheap manual test that closes the gap: prompt the
assistant in each target language and observe which domains it cites.
Opportunity queries (Input) narrow the gap from the data side.
Another-language queries in a localised domain's opportunity list are
evidence for routing. The brand's own name listed as an opportunity on one
of its domains means a sibling domain takes that citation.

The principle: on multilingual sites, per-version performance gaps may be
retrieval-language routing rather than page quality — test whether versions
are winning local queries or losing English queries to their strongest
sibling **before** diagnosing the pages themselves.

### 8. Competitive share — within one property only

Citations rank queries by demand; Citation Share separates queries the site
owns from queries where it is one of many sources. Place one property's
queries on two axes, citations against share, and read the quadrants. High
volume with high share is owned ground. High volume with low share is
where the brand is present but marginal, which calls for a different
action from absence (the opportunity queries). Run the cut per property: a
share has no common denominator across properties, so never place two
properties' queries on one chart or rank them together.

---

## Sequencing

Run the coverage and concentration checks before any cut. Run the cuts
roughly in order. Cut 1 (branded/non-branded) is mandatory and
goes first because it frames everything after it. Cut 5 (winner-vs-control)
is the highest-value causal cut. Cut 6 carries the biggest interpretation
risk — never publish a ranking correlation without the demand control. Cut 7
applies only when the dataset spans multiple language versions; when it
does, run it before attributing any per-version gap to page quality.

---

## Presenting the cuts

This section lives in `references/presenting-the-cuts.md`. **Load it before drafting the deliverable that presents the cuts.**

---

## Pre-flight verification step

Before declaring the analysis done, re-read the rules above and confirm all
seven:

- **(a)** The **branded vs non-branded split was performed first**, was
  **validated with the second-pass fragment search** of the non-branded
  remainder and the Topic check of the branded set, and is reported as a
  share of citations.
- **(b)** The **demand-vs-rank confound was explicitly addressed** before any
  ranking correlation was claimed — or the correlation is flagged as
  confounded and not relied upon.
- **(c)** If the output is stakeholder-facing, the **"Presenting the cuts"
  rules were applied**: branded share framed as composition with risk labels,
  rankings segmented by market before any aggregation, URLs on every named
  asset, engine set named on every chart.
- **(d)** **Every recommendation's premise was verified**: no "fix X" claim
  entered the deliverable without the existence check and (for version
  gaps) the parity check against the strongest sibling.
- **(e)** If the input is a single snapshot, the **snapshot deliverable arc
  was applied**: pointers, open questions, measurement case — with at most
  one or two no-regret actions.
- **(f)** **For public output, no client-identifying detail leaked**: no
  real brand name, industry, directory or platform name, no real
  misclassification specifics. In any output, no dataset percentage is
  presented as if it were a universal benchmark.
- **(g)** **Coverage is stated beside every share**, the concentration check
  ran (anything above ~20% reported with and without it), and no Citation
  Share was compared or summed across properties.

If any check fails, fix it before delivering.

## Confidentiality post-draft sweep

For **public output** from this skill (a post, a talk, a case study), run a
final sweep: the only proper nouns are the ones the piece needs — no client
brand, industry, directory or platform name, no misclassification
specifics, every number marked illustrative. A slightly more generic finding
is always better than one that narrows down a client. A **client
deliverable** names the client's own brand and site, and labels every figure
as that dataset's, never as a benchmark.

## Feedback on this skill

If the user has methodology feedback, offer to draft a report for
https://github.com/rebelytics/bing-grounding-query-analysis; before drafting, search that
repository's open and closed issues and pull requests for the same problem,
and read its CONTRIBUTING file for how the maintainer takes reports. If the
problem is the agent not following the skill's rules, acknowledge and
correct it instead.

Bing Grounding-Query Analysis: Eoghan Henn and contributors | CC BY 4.0
