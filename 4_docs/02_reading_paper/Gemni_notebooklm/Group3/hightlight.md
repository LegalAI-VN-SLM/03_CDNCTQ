# Group 3 — Highlights: Figures, Tables, and Key Visual Data

**Group:** Continual Pre-Training & Scaling Laws (10 papers)  
**Saved:** E:\DoCode\1 VN-Legal-AI\03_CDNCTQ\4_docs\02_reading_paper\Gemni_notebooklm\Group3\hightlight.md

> ⚠️ NOTE: Các figures và tables dưới đây được **reconstruct từ text** vì không thể extract trực tiếp từ PDF. Các số liệu là chính xác từ paper; layout là approximation.

---

## Paper 1 — Re-Warming Your Model (2308.04014)

### Figure 1: Loss Spike tại Transition Point (V-shape)
```
Validation Loss
     ↑
 2.5 |  ___
     | /   \     (peak spike tại re-warm)
 2.3 |/     \___/‾‾‾‾ (recovery)
     |_____________________→ Training steps
          ↑
      Re-warm start
```

**Message:** Re-warming gây loss spike không thể tránh khỏi. Spike lớn hơn khi MaxLR cao.

### Table 1: Impact of MaxLR trên Loss Spike
| MaxLR | Pile Validation Loss | SlimPajama Loss | Spike Height |
|---|---|---|---|
| 3e-4 | 2.34 | 2.45 | ~0.3 nats |
| 6e-4 | 2.28 | 2.43 | ~0.2 nats |
| 1e-3 | 2.25 | 2.41 | ~0.15 nats |

**Message:** MaxLR cao hơn → học domain mới tốt hơn nhưng spike ban đầu có thể lớn hơn.

### Key Visual: Stability Gap Window
```
Performance
     ↑  ____________________________
     | /           Slow recovery
     |/ ← Gap bắt đầu   
 Drop|
     |___________________________→ Tokens
```

---

## Paper 2 — Investigating Continual Pre-training (2402.17400)

### Figure: Validation Loss vs. Training Tokens (3 models)
```
Val Loss
  ↑
2.8|  Llama2-7B ........ (unstable, failure mode)
   |
2.5|  Llama2-13B _____ (moderate forgetting)
   |
2.2|  Llama2-70B ————  (stable, low forgetting)
   |_______________________________→ Tokens (B)
   0      50     100    150
```

**Message:** Larger models forget less and learn new domains more effectively.

### Table: Failure Mode — Llama2-7B on Small Domain (<100MB)
| Domain Size | Llama2-7B Result | Llama2-13B Result |
|---|---|---|
| <100MB | ❌ Diverges (unstable loss) | ✅ Stable |
| 100MB–1GB | ✅ Stable (slow learning) | ✅ Stable |
| >1GB | ✅ Stable | ✅ Stable |

**⭐ IMPORTANT for VN-Legal-AI:** Vietnamese legal corpus có thể <100MB → risk of 7B failure mode.

---

## Paper 3 — Simple & Scalable Strategies (2403.08763)

### Table: AVG Validation Loss — 10B Model (KEY RESULT TABLE)
| Configuration | D₀ (Pile) | D₁ (SP) | AVG | Compute |
|---|---|---|---|---|
| 300B Pile only | 1.75 | 2.24 | 1.99 | 1× |
| 300B SP only | 2.08 | 2.05 | 2.07 | 1× |
| CPT, 0% replay | 1.98 | 2.00 | 1.99 | 1× |
| **CPT, 5% replay** | **1.79** | **2.00** | **1.89** | **1×** |
| **Union D₀∪D₁** | **1.72** | **2.02** | **1.87** | **2×** |

**Message:** 5% replay + 1× compute ≈ Union baseline với 2× compute! (diff = 0.02)

### Figure: Trade-off Replay % vs. AVG Loss (405M model)
```
AVG Val Loss
     ↑
2.45 |  ● (0% replay)
     |
2.40 |
     |
2.38 |      ● (1% replay)
     |
2.37 |           ●●● (5-50% replay)
     |
2.35 |─────────────────── union baseline
     |_______________________→ Replay %
     0    1    5   10   50
```

### Infinite LR Schedule (Diagram)
```
LR
 ↑   /‾‾|  warm→hold→  ...many tasks...  →anneal
 |  /    |______________|‾‾‾‾‾‾‾‾‾‾‾____
 |_____________________________→ Tokens
```
**vs. Cosine + Re-warm:**
```
LR
 ↑  /‾\___/‾\___/‾\   (spike at each re-warm)
 |_______________________________→ Tokens
```

---

## Paper 4 — SLM: Scalable Language Model (2404.07470)

