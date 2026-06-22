# Scaling Laws for Forgetting during Finetuning with Pretraining Data Injection

**ArXiv:** 2502.06042  
**Authors:** Louis Bethune, David Grangier, Dan Busbridge, Eleonora Gualdoni, Marco Cuturi, Pierre Ablin (Apple)  
**Venue:** ICML 2025  
**Source File:** `3_ 2502.06042 _Scaling Laws for Forgetting during Finetuning with Pretraining Data Injection.pdf`

---

## 1. High-Level Summary (5–7 câu)

Bài báo (từ Apple ICML 2025) trả lời câu hỏi quantitative: **khi fine-tune LLM trên domain-specific data nhỏ, bao nhiêu pretraining data injection là đủ để ngăn catastrophic forgetting?** Kết quả chính: **chỉ cần inject 1% pretraining data vào fine-tuning mixture là đủ để shield model khỏi forgetting**, trong khi hầu như không ảnh hưởng đến fine-tuning performance. Bài đề xuất hai scaling laws: (1) **Scaling Law cho forgetting**: `L_pt = L⁰_pt + A·D_ft^β / ((1+Bp)·N)^α`, dự đoán pretraining loss sau fine-tuning theo N, D_ft, p; (2) **Scaling Law cho fine-tuning loss** (multiplicative, p-independent). Phát hiện quan trọng: **small models forget catastrophically** (up to 95% pretraining progress lost), trong khi larger models chỉ mất ~20%. Domain distance là yếu tố quan trọng: domains xa pretraining distribution forgetting nhiều hơn và benefit nhiều nhất từ injection.

---

## 2. Section Summaries

### 2.1 Introduction
- Fine-tuning LLM trên small domain data: 2 thách thức — overfitting và catastrophic forgetting.
- Pretraining data injection là giải pháp phổ biến nhưng chưa ai quantify chính xác cần bao nhiêu.
- Bài này: derive scaling laws để predict forgetting và fine-tuning performance.

### 2.2 Method: Two Scaling Laws

**Scaling Law for Forgetting (Eq. 7):**
```
L_pt = L⁰_pt + A·D_ft^β / ((1+Bp)·N)^α
```
- L_pt = pretraining loss sau fine-tuning (proxy cho forgetting)
- L⁰_pt = pretraining loss trước fine-tuning
- N = model size (parameters)
- D_ft = số fine-tuning tokens
- p = fraction of pretraining data injected (0 to 1)
- A, B, α, β = domain-dependent constants

**Scaling Law for Fine-tuning (Eq. 6):**
```
L_ft = A/(N^α · D_ft^β) + E
```
- p (injection fraction) hầu như không ảnh hưởng L_ft → injection không hại fine-tuning performance.

### 2.3 Key Findings

**1% injection rule (Figure 1, Observation):**
- p=0%: pretraining loss tăng liên tục (forgetting)
- p=1%: pretraining loss ổn định (no forgetting)
- p=5%: tương tự 1% nhưng mất nhiều fine-tuning tokens hơn
- **Fine-tuning validation loss: KHÔNG bị ảnh hưởng bởi p** → free lunch!

**Model size vs. forgetting (Figure 2, 14):**
| Model size | % pretraining progress lost |
|---|---|
| Tiny | ~95% |
| Small | ~80% |
| Medium | ~50% |
| Large | ~30% |
| **XL** | **~20%** |

*Small models catastrophically forget; large models resilient.*

**Domain distance (Figure 4):**
- Dm Mathematics (far from pretraining): B ≈ 10⁴ → highly benefits from injection
- Wikipedia (close to pretraining): B ≈ 829 → modest benefit

**Extrapolation accuracy (Table 4):**
- Fit với Medium (334M) + 3M tokens → predict XL (1.3B) + 30M tokens
- Error: <2.01% fine-tuning, <0.83% pretraining → highly accurate.

### 2.4 Discussion / Conclusions
- 1% injection rule is robust across domains trong The Pile.
- Pretraining injection = dual role: anti-forgetting regularizer + overfitting reducer.
- Cost of forgetting cao hơn cho large models (vì large models đắt hơn để pre-train).
- Limitation: chỉ test unsupervised fine-tuning (next-token prediction), không test SFT hay RLHF.

---

## 3. Methodology Deep-Dive

### Framework
**Empirical scaling law fitting** trên nhiều model sizes (Tiny đến XL) và fine-tuning token counts (300K đến 30M), across domains của The Pile.

### Core Ideas
1. **1% injection = "anchor" cho pretraining features:** Injection nhỏ đủ để model "nhớ" pretraining distribution, ngăn parameter drift.
2. **Forgetting chủ yếu do capacity constraint:** Small models phải "allocate" parameter space cho fine-tuning → push out pretraining knowledge.
3. **Multiplicative law captures interaction:** D_ft và N interact multiplicatively → law phức tạp hơn additive form của Chinchilla.

### Data / Setup / Metrics
- **Pre-training data:** The Pile (diverse domains).
- **Fine-tuning domains:** Multiple The Pile domains (Arxiv, Github, DM Mathematics, Wikipedia, etc.)
- **Models:** Tiny, Small, Medium (334M), Large, XL (1.3B).
- **Injections:** p ∈ {0%, 1%, 5%}.
- **Metrics:** Pretraining loss (forgetting proxy), Fine-tuning validation loss (performance proxy), Bootstrapped MRE (fitting quality).

