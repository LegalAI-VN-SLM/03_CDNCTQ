# Scalable Language Model with Generalized Continual Learning (SLM)

**ArXiv:** 2404.07470  
**Authors:** Bohao Peng, Zhuotao Tian, Shu Liu, Mingchang Yang, Jiaya Jia (Chinese Univ. of Hong Kong + SMartMore)  
**Venue:** Preprint 2024  
**Source File:** `3_ 2404.07470 _Scalable Language Model with Generalized Continual Learning.pdf`

---

## 1. High-Level Summary (5–7 câu)

Bài báo giới thiệu **Scalable Language Model (SLM)** — một framework continual learning cho LLMs không cần replay, không có optimization constraints, và không cần Task-ID lúc inference. Ý tưởng cốt lõi: **lưu trữ kiến thức từng task dưới dạng low-rank weight increments (LoRA-style)**, sau đó dùng **vector-space retrieval (KNN)** để tự động tìm và kết hợp các weight increments phù hợp với từng input lúc inference. SLM gồm hai thành phần: **DTKR** (Dynamic Task-related Knowledge Retrieval) — dùng frozen Sentence-BERT để encode input thành query vector, rồi KNN để tìm top-K relevant weight increments; và **JARe** (Joint Adaptive Re-parameterization) — kết hợp các weight increments theo similarity-weighted sum để re-parameterize model on-the-fly. Trên benchmark BERT, SLM đạt SOTA 79.1% accuracy (không cần Task-ID hay replay), giảm forgetting 82.8% so với SOTA trước. Trên LLaMA2-7B với cross-domain tasks (Medical QA + Finance + MMLU), SLM đạt 82.3% vs. Replay 62.3% vs. Fine-tune 38.5%. Đây là bài hiếm hoi mở rộng CL sang multi-task-type generative LLM setting.

---

## 2. Section Summaries

### 2.1 Introduction
- Catastrophic forgetting là vấn đề lớn khi fine-tune LLM tuần tự trên nhiều tasks.
- Các phương pháp hiện tại có hạn chế: (a) Replay: tốn resource, privacy concerns; (b) Regularization: không ổn định trên long task sequences; (c) Architecture-based (Progressive Prompts): cần Task-ID lúc inference — không thực tế.
- Mục tiêu: Continual learning mà không cần replay, constraints, hay Task-ID. Và mở rộng ra nhiều task types (không chỉ classification).

### 2.2 Method
**DTKR (Dynamic Task-related Knowledge Retrieval):**
1. Frozen Sentence-BERT encode input text → query vector q.
2. KNN cosine similarity với learnable key vectors → chọn top-K keys.
3. Optimization anti-collapse: Group-based retrieval (partition keys/query thành groups) + Random keys masking (Bernoulli masking để tránh local optima).

**JARe (Joint Adaptive Re-parameterization):**
- Mỗi task học ΔθT (low-rank, frozen backbone).
- Lúc inference: Δθ_p = Σᵢ D(p, pᵢ) · Δθᵢ / Σᵢ D(p, pᵢ) — weighted average của top-K weight increments.
- Backbone frozen, chỉ update low-rank adapters → memory efficient.

### 2.3 Experiments & Results

**BERT Benchmark (5 classification tasks, 4 orders):**

| Method | Task-ID | Replay | Avg Acc | Avg Forgetting |
|---|---|---|---|---|
| Fine-tune | No | No | 18.4% | - |
| Replay | Yes | Yes | 57.8% | - |
| IDBR | Yes | No | 76.3% | 2.9% |
| ProgPrompt | Yes | No | 77.9% | - |
| **SLM** | **No** | **No** | **79.1%** | **<0.5%** |
| SLM-TI | Yes | No | 80.0% | - |

**T5 Benchmark (few-shot, 16 examples/class):**

| Method | Avg Acc |
|---|---|
| LFPT5 (SOTA) | 52.7% |
| **SLM** | **73.1%** |

**LLaMA2-7B Benchmark (Medical QA + MMLU + Finance, 2 orders):**

| Method | Avg Acc |
|---|---|
| Fine-tune | 38.5% |
| Replay (1%) | 62.3% |
| **SLM** | **82.3%** |

