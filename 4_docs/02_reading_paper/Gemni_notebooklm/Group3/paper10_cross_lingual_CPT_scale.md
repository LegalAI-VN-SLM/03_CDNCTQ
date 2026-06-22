# Breaking Language Barriers: Cross-Lingual Continual Pre-Training at Scale

**Paper:** 3_ 10.18653_v1_2024.emnlp-main.441  
**Authors:** Wenzhen Zheng, Wenbo Pan, Xu Xu, Libo Qin, Li Yue, Ming Zhou (CAS + HIT + PKU + CSU + Langboat Inc.)  
**Venue:** EMNLP 2024  
**Source File:** `3_ 10.18653_v1_2024.emnlp-main.441 _Breaking Language Barriers Cross-Lingual Continual Pre-Training at Scale.pdf`

---

## 1. High-Level Summary (5–7 câu)

Bài báo nghiên cứu câu hỏi thiết thực: **thay vì train LLM từ đầu cho ngôn ngữ mới, có thể bắt đầu từ một LLM đã train tiếng Anh và continual pre-train sang ngôn ngữ mới không?** Với 40 model sizes (40M–5.5B) trained song song, kết quả cho thấy **Cross-Lingual CPT tiết kiệm 25%–50% training FLOPs** để đạt cùng validation loss so với training từ đầu. Bài đề xuất **extended scaling law** mở rộng Chinchilla Law với một joint data-parameter scaling term để capture transfer effect. Phát hiện quan trọng: compute-optimal allocation cho CPT **ưu tiên model size hơn dataset size** — khác với training from scratch. Về forgetting: CPT không có replay gây catastrophic forgetting (English loss từ 2.40 → 3.68), nhưng **chỉ cần 5%–30% English data replay đủ để prevent forgetting**. Linguistic similarity ảnh hưởng transfer: French (gần English) benefit nhiều hơn Russian/Chinese.

---

## 2. Section Summaries

### 2.1 Introduction
- Training LLM từ đầu cho ngôn ngữ mới = tốn compute và data lớn.
- CPT từ English checkpoint = efficient alternative.
- Study: 40 model sizes, 3 target languages (Chinese main, French/Russian secondary), analyze scaling behaviors.

### 2.2 Method: Extended Scaling Law for CPT

**Chinchilla Law (baseline):**
```
L(N, D) = E + A/N^α + B/D^β
```

**Extended CPT Scaling Law:**
```
L(N, D) = E + A/N^α + B'/(D^β' · N^γ)
```
- Joint scaling term `B'/(D^β' · N^γ)` captures interaction giữa data và model size.
- Với CPT: β=0.20, γ=0.08 (positive) → transfer effect tăng theo model size.
- "Effectively transferred data" từ Hernandez et al. 2021 được incorporated.

**Compute-optimal allocation cho CPT:**
- CPT efficient frontier: **ưu tiên N (model size) hơn D (dataset size)** vs. training from scratch.
- Reasoning: Model "pre-matured" từ source language knowledge → cần ít data hơn để adapt.

### 2.3 Key Results

**Compute savings (Figure 1, Table 2):**
| Model | CPT vs. Scratch | FLOP savings |
|---|---|---|
| 5.5B | Same loss at 70B tokens vs 110B tokens | **~36% savings** |
| All sizes | Throughout training | **25%–50% savings** |

**Transfer effect by model size:**
- Larger models benefit MORE từ CPT (γ > 0 in scaling law).
- Transfer effect mạnh nhất trong early stages; robust across training duration.

**Linguistic similarity (Figure 3, downstream tasks):**
| Language | Similarity to English | CPT benefit |
|---|---|---|
| French | High (shared vocab + grammar) | **Most** |
| Russian | Medium | Moderate |
| Chinese | Low | Least |

**Catastrophic forgetting (1.4B model):**
- English validation loss: 2.40 → 3.68 (WITHOUT replay) — severe forgetting.
- WITH 10%-30% English replay: English loss stays below 2.40 → **forgetting prevented**.
- Replay không ảnh hưởng Chinese performance: models reach same val loss regardless of replay ratio (1%–80%) at fixed compute.

