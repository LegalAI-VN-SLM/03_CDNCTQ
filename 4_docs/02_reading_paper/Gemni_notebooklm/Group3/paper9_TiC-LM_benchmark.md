# TiC-LM: A Web-Scale Benchmark for Time-Continual LLM Pretraining

**ArXiv:** 2504.02107  
**Authors:** Jeffrey Li, Mohammadreza Armandpour, Iman Mirzadeh, Sachin Mehta, et al. (UW + Apple)  
**Venue:** Preprint 2025 (Apple Research)  
**Source File:** `3_ 2504.02107 _TiC-LM A Web-Scale Benchmark for Time-Continual LLM Pretraining.pdf`

---

## 1. High-Level Summary (5–7 câu)

Bài báo giải quyết một vấn đề thực tế quan trọng: **LLMs được train trên web data lịch sử sẽ bị outdated khi thông tin mới xuất hiện liên tục**. Để nghiên cứu cách update LLMs theo thời gian một cách hiệu quả, nhóm tác giả Apple giới thiệu **TiC-LM** (Time-Continual Learning of LMs) — benchmark web-scale đầu tiên sử dụng **114 monthly dumps của Common Crawl (5/2013 – 7/2024, 2.9 trillion tokens)**. Benchmark đi kèm với evaluations time-stratified cho general web data (TiC-CC), Wikipedia (TiC-WIKI), StackExchange (TiC-STACKE), và code documentation (TiC-CODEDOCS, theo dõi version changes của NumPy và PyTorch). Kết quả chính: **Continual pretraining với replay (αt=1/2) + Autoregressive meta-schedule đạt comparable performance với Oracle retraining từ đầu, nhưng chỉ tốn 2.6× ít compute hơn**. Phát hiện quan trọng: replay là crucial cho general web data nhưng có thể hại cho rapidly evolving domains (StackOverflow, PyTorch) vì outdated information interferes.

---

## 2. Section Summaries

### 2.1 Introduction
- LLMs bị knowledge cutoff, deteriorate trên newer data.
- Retraining từ đầu rất tốn kém; continual pretraining là giải pháp nhưng thiếu benchmark thực tế.
- TiC-LM = benchmark với 114 timesteps, 100× tokens và 10× timesteps so với prior benchmarks.
- Câu hỏi chính: (1) Continual PT có match retraining không? (2) Forgetting có nghiêm trọng không? (3) Impact có khác nhau theo domain không?

### 2.2 Dataset: TiC-CC (2.9T tokens)
- **114 Common Crawl dumps**, mỗi dump = 1 month, 5/2013 – 7/2024.
- Pre-processing theo DataComp-LM pipeline; causality preserved (no future info trong older dumps).
- **Split:** 110B tokens cho initial pre-training (5/2013); remaining tokens chia đều cho 113 continual timesteps.

**Evaluation sets:**
1. **TiC-CC:** Held-out general web data theo từng month.
2. **TiC-WIKI:** Wikipedia dumps 2014-2024. Tập trung proper nouns → factual knowledge.
3. **TiC-STACKE:** StackExchange QA across topics (Math, StackOverflow, etc.)
4. **TiC-CODEDOCS:** NumPy (16 releases, 2017-2024) + PyTorch (11 releases, 2021-2024) documentation.

### 2.3 Key Findings

**Finding 1: 2.6× compute savings vs. retraining**
- Replay (αt=1/2) + AR meta-schedule: comparable held-out loss với Oracle retraining.
- Oracle = series of models retrained from scratch every 2 years.
- Continual PT có 62% less compute vs Oracle series.

**Finding 2: Forgetting prevalence on general web (TiC-CC)**
- Without replay: older CC dumps bị forgotten significantly.
- Replay essential: αt = 1/2 (replay 50% old data) → minimal forgetting.
- Too much replay (αt = 1/t): performance drops on recent data as timesteps increase.

