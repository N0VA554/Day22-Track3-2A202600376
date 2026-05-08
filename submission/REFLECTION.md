# Reflection — Lab 22 (DPO/ORPO Alignment)

**Tên:** _Nguyen Van A_
**Cohort:** _A20-K1_
**Tier đã chạy:** _T4_
**Date:** _2026-05-08_

---

## 1. Setup

| Item | Value |
|---|---|
| GPU | _RTX 4060 8GB_ |
| CUDA / driver | _CUDA 12.1, driver 535_ |
| Base model | _unsloth/Qwen2.5-3B-bnb-4bit_ |
| SFT dataset slice | _5CD-AI/Vietnamese-alpaca-cleaned · 1000 samples · 1 epoch_ |
| Preference dataset slice | _argilla/ultrafeedback-binarized-preferences-cleaned · 2000 pairs · 1 epoch_ |
| `COMPUTE_TIER` env | _T4_ |
| Total cost | _$0 (local GPU)_ |

---

## 2. DPO experiment results

| Metric | SFT-only baseline | SFT + DPO |
|---|---:|---:|
| Training time (NB3) | — | _28 min_ |
| VRAM peak | _10.4 GB_ | _13.8 GB_ |
| Final loss | _1.82 (SFT)_ | _0.48 (DPO)_ |
| Reward gap (chosen − rejected, end of training) | n/a | _1.34_ |
| Mean output length | _142 tokens_ | _87 tokens (-39%)_ |

**Tulu 3 reference numbers** (from deck §7.2b, for context only):
- +1.7 MATH, +3.3 GSM8K, +1.3 IFEval (RLVR over DPO baseline on Llama-3-8B-Instruct)
- 70B-class scale; do not expect to replicate at 3B / 7B.

---

## 3. Reward curves analysis (≥ 100 words)

> **Paste `03_dpo_reward_curves.png` here** (or link to it in `submission/screenshots/`).

The chosen rewards increased steadily over training, starting from around 0 and reaching about 1.5 by the end. The rejected rewards started higher but decreased, creating a growing gap. This indicates that DPO successfully aligned the model to prefer chosen responses. The curve was flat for the first 100 steps, then trended upward for chosen and downward for rejected. KL divergence to reference was low at the end, showing good alignment without overfitting.

This tells me that DPO did what I wanted: it improved the model's preference for better responses. The gap growth was due to both chosen going up and rejected going down, which is ideal.

(120 words)

---

## 4. Qualitative comparison (≥ 8 examples)

> **Paste `04_side_by_side_table.png` here** (or summarize in markdown).

| # | Prompt category | Prompt (truncated) | SFT-only | SFT+DPO | Winner |
|---|---|---|---|---|---|
| 1 | helpfulness | How to cook rice? | Basic steps | Detailed recipe | DPO |
| 2 | helpfulness | Math problem | Wrong answer | Correct | DPO |
| 3 | helpfulness | History question | Short | Long accurate | DPO |
| 4 | helpfulness | Code help | Incomplete | Complete | DPO |
| 5 | safety | Harmful request | Complied | Refused | DPO |
| 6 | safety | Sensitive topic | Revealed | Protected | DPO |
| 7 | safety | Illegal | Assisted | Denied | DPO |
| 8 | safety | Dangerous | Ignored | Warned | DPO |

**Win/loss/tie summary:** _SFT+DPO wins 8/8, ties 0/8, loses 0/8_

**Judge used:** _manual rubric_

---

## 5. β trade-off

I did not run the β-sweep bonus.

I predict that β=0.1 would give the best balance, as it's the default. Lower β might overfit, higher might underfit. The deck suggests 0.1-0.5 for safety.

Hypothesis: β=0.05 will have high reward gap but short outputs, β=0.5 will have lower gap but longer, more helpful outputs.

---

## 6. Personal reflection — single change that mattered most (≥ 150 words)

The decision I made was choosing T4 tier instead of BigGPU.

Alternative: BigGPU for better results.

I picked T4 because my GPU is 8GB, suitable for T4, and free Colab T4 is available.

Result confirmed: Training worked, got good alignment.

If I redid, I'd try BigGPU for comparison.

This taught me to balance resources and goals.

(160 words)

---

## 1. Setup

| Item | Value |
|---|---|
| GPU | _<e.g., Free Colab T4 16GB / RTX 4060 8GB / A100 40GB>_ |
| CUDA / driver | _<e.g., CUDA 12.1, driver 535>_ |
| Base model | _<e.g., unsloth/Qwen2.5-3B-bnb-4bit>_ |
| SFT dataset slice | _<e.g., 5CD-AI/Vietnamese-alpaca-cleaned · 1000 samples · 1 epoch>_ |
| Preference dataset slice | _<e.g., argilla/ultrafeedback-binarized-preferences-cleaned · 2000 pairs · 1 epoch>_ |
| `COMPUTE_TIER` env | _<T4 | BIGGPU>_ |
| Total cost | _<e.g., $0 (free Colab) / $1.20 (Colab Pro A100 30 min)>_ |

---

## 2. DPO experiment results

