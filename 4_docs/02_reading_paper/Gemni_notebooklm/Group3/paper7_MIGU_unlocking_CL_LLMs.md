# Unlocking Continual Learning Abilities in Language Models (MIGU)

**ArXiv:** 2406.17245  
**Authors:** Wenyu Du, Shuang Cheng, Tongxu Luo, Zihan Qiu, Zeyu Huang, Ka Chun Cheung, Reynold Cheng, Jie Fu (HKU + CAS + CUHK-SZ + THU + Edinburgh + NVIDIA + HKUST)  
**Venue:** ACL 2024 / Preprint  
**Code:** https://github.com/wenyudu/MIGU  
**Source File:** `3_ 2406.17245 _Unlocking Continual Learning Abilities in Language Models.pdf`

---

## 1. High-Level Summary (5–7 câu)

Bài báo xuất phát từ một observation thú vị: **các linear layers trong LM tự nhiên tạo ra L1-normalized output magnitude distributions khác nhau cho từng task** — như một "task signature" nội tại của model mà không cần explicit task IDs. Từ đó, **MIGU** (MagnItude-based Gradient Updating) chỉ update những parameters có magnitude lớn nhất (top-T%) trong quá trình backward pass, giúp các tasks tự nhiên update vào các sub-networks khác nhau mà không xung đột. Phương pháp này hoàn toàn **không cần replay (rehearsal-free)** và **không cần task labels (task-label-free)**. Kết quả: LoRA+MIGU cải thiện +15.2% accuracy so với vanilla LoRA trên 15-task long sequence benchmark; tích hợp với các CL methods hiện có (IncLoRA, Replay) đều cải thiện thêm. MIGU áp dụng được cho encoder-only (RoBERTa), encoder-decoder (T5), và decoder-only (Llama2-7B).

---

## 2. Section Summaries

### 2.1 Introduction
- Catastrophic forgetting = vấn đề core của CL trong LMs.
- Các phương pháp hiện tại: rehearsal (cần old data), architecture-based / parameter-based (cần task IDs) — đều có hạn chế trong production.
- Insight: LM linear layers có magnitude distributions khác nhau cho từng task → dùng như proxy cho task identity mà không cần explicit labels.
- MIGU = gradient masking dựa trên L1 magnitude → task-label-free, rehearsal-free.

### 2.2 Core Observation
- Với T5-large, tại FFN layer cuối của Transformer block, L1-normalized magnitude distributions của BoolQA, COPA, Yelp **rõ ràng khác nhau** (Figure 1 & 17).
- Điều này có nghĩa LM đã "biết" data thuộc task nào thông qua activation patterns — chỉ cần unlock knowledge này.

### 2.3 Method (MIGU)

**4-step process:**
1. **Forward:** Tính output `h_i = x · w_i` cho từng weight vector w_i.
2. **Cache:** Tính L1 magnitude `n_i = ||h_i||_1` → lưu lại.
3. **Backward:** Backprop bình thường → gradient ∇W.
4. **Mask & Update:** Chỉ update top-T% weights có magnitude cao nhất:
   ```
   M = BinaryTopT(n, T)
   W_new ← W - η · M ⊙ ∇W
   ```

**Threshold T:** Ablation cho thấy optimal T ≈ 0.7 (mask 70% → update top 30%) cho FT+MIGU và LoRA+MIGU.

**MIGU trong LoRA:** Applied cho matrix A của LoRA; magnitude được tính từ output xO.

### 2.4 Experiments & Results

**T5-large Continual Finetuning (Table 2):**

| Method | Standard (4-task) | Long (15-task) |
|---|---|---|
| LoRA | 67.9% | 46.0% |
| **LoRA + MIGU** | **73.3% (+5.4%)** | **61.2% (+15.2%)** |
| FT | 75.7% | 68.3% |
| **FT + MIGU** | **78.8% (+3.1%)** | **73.8% (+5.5%)** |
| IncLoRA | 68.8% | 64.7% |
| **IncLoRA + MIGU** | **76.4% (+7.6%)** | **68.7% (+4.0%)** |
| SAPT-LoRA (SOTA, needs task labels + data) | — | 82.0% |

