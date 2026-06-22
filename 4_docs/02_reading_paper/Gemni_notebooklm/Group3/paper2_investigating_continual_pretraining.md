# Investigating Continual Pretraining in Large Language Models: Insights and Implications

**ArXiv:** 2402.17400  
**Authors:** Çağatay Yıldız, Nishaanth Kanna Ravichandran, Nitin Sharma, Matthias Bethge, Beyza Ermis (Univ. Tübingen + Cohere for AI)  
**Venue:** Preprint 2024  
**Source File:** `3_ 2402.17400 _Investigating Continual Pretraining in Large Language Models Insights and Implications.pdf`

---

## 1. High-Level Summary (5–7 câu)

Bài báo xây dựng một benchmark quy mô lớn cho **continual domain-adaptive pretraining** của LLMs, sử dụng M2D2 dataset với 159 domain đa dạng (6.6B tokens). Thay vì đề xuất giải pháp mới, mục tiêu chính là **đặc trưng hóa các thách thức** của CL trong LLMs và kiểm tra xem các insight từ CL nhỏ có generalize không. Kết quả cho thấy: model lớn hơn luôn tốt hơn và ít quên hơn; continual pretraining tốt hơn standalone domain adaptation; thứ tự domain ảnh hưởng mạnh đến trade-off giữa specialization và forgetting. Phát hiện đáng ngạc nhiên nhất: **Llama2-7B bị degraded** khi continual pretrain trên các domain nhỏ (<100MB), trong khi GPT-2 được cải thiện. RoBERTa (encoder) không thể hiện catastrophic forgetting như decoder-only models. Benchmark này phục vụ như nền tảng cho các nghiên cứu tương lai về CL trong LLMs.

---

## 2. Section Summaries

### 2.1 Introduction
- Chi phí tài chính và sinh thái khi train LLMs từ đầu quá cao → CL là giải pháp bền vững.
- Các benchmark CL hiện tại quá nhỏ (split CIFAR-100, v.v.) và không phản ánh thực tế continual pretraining ở quy mô LLM.
- Mục tiêu: Benchmark toàn diện cho continual domain-adaptive pretraining, không phải continual fine-tuning.
- 5 key findings được tóm tắt ngay ở đầu bài.

### 2.2 Methodology
- **M2D2 Dataset:** 8.5B tokens, 236 domains từ Wikipedia và Semantic Scholar (S2ORC). Bài dùng 159 domain (loại >5GB như Medicine).
- **Models:** GPT2-S/M/L/XL (117M–1.5B), Llama2-7B (decoder-only); RoBERTa-base/large (encoder).
- **Domain ordering:** (i) **Similar-order** — domain gần nhau về semantic được xếp liên tiếp; (ii) **Random-order** — domain bị shuffle.
- **Metrics:** Zero-shot (ZS), DAPT, Continual Pretraining (CPT), Last Checkpoint (LC), Forgetting (FG), Backward/Forward Transfer.
- **Downstream:** 5 BIG-Bench tasks (Arithmetic, General Knowledge, Physics, CS Algorithms, Few-shot NLG).

### 2.3 Experiments & Results
| Phát hiện | Chi tiết |
|---|---|
| **CL vs. DAPT** | Continual pretraining luôn tốt hơn standalone DAPT cho GPT-2 family |
| **Model size** | Lớn hơn → perplexity tốt hơn, ít quên hơn. Nhỏ hơn → cải thiện nhiều hơn nhưng quên nhiều hơn |
| **Llama2-7B** | Bị degraded khi domain <100MB. ZS PPL=6.84, LC PPL=11.42 (tệ hơn) |
| **Random order** | Positive forward transfer, ít forgetting hơn |
| **Similar order** | Specialization tốt hơn nhưng forgetting nặng khi domain shift xa |
| **RoBERTa** | Không thể hiện catastrophic forgetting — ngược với decoder-only |