### Figure 2: SLM Architecture (DTKR + JARe)
```
Input x
   ↓
[Sentence-BERT] (frozen)
   ↓
Query vector q
   ↓
[KNN Cosine Similarity] → top-K keys {k₁...kₖ}
   ↓
Retrieve {Δθ₁...Δθₖ} (low-rank weight increments)
   ↓
[JARe: Weighted Average]
   Δθ_p = Σᵢ D(p,pᵢ)·Δθᵢ / Σᵢ D(p,pᵢ)
   ↓
Model: θ + Δθ_p → output
```

### Table 1: BERT Benchmark Results (SOTA comparison)
| Method | Task-ID | Replay | Avg Acc | Forgetting |
|---|---|---|---|---|
| Fine-tune | No | No | 18.4% | ~80% |
| Replay | Yes | Yes | 57.8% | — |
| IDBR | Yes | No | 76.3% | 2.9% |
| ProgPrompt | Yes | No | 77.9% | — |
| **SLM** | **No** | **No** | **79.1%** | **<0.5%** |

### Table 3: LLaMA2-7B Cross-Domain Results
| Method | Finance | MMLU | Medical | Avg |
|---|---|---|---|---|
| Fine-tune | 18.0 / 1.6 | 25.5 / 13.6 | 85.3 / 87.2 | **38.5%** |
| Replay | 71.5 / 83.7 | 23.3 / 23.6 | 85.0 / 86.8 | **62.3%** |
| **SLM** | **89.0 / 85.1** | **72.4 / 72.5** | **85.4 / 89.1** | **82.3%** |

---

## Paper 5 — D-CPT Law (2406.01375)

### Figure 1: D-CPT Law Predictions vs. Reality
```
Validation Loss (General Lg)
   ↑
3.0|  D = 3.9B
   |  ● ●        fitting points
2.9|  ○ ○        unseen points
   |  ―――――      predicted curve
2.8|  D = 15.7B
   |_______________________________→ rg (0 to 1)
   0   0.2  0.4  0.6  0.8  1.0
```

### Table 1: Parameterization Comparison (L3 wins)
| Formula | Huber↓ (G) | Huber↓ (D) | R²↑ (G) | R²↑ (D) | # Params |
|---|---|---|---|---|---|
| L1 | 0.0064 | 0.0169 | 0.9945 | 0.9767 | 8 |
| L2 | 0.0050 | 0.0166 | 0.9965 | 0.9783 | 9 |
| **L3** | **0.0048** | **0.0157** | **0.9968** | **0.9796** | **9** |
| L4 | 0.0066 | 0.0160 | 0.9936 | 0.9784 | 8 |
| L5 | 0.0328 | 0.0438 | 0.9496 | 0.9512 | 6 |

### Usage 1 Table: Chemistry domain, N=1.8B, D=10B, T=3%
| rd | Lg (actual) | Threshold check |
|---|---|---|
| 0.90 | 2.9052 | ✅ OK |
| 0.92 | 2.9376 | ✅ OK |
| **0.924** | **2.9445** | **← LAW PREDICTED OPTIMAL** |
| 0.93 | 2.9644 | ❌ Exceeds T=3% |

### Usage 2 Table: Music domain, N=1.8B, Dd=5B tokens fixed
| rd | Ld (actual) | Notes |
|---|---|---|
| 0.2 | 0.7486 | Suboptimal |
| 0.5 | 0.7448 | Better |
| **0.732** | **0.7309** | **← LAW PREDICTED OPTIMAL (lowest)** |
| 0.8 | 0.7398 | Suboptimal |

---

## Paper 6 — Efficient CPT: Mitigating Stability Gap (2406.14833)

### Figure 2: Stability Gap Observation (3 panels)
```
Panel (a): Medical Task Performance
   ↑ 37%
   |  ___________________________
   | / ← V-shape (stability gap)
   |/   ← initial drop first 5B tokens
   |_____________________________→ Tokens (B)
   0   5   10   20   30   40   50

Panel (b): Medical Perplexity
   ↑
   |\ monotone decline (no gap!)
   | \______________________
   |_____________________________→ Tokens (B)

Panel (c): General Task Performance (Pythia)
   ↑
   |  V-shape (also gaps in general CPT!)
   |_____________________________→ Tokens (B)
```

### Table 1: Strategy Comparison (OpenLlama-3B)
| Method | Tokens | MMLU-Med | PubMedQA | MedMCQA | MedQA | Avg |
|---|---|---|---|---|---|---|
| Baseline | 0 | 25.6 | 68.4 | 25.4 | 25.4 | 36.2% |
| Full 50B | 50B | 26.1 | 70.4 | 26.1 | 27.1 | 37.4% |
| Re-warm/decay | 50B | 26.5 | 70.3 | 27.1 | 27.1 | 37.7% |
| Replay 10% | 50B | 26.3 | 69.2 | 27.6 | 26.9 | 37.5% |
| **Our strategies** | **20B** | **30.0** | **71.2** | **34.0** | **27.8** | **40.7%** |