**Finding 3: Domain-dependent replay impact (Figure 4)**
| Domain | Replay helps? | Reason |
|---|---|---|
| Math | ✅ Yes | Stable domain; old data still valid |
| NumPy docs | ✅ Yes | Documentation stable across versions |
| PyTorch docs | ❌ No (can harm) | Rapidly evolving; outdated API info confuses |
| StackOverflow | ❌ No | Rapidly evolving; old answers outdated |

**Finding 4: Factual knowledge surprisingly resilient without replay**
- TiC-WIKI: models without replay perform well on backward transfer!
- Reason: persistent facts re-appear trong newer CC dumps → models naturally refresh.
- Performance on TiC-WIKI often peaks **years after** the corresponding CC dump was seen.

### 2.4 Methods Evaluated
- **Cyclic Cosine:** Restart cosine schedule periodically → strong in-distribution, high forgetting.
- **Autoregressive (AR) meta-schedule:** Gradually increase LR over time.
- **Replay (αt = constant):** Fixed fraction of old data. Best: αt = 1/2.
- **Replay (αt = 1/t):** Decreasing old data fraction. Scales poorly with many timesteps.

---

## 3. Methodology Deep-Dive

### Framework
**Time-continual pretraining benchmark** với realistic setup: sequential monthly CC dumps, không có access to future data, measured over decade-long span.

### Core Ideas
1. **Autoregressive meta-schedule:** LR tăng dần theo timestamp → model "biết" đang học data mới, optimize for recent performance.
2. **Fixed-ratio replay (αt=1/2):** 50% tokens từ random old dumps, 50% từ current month → balance stability/plasticity.
3. **Domain-dependence của forgetting:** Key insight mới — không phải mọi forgetting đều hại. Với rapidly evolving domains, forgetting outdated knowledge là TỐT.

### Data / Setup / Metrics
- **Models:** 1B và 3B parameters (OpenLM framework).
- **Scale:** 220B và 440B tokens (4× và 8× Chinchilla optimal).
- **Hyperparameter selection:** Chỉ dùng 10 timesteps đầu để tune → realistic setup.
- **Metrics:** Held-out perplexity (ppltoken, pplnoun); Regret matrix (ID vs. Backward transfer tradeoff).

### 3 Key Assumptions
1. **Assumption 1: Common Crawl đại diện cho general web knowledge** — CC có nhiều noise và domain bias. Kết quả có thể không generalize sang curated domain-specific corpora.
2. **Assumption 2: Monthly timestep là granularity tốt** — Bài dùng 1 month/timestep. Với legal domain (các văn bản pháp luật không ra theo lịch tháng), granularity có thể cần khác.
3. **Assumption 3: Forgetting older data = forgetting older knowledge** — Với factual domains, đây không đúng (TiC-WIKI finding). Với legal domain, cũ chưa chắc sai (luật cũ vẫn có hiệu lực).

---

## 4. Brutally Honest Review

### Summary
Bài benchmark quan trọng và timely. Scope rất lớn (150+ experiments, 2.9T tokens). Kết quả nuanced và có practical impact. Hạn chế là chỉ English và general web — chưa verify cho specialized domains.

### Strengths
- ✅ **Scale lớn nhất trong lĩnh vực:** 2.9T tokens, 114 timesteps, 150+ experiments.
- ✅ **4 evaluation domains:** CC, Wikipedia, StackExchange, CodeDocs → diverse coverage.
- ✅ **2.6× compute savings:** Kết quả rất tangible cho practitioners.
- ✅ **Domain-dependent replay finding:** Insight mới và counter-intuitive (replay có thể hại).
- ✅ **Apple Research quality:** Well-designed, rigorous, open benchmark.

### Weaknesses
- ❌ **Chỉ English, chỉ Common Crawl:** Không test tiếng Việt hay specialized legal/medical domains.
- ❌ **Không test instruction-tuned models:** Chỉ pre-training; không biết liệu knowledge updates có transfer sang fine-tuned models không.
- ❌ **Replay αt=1/2 là heuristic:** Không có principled justification cho 50% replay; chỉ empirically best.
- ❌ **Large scale = hard to replicate:** 150 experiments tại 3B scale = expensive; không accessible cho researchers không có Apple compute.
- ❌ **Thiếu ablation về replay content:** Dùng random old data. Curated/importance-weighted replay có thể tốt hơn nhiều.