### 2.4 Discussion / Conclusions
- Insights từ small-scale CL **không generalize** cho LLMs — cần benchmark mới như bài này.
- Llama2-7B cần domain >100MB mới benefit từ continual pretraining.
- Để retain general knowledge → random order; để specialize → similar order.
- Hạn chế: Không replay, không regularization; chỉ đo perplexity (không phải NLG generation quality toàn diện).

---

## 3. Methodology Deep-Dive

### Framework
Tiếp cận **domain-incremental continual learning** không có replay hay regularization. Model M₀ pre-trained trên data rộng (D₀), sau đó được update lần lượt qua N domain: D₁ → D₂ → ... → D_N. Objective là next-token prediction thông thường.

### Core Ideas
1. **Scale benchmark:** 159 domain thay vì 4–10 domain như trước đây; 6.6B tokens; các model từ 117M đến 7B.
2. **Domain ordering effect:** Similar-order tăng specialization nhưng gây severe forgetting khi switch macro-topic; Random-order cân bằng hơn.
3. **Prediction rank metric:** Thay vì chỉ dùng perplexity, đề xuất rank-based analysis dùng Sentence-BERT clustering và n-gram extraction để đo knowledge accumulation không bị ảnh hưởng bởi prompt sensitivity.

### Data / Setup / Metrics
- **Data:** M2D2 (159 domains, 6.6B tokens). Wikipedia (culture, history, tech...) + S2ORC (physics, CS, math...).
- **Optimizer:** Adam, batch=16, NVIDIA A100; DeepSpeed auto config (dropout=0.2, auto LR).
- **Metrics:** Perplexity (primary), BIG-Bench downstream (normalized aggregate score), Prediction rank.

### 3 Key Assumptions
1. **Assumption 1: Perplexity đại diện đủ cho knowledge** — Bài dùng perplexity làm primary metric, nhưng LLMs hiện đại được đánh giá chủ yếu bằng instruction-following và reasoning tasks. Perplexity có thể không phản ánh khả năng real-world.
2. **Assumption 2: M2D2 domains đủ diverse** — M2D2 chủ yếu là Wikipedia và academic papers (S2ORC); thiếu code, multilingual, legal/medical text → có thể không representative cho real deployment.
3. **Assumption 3: Không replay = thực tế** — Thực tế nhiều continual learning systems dùng ít replay; nhưng không có even 1% replay là cực đoan và có thể inflate forgetting metrics.

---

## 4. Brutally Honest Review

### Summary
Benchmark paper chắc chắn với scale lớn và nhiều finding hữu ích. Tuy nhiên mang tính descriptive hơn prescriptive — bài mô tả thực trạng nhưng ít hướng dẫn thực hành.

### Strengths
- ✅ **Scale thực sự lớn:** 159 domain, 12,561 evaluations, 4 tháng training Llama2-7B.
- ✅ **Multi-architecture comparison:** GPT-2 (decoder) vs. RoBERTa (encoder) → phát hiện RoBERTa không bị forgetting là insight mới.
- ✅ **Prediction rank metric** là đóng góp methodological đáng giá, tránh được prompt sensitivity của perplexity-based evaluation.
- ✅ **Llama2-7B failure** là cảnh báo quan trọng cho practitioners: không nên continual pretrain LLM lớn trên small domain corpus.
- ✅ **Domain ordering analysis** phong phú với similar vs. random order.

### Weaknesses
- ❌ **Không có giải pháp mới:** Bài hoàn toàn descriptive, không propose method nào để mitigate forgetting.
- ❌ **M2D2 không diverse:** Chủ yếu English Wikipedia + academic papers; thiếu code, multilingual, legal/medical.
- ❌ **Không replay baseline:** Không có comparison với even 1% replay → khó biết forgetting có thể giảm bao nhiêu với intervention đơn giản nhất.
- ❌ **BIG-Bench evaluation quá nhỏ:** 5 tasks, và một số tasks có score <0 → metrics noise cao.
- ❌ **AutoLR selection là heuristic:** Bài dùng DeepSpeed auto config cho LR — có thể không optimal, khó reproduce.

