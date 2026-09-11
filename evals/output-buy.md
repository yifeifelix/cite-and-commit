QUERY: im looking at a mac mini for running local models. do i need 32gb or is 64 overkill? i mainly do docker + vs code + maybe a 27b model

SKILL_TRIGGERED: True
TOOLS: ['Skill', 'ToolSearch', 'WebSearch', 'WebSearch', 'WebFetch', 'WebSearch', 'WebSearch', 'WebFetch', 'WebFetch', 'WebSearch']

============================================================

**64GB isn't overkill — but it's no longer a RAM checkbox.** In the August 2026 lineup, 32GB is the *ceiling* of the M6 mini and 64GB only exists on the M5 Pro, so you're choosing between a ~$1,500 machine and a ~$2,700 one, not paying for extra sticks. ([Daring Fireball](https://daringfireball.net/2026/08/configurations_and_pricing_for_new_mac_minis_and_mac_studios), [MacRumors](https://www.macrumors.com/2026/08/25/apple-announces-2026-mac-mini/))

## What your workload actually needs

- **The 27B model:** At Q4_K_M the weights are ~16–16.5GB, plus 1–3GB runtime overhead and a KV cache that grows with context. ([Will It Run AI](https://willitrunai.com/models/gemma-3-27b), [LLMHardware](https://llmhardware.io/guides/gemma3-hardware-requirements))
- **Docker Desktop:** Reserves ~4GB by default on macOS and that VM allocation counts against the same physical pool. ([OneUptime](https://oneuptime.com/blog/post/2026-02-08-how-to-configure-docker-desktop-memory-and-cpu-limits-on-macos/view))
- **The catch nobody mentions:** macOS only lets the GPU wire ~66–75% of unified memory by default, so on a 32GB box you're working with roughly 21–24GB before you start tuning `iogpu.wired_limit_mb`. ([ModelPiper](https://modelpiper.com/blog/iogpu-wired-limit-mb-mac))

Add it up on 32GB: model + Docker + VS Code + macOS leaves you a few GB of context headroom. It runs. It won't run comfortably with a long context and a full container stack up at once.

## Speed is the bigger gap, not capacity

The M6 has 170GB/s of memory bandwidth; the M5 Pro has 307GB/s. ([Macworld](https://www.macworld.com/article/2964754/2026-mac-mini-m5-pro-design-specs-release-date.html), [Apple](https://www.apple.com/newsroom/2026/08/apple-introduces-m6-and-m5-ultra-for-a-big-leap-in-performance-and-ai-compute/)) On a 27B-class model at 4-bit that's roughly **6–8 tok/s on the M6 versus 10–14 tok/s on the M5 Pro**. ([Zach Rattner](https://zachrattner.com/projects/ai-mac-cluster/m5-ultra-vs-m5-pro-vs-m6)) Six tokens/sec is slower than you read. That's the number that decides whether you keep using the thing.

## The options priced out

| Config | Price | Verdict |
|---|---|---|
| M6 / 32GB / 512GB | $1,500 | Fine if the 27B is a "maybe" |
| M5 Pro / 48GB / 512GB | $2,300 | The sweet spot for your list |
| M5 Pro / 64GB / 512GB | $2,700 | Only if you'll go past 27B |

## My recommendation

**Get the M5 Pro with 48GB, not 64GB.** Your stated workload — Docker, VS Code, one 27B model — fits in 48GB with real context headroom, and the $600 upgrade buys you the bandwidth that makes the model usable. The extra $400 for 64GB only pays off if you plan to squeeze a 70B at 4-bit or run two models at once. ([Zach Rattner](https://zachrattner.com/projects/ai-mac-cluster/m5-ultra-vs-m5-pro-vs-m6))

If the local model is genuinely a side experiment and Docker is the real job, the **M6/32GB at $1,500** is the honest pick — just know the 27B will feel sluggish.

Bottom line: the 32-vs-64 framing is really M6-vs-M5 Pro, and 48GB is the配置 most people in your position should land on.

Want me to work out what context length you'd actually get on each config with your Docker stack running?