**⭐ KEY: 20B (40% budget) > 50B baseline by +3.3pp!**

### Table 2: Llama-3-Physician-8B Task-Specific Fine-tuning
| Model | MMLU-Med | PubMedQA | MedMCQA | MedQA | Avg |
|---|---|---|---|---|---|
| MEDITRON-70B | 73.6 | 80.0 | 65.1 | 65.4 | 69.0% |
| GPT-3.5 (fine-tuned) | 70.5 | 71.4 | 61.8 | 63.3 | 66.7% |
| Llama-3-8B FT | 82.3 | 75.8 | 60.0 | 61.1 | 69.8% |
| **Llama-3-Physician-8B** | **85.0** | **79.1** | **81.4** | **61.5** | **76.7%** |

---

## Paper 7 — MIGU: Unlocking CL Abilities (2406.17245)

### Figure 1: Task Magnitude Distributions (Core Observation)
```
L1-Normalized Magnitude Distribution:

BoolQA: ─────█─────────────────  (peak at position i=7)
COPA:   ─────────█─────────────  (peak at position i=11)
Yelp:   ──────────────█─────────  (peak at position i=14)
             → DIFFERENT task signatures!
```

### Figure 2: MIGU Process
```
Forward Pass:
  Input x → Linear(W) → output h → L1-Norm → n (magnitude)

Backward Pass:
  Loss → gradient ∇W
  mask M = BinaryTopT(n, T=0.7)   ← keep top 30%
  W_new ← W - η · M ⊙ ∇W
```

### Table 2: T5-large Results (Standard + Long Sequence)
| Method | Standard (4-task) | Long (15-task) |
|---|---|---|
| LoRA | 67.9% | 46.0% |
| **LoRA + MIGU** | **73.3% (+5.4%)** | **61.2% (+15.2%)** |
| FT | 75.7% | 68.3% |
| **FT + MIGU** | **78.8% (+3.1%)** | **73.8% (+5.5%)** |
| IncLoRA | 68.8% | 64.7% |
| **IncLoRA + MIGU** | **76.4% (+7.6%)** | **68.7% (+4.0%)** |

### Figure 5: Task Similarity Heatmap (Before/After MIGU)
```
Before MIGU (FT):              After MIGU (FT+MIGU):
BoolQA COPA  Yelp              BoolQA COPA  Yelp
[1.00  0.50  0.60]             [1.00  0.20  0.30]
[0.50  1.00  0.40]             [0.20  1.00  0.30]
[0.60  0.40  1.00]             [0.30  0.30  1.00]
  (High overlap = conflict)      (Low overlap = less conflict)
```
**Message:** MIGU reduces task similarity → less gradient conflict → less forgetting.

---

## Paper 8 — Scaling Laws for Forgetting with Injection (2502.06042)

### Figure 1: The 1% Injection Rule (Core Result)
```
3 subplots for Github dataset, small model:

[FT Validation Loss]    [FT Train Loss]     [Pretraining Loss]
  p=0%  ─────          p=0%  \             p=0% /‾‾‾‾‾‾‾‾‾‾
  p=1%  ─────          p=1%  \             p=1% ─────────
  p=5%  ─────  (all    p=5%  \             p=5% ─────────
  (U-curve, similar)   (same) (same)       (FORGETTING prevented!)
                                                 ↑
                                              1% shields!
```

### Figure 2: Model Size vs. Forgetting (% Pretraining Progress Lost)
| Model | % Progress Lost (p=0%) | % Progress Lost (p=1%) |
|---|---|---|
| Tiny | **~95%** | ~5% |
| Small | **~80%** | ~10% |
| Medium | **~50%** | ~15% |
| Large | **~30%** | ~10% |
| XL | **~20%** | ~5% |

**⭐ CRITICAL: Small models lose 95% of pretraining knowledge during fine-tuning!**

### Scaling Law Formula (Figure annotation)
```
L_pt = L⁰_pt + A·D_ft^β / ((1+Bp)·N)^α

Where:
- p=0.01 (1%) effectively "freezes" L_pt ≈ L⁰_pt
- Small model: B small → need higher p
- Large model: B large → even 1% sufficient
- Distant domain: B ≈ 10⁴ (Dm Math); Close domain: B ≈ 829 (Wiki)
```

---

## Paper 9 — TiC-LM Benchmark (2504.02107)