**RoBERTa Continual Pre-training (Table 3, DAS benchmark):**
| Method | MF1 | ACC |
|---|---|---|
| DAS (SOTA, needs task info) | 77.90 | 81.90 |
| FT | 76.36 | 80.77 |
| **FT + MIGU** | **76.73 (+0.37)** | **81.19 (+0.42)** |

**Llama2-7B Continual Instruction Tuning (Figure 3):**
- Coding task (HumanEval): LoRA+MIGU ≈ LoRA (learn same)
- General knowledge (HellaSwag, WinoGrande, ARC): MIGU 59.4 vs. FT 58.4 (forget less)

**Task Isolation Visualization (Figure 5):**
- FT: Task similarity BoolQA-COPA = 0.50; với MIGU = **0.20** (−60% overlap)
- Giảm overlap → COPA accuracy tăng **+10%** (45.0% → 55.0%)

### 2.5 Ablation
- **Threshold T = 0.7** là optimal cho FT và LoRA (update top 30%).
- LoRAReplay optimal ở T=0.4; OIncLoRA ở T=0.1.
- Overhead nhỏ: chỉ thêm L1-norm caching trong forward pass.

---

## 3. Methodology Deep-Dive

### Framework
**Gradient masking based on activation magnitude.** Không thêm parameters mới, không cần auxiliary models, không cần external information.

### Core Ideas
1. **Inherent task signature:** LM đã implicitly "categorize" tasks qua magnitude distributions → khai thác điều này thay vì tạo explicit task identifiers.
2. **High magnitude = task-critical neurons:** Neurons activate mạnh cho một task = neurons "quan trọng" cho task đó → chỉ update những neurons này → tránh overwrite neurons của task khác.
3. **Plug-in orthogonality:** MIGU hoạt động song song với rehearsal, architecture, parameter-based methods → có thể stack lên nhau.

### Data / Setup / Metrics
- **T5 continual finetuning:** 4-task standard (3 orders) + 15-task long sequence (3 orders).
- **RoBERTa continual pre-training:** DAS benchmark (6 domains: Yelp, Amazon×2, ACL, AI, PubMed).
- **Llama2-7B:** Magicoder-Evol-Instruct-110K (coding); eval: HumanEval + HellaSwag + WinoGrande + ARC.
- **Metrics:** Average accuracy after training on last task (ACC); Macro-F1 cho DAS.

### 3 Key Assumptions
1. **Assumption 1: L1 magnitude = reliable task proxy** — Assumed rằng magnitude distributions đủ distinct cho mọi task pair. Chưa test với tasks rất similar (ví dụ: Vietnamese civil law vs. Vietnamese criminal law).
2. **Assumption 2: Top-T% masking = good task separator** — Threshold T được tune per experiment nhưng không có principled cách chọn T cho unseen task pairs.
3. **Assumption 3: Batch-averaged magnitude = representative** — MIGU average magnitudes theo batch. Nếu batch size nhỏ hoặc data noisy, average có thể unreliable.

---

## 4. Brutally Honest Review

### Summary
MIGU là một trong những phương pháp CL elegant nhất trong nhóm — insight rõ ràng, implementation đơn giản, kết quả tốt, áp dụng được cho nhiều architectures. Hạn chế chính là gap với SOTA methods có task labels.

### Strengths
- ✅ **Insight mới về LM inherent properties:** L1 magnitude = task signature là observation thực sự mới.
- ✅ **Implementation cực đơn giản:** Chỉ thêm L1 caching + binary masking → <5% overhead.
- ✅ **Universal:** Hoạt động trên T5, RoBERTa, Llama2 — 3 architectures khác nhau.
- ✅ **Plug-in:** Tăng performance của mọi baseline khi combine.
- ✅ **Visualization thuyết phục (Figure 5):** Heatmap task similarity rõ ràng trước/sau MIGU.

### Weaknesses
- ❌ **Gap với SOTA (cần task labels):** LoRA+MIGU (61.2%) vs SAPT-LoRA (82.0%) trên long sequence — gap 20.8% là rất lớn.
- ❌ **Threshold T không principled:** Cần tune T per setup; không có automatic way để chọn T.
- ❌ **Long sequence (15 tasks) chưa verify ở LLM scale:** Chỉ test 15 tasks với T5-large; không test Llama2 trên long sequence.
- ❌ **Magnitude observation không guaranteed với arbitrary tasks:** Với tasks rất similar (ví dụ domain-specific QA + domain-specific classification trong cùng domain), magnitudes có thể overlap.
- ❌ **Continual pre-training result modest (DAS benchmark):** +0.37% MF1, +0.42% ACC — không impressive so với effort.