**Replay key finding (Figure 4):**
- Target language loss: same across replay ratios 1%–80% at fixed compute → replay is "free" cho target language performance.
- Source language: 1%–5% replay insufficient cho long training; 10%–30% robust.
- ~30% English data = optimal balance.

### 2.4 Discussion / Conclusions
- CPT từ English là efficient pathway cho new language LLMs.
- Extended scaling law captures CPT dynamics tốt hơn vanilla Chinchilla.
- Practical recommendation: Use large model size for CPT; replay 10%–30% source data.

---

## 3. Methodology Deep-Dive

### Framework
**Parallel pre-training study**: 40 sizes (40M–5.5B) × 2 strategies (CPT vs. Scratch) × 3 languages (Chinese main, French/Russian secondary) = massive experimental scale.

### Core Ideas
1. **CPT = Transfer Learning at scale:** English checkpoint already contains linguistic knowledge (grammar, semantics, world knowledge) transferable to new languages.
2. **Joint scaling term:** D^β' · N^γ captures synergy: larger models transfer MORE efficiently, requiring proportionally LESS data.
3. **Replay as free lunch for source language:** Replay 10%–30% doesn't hurt target language learning but prevents English forgetting.

### Data / Setup / Metrics
- **Source:** English pre-trained checkpoints.
- **Target:** Chinese (main), French, Russian (secondary, 1.4B only).
- **Scale:** 40 sizes, 40M to 5.5B parameters.
- **Replay ratios:** 1%, 5%, 10%, 20%, 30%, 50%, 80%.
- **Metrics:** Validation loss (cross-entropy); downstream benchmarks (multilingual MMLU-style).

### 3 Key Assumptions
1. **Assumption 1: English knowledge is universally transferable** — CPT works well for French (high similarity) nhưng benefit giảm cho Chinese/Russian. Tiếng Việt (medium similarity, SVO structure) có thể có intermediate transfer.
2. **Assumption 2: 30% replay optimal** — Kết luận "~30% optimal" dựa trên 1.4B model. Không biết optimal cho model sizes khác, đặc biệt với large models.
3. **Assumption 3: Dataset quality không khác nhau** — Chinese data và English data được treat như equal quality. Trong practice, Vietnamese legal data có thể khác về quality và domain.

---

## 4. Brutally Honest Review

### Summary
Bài rất relevant với VN-Legal-AI scenario — đây chính là problem của chúng ta: adapt English-based LLM sang tiếng Việt (cross-lingual CPT). Kết quả về compute savings và replay rules là actionable. Tuy nhiên, study chủ yếu là Chinese, không hoàn toàn representative cho Vietnamese.

### Strengths
- ✅ **Directly relevant:** Cross-lingual CPT English → Vietnamese là use case của VN-Legal-AI.
- ✅ **Scale:** 40 model sizes → robust scaling law; không phải single-point estimation.
- ✅ **25%–50% compute savings:** Rất significant cho practical budget.
- ✅ **5%–30% replay rule:** Actionable và đơn giản.
- ✅ **Downstream task validation:** Không chỉ perplexity mà còn benchmark accuracy.

### Weaknesses
- ❌ **Chinese ≠ Vietnamese:** Chinese là lingua franca study nhưng Chinese characters rất khác Vietnamese (Latin script với tones). Transfer dynamics có thể khác.
- ❌ **Chỉ test 1.4B cho replay:** Replay experiments chỉ ở 1.4B; không biết optimal ratio cho 7B hay 13B models.
- ❌ **Không test domain-specific CPT:** Study dùng general Chinese/French/Russian text, không test domain-specific (như legal) scenarios.
- ❌ **Tokenizer không được nghiên cứu:** Dùng cùng tokenizer? Hay train tokenizer mới? Tokenizer mismatch là critical issue cho Vietnamese.
- ❌ **"Langboat Inc." có thể bias:** Commercial stakeholder trong research → possible publication bias toward positive results.