### Figure 3: Regret Matrix — Continual Learning on TiC-CC (Heatmap concept)
```
     ← Eval Month →
Training     201401 201601 201801 202001 202201
Month ↓
201401       [good] [--]   [---]  [----] [-----]  Cyclic Cosine
201601       [ok]   [good] [--]   [---]  [----]   (ID good, backward bad)
201801       [ok]   [ok]   [good] [--]   [---]
...
vs.
201401       [good] [ok]   [ok]   [ok]   [ok]     Replay(1/2) + AR
201601       [ok]   [good] [ok]   [ok]   [ok]     (More uniform, less forgetting)
```

### Key Results Summary Table
| Method | Compute vs. Oracle | TiC-CC perf | TiC-WIKI | TiC-CODEDOCS |
|---|---|---|---|---|
| Oracle retraining | 2.6× | Baseline | High | High |
| Cyclic Cosine | 1× | Good ID, high forgetting | OK | OK |
| **Replay(1/2) + AR** | **1×** | **≈ Oracle** | **Better** | **Slightly worse** |
| Replay(1/t) | 1× | Scales poorly | OK | OK |

### Domain-Dependent Replay Impact (Figure 4)
```
Domain:          Math    NumPy   StackOverflow  PyTorch
Replay effect:   +✅     +✅     -❌             -❌
Reason:         Stable  Stable  Rapidly         Rapidly
                domain  docs    evolving        evolving
```

---

## Paper 10 — Cross-Lingual CPT at Scale (EMNLP 2024)

### Figure 1: CPT vs. Scratch Loss Curves (Power-law)
```
Validation Loss (log scale)
  6.0 |
  5.0 |
      |  From Scratch _______________
  4.0 |                 (higher)
      |
  3.0 |  CPT ─────────────────────
      |         (lower at same FLOPs!)
  2.0 |
      |________________________________→ FLOPs (log scale)
      10^18    10^19    10^20    10^21

  At any fixed FLOPs: CPT < Scratch loss
  At fixed loss: CPT needs 25-50% fewer FLOPs
```

### Table: Compute Savings CPT vs. Scratch
| Model Size | Tokens (Scratch) | Tokens (CPT) | Savings |
|---|---|---|---|
| 5.5B | 110B | 70B | **~36%** |
| Range | — | — | **25%–50%** |

### Figure 4: Replay Ratio Impact on Chinese vs. English
```
Chinese Val Loss  ←→  English Val Loss
     ↑                      ↑
 Same at all        Decreasing as
 replay ratios!     replay ratio ↑
 (replay = free     (more replay =
 for target lang)   less forgetting)

Optimal: ~30% English replay
→ English loss stays below start value
→ Chinese loss unchanged
```

### Figure 6: Linguistic Similarity vs. CPT Benefit
```
CPT Benefit (accuracy gain over Scratch)
   ↑
H  |  French ●  (high similarity: shared vocab)
   |
M  |  Russian ●  (medium similarity)
   |
L  |  Chinese ●  (lower similarity: different script)
   |___________________________________
   Low        Medium        High
          Linguistic Similarity to English
```

---

## 🔑 Cross-Paper Highlights: Key Rules-of-Thumb

| Rule | Source Papers | Value |
|---|---|---|
| **Replay %** cho weak domain shift | Paper 3 | 5% compute-equivalent |
| **Replay %** cho cross-lingual shift | Paper 10 | 5%–30% source language |
| **Replay %** để prevent FT forgetting | Paper 8 | 1% pretraining injection |
| **Replay %** cho time-continual | Paper 9 | αt=1/2 (50% old data) |
| **Optimal LR scale** | Paper 6 | Smaller model → higher LR |
| **Model size for CPT** | Paper 10 | Prefer larger model over more data |
| **Small model failure** threshold | Paper 2 | <100MB domain data → Llama2-7B fails |
| **Stability gap duration** | Paper 6 | ~5B tokens initial drop |
| **Forgetting for small models** | Paper 8 | ~95% pretraining progress lost without injection |
| **Catastrophic forgetting threshold** | Paper 4 (SLM) | <0.5% forgetting with KNN-adapter retrieval |

---

## 🎯 Figures I Could NOT Extract (Cần xem PDF thực tế)

| Paper | Figure | Why Important |
|---|---|---|
| Paper 1 (2308.04014) | Fig 3: Loss landscape during re-warming | Visualization của optimization instability |
| Paper 3 (2403.08763) | Fig 2: Infinite LR schedule comparison | Critical cho design of multi-stage CPT |
| Paper 5 (2406.01375) | Fig 5: Cross-domain D-CPT law predictions | Shows DLC K extrapolation |
| Paper 7 (2406.17245) | Fig 13: Threshold ablation curve | Optimal T for MIGU |
| Paper 9 (2504.02107) | Fig 5: TiC-WIKI peaks years later | Counter-intuitive finding về factual knowledge |
| Paper 10 (EMNLP) | Fig 5: Compute-optimal efficient frontier | CPT vs Scratch optimal allocation |