### 3 Key Assumptions
1. **Assumption 1: Pretraining loss = good forgetting proxy** — Dùng pretraining loss tăng lên như proxy cho forgetting. Nhưng pretraining loss cao không nhất thiết nghĩa là performance trên downstream tasks giảm.
2. **Assumption 2: Power-law relationship holds across domains** — Fitting accuracy tốt (MRE <2%) nhưng chỉ test trên The Pile domains (English text). Chưa verify cho tiếng Việt hay specialized technical domains.
3. **Assumption 3: Injection không có structure** — Bài inject pretraining data randomly (streaming). Có thể curated replay (importance-weighted) sẽ hiệu quả hơn ở p<1%.

---

## 4. Brutally Honest Review

### Summary
Bài sạch sẽ, well-executed, kết quả đơn giản nhưng powerful: 1% injection rule. Phù hợp tốt với D-CPT Law bài Group 3 #5 (mixture ratio) nhưng tiếp cận từ góc độ khác (fine-tuning thay vì pre-training).

### Strengths
- ✅ **1% rule là extremely actionable:** Practitioners có thể dùng ngay mà không cần full grid search.
- ✅ **ICML 2025 (Apple research):** Chất lượng cao, peer-reviewed.
- ✅ **Dual role của injection:** Vừa anti-forgetting, vừa anti-overfitting → thuyết phục.
- ✅ **Extrapolation cost saving:** Fit với 334M/3M tokens → predict 1.3B/30M tokens với <1% error → tiết kiệm compute.
- ✅ **Rõ ràng và concise:** Paper well-written, kết quả presented rõ.

### Weaknesses
- ❌ **Chỉ test unsupervised fine-tuning (next-token prediction):** Không test SFT với instruction following. 1% rule có thể không hold cho SFT trên small datasets.
- ❌ **The Pile domains chỉ = English:** Không test cross-lingual scenarios (ví dụ English pre-train → Vietnamese fine-tune).
- ❌ **Không test với LoRA / PEFT:** Chỉ test full fine-tuning. LoRA với 1% injection có thể có dynamics khác.
- ❌ **Domain-specific B constants không generalizable:** Để dùng scaling law, cần fit B cho từng domain mới → vẫn cần small experiments.

### Questions
1. Với SFT trên Vietnamese legal data (small dataset ~10K examples), injection bao nhiêu là cần?
2. LoRA fine-tuning có còn cần 1% injection để prevent forgetting không? Hay LoRA đã bảo vệ đủ?
3. B constant cho Vietnamese legal domain sẽ lớn hay nhỏ so với Dm Mathematics?

### Recommendation
**Strong Accept (ICML). Bài excellent về clarity và practical impact.** Rule-of-thumb đơn giản nhưng được justify quantitatively.

---

## 5. Data & Metrics View Designer

### Tasks / Datasets / Baselines / Metrics
| | Details |
|---|---|
| **Pre-training** | The Pile (diverse English) |
| **Fine-tuning domains** | Arxiv, Github, DM Mathematics, Wikipedia, etc. |
| **Model sizes** | Tiny, Small, Medium (334M), Large, XL (1.3B) |
| **Injection fractions p** | 0%, 1%, 5% |
| **Metrics** | Pretraining loss (forgetting), FT val loss (perf), MRE (fitting) |

### 3 Chart Proposals
1. **1% injection Figure 1 replica:** 3 subplots over training iterations. Left: FT val loss (U-curve, p=0%/1%/5% almost identical). Middle: FT train loss (monotone, memorization). Right: Pretraining loss (p=0% increases, p=1%/5% flat). Annotate "1% shields forgetting".
2. **Model Size vs. % Pretraining Progress Lost:** Bar chart: X = model size (Tiny → XL). Y = % pretraining progress lost at bottom of U-curve. Dual bars: p=0% vs p=1%. Shows small models lose 95% without injection but stabilize with 1%.
3. **Domain Distance vs. B coefficient:** Scatter plot: X = domain proximity to pretraining (estimated), Y = B coefficient. Annotate domains: Dm Mathematics (B=10⁴), Wikipedia (B=829). Shows distant domains benefit more from injection.

### Table Structure — Key Numbers
| Model | % Progress Lost (p=0%) | % Progress Lost (p=1%) |
|---|---|---|
| Tiny | ~95% | ~5% |
| Small | ~80% | ~10% |
| Medium | ~50% | ~15% |
| Large | ~30% | ~10% |
| XL | ~20% | ~5% |

### Raw Data Extraction
- **Critical rule:** p=1% injection prevents forgetting across all domains and model sizes.
- **Dm Mathematics B ≈ 10⁴** (high forgetting domain); **Wikipedia B ≈ 829** (low forgetting).
- **Extrapolation accuracy:** Medium (334M) + 3M tokens → predict XL (1.3B) + 30M tokens. MRE: 2.01% (FT), 0.83% (PT).
- **Overfitting regularization:** p>0 reduces bottom of U-curve → better generalization even on FT set.

---

## 6. Key Takeaways & Relevance to VN-Legal-AI / CDNCTQ

| Takeaway | Relevance |
|---|---|
| **1% pretraining injection = free lunch** | Khi SFT/CPT trên Vietnamese legal data, inject 1% general Vietnamese data để prevent forgetting, without hurting legal performance |
| **Small models (<1B) forget catastrophically** | Nếu dùng model nhỏ (cho edge deployment), cần injection nhiều hơn 1% |
| **Vietnamese legal = high B domain?** | Vietnamese legal domain rất khác English pretraining → expect B cao → cần injection nhiều hơn để mitigate |
| **Scaling law extrapolation** | Fit với small model (1-3B) + small data → predict cho 7B+ model và large dataset → tiết kiệm compute khi plan training |
| **Combines with D-CPT Law** | D-CPT Law → optimal rd (domain ratio); Scaling Law for Forgetting → confirm 1% general injection đủ |