### Questions
1. Với Vietnamese legal domain (documents có temporal changes như luật sửa đổi), replay strategy nào là optimal?
2. Factual knowledge trong legal domain có "persistent" như Wikipedia không, hay outdated luật có hại cho model?
3. Với much smaller scale (7B model, 10B tokens), findings có còn hold không?

### Recommendation
**Accept (NeurIPS/ICML Datasets & Benchmarks track).** Benchmark contribution rõ ràng, scale lớn, findings nuanced. Limitation về diversity domain là issue nhưng acceptable cho scope của paper.

---

## 5. Data & Metrics View Designer

### Tasks / Datasets / Baselines / Metrics
| | Details |
|---|---|
| **TiC-CC** | 2.9T tokens, 114 CC monthly dumps, 5/2013 – 7/2024 |
| **TiC-WIKI** | Wikipedia dumps 2014-2024 (factual knowledge) |
| **TiC-STACKE** | StackExchange across topics (Math, StackOverflow) |
| **TiC-CODEDOCS** | NumPy (16 releases) + PyTorch (11 releases) |
| **Models** | 1B, 3B (OpenLM) |
| **Methods** | Cyclic Cosine, AR meta-schedule, Replay (αt=1/2, 1/t) |
| **Metrics** | ppltoken, pplnoun, Regret matrix (ID vs. Backward) |

### 3 Chart Proposals
1. **Regret Matrix (Figure 3 replica):** Heatmap NxN: rows = training month, cols = eval month. Color = performance (loss). Diagonal = in-distribution performance. Off-diagonal = backward transfer. Compare: Cyclic Cosine (diagonal bright) vs Replay (more uniform).
2. **Compute vs. Performance Frontier:** Scatter plot: X = compute (GPU-hours), Y = held-out loss. Points: Oracle retraining series, Continual PT variants. Annotate "2.6× savings" arrow.
3. **Domain-specific Replay Impact:** Bar chart: domains (Math, NumPy, PyTorch, StackOverflow). Y = performance change with replay vs. no replay. Shows Math/NumPy +positive, PyTorch/StackOverflow -negative.

### Table Structure — Key Numbers (Table 2)
| Method | TiC-CC held-out loss | Compute | Notes |
|---|---|---|---|
| Oracle retraining | ~Baseline | 2.6× (more) | Retrain from scratch |
| Cyclic Cosine + AR | Slightly worse | 1× | High ID, high forgetting |
| **Replay (αt=1/2) + AR** | **≈ Oracle** | **1×** | **Best balance** |
| Replay (αt=1/t) | Worse at many timesteps | 1× | Scales poorly |

### Raw Data Extraction
- **2.6× compute savings:** Continual PT (Replay 1/2 + AR, 440B tokens) ≈ Oracle series.
- **TiC-WIKI finding:** Non-replay methods competitive on backward transfer. Peak often years after corresponding CC dump.
- **Domain-dependent:** Math/NumPy → replay helps; StackOverflow/PyTorch → replay hurts.
- **Scale:** 1B vs 3B models: similar patterns but 3B more consistent.

---

## 6. Key Takeaways & Relevance to VN-Legal-AI / CDNCTQ

| Takeaway | Relevance |
|---|---|
| **2.6× compute savings vs retraining** | Continual PT của legal LLM khi có bộ luật mới hiệu quả hơn retraining từ đầu |
| **Replay αt=1/2 tốt cho general web** | Giữ 50% old legal documents trong mỗi update round |
| **Factual knowledge resilient without replay (TiC-WIKI)** | Các điều luật ổn định (Hiến pháp, BLDS) có thể không cần replay nhiều; mới ban hành (NĐ, TT) cần replay ít hơn |
| **Outdated legal info ≠ outdated factual info** | Luật cũ BỊ thay thế = outdated và CÓ HẠI nếu replay → cần selective replay cho legal domain |
| **Monthly timesteps → Legal update cycle** | Có thể design update cycle theo lịch ban hành văn bản (ví dụ quarterly update cycle) |