**Zero-shot evaluation (Arc-c, Arc-e, PIQA, Wino) sau continual fine-tuning:**
- Fine-tune: Arc-c 31.8, Arc-e 42.6, PIQA 67.9, Wino 64.3 (tệ hơn LLaMA2 baseline)
- SLM: Arc-c 44.7, Arc-e 76.0, PIQA 76.3, Wino 67.7 (tốt hơn hoặc bằng LLaMA2 baseline)

### 2.4 Discussion / Conclusions
- SLM overhead: Retrieval time = 7–10ms vs. generation time ~150–2600ms (5% overhead).
- Storage overhead nhỏ do chỉ lưu low-rank weight increments.
- Limitation: Khi số tasks tăng, số weight increments tăng → storage và retrieval time có thể scale.

---

## 3. Methodology Deep-Dive

### Framework
**Architecture-based continual learning** với dynamic retrieval. Key insight: thay vì tìm một model parameter set duy nhất phục vụ tất cả tasks, SLM **maintain một thư viện các task-specific weight adapters** và dynamically mix chúng lúc inference dựa trên input similarity.

### Core Ideas
1. **Vector space = task identity:** Giả định rằng mỗi task có một distribution riêng trong semantic space (Sentence-BERT space). Các inputs từ cùng task sẽ cluster lại gần nhau.
2. **KNN retrieval thay thế Task-ID:** Thay vì biết Task-ID, dùng nearest neighbor search để tự động xác định task distribution.
3. **JARe = soft mixture of experts:** Thay vì chọn cứng 1 adapter, JARe tính weighted average của K adapters → smooth interpolation, giảm catastrophic forgetting và tăng transfer.

### Data / Setup / Metrics
- **BERT benchmark:** 5 text classification tasks (AG News, Yelp, Amazon, DBPedia, Yahoo). 115K train, 7.6K test per task. 4 orders.
- **T5 (few-shot):** Same 5 tasks, 16 examples/class.
- **LLaMA2-7B:** Medical QA (BERTScore), MMLU (accuracy), Finance sentiment (accuracy). 2 orders.
- **Baselines:** Fine-tune, 1% Replay, MBPA++, IDBR, LFPT5, ProgPrompt.

### 3 Key Assumptions
1. **Assumption 1: Task distribution separable trong Sentence-BERT space** — SLM giả định rằng các tasks có distinct distributions trong vector space để KNN có thể phân biệt. Nếu 2 tasks rất similar về text (ví dụ Medical tiếng Anh và Medical tiếng Việt), retrieval có thể bị confused.
2. **Assumption 2: Số tasks M tương đối nhỏ** — KNN trên M key vectors có complexity O(M × d). Khi M rất lớn (hàng trăm tasks), cần approximate KNN (FAISS), không được test trong bài.
3. **Assumption 3: Task boundaries rõ ràng lúc training** — SLM cần biết task identity lúc training (để optimize keys và values per task). Không phải task-free CL.

---

## 4. Brutally Honest Review

### Summary
Bài có ý tưởng thú vị: combine KNN retrieval với LoRA-style adapters để solve CL mà không cần replay hay Task-ID. Kết quả ấn tượng, đặc biệt trên LLaMA2-7B. Tuy nhiên có một số giả định không được kiểm tra đủ.

### Strengths
- ✅ **Không cần Task-ID lúc inference** — giải quyết bottleneck thực tế lớn.
- ✅ **Kết quả ấn tượng trên LLaMA2-7B:** 82.3% vs Replay 62.3% là leap lớn.
- ✅ **Multi-task-type evaluation:** Medical QA + Finance sentiment + MMLU — thực tế hơn nhiều so với chỉ text classification.
- ✅ **Zero-shot preservation:** SLM không chỉ không quên mà còn preserve zero-shot capabilities → quan trọng với LLM deployment.
- ✅ **Overhead nhỏ:** <5.7% inference time overhead — acceptable cho production.

### Weaknesses
- ❌ **Task-ID vẫn cần lúc training:** Bài nói không cần Task-ID nhưng thực ra training per task vẫn cần explicit task boundaries — đây là domain-incremental, không phải task-agnostic.
- ❌ **Scale với số tasks chưa được test:** Chỉ test 3–5 tasks. Không rõ behavior khi M=50 hay M=100 tasks.
- ❌ **Benchmark đơn giản:** BERT benchmark dùng text classification, các tasks khá distinct. Kết quả có thể inflate so với more similar tasks.
- ❌ **LLaMA2 benchmark nhỏ:** Chỉ 3 tasks, 2 orders — sample size thống kê rất thấp.
- ❌ **Sentence-BERT là bottleneck:** Nếu 2 tasks có overlapping semantic space (ví dụ Legal Vietnamese vs. General Vietnamese), retrieval sẽ fail.

