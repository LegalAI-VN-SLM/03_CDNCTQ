# Simple and Scalable Strategies to Continually Pre-train Large Language Models

**ArXiv:** 2403.08763  
**Authors:** Adam Ibrahim, Benjamin Thérien, Kshitij Gupta, Mats L. Richter, Quentin Anthony, Timothée Lesort, Eugene Belilovsky, Irina Rish (Université de Montréal / Concordia / Mila / EleutherAI)  
**Venue:** ICLR 2024 (OpenReview)  
**Source File:** `3_ 2403.08763 _Simple and Scalable Strategies to Continually Pre-train Large Language Models.pdf`

---

## 1. High-Level Summary (5–7 câu)

Bài báo trả lời câu hỏi thực tế: **làm thế nào để cập nhật LLM trên dữ liệu mới mà không cần train lại từ đầu?** Kết quả chính là tổ hợp đơn giản gồm **LR re-warming + LR re-decaying + compute-equivalent replay ~5%** đủ để match performance của model train từ đầu trên tất cả data. Thí nghiệm được tiến hành ở 2 scale model (405M và 10B) với 2 loại distribution shift (weak: English→English Pile→SlimPajama, và strong: English→German). Điểm đặc biệt của bài là dùng **compute-equivalent replay** — khi replay x% data cũ, đổi lại giảm x% tokens mới — đảm bảo so sánh công bằng. Ngoài ra, bài đề xuất **Infinite LR schedules** như giải pháp thay thế cosine decay, tránh được vấn đề optimization-induced forgetting từ re-warming. Kết quả downstream: 10B model với 5% replay đạt avg accuracy 47.68% vs. union baseline 48.00% — gần như tương đương với nửa compute.

---

## 2. Section Summaries

### 2.1 Introduction
- Train LLM từ đầu mỗi khi có data mới = rất tốn kém; continual pre-training là giải pháp.
- Hai thách thức: (1) poor adaptation — model đã decay LR về nhỏ, không học tốt data mới; (2) catastrophic forgetting — mất kiến thức cũ.
- Giải pháp: Re-warm LR + re-decay LR + replay nhỏ. Baseline để so sánh: train from scratch trên D₀ ∪ D₁.

### 2.2 Method
**3 thành phần cốt lõi:**
1. **LR Re-warming + Re-decaying:** Sau khi pre-train với cosine decay về LR_min, khi bắt đầu dataset mới: re-warm lên LR_max rồi cosine decay lại. MaxLR là dial kiểm soát trade-off.
2. **Compute-equivalent Replay:** Giữ total training budget cố định. Ví dụ 5% replay trên 300B tokens: 285B tokens SlimPajama + 15B tokens Pile. Replay theo thứ tự gốc (không shuffle).
3. **Infinite LR Schedules:** Thay vì cosine decay mỗi dataset, dùng schedule: warm-up → cooldown → **constant LR** (qua nhiều tasks) → annealing cuối. Không cần re-warm khi chuyển task mới.

### 2.3 Experiments & Results

**Table 4 — Validation Loss (10B model):**

| Configuration | D₀ (Pile) | D₁ (SP) | AVG |
|---|---|---|---|
| 300B Pile only | 1.75 | 2.24 | 1.99 |
| 300B SP only | 2.08 | 2.05 | 2.07 |
| Continual PT (no replay) | 1.98 | 2.00 | 1.99 |
| **Continual PT (5% replay)** | **1.79** | **2.00** | **1.89** |
| 600B Pile ∪ SP (baseline) | 1.72 | 2.02 | **1.87** |

*Diff chỉ 0.02 — continual PT với 5% replay gần như match union baseline.*

**Table 2 — Replay Effect (405M, Weak Shift):**

| Replay % | D₀ Pile | D₁ SP | AVG |
|---|---|---|---|
| 0% | 2.44 | 2.50 | 2.47 |
| 1% | 2.26 | 2.50 | 2.38 |
| **5%** | **2.23** | **2.51** | **2.37** |
| 50% | 2.16 | 2.54 | 2.35 |
| Union baseline | 2.17 | 2.53 | **2.35** |

**Strong shift (German):** Cần ~25% replay để match union baseline (forgetting nặng hơn nhiều do language shift).

**Downstream (10B, Table 5):** Avg accuracy 47.68% (5% replay) vs 48.00% (union) — không có difference thống kê có ý nghĩa.