### Questions
1. Nếu dùng 1% replay (Llama's training data), Llama2-7B có recover không?
2. Tại sao RoBERTa không bị catastrophic forgetting? Có phải do masked LM objective hay bottleneck architecture?
3. Với LLM tiếng Việt (ví dụ Vistral-7B), domain size threshold sẽ là bao nhiêu so với 100MB của Llama2-7B?

### Recommendation
**Accept as Main Conference (ICLR/EMNLP) — Benchmark Track.** Bài có đóng góp rõ ràng về benchmark và phát hiện mới. Nhược điểm chính là thiếu prescriptive guidance.

---

## 5. Data & Metrics View Designer

### Tasks / Datasets / Baselines / Metrics
| | Details |
|---|---|
| **Dataset** | M2D2 (159 L2-domains, 6.6B tokens, Wikipedia + S2ORC) |
| **Models** | GPT2-S/M/L/XL, Llama2-7B, RoBERTa-base/large |
| **Baselines** | Zero-Shot (ZS), Domain Adaptive Pretraining (DAPT) |
| **Primary Metric** | Validation Perplexity (CPT, LC, FG, Backward/Forward Transfer) |
| **Secondary** | BIG-Bench Normalized Aggregate Score (5 tasks); Prediction Rank |

### 3 Chart Proposals
1. **Model Scale vs. Forgetting Bar Chart (Figure 3 replica):** So sánh ZS, DAPT, CPT, LC perplexity theo model size. Highlight Llama2-7B failure (LC > ZS) vs. GPT-2 success.
2. **Domain Order Trade-off (Backward Transfer Heatmap):** Heatmap N×N: row = checkpoint domain, col = evaluated domain. Color = perplexity improvement over ZS. Similar-order vs. Random-order side by side.
3. **Llama2-7B Domain Size Scatter (Figure 17 replica):** X = domain size (MB), Y = perplexity after DAPT normalized by ZS. Threshold line tại 100MB.

### Table Structure
| Model | Order | ZS PPL | DAPT PPL | CPT PPL | LC PPL | FG Score |
|---|---|---|---|---|---|---|
| GPT2-S | Random | ~35 | ~25 | ~22 | ~20 | Medium |
| GPT2-M | Random | ~28 | ~20 | ~18 | ~17 | Low-Med |
| GPT2-L | Random | ~23 | ~17 | ~15 | ~14 | Low |
| GPT2-XL | Random | ~20 | ~15 | ~13 | ~12 | Lowest |
| Llama2-7B | Random | **6.84** | ~8.5 | ~10 | **11.42** | **Degraded** |

*(Giá trị ước lượng từ Figure 3, cần đọc paper gốc để lấy số chính xác)*

### Raw Data Extraction
- **Llama2-7B:** ZS PPL = 6.84 → LC PPL = 11.42/11.54 (tệ hơn đáng kể).
- **Domain size threshold:** Llama2-7B cần domain >100MB để benefit từ DAPT/CPT.
- **Training time:** GPT2-S = 6 ngày, Llama2-7B = 4 tháng (1 run).
- **Total evaluations:** 12,561 perplexity evaluations.
- **RoBERTa:** Không exhibit forgetting, positive forward transfer most of the time.

---

## 6. Key Takeaways & Relevance to VN-Legal-AI / CDNCTQ

| Takeaway | Relevance |
|---|---|
| Llama2-7B cần domain >100MB để benefit | Corpus pháp lý tiếng Việt cần đủ lớn (>100MB compressed) trước khi continual pretrain LLM 7B |
| Random order tốt hơn similar order để retain general knowledge | Nếu pretrain trên nhiều loại văn bản pháp lý, hãy shuffle thứ tự thay vì group theo loại |
| Continual pretraining > standalone DAPT | Nên continual pretrain liên tục thay vì fine-tune từng batch độc lập |
| Perplexity improvement ≠ downstream task improvement | Luôn đánh giá trên downstream legal QA tasks, không chỉ dựa perplexity |
| RoBERTa không bị forgetting | Encoder-based models (PhoBERT) có thể là lựa chọn an toàn hơn cho legal NER/classification |
