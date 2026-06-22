# Efficient Continual Pre-training by Mitigating the Stability Gap

**ArXiv:** 2406.14833  
**Authors:** Yiduo Guo, Jie Fu, Huishuai Zhang, Dongyan Zhao, Yikang Shen (Peking Univ. + HKUST + MIT-IBM Watson AI Lab)  
**Venue:** Preprint 2024  
**Source File:** `3_ 2406.14833 _Efficient Continual Pre-training by Mitigating the Stability Gap.pdf`  
**Model Released:** [Llama-3-Physician-8B-Instruct](https://huggingface.co/YiDuo1999/Llama-3-Physician-8B-Instruct)

---

## 1. High-Level Summary (5–7 câu)

Bài báo quan sát một hiện tượng thú vị và thực tế: khi continual pre-train LLM trên domain mới, **performance ban đầu drop mạnh (V-shape) trước khi hồi phục** — gọi là "stability gap". Nguyên nhân: gradient plasticity (học data mới) ban đầu quá lớn so với gradient stability (duy trì instruction-following), phá vỡ khả năng follow instructions trước khi domain knowledge đủ mạnh để bù lại. Bài đề xuất 3 chiến lược đơn giản để mitigate stability gap: (1) **Multi-epoch subset training** (thay vì 1 epoch trên toàn bộ data, dùng subset nhỏ × nhiều epoch); (2) **High-quality data filtering** (dùng KenLM để chọn high-quality tokens); (3) **Pre-training data mixture** (giữ tỷ lệ mix giống original pre-training để reduce distribution shift). Kết quả: OpenLlama-3B tăng từ 36.2% → 40.7% trên medical tasks với chỉ 20B tokens (40% compute budget so với 50B baseline). **Llama-3-Physician-8B** đạt 76.7% avg trên medical QA (vượt Meditron-70B 69.0%, GPT-3.5 66.7%).

---

## 2. Section Summaries

### 2.1 Introduction
- Continual pre-training on new domain = standard approach, nhưng tại sao performance ban đầu drop?
- "Stability gap" từ vision continual learning: performance drop rồi recover khi học task mới.
- Giải thích: plasticity gradient > stability gradient → instruction-following bị phá trước khi domain knowledge đủ mạnh.
- 3 strategies để mitigate: subset + multi-epoch, high-quality data, proper mixture ratio.

### 2.2 Observations (Empirical)

**3 observations được verify:**
1. **Medical task performance V-shape:** OpenLlama-3B và TinyLlama-1.1B đều drop trong 5B token đầu, sau đó recover.
2. **Medical perplexity KHÔNG drop:** PPL liên tục giảm đều → LLM học domain knowledge từ đầu; vấn đề là instruction-following bị phá.
3. **Top layers vs. bottom layers:** Bottom layers (syntax/low-level) update nhanh ban đầu (high plasticity); top layers (instruction-following) update chậm → stability gradient thiếu.

**Stability gap formula:**
```
L = α·L_plasticity + (1-α)·L_stability
```
Ban đầu L_plasticity >> L_stability → stability bị neglect.

### 2.3 Three Strategies

| Strategy | Mô tả | Hiệu quả |
|---|---|---|
| **I. Multi-epoch Subset** | 5B HQ tokens × 5 epochs thay vì 50B × 1 epoch | Faster performance recovery |
| **II. HQ Data Filtering** | KenLM chọn top-quality medical tokens (lowest PPL vs. reference corpus) | Higher peak performance |
| **III. Data Mixture** | Giữ tỷ lệ mix giống Llama pre-training; thay CC/C4 bằng medical tokens | Maintain instruction-following |

### 2.4 Experiments & Results

**Table 1 — Zero-shot Medical Accuracy (OpenLlama-3B):**

| Method | Training Tokens | MMLU-Med | PubMedQA | MedMCQA | MedQA | Avg |
|---|---|---|---|---|---|---|
| Baseline | 0 | 25.6 | 68.4 | 25.4 | 25.4 | 36.2 |
| Full token baseline | 50B | 26.1 | 70.4 | 26.1 | 27.1 | 37.4 |
| Re-warming/re-decaying | 50B | 26.5 | 70.3 | 27.1 | 27.1 | 37.7 |
| Replay 10% | 50B | 26.3 | 69.2 | 27.6 | 26.9 | 37.5 |
| **Our strategies** | **20B** | **30.0** | **71.2** | **34.0** | **27.8** | **40.7** |

*40% compute → 40.7% vs 50B baseline 37.4%!*

**Table 2 — Task-specific Fine-tuning (Llama-3-Physician-8B):**

| Model | MMLU-Med | PubMedQA | MedMCQA | MedQA | Avg |
|---|---|---|---|---|---|
| MEDITRON-70B | 73.6 | 80.0 | 65.1 | 65.4 | 69.0 |
| GPT-3.5 fine-tuned | 70.5 | 71.4 | 61.8 | 63.3 | 66.7 |
| Llama-3-8B Fine-tuned | 82.3 | 75.8 | 60.0 | 61.1 | 69.8 |
| **Llama-3-Physician-8B** | **85.0** | **79.1** | **81.4** | **61.5** | **76.7** |

**Table 3 — Instruction-tuning:**
- Llama-3-Physician-8B Instruct: Avg QA = **74.1%** vs GPT-4 = **77.0%** (close!)
- GPT-4 HOC: 60.2; SLM: **78.9%** | GPT-4 BioNLI: 57.8; SLM: **76.2%** (vượt GPT-4)

### 2.5 Learning Rate Analysis
- TinyLlama (1.1B): optimal LR = 3e-4
- OpenLlama-3B: optimal LR = 3e-5
- LR quá cao → plasticity gradient quá lớn → destroy instruction-following.

---

## 3. Methodology Deep-Dive

### Framework
3 strategies ghép lại, không phải một framework mới. Tiếp cận pragmatic/engineering-focused.

### Core Ideas
1. **Stability gap = optimization imbalance:** Không phải vấn đề data hay architecture, mà là gradient dynamics ban đầu.
2. **Subset × multi-epoch > full corpus × 1 epoch:** Sau epoch đầu, plasticity gradient tự nhiên giảm (model đã "thấy" data) → stability gradient có cơ hội bắt kịp.
3. **KenLM quality filtering:** Dùng n-gram LM (lightweight) để score quality của training samples — proxy hiệu quả cho data quality mà không cần LLM-judge.

### Data / Setup / Metrics
- **Domain:** Medical (primary), General/RefinedWeb (secondary).
- **Models:** OpenLlama-3B, TinyLlama-1.1B, Pythia-410M, Llama-3-8B.
- **Compute budget:** Default = 50B tokens; proposed strategies = 20B tokens.
- **Baselines:** 50B 1-epoch, re-warm/re-decay, 10% replay, layer freezing (top/bottom 5).
- **Metrics:** Zero-shot accuracy (medical: MMLU-Med, PubMedQA, MedMCQA, MedQA; general: 10 commonsense tasks).

### 3 Key Assumptions
1. **Assumption 1: Stability gap là universal** — Chỉ verify trên medical domain (một domain). Không rõ stability gap có magnitude tương tự cho code, law, Vietnamese legal domain không.
2. **Assumption 2: KenLM quality ≡ real quality** — PPL từ n-gram LM được dùng làm proxy cho data quality. Nhưng low PPL không đảm bảo factual accuracy hay relevance cho downstream tasks.
3. **Assumption 3: 5 epochs là optimal** — Multi-epoch training được test chủ yếu ở 5 epochs. Không có systematic search cho optimal epoch count; có thể overfitting sau nhiều epochs hơn.

---

## 4. Brutally Honest Review

### Summary
Bài rất practical và có real-world impact (mô hình Llama-3-Physician được release). Insights về stability gap rõ ràng và được verify empirically. Tuy nhiên, solutions khá engineered và thiếu theoretical depth.

### Strengths
- ✅ **Thực tế cao:** 3 strategies đơn giản, dễ implement, không cần architectural changes.
- ✅ **Compute efficiency ấn tượng:** 40% compute → tốt hơn 50B baseline (40.7% vs 37.4%).
- ✅ **Llama-3-Physician-8B released:** Validated trong production setting với GPT-4 comparison.
- ✅ **Layer-wise analysis (Figure 3):** Chứng minh top vs bottom layer gradient dynamics là insight mới.
- ✅ **Diverse baselines:** So sánh với re-warm, replay, layer freezing → honest comparison.

### Weaknesses
- ❌ **Chỉ medical domain:** 3 strategies chưa được verify trên code, math, law, hay multilingual domains.
- ❌ **KenLM quality filtering phụ thuộc vào reference corpus:** Cần high-quality reference corpus để train KenLM. Với domains như legal Vietnamese (ít data tốt), khó build reference corpus.
- ❌ **LR recommendation chưa đủ principled:** Chỉ biết "lớn cần LR nhỏ hơn" nhưng không có formula hay thủ tục để tính optimal LR.
- ❌ **Multi-epoch potential overfitting:** 5 epochs trên 5B HQ subset = model thấy mỗi token 5 lần → memorization risk, đặc biệt với data nhỏ.
- ❌ **Comparison với GPT-4 có thể biased:** GPT-4 được test qua API với prompt format khác → không controlled comparison.

### Questions
1. Stability gap có xảy ra với data ít phân kỳ (ví dụ, tiếp tục train trên Vietnamese thay vì medical English)?
2. Với Vietnamese legal domain (corpus ~1-5B tokens), optimal subset size và epoch count là bao nhiêu?
3. Nếu dùng Llama-based model, không biết original data mixture → Strategy III khó apply. Có proxy nào không?

### Recommendation
**Accept (EMNLP/ACL).** Practical contributions rõ ràng, có model release. Thiếu generalizability study nhưng đóng góp engineering là thực sự có giá trị.

---

## 5. Data & Metrics View Designer

### Tasks / Datasets / Baselines / Metrics
| | Details |
|---|---|
| **Domain** | Medical (primary); General/RefinedWeb (secondary) |
| **Models** | OpenLlama-3B, TinyLlama-1.1B, Pythia-410M, Llama-3-8B |
| **Compute budget** | 50B tokens (baseline) vs 20B (proposed) |
| **Medical Benchmarks** | MMLU-Med, PubMedQA, MedMCQA, MedQA-4-Option |
| **Instruction-tuning** | HOC (classification), DDI-2013 (RE), BioNLI (NLI), MIMIC-CXR (summarization) |

### 3 Chart Proposals
1. **Stability Gap Visualization (Figure 2 replica):** Line chart: X = training tokens (B), Y = task performance (%). Multiple lines: Medical performance (V-shape drop then rise), Medical PPL (monotone decrease). Annotate "stability gap" region.
2. **Strategy Comparison Bar Chart (Table 1):** Grouped bar: 8 configs × 4 benchmarks. X = method, Y = avg accuracy. Highlight 20B strategies outperforming 50B baselines.
3. **Llama-3-Physician vs. GPT-4 Radar Chart:** 8 axes = medical tasks (MMLU-Med, PubMedQA, MedMCQA, MedQA, HOC, DDI, BioNLI, MIMIC-CXR). Overlay: GPT-4, GPT-3.5, Llama-3-Physician-8B, Meditron-70B.

### Table Structure — Key Numbers
| Method | Tokens | Avg Medical (0-shot) | vs. Baseline |
|---|---|---|---|
| OpenLlama-3B base | 0 | 36.2% | — |
| 50B full baseline | 50B | 37.4% | +1.2pp |
| Re-warm/re-decay | 50B | 37.7% | +1.5pp |
| 10% Replay | 50B | 37.5% | +1.3pp |
| **Our strategies** | **20B** | **40.7%** | **+4.5pp** |

### Raw Data Extraction
- **Critical result:** 20B tokens + 3 strategies = 40.7% vs 50B baseline 37.4% (= +3.3pp with 60% less compute).
- **Llama-3-Physician-8B task-specific:** 76.7% avg (MMLU-Med 85.0, PubMedQA 79.1, MedMCQA 81.4, MedQA 61.5).
- **vs. GPT-4 (instruction-tuning):** HOC 78.9 vs 60.2; BioNLI 76.2 vs 57.8; DDI 33.6 vs 29.2.
- **Learning rate sensitivity:** TinyLlama optimal LR=3e-4; OpenLlama-3B optimal LR=3e-5.

---

## 6. Key Takeaways & Relevance to VN-Legal-AI / CDNCTQ

| Takeaway | Relevance |
|---|---|
| **Stability gap xảy ra trong mọi CPT** | Khi CPT LLM sang tiếng Việt legal, expect performance drop ban đầu — ĐỪNG stop early! |
| **Multi-epoch subset > single-epoch large corpus** | Dùng 5B HQ legal tokens × 4-5 epochs thay vì toàn bộ corpus × 1 epoch (tiết kiệm compute) |
| **KenLM quality filtering thực tế** | Build KenLM từ high-quality legal references (ND, TT từ VGP, VPCP) → filter noisy legal data |
| **Strategy III: Giữ data mixture** | Cần estimate distribution của base model's pre-training data; nếu không biết, thêm ~15-20% general Vietnamese data |
| **LR smaller for larger model** | Với LLM 7B, dùng LR ≤ 1e-5 để tránh destroy instruction-following |
| **8B có thể vượt 70B** | Với continual PT + HQ data + proper strategy, model nhỏ có thể vượt model lớn |
