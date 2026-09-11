# Cite and Commit

Cite and Commit is a portable prompting skill that makes a large language model answer
research questions the way a good answer engine does: the answer in the first sentence,
a scannable body organised under decision-framed headings, a source attached to every
fact, uncertainty explicitly labelled rather than smoothed over, and a genuine
recommendation at the close instead of a list of options. It ships two ways: as a
Claude Code / Cowork skill (`SKILL.md` plus reference files) and as a self-contained
system prompt for any other LLM. The rules were not designed from first principles —
they were reverse-engineered from a corpus of real answer-engine outputs, then audited
rule by rule back against that same corpus.

It covers:

- Search-first behaviour: multiple varied searches, a source-priority order
  (primary/official, then reputable secondary, then community), and a source count
  scaled to the question type rather than a fixed number.
- A lead sentence that is the answer, under decision-framed headings rather than
  generic ones such as *Overview* or *Conclusion*.
- A citation on every factual claim, and no citation on the model's own judgement, so
  the reader can see at a glance which lines are sourced and which are opinion.
- Explicit labelling of anything unconfirmed, dated statement of anything time-sensitive,
  and a stated range where sources disagree.
- A closing recommendation that commits and rules things out, rather than "it depends".
- A portable system-prompt version for use outside Claude Code.

## What it changes

| Default LLM behaviour | With this skill |
|---|---|
| Restates the question, then builds up to the answer | Answer is sentence one |
| Headings like *Overview / Key Points / Conclusion* | Headings like *The catch / What I'd buy / When it's a bad fit* |
| Sources dumped at the bottom, or none | A source on each claim; none on the model's own judgement |
| "It depends on your needs" | "Buy the 48GB. Don't pay more for a newer chip capped at 32GB." |
| Undated facts stated flatly | "as of 30 July 2026", rumour labelled as rumour |
| Pads a failed search with adjacent facts | Says it found nothing, and names where the answer lives |
| "Hope this helps!" | A specific offer, or the one input that would sharpen the answer |

## Core rules

Four rules do most of the work; everything else in `SKILL.md` and the reference files
exists to support them.

1. **The lead is the answer.** If the reader takes in only the first sentence, they
   should already have what they came for.
2. **Headings are decisions, not categories.** Every heading is rewritten as the
   question that section answers.
3. **Cite facts, don't cite yourself.** The visual absence of a source is how the
   reader knows a line is the model's judgement rather than a looked-up fact.
4. **Commit.** An answer that only lists options has moved the work back onto the
   reader.

The name follows from the two rules a competent model does not already apply
unprompted. The scannable shape — verdict first, bold labels, a comparison table with
a verdict column — is something a good model tends to reach for on its own. What a
no-skill baseline did not reliably do was search, cite, or flag what it was unsure of.
So the two rules the skill actually buys are the two in the name: cite every fact, and
commit to a recommendation.

## Install

**Claude Code / Cowork:**

```bash
git clone https://github.com/yifeifelix/cite-and-commit ~/.claude/skills/cite-and-commit
```

Restart, then ask any research question — it triggers on questions that need a
web search. It can also be invoked explicitly.

**Any other LLM:**

Copy [`prompts/system-prompt.md`](prompts/system-prompt.md) into your system prompt,
custom instructions, or gem. It is self-contained and does not depend on the rest of
this repository. A trimmed variant is included for models with tight prompt budgets.

## Usage

Once installed in Claude Code or Cowork, the skill is meant to trigger on its own: ask
a question whose answer depends on looking something up — a price, a rule, a
comparison, a "why does this happen" — and the skill's rules apply automatically. It
can also be invoked directly:

```text
/cite-and-commit What's the stamp duty for a limited company buying a residential property?
```

For any other LLM, once `prompts/system-prompt.md` is in place there is nothing further
to invoke — every subsequent research question is answered under the same rules.

## Repository layout