### Questions
1. MIGU có hoạt động khi tasks rất similar (ví dụ Vietnamese civil law vs. criminal law)?
2. Khi tasks tăng lên 50-100, magnitude distributions có còn đủ distinct không?
3. MIGU có thể combine với LoRA + selective replay (5%) để đạt kết quả tốt hơn không?

### Recommendation
**Accept (ACL/EMNLP) — Solid Accept.** Insight mới, method đơn giản và effective, kết quả decent. Không breakthrough nhưng đóng góp methodological thực sự.

---

## 5. Data & Metrics View Designer

### Tasks / Datasets / Baselines / Metrics
| | Details |
|---|---|
| **T5 Standard** | 4 text classification tasks, 3 orders |
| **T5 Long Sequence** | 15 tasks (5 classification + 9 GLUE/SuperGLUE + IMDB), 3 orders |
| **RoBERTa DAS** | 6 domain corpora (reviews + academic papers) |
| **Llama2-7B** | Magicoder-Evol (coding fine-tuning) |
| **Baselines** | FT, LoRA, IncLoRA, OIncLoRA, LoRAReplay, LFPT5, SAPT-LoRA |
| **Metrics** | Average ACC (final); MF1 (DAS); HumanEval pass@1 (Llama) |

### 3 Chart Proposals
1. **Delta Accuracy Bar Chart (Table 2):** X = methods (FT, LoRA, IncLoRA, etc.), Y = accuracy. Paired bars: baseline vs. +MIGU. Annotate delta. Long sequence benchmark shows biggest gains.
2. **Task Similarity Heatmap (Figure 5 replica):** 3×3 matrices (tasks: BoolQA, COPA, Yelp). Left: FT similarity. Right: FT+MIGU similarity. Color gradient. Shows dramatic reduction in overlap.
3. **Llama2 Learning vs. Forgetting Tradeoff:** Scatter plot: X = HumanEval (coding ability), Y = HellaSwag+WinoGrande+ARC avg (general knowledge). Points at each epoch: FT (blue) vs. FT+MIGU (orange). Shows MIGU moves along Pareto frontier.

### Table Structure — Key Numbers
| Benchmark | Method | Baseline | +MIGU | Delta |
|---|---|---|---|---|
| T5 Long (15-task) | LoRA | 46.0% | 61.2% | **+15.2%** |
| T5 Long (15-task) | IncLoRA | 64.7% | 68.7% | **+4.0%** |
| T5 Standard (4-task) | FT | 75.7% | 78.8% | **+3.1%** |
| RoBERTa DAS | FT | 80.77% | 81.19% | **+0.42%** |
| Llama2 (general knowledge) | LoRA | 58.4% | 59.4% | **+1.0%** |

### Raw Data Extraction
- **Threshold optimal:** T=0.7 cho FT+MIGU (update top 30%); T=0.4 cho LoRAReplay+MIGU.
- **Task isolation:** BoolQA-COPA overlap FT=0.50 vs MIGU=0.20 → COPA accuracy +10%.
- **Overhead:** Minimal (L1-norm caching during forward, binary masking during backward).
- **DAS benchmark:** FT+MIGU MF1=76.73 vs DAS SOTA=77.90 (gap = 1.17% với no task labels).

---

## 6. Key Takeaways & Relevance to VN-Legal-AI / CDNCTQ

| Takeaway | Relevance |
|---|---|
| **MIGU = rehearsal-free + task-label-free CL** | Không cần lưu old legal documents (privacy!) và không cần route requests theo task |
| **+15.2% over LoRA trên long sequence** | Quan trọng khi muốn fine-tune model trên nhiều loại văn bản pháp lý tuần tự (ND → TT → QĐ → ...) |
| **Plug-in với LoRA** | Có thể add MIGU vào existing LoRA fine-tuning pipeline không cần thay đổi architecture |
| **Threshold T cần tune** | Cần ablation với legal domain để chọn T phù hợp (bắt đầu với T=0.7) |
| **Tasks rất similar có thể problematic** | Legal subdomains (dân sự vs. hình sự) có overlap cao → cần test xem magnitude distributions có đủ distinct không |
