# Cite and Commit

A portable skill that makes an LLM answer research questions the way a good answer
engine does, rather than the way a chat model defaults to: the verdict in the first
sentence, decision-framed sections instead of a taxonomy, a source next to every fact,
an explicit label on anything unconfirmed, and a real recommendation at the close. It
ships as a [Claude Code / Cowork skill](SKILL.md) and as a [portable system
prompt](prompts/system-prompt.md) for any other LLM.

It supports:

- Regulation lookups, product comparisons, entity research, causal "why" questions and
  fast-moving news, in both English and Chinese
- A tiered search strategy — 2 to 5 searches, primary/secondary/community source
  ranking, and source-count ceilings scaled to the question rather than a fixed target
- A fixed answer shape: a cited lead sentence, 3 to 6 decision-framed sections, a
  verdict table where the data is genuinely parallel, and a close that offers, asks or
  concludes
- Inline citations on every factual claim, with explicit epistemic labels (confirmed,
  reported, rumoured) and a date on anything time-sensitive
- A committed recommendation, tiered by scenario and grounded in the facts cited
  above it, rather than a list of undifferentiated options
- A self-contained, model-agnostic system prompt, plus a trimmed variant for tight
  prompt budgets, for use outside Claude Code

## Core advantages

| Advantage | Practical value |
|---|---|
| Reverse-engineered from a real corpus, not authored from a style guide | The rules were extracted from six real answer-engine outputs spanning five question types — regulation, product comparison, entity research, causal explanation and fast-moving news — in English and Chinese, then audited rule-by-rule back against that same corpus (see [`references/backtest.md`](references/backtest.md)). 21 of 22 rules were confirmed as written; the one that failed — allowing bold mid-sentence for general emphasis — was corrected in `SKILL.md`, `prompts/system-prompt.md` and `references/style-rules.md`. |
| Empirically tuned trigger description | Three candidate skill descriptions were run against the same 20-query eval set (10 that should trigger, 10 near-miss negatives) via `claude -p`. A short, descriptive version recalled 1/10; a longer descriptive version with a context list recalled 4/10; both held 0/10 false positives. The current explicit, pushy description with named negative triggers recalled 10/10 with 0/10 false positives (see [Triggering](#triggering)). |
| Portable, model-agnostic system prompt | [`prompts/system-prompt.md`](prompts/system-prompt.md) does not reference the rest of the repository, so it drops straight into ChatGPT custom instructions, a Gemini gem, an OpenWebUI system prompt, or a local model's system message. A trimmed variant is included for models with tight prompt budgets. |
| Citation and commitment enforced as explicit rules, not left to house style | Two rules do the actual work over an unprompted model: every factual claim — every number, date, rule, spec or name — carries an inline source next to the sentence it supports, and every answer ends committed to a recommendation with something specific ruled out, never just a list of options. |
| Worked examples include a deliberate null result | [`references/worked-examples.md`](references/worked-examples.md) gives four annotated examples; the fourth is a search that found nothing, showing how to say so in the lead, keep whatever was found, and name where the real answer actually lives instead of padding the gap with adjacent facts. |
| A self-review checklist, not take-it-on-faith prompting | [`references/style-rules.md`](references/style-rules.md) is a pre-send checklist covering the lead, headings, bullets, citations, honesty, opinion, close and language, plus seven named failure modes — the essay, the hedge, the bibliography, the taxonomy, the confident fabrication, the stale fact, the padded miss — each with a one-line fix. |

## What it changes

| Default LLM behaviour | With this skill |
|---|---|
| Restates the question, then builds up to the answer | Answer is sentence one |
| Headings like *Overview / Key Points / Conclusion* | Headings like *The catch / What I'd buy / When it's a bad fit* |
| Sources dumped at the bottom, or none | A source on each claim; none on your judgement |
| "It depends on your needs" | "Buy the 48GB. Don't pay more for a newer chip capped at 32GB." |
| Undated facts stated flatly | "as of 30 July 2026", rumour labelled as rumour |
| Pads a failed search with adjacent facts | Says it found nothing, and names where the answer lives |
| "Hope this helps!" | A specific offer, or the one input that would sharpen the answer |

## Install

### Claude Code / Cowork

```bash
git clone https://github.com/yifeifelix/cite-and-commit ~/.claude/skills/cite-and-commit
```

Restart, then ask any research question — it triggers on questions needing search.
Or invoke it explicitly: `/cite-and-commit`.

### Any other LLM

```bash
curl -o system-prompt.md https://raw.githubusercontent.com/yifeifelix/cite-and-commit/main/prompts/system-prompt.md
```

Copy [`prompts/system-prompt.md`](prompts/system-prompt.md) into your system prompt,
custom instructions, or gem. It's self-contained. A trimmed version is included for
models with tight prompt budgets.

## Use

Once installed, the skill triggers automatically: its frontmatter `description` (in
[`SKILL.md`](SKILL.md)) tells Claude to reach for it whenever a good answer depends on
looking something up rather than recalling it — prices, rules, news, comparisons,
buying decisions, entity research — and to skip it for coding, file edits, creative
writing, or trivial one-value lookups such as the weather. No explicit call is needed
for a normal research question.

To force it on a question that might not trigger automatically, invoke it directly
with `/cite-and-commit` in Claude Code or Cowork. Outside Claude Code, the skill has no
automatic trigger at all — the copied system prompt applies to every reply, so it
suits a dedicated research assistant, gem, or chat rather than a general-purpose one.

## The four rules that do most of the work

1. **The lead is the answer.** If the user reads only the first sentence they should have
   what they came for.
2. **Headings are decisions, not categories.** Rewrite every heading as the question that
   section answers.
3. **Cite facts, don't cite yourself.** The visual absence of a source is how the reader
   knows a line is your judgement.
4. **Commit.** An answer that only lists options has moved the work back onto the user.

## Why the name

The scannable shape — verdict first, bold labels, a comparison table with a verdict
column — is something a good model already reaches for unprompted. Measured against a
no-skill baseline, that part came out largely the same. What the baseline did *not* do
was search, cite, or flag what it wasn't sure of; on one test question it answered a
factual "why" from memory with zero sources. So the two rules the skill actually buys
you are the two in the name: **cite** every fact, and **commit** to a recommendation.

## Triggering

The description in the frontmatter is the only thing Claude sees when deciding whether to
consult this skill, and it was tuned empirically rather than written by feel. Measured over
20 realistic queries (10 that should trigger, 10 near-miss negatives), run against
`claude -p` with the skill installed:

| Description | Recall | False positives |
|---|---|---|
| Short, descriptive ("Answer research questions like a citation-first answer engine…") | 1/10 | 0/10 |
| Long, descriptive + context list | 4/10 | 0/10 |
| **Current — explicit, pushy, with negative triggers** | **10/10** | **0/10** |

The lesson generalises: Claude under-triggers skills far more than it over-triggers them.
A description that says what the skill *does* loses to one that says, in the user's own
messy phrasing, *when to reach for it* — and that names what it should not be used for.
The line that moved the needle most was making the trigger mechanical:
"if you are about to run a web search to answer a question, use this skill."

The eval set is in [`evals/trigger-eval.json`](evals/trigger-eval.json), and the three
runs behind the table above are recorded in `evals/probe-A-short-desc.json`,
`evals/probe-B-long-desc.json` and `evals/probe-C-pushy-desc.json`. Re-run the eval set
after any description change.

## Repository layout

```text
SKILL.md                        the skill itself
prompts/system-prompt.md        portable copy-paste version (+ trimmed variant)
references/style-rules.md       pre-send checklist and failure modes
references/output-template.md   skeleton, heading bank, bullet and table patterns
references/worked-examples.md   four annotated examples incl. a null result
references/backtest.md          rule-by-rule audit against the source corpus
evals/trigger-eval.json         20-query trigger eval set (10 positive, 10 negative)
evals/probe-A-short-desc.json   trigger run against the short description
evals/probe-B-long-desc.json    trigger run against the long description
evals/probe-C-pushy-desc.json   trigger run against the current description
LICENSE                         MIT
```

## Limits

- The skill constrains the *shape* of an answer — lead, headings, citations, close —
  but it cannot verify that a cited source is itself correct, current, or the best
  available one. Garbage in, well-formatted garbage out.
- The trigger eval is 20 queries run once against a single harness (`claude -p`). It
  shows the current description beats the two weaker ones on this set; it is not a
  statistically powered study and does not cover every phrasing a real user might use.
- The backtest in `references/backtest.md` audits the rules against the six-output
  corpus they were derived from, not against fresh, unseen questions — it confirms
  internal consistency, not out-of-sample generalisation.
- It deliberately does not apply to coding, file editing, drafting messages, searching
  the user's own email or documents, recalling earlier conversation, or trivial
  one-value lookups such as the weather or the time — see the "Skip it for" clause in
  `SKILL.md`'s frontmatter.
- Outside Claude Code, the portable system prompt has no trigger logic of its own; it
  applies to every reply in whatever surface it's pasted into, so it suits a dedicated
  research assistant rather than a general-purpose one.
- The worked examples in `references/worked-examples.md` use illustrative placeholder
  figures and sources — they demonstrate structure, not verified data, and are labelled
  as such in the file.

## Contributors

Fei ([@yifeifelix](https://github.com/yifeifelix))

## Licence

[MIT](LICENSE)
