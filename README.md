# The LLM Reference Card: Providers, Models & Benchmarks (2026 Snapshot)

**Snapshot date:** August 29, 2026
**Primary source:** [llm-stats.com](https://llm-stats.com) (LLM Leaderboard, Open LLM Leaderboard, per-model pages, benchmark pages), cross-checked against provider documentation, model/system cards, and technical reports.
**Scope:** Frontier and widely-deployed commercial (closed-weight) models and the leading open-weight model families, across the providers that matter most in production use today.

> ⚠️ **This field moves in weeks, not years.** Every table below is a snapshot. Context windows, prices, and benchmark leaders shift with almost every release cycle (some providers now ship point releases every 2–4 weeks). Treat exact scores as directional, re-verify anything price- or SLA-critical, and prefer relative comparisons ("Model A currently beats Model B on X") over absolute claims.

---

## Table of Contents

1. [How to Read This Card (Glossary)](#1-how-to-read-this-card-glossary)
2. [Provider Landscape Overview](#2-provider-landscape-overview)
3. [Commercial (Closed-Weight) Flagship Models](#3-commercial-closed-weight-flagship-models)
4. [Open-Weight Flagship Models](#4-open-weight-flagship-models)
5. [Efficient / Small Models (Edge & Cost-Sensitive Tier)](#5-efficient--small-models-edge--cost-sensitive-tier)
6. [Benchmark Reference Table](#6-benchmark-reference-table)
7. [Workload → Recommended Model Matrix](#7-workload--recommended-model-matrix)
8. [Current Leaderboard Snapshot](#8-current-leaderboard-snapshot)
9. [Pricing Cheat-Sheet](#9-pricing-cheat-sheet)
10. [Caveats & Methodology Notes](#10-caveats--methodology-notes)
11. [Sources](#11-sources)

---

## 1. How to Read This Card (Glossary)

| Term | Meaning |
|---|---|
| **Context window** | Max combined input + output tokens a model can process in one call. Shown as "input / output" where providers cap output separately (e.g., 1.0M in / 128K out). |
| **Knowledge cutoff** | The last date of data in the pretraining corpus. Not the same as release date — models are typically released 3–12 months after their data cutoff. |
| **Parameters (dense)** | Every weight is used on every forward pass (e.g., a 70B dense model uses all 70B weights per token). |
| **Parameters (MoE / Mixture-of-Experts)** | Model has many "expert" sub-networks; only a subset activates per token. Reported as **Total / Active** (e.g., DeepSeek-R1 = 671B total / 37B active). Active parameters roughly drive inference cost and speed; total parameters roughly drive capacity and memory footprint. |
| **Reasoning / "thinking" model** | Model trained to emit extended chain-of-thought before answering (e.g., o-series, DeepSeek-R1, Claude extended thinking, Gemini "thinking"). Usually scores higher on math/science benchmarks at the cost of latency and token spend. |
| **License — Proprietary** | API/product access only; no downloadable weights. |
| **License — Open-weight (MIT / Apache 2.0)** | Weights downloadable, self-hostable, no restrictions on commercial use. |
| **License — Community / Research license** | Weights downloadable but with usage caveats (e.g., Llama 4's Community License restricts companies with >700M MAU; Mistral's Research License restricts commercial self-deployment without a separate commercial license). |
| **Self-reported vs. verified benchmark** | Most frontier scores in 2025–2026 are **self-reported** by the lab in a model/system card, not independently re-run. Treat cross-lab comparisons with a healthy discount, especially near the top of a leaderboard. |
| **Blended price** | A single $/M-token number combining input and output pricing at a fixed ratio (llm-stats uses 8:1 input:output to approximate typical chat/agent traffic) — useful for quick cost comparison across models with different in/out pricing structures. |
| **TTFT** | Time-to-first-token — latency before streaming begins; matters for interactive chat UX. |

---

## 2. Provider Landscape Overview

| Provider | HQ / Country | Type | Flagship model family (2026) | Notes |
|---|---|---|---|---|
| **OpenAI** | USA | Commercial + selective open-weight | GPT-5.x / GPT-5.6 (Sol, Terra, Luna) | Also ships open-weight **GPT-OSS** (120B/20B, Apache 2.0) — first open release since GPT-2. |
| **Anthropic** | USA | Commercial only | Claude Opus 5, Sonnet 5, Haiku 4.5, Fable 5 | New **Mythos** tier sits above Opus; **Claude Mythos Preview** is research-only (Project Glasswing), not publicly released. |
| **Google DeepMind** | USA | Commercial + open-weight | Gemini 3 Pro / Flash / Flash-Lite | Also ships open-weight **Gemma 3** family (1B–27B). |
| **xAI** | USA | Commercial + partial open-weight | Grok 4 / Grok 4 Heavy / Grok 4.5 / Grok-4 Fast | Has previously open-sourced older Grok-1 and Grok-2 weights. |
| **Amazon** | USA | Commercial only | Nova Premier / Pro / Lite / Micro | Bedrock-native; positioned for cost/latency and as a distillation "teacher" model. |
| **Microsoft** | USA | Commercial + open-weight | MAI-Thinking-1 (in-house) | Also ships open-weight **Phi** small-model family; hosts most third-party frontier models via Azure. |
| **Cohere** | Canada | Commercial + partial open-weight | Command A / Command A Reasoning | Enterprise/RAG and tool-use focus; Command R/R+ generations were released with more open access. |
| **Meta** | USA | Open-weight (community license) | Llama 4 (Scout, Maverick); newer "Muse" line emerging in 2026 | Llama 4 Behemoth (2T total / 288B active) was announced but its release has been repeatedly delayed. |
| **Mistral AI** | France | Open-weight + commercial API | Mistral Large 3, Medium 3.5, Small, Ministral | Mixes fully open releases with a hosted commercial API layer (La Plateforme). |
| **DeepSeek** | China | Open-weight (MIT) | DeepSeek-V3 / R1 / V3.2 / V4 | Triggered the 2025 "efficiency shock" with low-cost frontier-class training and inference. |
| **Alibaba Cloud (Qwen Team)** | China | Open-weight (mostly Apache 2.0) | Qwen3, Qwen3-Max, Qwen3.5–3.8 | Broadest size range of any single family (dense + MoE, 0.5B → 2T+). |
| **Zhipu AI / Z.ai** | China | Open-weight (MIT) | GLM-4.5 / 4.6 / 5 / 5.x | GLM-5 (744B) trained on domestic (non-NVIDIA) accelerators. |
| **Moonshot AI** | China | Open-weight (modified MIT) | Kimi K2 / K2.5 / K3 | Pioneered very-long-context, trillion-parameter open MoE models with agent-swarm features. |
| **MiniMax / ByteDance / Xiaomi / StepFun / Tencent** | China | Open-weight | M2/M3 (MiniMax), Seed 2.0 (ByteDance), MiMo (Xiaomi), Step-3.5 (StepFun), Hy3 (Tencent) | Fast-growing second tier of Chinese open labs, frequently near frontier on cost-adjusted benchmarks. |
| **NVIDIA** | USA | Open-weight | Nemotron 3 (Ultra/others) | Built on top of/alongside Llama and other bases; optimized for enterprise inference stacks. |

---

## 3. Commercial (Closed-Weight) Flagship Models

All prices are **$ per million tokens, input / output**, list price at the primary provider. Context is **input window** unless otherwise noted. "—" = not publicly disclosed.

| Model | Provider | Released | Params | Context (in/out) | Knowledge cutoff | Modalities (in) | Price $/M (in/out) | License |
|---|---|---|---|---|---|---|---|---|
| **GPT-5.6 Sol** | OpenAI | 2026 | undisclosed | 1.1M / 128K | undisclosed | text | $5.00 / $30.00 | Proprietary |
| **GPT-5.6 Terra** | OpenAI | 2026 | undisclosed | 1.1M | undisclosed | text | $2.00 / $12.00 | Proprietary |
| **GPT-5.6 Luna** | OpenAI | 2026 | undisclosed | 1.1M | undisclosed | text | $0.20 / $1.20 | Proprietary |
| **GPT-5.5** | OpenAI | Apr 23, 2026 | undisclosed | 1.1M | undisclosed | text+image | $5.00 / $30.00 | Proprietary |
| **GPT-5.2 / GPT-5.2 Pro** | OpenAI | Dec 11, 2025 | undisclosed | 400K | undisclosed | text+image | $1.75 / $14.00 | Proprietary |
| **GPT-5.1** | OpenAI | Nov 13, 2025 | undisclosed | 400K / 128K | Sep 2024 | text+image | $1.25 / $10.00 | Proprietary |
| **GPT-5** | OpenAI | Aug 7, 2025 | undisclosed | 400K | undisclosed | text+image | — | Proprietary |
| **Claude Opus 5** | Anthropic | Jul 24, 2026 | undisclosed | 1.0M / 128K | undisclosed | text+image | $5.00 / $25.00 (cached in $0.50) | Proprietary |
| **Claude Fable 5** | Anthropic | Jun 9, 2026¹ | undisclosed | 1.0M | undisclosed | text+image | $10.00 / $50.00 | Proprietary |
| **Claude Sonnet 5** | Anthropic | 2026 | undisclosed | 1.0M | undisclosed | text+image | $2.00 / $10.00 | Proprietary |
| **Claude Opus 4.6** | Anthropic | Feb 5, 2026 | undisclosed | 1.0M / 128K | undisclosed | text+image | $5.00 / $25.00 | Proprietary |
| **Claude Sonnet 4.6** | Anthropic | 2026 | undisclosed | 200K | undisclosed | text+image | $3.00 / $15.00 | Proprietary |
| **Claude Opus 4.5** | Anthropic | Nov 24, 2025 | undisclosed | 200K / 64K | Nov 2025 | text+image | $5.00 / $25.00 | Proprietary |
| **Claude Haiku 4.5** | Anthropic | Oct 2025 | undisclosed | 200K | undisclosed | text+image | $1.00 / $5.00 | Proprietary |
| **Gemini 3.1 Pro** | Google | 2026 | undisclosed | 1.0M | undisclosed | text+image+audio+video | $2.50 / $15.00 | Proprietary |
| **Gemini 3 Pro** | Google | Nov 18, 2025 | undisclosed | 1.0M / 65.5K | Jan 2025 | text+image+audio+video | $2.00 / $12.00 | Proprietary |
| **Gemini 3 Flash** | Google | 2025/26 | undisclosed | 1.0M | undisclosed | text+image+audio+video | $0.50 / $3.00 | Proprietary |
| **Gemini 3.1 Flash-Lite** | Google | 2026 | undisclosed | 1.0M | undisclosed | text+image | $0.25 / $1.50 | Proprietary |
| **Grok 4.5** | xAI | 2026 | undisclosed | 500K | undisclosed | text+image | $2.00 / $6.00 | Proprietary |
| **Grok-4 Heavy** | xAI | Jul 9, 2025 | undisclosed | — | Dec 2024 | text+image | — | Proprietary |
| **Grok-4 Fast Reasoning** | xAI | 2025 | undisclosed | ~2.0M (largest tracked) | undisclosed | text+image | — | Proprietary |
| **Amazon Nova Premier** | Amazon | Oct 31, 2025 | undisclosed | 1.0M / 25K out | Oct 2024 | text+image+video | $2.50 / $12.50 | Proprietary |
| **Amazon Nova Pro / Lite / Micro** | Amazon | 2024–25 | undisclosed | 300K (Pro/Lite) / 128K (Micro) | Dec 2024 | text(+image/video for Pro/Lite) | $0.04–$0.80 range | Proprietary |
| **Cohere Command A** | Cohere | Mar 2025 | ~111B (dense) | 256K | undisclosed | text | enterprise/negotiated | Proprietary |

¹ Claude Fable 5 (and its research-only sibling **Claude Mythos 5 / Claude Mythos Preview**) launched June 9, 2026; U.S. export-control action briefly suspended access June 12–July 1, 2026. Claude Mythos Preview itself remains unreleased to the public (Project Glasswing).

---

## 4. Open-Weight Flagship Models

Sorted roughly by capability tier. **Total / Active** parameters given for MoE models.

| Model | Provider | Released | Parameters | Context | License | Notable benchmark |
|---|---|---|---|---|---|---|
| **Kimi K3** | Moonshot AI | 2026 | 2.8T (MoE) | 1.0M | Modified MIT | GPQA-D 93.5% (#1 open) |
| **DeepSeek-V4-Pro-0813 / -Max** | DeepSeek | 2026 | 1.6T (MoE) | 1.0M | MIT/Open | SWE-bench Verified 80.6% (#1 open) |
| **Qwen3.8 Max** | Alibaba (Qwen) | 2026 | 2.4T (MoE) | 1.0M | Apache 2.0-family | GPQA-D 92.6% |
| **GLM-5 / GLM-5.3** | Zhipu AI / Z.ai | Feb 2026 / 2026 | 744B total / 40B active (GLM-5); 753B (5.3) | 200K–1.0M | MIT | SWE-bench Verified 77.8% (GLM-5) |
| **Kimi K2.5 / K2.6** | Moonshot AI | Jan 2026 | 1T total / ~32B active | 256K | Modified MIT | Agent-swarm (100 parallel sub-agents), GPQA-D 87.6–90.5% |
| **Kimi K2 (0905) / K2-Thinking** | Moonshot AI | Jul/Sep 2025 | 1T total / 32B active | 128K→256K | Modified MIT | SWE-bench Verified 65.8–69.2%; AIME 2025 100% (Thinking variant) |
| **Qwen3-Max** | Alibaba (Qwen) | Sep 2025 | >1T (MoE) | 1.0M | Proprietary-hosted / weights not fully open for -Max | SWE-bench Verified 69.6% |
| **Qwen3-235B-A22B-Instruct-2507** | Alibaba (Qwen) | Jul 2025 | 235B total / 22B active | 262K | Apache 2.0 | GPQA 77.5%, AIME25 70.3% |
| **DeepSeek-R1-0528** | DeepSeek | May 28, 2025 | 671B total / 37B active | 131K | MIT | Frontier open reasoning model of its generation |
| **DeepSeek-V3.2 / V3.2-Speciale** | DeepSeek | 2025/26 | ~685B (MoE) | 128K–164K | MIT | SWE-bench Verified ~73% |
| **Mistral Large 3** | Mistral AI | 2025 | 675B (MoE) | 256K | Open-weight (Mistral license) | First Mistral MoE since Mixtral; strong multilingual |
| **Llama 4 Maverick** | Meta | Apr 5, 2025 | 400B total / 17B active (128 experts) | 1.0M | Llama 4 Community License | GPQA-D ~69.8%; near GPT-4o/DeepSeek-V3 class |
| **Llama 4 Scout** | Meta | Apr 5, 2025 | 109B total / 17B active (16 experts) | 10M (headline) | Llama 4 Community License | GPQA-D 57.2%, MMLU-Pro 74.3% |
| **Llama 4 Behemoth** | Meta | Announced, undelivered | 2T total / 288B active (planned) | — | — | Repeatedly delayed; used only as a teacher model for Scout/Maverick |
| **GPT-OSS-120B** | OpenAI | Aug 5, 2025 | 117B total / 5.1B active | 128K | Apache 2.0 | Near o4-mini on reasoning; runs on 1×80GB GPU |
| **GPT-OSS-20B** | OpenAI | Aug 5, 2025 | 21B total / 3.6B active | 128K | Apache 2.0 | Runs in 16GB RAM; o3-mini class |
| **GLM-4.6 / GLM-4.5** | Zhipu AI / Z.ai | Sep 2025 / Jul 2025 | 355–357B total / 32B active | 128K–200K | MIT | SWE-bench-class agentic coding |
| **DeepSeek-V4-Flash (0423/Max)** | DeepSeek | 2025/26 | 284B (MoE) | 1.0M | MIT | Best-value tier: GPQA 0.87–0.88 at <$0.30/M blended |
| **Mistral Medium 3.5** | Mistral AI | 2025/26 | 128B | 256K | Open-weight | SWE-bench Verified 77.6% |
| **Qwen3-Coder-480B-A35B** | Alibaba (Qwen) | 2025 | 480B total / 35B active | 262K | Apache 2.0 | Coding/agentic specialist |
| **MiniMax M2 / M2.5 / M3** | MiniMax | 2025/26 | 10B active (M2) → 230–428B total (M2.5/M3) | ~196K–1.0M | Open-weight | SWE-bench Verified up to 80.5% (M3) |

---

## 5. Efficient / Small Models (Edge & Cost-Sensitive Tier)

| Model | Provider | Released | Parameters | Context | License | Best for |
|---|---|---|---|---|---|---|
| **Claude Haiku 4.5** | Anthropic | Oct 2025 | undisclosed | 200K | Proprietary | Fast, cheap production agents |
| **GPT-5 nano / mini** | OpenAI | 2025 | undisclosed | 400K | Proprietary | High-volume, low-cost API traffic |
| **Gemini Flash-Lite** | Google | 2025/26 | undisclosed | 1.0M | Proprietary | Cheapest frontier-adjacent multimodal |
| **Amazon Nova Micro** | Amazon | Dec 2024 | undisclosed | 128K | Proprietary | Lowest-latency text-only tasks |
| **GPT-OSS-20B** | OpenAI | Aug 2025 | 21B / 3.6B active | 128K | Apache 2.0 | Local/offline deployment, 16GB RAM |
| **Gemma 3 (1B / 4B / 12B / 27B)** | Google DeepMind | Mar 12, 2025 | 1B–27B dense | 32K (1B) / 128K (4B+) | Gemma license (open weights) | Single-GPU / on-device, 140+ languages |
| **Qwen3-30B-A3B / Qwen3-4B** | Alibaba (Qwen) | Apr 2025 | 30B total/3B active; 4B dense | up to 262K | Apache 2.0 | Outperforms 10x larger prior-gen models per-active-param |
| **Mistral Small 3.x / Ministral** | Mistral AI | 2025 | 14B–24B dense | 128K–262K | Apache 2.0 | Lightweight agentic/coding assistants |
| **Phi-4 family** | Microsoft | Dec 2024+ | 3.8B–14B dense | 16K–128K (mini variant) | MIT | Reasoning-per-parameter efficiency, edge devices |
| **Cohere Command R7B** | Cohere | Dec 2024 | 7B dense | 128K | Open weights (research) | Lightweight RAG/tool-use |
| **Llama 4 Scout** (as "small-footprint frontier") | Meta | Apr 2025 | 109B/17B active | up to 10M | Community License | Fits a single H100; huge context at low active-param cost |

---

## 6. Benchmark Reference Table

| Benchmark | What it measures | Scale | Frontier ceiling (2026) | Best open-weight | Source |
|---|---|---|---|---|---|
| **MMLU** | Broad academic/professional knowledge, 57 subjects (STEM, humanities, law, medicine, etc.) | 0–1 accuracy | ~0.925 (GPT-5) | high-90th percentile open MoE models close in | Hendrycks et al., 2020 |
| **MMLU-Pro** | Harder MMLU variant: 10 answer choices, more reasoning-dependent, less saturated | 0–1 accuracy | mid-to-high 0.8s | Llama 4 Scout ~0.743 (mid-2025 baseline) | Wang et al., 2024 |
| **GPQA / GPQA-Diamond** | 448 PhD-level "Google-proof" questions in biology, physics, chemistry; domain PhDs score ~65% | 0–1 accuracy | 0.946 (GPT-5.6 Sol / Claude Mythos Preview) | Kimi K3 0.935 | Rein et al., arXiv:2311.12022 |
| **MATH** | Competition-style math word problems, multiple difficulty tiers | 0–1 accuracy | near-saturated at frontier | — | Hendrycks et al., 2021 |
| **AIME 2024/2025** | American Invitational Mathematics Exam — olympiad-level problems | 0–1 accuracy | Kimi K2-Thinking 100% (2025) | Qwen3-Max-Thinking 100% | AIME (competition), aggregated by multiple labs |
| **HumanEval** | Python function-completion correctness, pass@1 | 0–1 pass rate | near-saturated (>0.9) at frontier | — | Chen et al. (OpenAI), 2021 |
| **LiveCodeBench** | Continuously updated, contamination-resistant coding-contest problems | 0–1 accuracy | high-0.7s to high-0.8s | GPT-OSS-120B 87.8% (self-reported) | Jain et al., 2024 |
| **SWE-Bench Verified** | 500 human-validated real-world GitHub issues; model must generate a working patch | 0–1 resolve rate | 0.950 (Claude Fable 5) | DeepSeek-V4-Pro-Max 0.806 | Jimenez et al., arXiv:2310.06770 |
| **SWE-Bench Multimodal / Pro** | SWE-Bench variants adding visual inputs (UI screenshots) or harder multi-repo tasks | 0–1 | lower than base SWE-bench across the board | — | SWE-bench team extensions |
| **MMMU / MMMU-Pro** | College-level multimodal exam questions (image + text reasoning) | 0–1 accuracy | frontier multimodal models lead | Llama 4 Maverick / Qwen-VL competitive | Yue et al., 2023 |
| **Humanity's Last Exam (HLE)** | Extremely difficult, expert-curated, closed-book multi-domain exam; designed to stay unsaturated | 0–1 accuracy | frontier models still under ~20% | GPT-OSS-120B 18.5% | Phan et al. (CAIS/Scale), 2025 |
| **BBH (BIG-Bench Hard)** | 23 challenging tasks pulled from BIG-Bench where prior models did poorly | 0–1 accuracy | near-saturated at frontier | — | Suzgun et al., 2022 |
| **HellaSwag** | Commonsense sentence-completion / plausibility judgment | 0–1 accuracy | fully saturated (>0.95) — rarely reported for 2026 models | — | Zellers et al., 2019 |
| **IFEval** | Precise instruction-following with programmatically verifiable constraints (word count, format, etc.) | 0–1 accuracy | frontier models >0.9 | — | Zhou et al. (Google), 2023 |
| **TruthfulQA** | Resistance to reproducing common human misconceptions | 0–1 accuracy | improves with RLHF/alignment tuning | — | Lin et al., 2021 |
| **ARC-AGI / ARC-AGI-2** | Novel abstract visual-pattern reasoning; explicitly designed to resist memorization | 0–1 accuracy | frontier models still well under 50% on v2 | — | Chollet (ARC Prize) |
| **FrontierMath** | Research-mathematician-level problems, tiered by difficulty; among the hardest math benchmarks | 0–1 accuracy | frontier models <40% on hardest tiers | — | Epoch AI, 2024 |
| **SciCode** | Scientific-research coding and numerical-methods problems | 0–1 accuracy | frontier models ~0.35–0.5 | — | Tian et al., 2024 |
| **Terminal-Bench** | Agentic terminal/CLI task completion (multi-step shell work) | 0–1 success | frontier agent models ~0.5–0.6 | — | Community/lab benchmark |
| **OSWorld** | Real desktop "computer-use" GUI agent tasks | 0–1 success | Claude Opus-class ~0.66 | — | Xie et al., 2024 |
| **BFCL (Berkeley Function-Calling Leaderboard)** | Accuracy and format-correctness of tool/function calls | 0–1 / % | frontier models >0.85 on simple calls | — | Berkeley Gorilla team |
| **Tau2-Bench** | Simulated multi-turn agentic tasks (e.g., customer support workflows) with tool use | 0–1 success | frontier/open leaders ~0.66–0.75 | Qwen3-Max 0.748 | Sierra/community benchmark |
| **Chatbot/LMArena (Elo)** | Blind human pairwise-preference voting, aggregated into an Elo-style rating | ~1000–2000+ Elo | Claude Sonnet 4.6 leads preference arena (2026 snapshot) | GLM-5.2 best value | LMArena / community crowdsourced |
| **AA-LCR / LVBench** | Long-context retrieval and long-video/long-document comprehension | 0–1 accuracy | best long-context models >0.8 | Llama 4 Scout / Kimi K2.5 competitive | Independent long-context benchmarks |

---

## 7. Workload → Recommended Model Matrix

| Workload category | What matters most | Strong commercial picks | Strong open-weight picks | Benchmarks to check |
|---|---|---|---|---|
| **General chat / knowledge assistant** | Broad accuracy, instruction-following, low hallucination | GPT-5.6 Sol, Claude Opus 5, Gemini 3.1 Pro | Kimi K3, Qwen3.8 Max, GLM-5.3 | MMLU-Pro, GPQA, IFEval |
| **Agentic software engineering (SWE agents)** | Multi-file patches, tool use, long-horizon planning | Claude Opus 5 / Fable 5, GPT-5.6 Terra | DeepSeek-V4-Pro-Max, GLM-5, Kimi K2.6 | SWE-bench Verified, Terminal-Bench |
| **One-shot / competitive code generation** | Single-turn correctness on fresh problems | GPT-5.6 Sol/Terra, Claude Opus 5 | GPT-OSS-120B, Qwen3-Coder-480B | LiveCodeBench, HumanEval |
| **Math & scientific reasoning** | Rigorous multi-step derivation | GPT-5.6 Sol, Claude Opus 5, Gemini 3.1 Pro | Kimi K2-Thinking, Qwen3-Max-Thinking | AIME, MATH, FrontierMath |
| **Long-document analysis / RAG over huge corpora** | Effective (not just advertised) context length | Grok-4 Fast Reasoning (~2M), Claude Opus 5 (1M), Gemini 3.1 Pro (1M) | Llama 4 Scout (10M headline), Kimi K2.5 (256K) | AA-LCR, LVBench, needle-in-haystack tests |
| **Multimodal (image/video/audio) understanding** | Native cross-modal grounding, not bolt-on vision | Gemini 3 Pro (native audio+video), GPT-5.x | Llama 4 Maverick, GLM-4.5V, Qwen-VL | MMMU / MMMU-Pro |
| **Enterprise RAG & tool/function calling** | Grounded citations, reliable structured tool calls | Cohere Command A, Claude Opus/Sonnet | Command R+, GLM-5, Qwen3-Max | BFCL, Tau2-Bench |
| **Computer-use / GUI agents** | Screen perception + action planning | Claude Opus 5 (with "zoom" tool), GPT-5.x | fewer open leaders yet; GLM-4.5V experimenting | OSWorld |
| **Cost-sensitive high-volume production** | $/task at acceptable quality | Claude Haiku 4.5, GPT-5 mini/nano, Gemini Flash-Lite | GLM-5.3-Flash, DeepSeek-V4-Flash, Qwen3-Flash-Next | Price vs. benchmark "value" leaderboards |
| **On-device / edge / offline** | Small footprint, runs without a datacenter GPU | (few commercial options are truly on-device) | Gemma 3 (1B–27B), Phi-4-mini, GPT-OSS-20B, Qwen3-4B | Latency + accuracy trade-off, not raw score |
| **Self-hosting / data sovereignty / fine-tuning** | Must ship downloadable weights with a workable license | — (by definition, not commercial-only) | Llama 4 (community license), Mistral Large 3, DeepSeek (MIT), GLM (MIT), Qwen (Apache 2.0) | Check exact license terms, not just "open" |
| **Conversational quality / creative writing** | Human-preferred tone, style, engagement | Claude Sonnet 4.6 / Opus 4.6/4.7 (arena leaders), GPT-5.5 | GLM-5.2 (best preference-per-dollar) | LMArena / preference-arena Elo |
| **Multilingual support** | Consistent quality outside English/Chinese | Gemini (140+ languages via Gemma lineage), Mistral (strong EU languages) | Mistral Large 3 (non-English/non-Chinese focus), Qwen (broad Asian-language coverage) | Multilingual MMLU (MMMLU), internal multilingual evals |

---

## 8. Current Leaderboard Snapshot

*(Composite "LLM Stats Score" — blends GPQA-Diamond, SWE-Bench Verified, coding-arena performance, and price; from llm-stats.com, Aug 29 2026. Higher = better. This composite is one lens among many — see Section 7 for task-specific picks.)*

### Overall Top 15 (all models)

| Rank | Model | Provider | Score | Context | License |
|---|---|---|---|---|---|
| 1 | GPT-5.6 Sol | OpenAI | 57.2 | 1.1M | Proprietary |
| 2 | Claude Opus 5 | Anthropic | 56.2 | 1.0M | Proprietary |
| 3 | Claude Mythos Preview | Anthropic | 55.9 | — | Unreleased (research) |
| 4 | Claude Fable 5 | Anthropic | 55.9 | 1.0M | Proprietary |
| 5 | Kimi K3 | Moonshot AI | 54.7 | 1.0M | Open Source |
| 6 | GLM-5.3 | Zhipu AI | 54.2 | 1.0M | Proprietary¹ |
| 7 | DeepSeek-V4-Pro-0813 | DeepSeek | 54.1 | 1.0M | Open Source |
| 8 | Qwen3.8 Max | Alibaba (Qwen) | 53.0 | 1.0M | Open Source |
| 9 | GPT-5.6 Terra | OpenAI | 52.6 | 1.1M | Proprietary |
| 10 | Claude Opus 4.8 | Anthropic | 51.7 | 1.0M | Proprietary |
| 11 | GLM-5.3-Flash | Zhipu AI | 51.6 | 1.0M | Open Source |
| 12 | Muse Spark 1.1 | Meta | 51.0 | 1.0M | Proprietary |
| 13 | Gemini 3.7 Flash | Google | 50.9 | 1.0M | Proprietary |
| 14 | Qwen3.8-Flash-Next | Alibaba (Qwen) | 50.5 | — | Open Source |
| 15 | Claude Sonnet 5 | Anthropic | 49.3 | 1.0M | Proprietary |

¹ Some GLM point-releases are hosted/priced as proprietary variants even though the base model line is MIT-licensed — check the specific release.

### Open-weight leaders (from the dedicated Open LLM Leaderboard)

| Category leader | Model | Score |
|---|---|---|
| Best coding-arena | DeepSeek-V4-Flash-0731 | 24.1 |
| Best GPQA-Diamond | Kimi K3 | 93.5% |
| Best AIME 2025 | Kimi K2-Thinking-0905 | 100.0% |
| Best SWE-Bench Verified | DeepSeek-V4-Pro-Max | 80.6% |
| Largest context window | Kimi K3 | 1.0M (Llama 4 Scout claims 10M headline) |
| Cheapest input (frontier-adjacent) | Nemotron 3.5 Lightning (30B-A3B) | $0.05 / M input tokens |

---

## 9. Pricing Cheat-Sheet

Approximate **blended $/M tokens** (8 input : 1 output weighting), cheapest-to-most-expensive within tier. Use this only for rough ordering — always confirm current list price before committing spend.

| Tier | Examples | Blended $/M (approx.) |
|---|---|---|
| Ultra-low-cost / edge | Amazon Nova Micro, Gemini Flash-Lite, GLM-5.3-Flash | $0.04 – $0.30 |
| Value open-weight frontier | DeepSeek-V4-Flash, Qwen3.7-Plus, GLM-5.2 | $0.30 – $1.20 |
| Mid-tier commercial | Gemini 3 Flash, GPT-5.6 Terra, Grok 4.5 | $1.00 – $3.50 |
| Frontier open-weight (large MoE) | Kimi K3, Qwen3.8 Max, DeepSeek-V4-Pro | $2.00 – $4.50 |
| Frontier commercial mid | Claude Sonnet 5, Gemini 3.1 Pro | $2.90 – $3.50 |
| Frontier commercial flagship | Claude Opus 5, GPT-5.6 Sol, GPT-5.5 | $7.00 – $8.00 |
| Premium safety-hardened / max-capability | Claude Fable 5 | ~$14.40 |

---

## 10. Caveats & Methodology Notes

- **Snapshot, not ground truth.** Model rosters, prices, and rankings on every public leaderboard (llm-stats.com, LMArena, Artificial Analysis, Vellum, etc.) change continuously — some entries here may already be superseded by the time you read this.
- **Self-reported benchmarks dominate.** At the frontier, the overwhelming majority of published scores come from the vendor's own model/system card rather than an independently re-run, audited evaluation. Cross-vendor comparisons near the top of any leaderboard should be treated as directional, not exact.
- **"Open" is not one thing.** Apache 2.0 and MIT licenses (DeepSeek, GLM, most Qwen releases, GPT-OSS) permit unrestricted commercial self-hosting. Community/research licenses (Llama 4, base Mistral Large releases) add usage conditions — always check before deploying commercially.
- **Context window ≠ effective context.** A model advertising a 1M–10M token window does not necessarily *use* that whole window well; retrieval/needle-in-haystack accuracy degrades unevenly across providers as documents get longer. Check long-context-specific benchmarks (AA-LCR, LVBench) rather than the headline number alone.
- **No universal "best" model.** Composite scores (like the "LLM Stats Score" used in Section 8) are convenient for a first cut, but real deployment decisions should weight the benchmarks and workload categories in Sections 6–7 that most resemble your actual task.
- **Parameter counts are often undisclosed for commercial models.** OpenAI, Anthropic, Google, xAI, Amazon, and Cohere do not publish exact parameter counts for their flagship proprietary models; only open-weight releases reliably disclose architecture details.
- **Benchmarks saturate.** MMLU, HellaSwag, and (increasingly) HumanEval and MATH no longer meaningfully separate frontier models — that is why the field has shifted toward harder, more contamination-resistant tests (GPQA-Diamond, Humanity's Last Exam, ARC-AGI-2, FrontierMath, LiveCodeBench, SWE-Bench Verified).

---

## 11. Sources

- **llm-stats.com** — LLM Leaderboard, Open LLM Leaderboard, per-model pages (Claude Opus 5, GPT-5.1, Gemini 3 Pro, Grok-4 Heavy, DeepSeek-R1-0528, etc.), benchmark pages (GPQA, SWE-Bench Verified, MMLU, full Benchmarks index) — primary source per user request, accessed August 29, 2026.
- Provider documentation and model/system cards: OpenAI (platform.openai.com, openai.com/index), Anthropic (anthropic.com/news, docs.anthropic.com), Google (ai.google.dev, blog.google/products/gemini, Vertex AI docs), xAI (x.ai/api), Amazon (AWS Bedrock model cards, AWS News Blog), Cohere/Oracle OCI GenAI docs.
- Model cards and community technical write-ups: Hugging Face model/blog pages (Gemma 3, Kimi K2/K2.5, Qwen3-235B, GLM-4.5/4.6/5, DeepSeek-R1), NVIDIA NIM/build catalog, Meta AI blog (Llama 4).
- Original benchmark papers: MMLU (Hendrycks et al. 2020), GPQA (Rein et al., arXiv:2311.12022), SWE-bench (Jimenez et al., arXiv:2310.06770), MMMU (Yue et al. 2023), Humanity's Last Exam (Phan et al., CAIS/Scale, 2025), BBH (Suzgun et al. 2022), HellaSwag (Zellers et al. 2019), IFEval (Zhou et al. 2023), ARC-AGI (Chollet, ARC Prize), FrontierMath (Epoch AI 2024), OSWorld (Xie et al. 2024).

---

*Compiled as a living reference — re-check llm-stats.com and provider release notes before making procurement, benchmarking, or architecture decisions based on this document.*