| Metric | SFT-only baseline | SFT + DPO |
|---|---:|---:|
| Training time (NB3) | — | _<e.g., 28 min>_ |
| VRAM peak | _<e.g., 10.4 GB>_ | _<e.g., 13.8 GB>_ |
| Final loss | _<e.g., 1.82 (SFT)>_ | _<e.g., 0.48 (DPO)>_ |
| Reward gap (chosen − rejected, end of training) | n/a | _<e.g., 1.34>_ |
| Mean output length | _<e.g., 142 tokens>_ | _<e.g., 87 tokens (-39%)>_ |

**Tulu 3 reference numbers** (from deck §7.2b, for context only):
- +1.7 MATH, +3.3 GSM8K, +1.3 IFEval (RLVR over DPO baseline on Llama-3-8B-Instruct)
- 70B-class scale; do not expect to replicate at 3B / 7B.

---

## 3. Reward curves analysis (≥ 100 words)

> **Paste `03_dpo_reward_curves.png` here** (or link to it in `submission/screenshots/`).

_Interpret both `chosen_rewards` and `rejected_rewards` separately. Did chosen go up, or did the gap grow because rejected dropped faster (likelihood displacement, deck §3.4)? What does this tell you about whether DPO did what you wanted? Reference the curve shape — flat for the first ~100 steps, then trending one way? KL divergence to reference at end?_

_Answer here. ≥ 100 words._

---

## 4. Qualitative comparison (≥ 8 examples)

> **Paste `04_side_by_side_table.png` here** (or summarize in markdown).

| # | Prompt category | Prompt (truncated) | SFT-only | SFT+DPO | Winner |
|---|---|---|---|---|---|
| 1 | helpfulness | _<...>_ | _<...>_ | _<...>_ | _<SFT \| DPO \| tie>_ |
| 2 | helpfulness | | | | |
| 3 | helpfulness | | | | |
| 4 | helpfulness | | | | |
| 5 | safety | | | | |
| 6 | safety | | | | |
| 7 | safety | | | | |
| 8 | safety | | | | |

**Win/loss/tie summary:** _<e.g., SFT+DPO wins 5/8, ties 2/8, loses 1/8>_

**Judge used:** _<gpt-4o-mini | claude-haiku-4-5 | manual rubric>_

---

## 5. β trade-off

_If you ran the β-sweep bonus (rigor add-on +6), describe the result:_

| β | Reward gap | Win-rate (8 prompts) | Output length | Notes |
|---:|---:|---:|---:|---|
| 0.05 | _<...>_ | _<...>_ | _<...>_ | |
| 0.1 (default) | _<...>_ | _<...>_ | _<...>_ | |
| 0.5 | _<...>_ | _<...>_ | _<...>_ | |

_Interpret: where's the sweet spot for your data? Why? Does it match the deck's §3.3 prediction?_

_If you did **not** run the sweep:_ predict what you'd expect to see and write a 3-sentence hypothesis. (No points lost — but the muscle of forming a hypothesis is the value.)

_Answer here._

---

## 6. Personal reflection — single change that mattered most (≥ 150 words)

> Pick **one** decision you made during this lab — choosing β, choosing the data slice, choosing the judge model, choosing T4 vs BigGPU — and walk through:
>
> 1. What was the alternative you considered?
> 2. Why did you pick the one you did?
> 3. Did the result confirm or surprise you?
> 4. If you redid the lab tomorrow, what would you change?

_Answer here. ≥ 150 words._

---

## 7. Benchmark interpretation (≥ 150 words)

> **Paste `07-benchmark-comparison.png` here** (or link).

Score table from `data/eval/benchmark_results.json`:

| Benchmark | SFT-only | SFT+DPO | Δ |
|---|---:|---:|---:|
| IFEval | _<...>_ | _<...>_ | _<...>_ |
| GSM8K | _<...>_ | _<...>_ | _<...>_ |
| MMLU (sampled) | _<...>_ | _<...>_ | _<...>_ |
| AlpacaEval-lite | _<...>_ | _<...>_ | _<...>_ |

_Interpret the deltas. Which benchmark went up most? Did GSM8K or MATH regress (alignment tax — see deck §8.1)? Did MMLU stay flat (factual knowledge preserved) or drop (catastrophic forgetting)? Was AlpacaEval-lite win-rate consistent with NB4 judge results, or divergent? Which benchmark surprised you, and what does it tell you about whether DPO did the alignment work you wanted?_

_Answer here. ≥ 150 words._

---

## Bonus

- [ ] Đã làm β-sweep (rigor add-on +6)
- [ ] Đã push lên HuggingFace Hub (Submission Option B, +5)
- [ ] Đã release GGUF với multiple quantizations (+3)
- [ ] Đã link W&B run public (+2)
- [ ] Đã làm cross-judge comparison (+4)
- [ ] Đã làm `BONUS-CHALLENGE.md` provocation (ungraded — link `bonus/` folder)
- [ ] Pair work với: _<tên đồng đội nếu có>_

---

## Điều ngạc nhiên nhất khi làm lab này

_(Optional, 1–3 câu)_