### Questions
1. Khi M tăng lên 20-50 tasks, SLM có vẫn retrieve đúng task không, hay accuracy sẽ drop?
2. Có thể dùng online key updating (thêm tasks mới mà không re-train keys cũ) không?
3. Với tiếng Việt, Sentence-BERT tiếng Anh có còn hoạt động tốt làm feature extractor không?

### Recommendation
**Accept (ICLR/ICML) — Borderline to Weak Accept.** Ý tưởng mới, kết quả tốt, nhưng cần thêm ablation về scale và more rigorous statistical comparison trên LLaMA benchmark.

---

## 5. Data & Metrics View Designer

### Tasks / Datasets / Baselines / Metrics
| | Details |
|---|---|
| **BERT Benchmark** | AG News, Yelp, Amazon, DBPedia, Yahoo (5 tasks, 4 orders) |
| **T5 Benchmark** | Same 5 tasks, few-shot (16/class) |
| **LLaMA2-7B Benchmark** | Medical QA + MMLU + Finance Sentiment (3 tasks, 2 orders) |
| **Baselines** | Fine-tune, Replay (1%), MBPA++, IDBR, LFPT5, ProgPrompt |
| **Metrics** | Accuracy (classification/MC), BERTScore (QA), Forgetting (%) |

### 3 Chart Proposals
1. **Bar Chart — Method Comparison (Table 1 replica):** Grouped bar chart: X = methods, Y = avg accuracy. Color-coded by "uses Task-ID" and "uses Replay". Clear visual that SLM wins without either.
2. **Forgetting Progression (Table 4 visualization):** Line chart: X = task number (2→5), Y = forgetting %. SLM (flat near 0) vs IDBR (gradually increasing to 2.9%).
3. **LLaMA2 Task-by-Task Breakdown:** Stacked bar với 3 tasks × 2 orders per method. Shows which specific tasks are forgotten most badly by each method.

### Table Structure — Key Numbers
| Method | BERT Avg Acc | T5 Avg Acc | LLaMA Avg Acc | BERT Forgetting |
|---|---|---|---|---|
| Fine-tune | 18.4% | 28.5% | 38.5% | ~80% |
| Replay | 57.8% | - | 62.3% | - |
| LFPT5/IDBR | 76.3% | 52.7% | - | 2.9% |
| **SLM** | **79.1%** | **73.1%** | **82.3%** | **<0.5%** |

### Raw Data Extraction
- **LLaMA2 Finance→MMLU→Medical:** Finetune avg 38.5%, Replay 62.3%, SLM **82.3%**.
- **Inference overhead:** Finance 4.7%, MMLU 5.7%, Medical 0.3% (retrieval/total time).
- **Forgetting reduction:** 82.8% reduction vs IDBR; SLM forgetting <0.5% across all BERT orders.
- **Zero-shot (LLaMA after sequential FT):** Fine-tune Arc-c 31.8 vs SLM 44.7 (LLaMA2 baseline 43.9).

---

## 6. Key Takeaways & Relevance to VN-Legal-AI / CDNCTQ

| Takeaway | Relevance |
|---|---|
| KNN retrieval thay Task-ID → không cần biết query thuộc task nào | Legal chatbot có thể xử lý câu hỏi từ nhiều lĩnh vực pháp lý (dân sự, hình sự, hành chính) mà không cần routing thủ công |
| JARe = dynamic mixture of LoRA adapters | Có thể train LoRA riêng cho từng loại văn bản pháp lý (ND, TT, QĐ), rồi mix dynamically lúc inference |
| Forgetting <0.5% trên 5 tasks | Phù hợp cho scenario: Bổ sung liên tục legal domains mới mà không quên domains cũ |
| Sentence-BERT tiếng Anh làm feature extractor | Cần PhoBERT hoặc multilingual SBERT cho tiếng Việt để DTKR hoạt động đúng |
| Overhead nhỏ (<6%) | Acceptable cho production deployment |