### Questions
1. Với Vietnamese (Latin script, tonal, SVO, moderate distance từ English), compute savings sẽ là bao nhiêu?
2. Nên dùng tokenizer của base English model hay train Vietnamese tokenizer mới trước CPT?
3. Với domain-specific CPT (English legal → Vietnamese legal), cần bao nhiêu replay English general + English legal?

### Recommendation
**Accept (EMNLP 2024 — deserved).** Bài solid về scale và contribution. Limitations về Vietnamese-specific insights là chấp nhận được — cần follow-up study.

---

## 5. Data & Metrics View Designer

### Tasks / Datasets / Baselines / Metrics
| | Details |
|---|---|
| **Source language** | English (base model) |
| **Target languages** | Chinese (main), French, Russian |
| **Model scales** | 40 sizes: 40M to 5.5B parameters |
| **Replay ratios** | 1%, 5%, 10%, 20%, 30%, 50%, 80% |
| **Baselines** | Training from scratch (same size) |
| **Metrics** | Validation perplexity; downstream multilingual benchmarks |

### 3 Chart Proposals
1. **CPT vs. Scratch Loss Curves (Figure 1 replica):** Log-log plot: X = training FLOPs, Y = validation loss. Multiple lines = different model sizes. Two sets: CPT (lower, solid) and Scratch (higher, dashed). Dashed efficiency frontiers connecting optimal points. Annotate "25-50% savings" bracket.
2. **Linguistic Similarity vs. Transfer Benefit:** Bar chart: X = languages (French, Russian, Chinese). Y = % FLOP savings vs. scratch at fixed loss target. Shows French >> Russian >> Chinese.
3. **Replay Ratio Impact (Figure 4, 6):** Dual Y-axis chart. X = replay ratio (1%–80%). Y_left = English validation loss (decreasing as replay %). Y_right = Chinese validation loss (flat = no harm). Show 10%–30% "sweet spot".

### Table Structure — Key Numbers
| Metric | CPT | Scratch | Savings |
|---|---|---|---|
| FLOPs to reach same loss (5.5B) | 70B tokens | 110B tokens | **~36%** |
| General training range | — | — | **25–50%** |
| English loss w/o replay (1.4B) | 3.68 | 2.40 (start) | **+53% increase** |
| English loss w/ 30% replay | ~2.38 | 2.40 (start) | **Preserved** |

### Raw Data Extraction
- **CPT scaling law parameters (vs. Scratch):** CPT β=0.20, γ=0.08 (positive → model size helps more in CPT).
- **Replay optimal:** ~30% source language; 10% effective minimum. 1%-5% insufficient for long training.
- **Linguistic similarity:** French benefit > Russian benefit > Chinese benefit.
- **Downstream:** CPT outperforms scratch across all 3 languages on multilingual benchmarks.

---

## 6. Key Takeaways & Relevance to VN-Legal-AI / CDNCTQ

| Takeaway | Relevance |
|---|---|
| **CPT từ English LLM → Vietnamese tiết kiệm 25-50% compute** | Không cần train LLM tiếng Việt từ đầu → CPT từ Llama3/Qwen2.5 (English-strong) |
| **5%–30% English replay để prevent forgetting** | Mix 20-30% English general data trong Vietnamese CPT pipeline |
| **Linguistic similarity ảnh hưởng benefit** | Vietnamese (Latin script, moderate distance) sẽ benefit nhiều hơn Chinese nhưng ít hơn French từ English CPT |
| **Ưu tiên model size hơn dataset size (CPT)** | Với cùng compute budget: dùng 13B model + ít data > 7B model + nhiều data hơn |
| **Tokenizer là missing piece** | Cần resolve tokenizer issue (extend Vietnamese tokens?) trước CPT; bài này không address |
| **Áp dụng direct:** English legal LLM → Vietnamese legal LLM | CPT từ English legal-trained checkpoint (ví dụ LexGPT) sang Vietnamese legal có thể tiết kiệm nhiều compute |
