# Worked examples

> **Note:** the facts, figures and sources below are *illustrative placeholders*, and the
> links deliberately point at `example.invalid` so they can never be mistaken for real
> citations. These examples demonstrate structure, not verified data. Never copy the
> numbers, and never copy a placeholder link into a real answer.

---

## Example 1 — Rule / regulation question

**Question:** "What's the stamp duty for a limited company buying a residential property?"

> For a limited company buying residential property in England or Northern Ireland,
> the higher residential SDLT rates apply — running from 5% up to 17% since 1 April 2025. ([gov.uk](https://example.invalid/source))
>
> ## Main rates
>
> The higher-rate bands are: up to £125,000 at 5%; £125,001-£250,000 at 7%;
> £250,001-£925,000 at 10%; £925,001-£1.5m at 15%; above £1.5m at 17%. ([gov.uk](https://example.invalid/source))
>
> ## The exception that catches people out
>
> - **Single dwelling over £500,000:** a flat 17% can apply to the *whole* price, not
>   just the top slice, unless a relief is claimed. ([gov.uk](https://example.invalid/source))
> - **Reliefs exist but must be claimed:** genuine rental or development structures may
>   fall back to the banded rates, but the conditions are narrow. ([Farrer & Co](https://example.invalid/source))
>
> ## Worked example
>
> A £600,000 purchase with no relief and the flat corporate rate applied: roughly
> £102,000 in SDLT. ([calculator](https://example.invalid/source))
>
> Give me the actual purchase price and whether you're claiming a relief and I'll
> work out the real figure both ways.

**What to notice**
- The lead has the range *and* the effective date. No preamble.
- "The exception that catches people out" — decision-framed, not "Exceptions".
- The worked example is its own section because a number people can check is worth surfacing.
- Close names the two inputs it needs, not a vague offer of help.
- The SDLT term stays in English regardless of answer language.

---

## Example 2 — Buying decision (table earns its place)

**Question:** "How much memory do I need to run a 27B model locally?"

> Buy 48GB. 32GB is the floor for stable 4-bit use, and 24GB will run it but leave you
> nothing for the rest of your machine. ([Hugging Face](https://example.invalid/source))
>
> ## Why 32GB is the floor
>
> The Q4_K_M weights are around 17GB, but you also need KV cache, context, and whatever
> else you have open. A 24GB machine has about 7GB left over once weights are loaded —
> and the OS wants a chunk of that. ([Hugging Face](https://example.invalid/source))
>
> ## Memory by quantisation
>
> | Memory | Verdict | What it gets you |
> |---|---|---|
> | 24GB | Tight | Q4 at short context, nothing else running |
> | 32GB | Minimum | Stable Q4 with an editor open |
> | 48GB | Recommended | Longer context, Docker alongside |
> | 64GB | Only for Q6/Q8 | Higher quantisations, vision input |
>
> ## The context-length trap
>
> A model advertising 262K context does not mean 262K is usable on your hardware.
> Plan for 8K-32K in practice and treat the headline number as a ceiling. ([Hugging Face](https://example.invalid/source))
>
> ## What I'd do
>
> - **Budget-limited:** 32GB, and keep context short.
> - **Want it to last:** 48GB — this is the sweet spot.
> - **Wouldn't bother:** paying more for a newer chip capped at 32GB.
>
> Bottom line: 48GB and a 1TB SSD. If the configuration you're looking at tops out at
> 32GB, buy the older machine with more memory instead.

**What to notice**
- Lead is a verdict, not a survey of options.
- The table has a **Verdict** column — that's what makes it worth a table.
- "The context-length trap" corrects a misconception the user probably has.
- "What I'd do" bullets are uncited — they're judgement, and the visual absence of a
  source is the signal.
- Bottom line ends it, and rules something *out*.

---

## Example 3 — Explanatory "why" question

**Question:** "Why do older UK houses still have separate hot and cold taps?"

> Because the hot and cold water came from two different sources with two different
> safety standards, and the plumbing rules kept them physically apart until the basin. ([BBC](https://example.invalid/source))
>
> ## The core reason: two water sources
>
> - **Cold** came straight off the mains and counted as potable. ([BBC](https://example.invalid/source))
> - **Hot** was heated in a cylinder fed by an open loft tank, which wasn't held to
>   drinking-water standards and could pick up sediment or contamination. ([BBC](https://example.invalid/source))
> - **Mixing them inside the tap body** risked backflow into the mains supply, so
>   bylaws discouraged it. Meeting in the basin carried no such risk. ([WRAS](https://example.invalid/source))
>
> ## Why they're still around
>
> - **Legacy pipework:** the basin and pipe runs were built for two tap holes; fitting
>   a mixer often means a new basin. ([forum discussion](https://example.invalid/source))
> - **No pressure to change:** a working system faces no regulatory deadline.
> - **Period aesthetics:** in Victorian and Edwardian houses some owners keep them on purpose.
>
> ## Practical upshot
>
> Modern regulations do allow mixers — they just require backflow protection such as
> check valves, which is the same safety concern solved by engineering rather than
> by separation. ([WRAS](https://example.invalid/source))
>
> If you want to convert one, a plumber can fit a mixer basin with the right valves;
> tell me whether your hot water is tank-fed or unvented and I'll tell you which type you need.

**What to notice**
- Lead gives the actual causal mechanism, not "there are several reasons".
- Forum source used only for "people leave them because it's a hassle" — a social claim.
  The regulatory claims come from official sources.
- "Practical upshot" turns history into something actionable.

---

## Example 4 — When the search finds nothing

**Question:** "What did number 14 on this street sell for in 2013?"

> I couldn't find a recorded sale for that address in 2013 — the public price-paid data
> shows transactions in 2007 and 2019 but nothing in between. ([HM Land Registry](https://example.invalid/source))
>
> ## What the record does show
>
> - **2007:** sold as a new build. ([HM Land Registry](https://example.invalid/source))
> - **2019:** sold again, freehold. ([HM Land Registry](https://example.invalid/source))
>
> ## Where a 2013 transfer would actually appear
>
> If it changed hands in 2013 without a price being recorded — a transfer between family,
> a probate transfer, or a lease extension — it wouldn't show in price-paid data at all.
> The register's title documents would show it, and those cost a few pounds to download
> from the Land Registry directly.
>
> Want me to walk you through pulling the title register for that title number?

**What to notice**
- The miss is stated in the first line. No hedging, no padding.
- What *was* found is given anyway — it's still useful.
- The answer names **where the information actually lives**. This is the most valuable
  part of a null result and the thing most assistants skip.
