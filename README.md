# bing-grounding-query-analysis

Open-source skill that **applies a repeatable analysis layer to Bing Webmaster Tools AI grounding-query data once it has been extracted into a spreadsheet** — the eight analytical cuts that turn a raw per-page citation export into decisions. Two matter most: the branded vs non-branded split and the matched winner-vs-control content test, the cut that actually isolates cause. It is the analysis companion to the extraction skill [`bing-wmt-ai-grounding-export`](https://github.com/rebelytics/bing-wmt-ai-grounding-export), which produces the spreadsheet this skill consumes. Load it into any MCP-capable AI agent (Claude, Cursor, Codex, n8n, etc.).

Part of the [Open Skill Library](https://github.com/rebelytics/one-skill-to-rule-them-all/blob/main/OPEN-SKILL-LIBRARY.md), the open-source agent skills maintained with Task Observer.

## When it triggers

Whenever the user wants to analyse Bing grounding queries, do AI citation analysis or Copilot citation analysis, split branded vs non-branded citations, detect GEO entity confusion, or run an AI visibility analysis of Bing data. Also on mentions of "branded vs non-branded citations", "grounding query analysis", "AI citation analysis", "entity confusion", "synthetic query share", or "what's driving our AI citations" in a Bing / Copilot context.

## What this repo contains

- `SKILL.md` — why the skill exists, the input it expects, the coverage and concentration checks that come first, the eight analytical cuts (branded vs non-branded split, intent and topic distribution weighted by citations, entity-confusion detection via the Topic classifier, synthetic-variant share, the winner-vs-matched-control content test, ranking correlation with an explicit demand confound, query-language composition, competitive share within one property), the sequencing, the pre-flight, the confidentiality post-draft sweep, and the pointer to the reference file.
- `references/presenting-the-cuts.md` — how the cuts are presented, and how recommendations are validated before they enter a deliverable.
- `LICENSE` — CC BY 4.0.

## Install

Install the whole skill directory — `SKILL.md` and `references/` must travel together — into your MCP-capable agent's skills directory, following the client-specific path, and restart the client.

## Contributing

Open an issue or a PR at [github.com/rebelytics/bing-grounding-query-analysis](https://github.com/rebelytics/bing-grounding-query-analysis).

## Credits

- Original author: [Eoghan Henn](https://www.rebelytics.com) / [LinkedIn](https://www.linkedin.com/in/eoghanhenn)

## License

CC BY 4.0 — the licence text is in [LICENSE](./LICENSE): the full legal code, unmodified, as Creative Commons publishes it; the [human-readable summary](https://creativecommons.org/licenses/by/4.0/) says what it allows. Use it, fork it, adapt it, monetise it. Keep the attribution.

Copyright (c) 2026 Eoghan Henn / rebelytics.com