```text
SKILL.md                        the skill itself
prompts/system-prompt.md        portable copy-paste version (+ trimmed variant)
references/style-rules.md       pre-send checklist and failure modes
references/output-template.md   skeleton, heading bank, bullet and table patterns
references/worked-examples.md   four annotated examples incl. a null result
references/backtest.md          rule-by-rule audit against the source corpus
evals/trigger-eval.json         the trigger eval set (10 positive, 10 negative)
evals/probe-*.json              the three measured runs against that eval set
```

`references/backtest.md` and `evals/` are provenance and testing records rather than
runtime references: they document how the rules were validated and how triggering was
measured, and the skill does not read them while answering a question.

## How it was built

The rules were reverse-engineered from a corpus of six real answer-engine outputs
spanning five question types — regulation lookup, product comparison, entity research,
causal explanation, and fast-moving news — in both English and Chinese. They were then
audited back against that same corpus, rule by rule, in
[`references/backtest.md`](references/backtest.md): 22 rules were checked, 21 were
confirmed, and one was corrected. The rule on bold text had assumed bold marked bullet
labels only; the audit showed bold also carries the decisive fact in the lead sentence,
and the rule was rewritten to cover both roles.

## Triggering

The description in the frontmatter is the only thing Claude sees when deciding whether
to consult this skill, so it was tuned empirically rather than written by feel.
Measured over 20 realistic queries (10 that should trigger, 10 near-miss negatives),
run against `claude -p` with the skill installed, one run per query:

| Description | Recall | False positives |
|---|---|---|
| Short, descriptive | 1/10 | 0/10 |
| Long, descriptive, with a context list | 4/10 | 0/10 |
| Current: explicit, with negative triggers | 10/10 | 0/10 |

The lesson generalises: Claude under-triggers skills far more than it over-triggers
them. A description that says what the skill does loses to one that says, in the
user's own messy phrasing, when to reach for it — and that also names what it should
not be used for. The single most effective line was making the trigger mechanical:
"if you are about to run a web search to answer a question, use this skill."

The eval set is [`evals/trigger-eval.json`](evals/trigger-eval.json); the three
measured runs are the `evals/probe-*.json` files. Re-run the eval after any
description change.

## Output testing

Triggering and output quality are separate questions, and the second one is the harder to
measure. Four queries were run end to end with the skill installed and web search enabled
— a regulation question, a buying decision, a deliberately unanswerable lookup, and a
single-fact query — and the answers were audited against the skill's own checklist. An
earlier round, before the fixes below, is what produced the fixes:

| Check | Before | After |
|---|---|---|
| Bold used as a bullet label rather than mid-sentence | 3/24 bullets | 12/12 bullets |
| Nested headings (`###`) | 0 | 0 |
| Within the word budget for the question type | 0/3 | 2/4, the other two marginal |
| Trailing Sources list contradicting the inline rule | 2/3 answers | 0/4 answers |

What held from the start: the lead sentence carrying the answer, flat headings, inline
citations, labelled uncertainty, and — the rule most likely to fail — the null result. Asked
for a house price that does not exist in the record, the skill said so in the first line,
gave the sales that do exist, explained which transfers never reach the register, and named
where the answer would actually live.

The citations were then verified against the primary source rather than taken on trust:
every figure in the null-result answer was checked against HM Land Registry Price Paid
Data and matched, including the claim that only two properties on that street sold in the
year asked about.

## Limits

- The output format assumes a markdown-rendering surface. Plain-text hosts will show
  the raw `##` and `**bold**` markup.
- The skill assumes the host has a web search tool available; it has no fallback
  behaviour for a host that cannot search.
- The source corpus is small: six real answers across five question types. A rule
  that held across all six is not the same as a rule proven at scale.
- The trigger measurements above are single runs per query, not averaged over
  repeats, so the recall figures should be read as directional rather than exact.
- The output testing covers four queries on one host. The structural results are hard
  signals (a count of zero nested headings across four answers means something); the
  formatting and length percentages come from too small a sample to be precise.
- Word budgets are still overshot at the margin. The cut-a-whole-section rule reduced the
  overshoot substantially but did not eliminate it.

## Licence

MIT
