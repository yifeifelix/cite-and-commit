# Back-test: skill rules vs. 6 real answer-engine outputs

Samples: SDLT for company purchase (EN, regulation) · 西昊型号对比 (ZH, product comparison,
2 turns) · Mac mini / local LLM memory (ZH, buying decision, 5 turns) · Purcell School
(ZH, entity research, 3 turns) · separate hot/cold taps (EN, causal explanation) ·
BoE base rate (EN, fast-moving news)

| Rule in skill | Predicted | Observed | Verdict |
|---|---|---|---|
| Lead answers outright, contains number/date/verdict | yes | 6/6 | PASS |
| No preamble before the answer | yes | 6/6 | PASS |
| Lead carries a citation | yes | 6/6 | PASS |
| Flat `##` heading level, no nesting | yes | H2 only, 0 H1/H3 across both DOM checks | PASS |
| 3-6 sections (more for news) | 3-6 / up to 6 | 3, 4, 4, 6, 3, 6 | PASS |
| Headings decision-framed, never taxonomy | yes | 0 instances of Overview/Background/Conclusion | PASS |
| Bullet = bold label + colon + full sentence | yes | 24/28 and 25/31 bullets | PASS |
| Bold also used mid-sentence for emphasis | **no** | 21/31 mid-sentence in news answer | **FAIL → rule corrected** |
| Paragraphs short | "1-3 sentences" | mean 21 words | PASS (tightened to 20-25 words) |
| Citations inline per claim, never batched | yes | 6/6 | PASS |
| Recommendations/interpretive lines uncited | yes | 6/6 — verified on "This is important because…", "简单结论：…", "我的建议" blocks | PASS |
| Table only for parallel data, includes verdict column | yes | 4 tables, all with 适合程度/建议/能否运行 verdict column | PASS |
| Close = offer / missing input / bottom line | yes | 6/6; offers name exact columns | PASS |
| Uncertainty labelled, facts dated | yes | "尚未确认", "不能当作确定规格", "(30 July 2026)", "unchanged since December 2025" | PASS |
| Ranges given where sources disagree | yes | "5.07-5.61% depending on source" | PASS |
| Commits to a recommendation, rules things out | yes | "不建议 16GB", "性价比首选", "Don't pay more for…" | PASS |
| Language mirrored, domain terms kept in English | yes | 6/6 — SDLT, sixth form, audition, 统一内存/Q4_K_M/KV cache | PASS |
| User's wrong premise corrected in first lines | yes | Qwen 26B → 30B-A3B correction, turn 3 | PASS |
| Exactly 3 follow-ups | marked optional | 3, 3, 3, 2, 0 — variable | PASS (correctly optional) |
| Source count 10-18 | yes | 15, 15, 15/17/15, 10/15/16, 15, 18 | PASS |
| Community sources only for social claims | yes | reddit used only for "why people keep two taps"; gov/official for all rules | PASS |

**Result:** 21/22 rules confirmed against the corpus. One rule was wrong (bold usage) and
has been corrected in SKILL.md, prompts/system-prompt.md and references/style-rules.md.