### 2.4 Discussion / Conclusions
- Warm-up length (0.5–2%) không quan trọng; 0% gây chaotic loss spike ban đầu.
- Re-warming trên cùng data vẫn gây loss spike (0.1–0.2 nats) → đây là pathology của optimizer.
- Infinite LR schedules tránh được vấn đề này và không cần biết token budget trước.
- Kết quả hold ở cả 405M và 10B → khả năng scale tốt.

---

## 3. Methodology Deep-Dive

### Framework
Continual pre-training 2 giai đoạn: D₀ (300B Pile) → D₁ (300B SlimPajama hoặc 200B German CC). Không có task boundaries rõ ràng trong continual LM. Reset optimizer states khi chuyển dataset.

### Core Ideas
1. **Re-warm + Re-decay là cần thiết:** Model đã decay LR về minimum nên không thể học data mới hiệu quả nếu không re-warm. Giống như restart learning.
2. **Compute-equivalent replay:** Sáng tạo methodological quan trọng — giữ compute budget cố định khi so sánh replay vs. no-replay, tránh confound với số tokens.
3. **Infinite LR:** Tránh re-warming bằng cách duy trì high constant LR qua nhiều tasks, chỉ anneal lần cuối khi deploy. Phù hợp hơn cho lifelong learning vì không cần biết total token budget.

### Data / Setup / Metrics
- **Upstream:** Pile (300B tokens); **Downstream (weak):** SlimPajama (300B); **Downstream (strong):** German Common Crawl (200B).
- **Models:** GPT-NeoX 405M và 9.6B (≈10B). AdamW, batch=1104, seqlen=2048, FP16.
- **Metrics:** Validation perplexity (D₀ + D₁); 11 English benchmarks (HellaSwag, ARC, PIQA, MMLU, etc.) và 4 German benchmarks; 0-shot và 5-shot.

### 3 Key Assumptions
1. **Assumption 1: 2-stage continual PT đủ đại diện** — Thí nghiệm chỉ có Pile → X (1 lần chuyển). Trong thực tế LLM updates liên tục nhiều lần; không rõ 5% replay có còn hiệu quả sau 5-10 stages.
2. **Assumption 2: Replay theo thứ tự gốc là đủ** — Không thử replay random shuffle hoặc curated replay. Smart replay (diversity-based, importance-weighted) có thể cần replay % thấp hơn.
3. **Assumption 3: Tokenizer không thay đổi** — Bài dùng Pile tokenizer cho cả German CC. Tokenizer không được optimize cho German → underfitting potential, và strong shift results có thể bị distorted.

---

## 4. Brutally Honest Review

### Summary
Bài strong và practical: thí nghiệm quy mô thực tế (10B model, 300B tokens), kết quả rõ ràng, insights hữu ích. Là bài tốt nhất trong nhóm continual pre-training về tính prescriptive.

### Strengths
- ✅ **Scale thực tế:** 10B parameter, 300B+ tokens — đây là quy mô production LLM thực sự.
- ✅ **Compute-equivalent replay:** Thiết kế thí nghiệm xuất sắc, loại bỏ confound quan trọng.
- ✅ **Hai loại shift:** Weak (English→English) và strong (English→German) — bao phủ nhiều use case.
- ✅ **Downstream evaluation:** 11 benchmark English + 4 German → comprehensive.
- ✅ **Infinite LR proposal:** Đóng góp methodological có giá trị thực tiễn.
- ✅ **Rules of thumb** được viết rõ ràng → practitioner-friendly.

### Weaknesses
- ❌ **Chỉ 2 datasets (Pile + SlimPajama/German):** Multi-stage (>3 stages) continual PT chưa được test.
- ❌ **Replay strategy đơn giản (sequential order):** Không test diversity-based replay hay importance sampling.
- ❌ **Tokenizer issue cho German:** Dùng Pile tokenizer cho German CC có thể inflate "forgetting" do tokenization mismatch.
- ❌ **Warm-up length experiment chỉ 50B tokens:** Không đủ để kết luận dứt khoát về long-term effects.
- ❌ **Infinite LR chưa được test ở 10B scale:** Chỉ test 405M — chưa rõ behavior ở model lớn hơn.

### Questions
1. Sau 5 giai đoạn continual PT liên tiếp với 5% replay, accumulated forgetting có còn kiểm soát được không?
2. Với Vietnamese legal domain (strong shift từ English pre-trained model), cần bao nhiêu % replay để maintain English capability?
3. Infinite LR có hoạt động tốt với learning rate warmup strategies khác (cosine vs. linear vs. trapezoidal)?

### Recommendation
**Strong Accept (ICLR track).** Bài này xứng đáng là công trình tham khảo chuẩn cho continual pre-training practitioners. Mở rộng tự nhiên là multi-stage và smarter replay.

---

## 5. Data & Metrics View Designer

### Tasks / Datasets / Baselines / Metrics
| | Details |
|---|---|
| **Upstream (D₀)** | The Pile (300B tokens) |
| **Downstream Weak (D₁)** | SlimPajama (300B tokens, English) |
| **Downstream Strong (D₁)** | German Common Crawl (200B tokens) |
| **Models** | GPT-NeoX 405M, GPT-NeoX 10B |
| **Baselines** | Constant LR (no rewarm), Union D₀∪D₁, D₁ only |
| **Primary Metric** | Validation Perplexity (D₀ + D₁, averaged) |
| **Secondary** | 11 EN + 4 DE benchmark accuracy |

### 3 Chart Proposals
1. **AVG Validation Loss vs. Replay % (Table 2 visualization):** Bar chart: X = replay %, Y = avg val loss. 3 bars per replay level: D₀ loss, D₁ loss, AVG. Overlay dashed line = union baseline. Shows optimal replay % clearly.
2. **Compute vs. Performance Trade-off (Figure 1):** Scatter plot: X = compute (in units of 300B tokens), Y = avg final val loss. Points: D₀ only, D₁ only, Continual PT variants, Union baseline. Highlights that continual PT with 5% replay uses 50% compute but matches union baseline.
3. **Infinite LR vs. Cosine Comparison:** Line plot của val loss theo training tokens, overlay Cosine+rewarm vs. Infinite LR. Highlight spike tại transition point cho cosine (bị absent ở Infinite LR).

### Table Structure (Key Numbers)
| Model | Strategy | Compute | D₀ Pile Loss | D₁ SP Loss | AVG | EN Benchmarks |
|---|---|---|---|---|---|---|
| 10B | Pile only | 1.0× | 1.75 | 2.24 | 1.99 | - |
| 10B | SP only | 1.0× | 2.08 | 2.05 | 2.07 | - |
| 10B | CPT (0% replay) | 1.0× | 1.98 | 2.00 | 1.99 | - |
| **10B** | **CPT (5% replay)** | **1.0×** | **1.79** | **2.00** | **1.89** | **47.68%** |
| 10B | Union D₀∪D₁ | **2.0×** | 1.72 | 2.02 | **1.87** | **48.00%** |

### Raw Data Extraction
- **Critical result:** 5% replay, 10B, weak shift → AVG loss 1.89 vs union 1.87 (diff = 0.02).
- **Strong shift:** 25% replay cho stable forgetting trên German shift (3.56→2.33 Pile loss với 25% replay vs 3.56→3.56 no replay).
- **Replay impact on forgetting (405M):** 0% replay → Pile loss 2.44; 5% replay → 2.23; matching union 2.17.
- **Re-warming pathology:** MaxLR 6e-4 re-warm trên cùng Pile data gây peak loss increase = +0.2 nats.

---

## 6. Key Takeaways & Relevance to VN-Legal-AI / CDNCTQ

| Takeaway | Relevance |
|---|---|
| **5% compute-equivalent replay đủ cho weak shift** | Khi adapt LLM sang tiếng Việt legal từ English base, giữ ~5% English general data trong replay buffer |
| **Strong shift (khác language) cần 25% replay** | Vietnamese là strong shift từ English pre-trained LLMs → có thể cần 15-25% replay general Vietnamese data |
| **Infinite LR phù hợp hơn cho multi-stage CPT** | Nếu plan update model nhiều lần (thêm NDs, nghị định mới), Infinite LR schedule tiết kiệm hơn cosine reset |
| **10B model match union với 1/2 compute** | Ước lượng: giả sử training from scratch 100 GPU-days, continual PT chỉ cần 50 GPU-days với chất lượng tương đương |
| **Tokenizer là bottleneck cho cross-lingual shift** | Cần xem xét specialized Vietnamese tokenizer trước khi continual pre-train từ English base model |
