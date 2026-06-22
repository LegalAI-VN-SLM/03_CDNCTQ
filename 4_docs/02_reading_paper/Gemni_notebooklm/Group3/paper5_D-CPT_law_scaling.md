# D-CPT Law: Domain-specific Continual Pre-Training Scaling Law for Large Language Models

**ArXiv:** 2406.01375  
**Authors:** Haoran Que, Jiaheng Liu, Ge Zhang, et al. (Alibaba Group + Univ. Waterloo + HKUST)  
**Venue:** Preprint 2024  
**Source File:** `3_ 2406.01375 _D-CPT Law Domain-specific Continual Pre-Training Scaling Law for Large Language Models.pdf`

---

## 1. High-Level Summary (5–7 câu)

Bài báo đặt câu hỏi thực tiễn cấp bách: **khi continual pre-train LLM trên domain cụ thể (math, code, law, v.v.), tỷ lệ mix giữa domain-specific corpus và general corpus nên là bao nhiêu?** Hiện tại, cách tìm tỷ lệ tối ưu là grid search tốn kém. Bài đề xuất **D-CPT Law** — mở rộng Chinchilla Scaling Law bằng cách thêm biến mixture ratio `r` vào công thức — cho phép dự đoán validation loss cho bất kỳ combination nào của (N, D, r) mà không cần chạy tất cả thí nghiệm. Thí nghiệm trên 6 domains (Code, Math, Law, Chemistry, Music, Medical) với Qwen-1.5 (0.5B–4B) cho thấy fitting accuracy R² > 0.97. Ngoài ra, **Cross-Domain D-CPT Law** giới thiệu "Domain-specific Learnable Coefficient" (DLC) để predict cho unseen domains với chỉ 1% training cost thông thường. Đây là bài đầu tiên hệ thống hóa scaling law cho mixture ratio trong domain-specific CPT.

---

## 2. Section Summaries

### 2.1 Introduction
- CPT trên specific domain cần mix domain-corpus + general-corpus để balance giữa learning mới và tránh forgetting.
- Grid search tỷ lệ mix = tốn GPU và không đảm bảo tối ưu.
- Scaling Law (Chinchilla) đã thành công dự đoán optimal N và D → có thể mở rộng sang mixture ratio không?
- Đóng góp: D-CPT Law (in-domain) + Cross-Domain D-CPT Law (unseen domains).

### 2.2 Method: D-CPT Law

**Công thức L3 (được chọn):**
```
L(N, D, r) = E + A/N^α + B·r^η/D^β + C/(r+ε)^γ
```
Trong đó:
- N = số parameters; D = số tokens; r = domain-corpus ratio (0 đến 1)
- {E, A, B, C, α, β, γ, η, ε} là fitting parameters (9 parameters)
- Fit bằng L-BFGS algorithm

**4 yêu cầu thiết kế:**
1. **Adaptability:** Valid cho r ∈ [0, 1]
2. **Explicit trends:** ∂L/∂N < 0, ∂L/∂D < 0, ∂L/∂r < 0
3. **Implicit trends:** ∂²L/∂D∂r < 0 (domain data và training size có interaction)
4. **Consistency:** Khi r cố định, degenerates về Chinchilla Law

**Cross-Domain D-CPT Law:**
```
L(N, D, r, K) = E + A/N^α + B·r^η/D^β + C/(r+ε)^γ + F/K^μ
```
Trong đó K = w₁/k₁ + w₂×k₂ (k₁ = initial val loss, k₂ = rate of decline) — "Domain-specific Learnable Coefficient" (DLC).

### 2.3 Experiments & Results

**Setup:** Qwen-1.5 {0.5B, 1.8B, 4B}; 6 domains; 9 mixture ratios {0:10, 1:9, ..., 10:0}; 20K training steps; Validate every 1K steps.

**Fitting Accuracy (Table 1, L3):**
| | Huber Loss ↓ | R² ↑ |
|---|---|---|
| General domain | 0.0048 | 0.9967 |
| Domain-specific | 0.0157 | 0.9796 |

**Generalizability (3-fold cross-validation):**
| Generalization Type | Huber Loss (D) | R² (D) |
|---|---|---|
| Model size (unseen) | 0.0166 | 0.9516 |
| Dataset size (unseen) | 0.0096 | 0.9126 |
| Mixture ratio (unseen) | 0.0067 | 0.9717 |

**Usage 1 — Trade-off control:** Cho N=1.8B, D=10B, T=3% general degradation threshold → predicted optimal rd = **0.924** → Real optimal = 0.924. ✅ Match chính xác.

**Usage 2 — Limited domain data:** Cho music domain, Dd=5B, rd predicted = **0.732** → Real lowest Ld at rd=0.732 = 0.7309 vs predicted = 0.7328. ✅ Error = 0.0019.

**Unseen 7B model:** Fit với 0.5B+1.8B, predict cho 7B (unseen) → accurate predictions.

**Cross-domain:** Fit với 4 domains → predict 2 unseen domains → K3 representation hoạt động tốt.

### 2.4 Discussion / Conclusions
- D-CPT Law là công cụ quantitative đầu tiên cho domain-specific CPT mixture ratio optimization.
- Thay thế hoàn toàn grid search tốn kém bằng fitting từ small-scale experiments.
- Hạn chế: Chỉ test trên 6 domains English; chưa test cross-lingual; chỉ dùng Qwen-1.5.

---

## 3. Methodology Deep-Dive

### Framework
**Scaling law extension:** Xuất phát từ Chinchilla parametrization (L = E + A/N^α + B/D^β), bài thêm mixture ratio r như một biến độc lập mới. Thiết kế mathematical đảm bảo 4 properties lý thuyết.

### Core Ideas
1. **Mixture ratio là hyperparameter có thể predict:** Tương tự như optimal N và D có thể predict từ compute budget, optimal r có thể predict từ D-CPT Law.
2. **DLC K = fingerprint của domain learnability:** Chỉ cần 2 metrics đơn giản (initial val loss + rate of decline) để characterize domain mới → ~1% training cost.
3. **3 usage scenarios:** (i) Trade-off control với threshold; (ii) Optimal mix khi domain data bị limit; (iii) Resource allocation (consistent với Chinchilla).

### Data / Setup / Metrics
- **Domains:** Code, Math, Law, Chemistry, Music, Medical (English).
- **Models:** Qwen-1.5 {0.5B, 1.8B, 4B}; verified trên 7B (unseen size).
- **Ratios:** 9 mixture ratios (0:10, 1:9, ..., 10:0).
- **Metrics:** Validation loss (primary), Huber loss và R² (để đánh giá fitting quality).

### 3 Key Assumptions
1. **Assumption 1: Scaling law power-law relationship holds** — Giả định rằng loss theo N, D, r đều là power-law functions. Điều này không guaranteed, đặc biệt cho các domains có peculiar learning dynamics (ví dụ low-resource languages).
2. **Assumption 2: Optimal ratio là stationary** — Law dự đoán optimal r tại thời điểm fitting. Nếu domain data quality thay đổi hoặc có curriculum trong CPT, optimal r có thể drift.
3. **Assumption 3: 6 domains đủ để fit Cross-Domain Law** — Cross-Domain D-CPT Law fit trên 4 domains và predict 2 domains khác. Với chỉ 6 domains, overfitting risk cao. Không có validation trên truly out-of-distribution domains (ví dụ tiếng Việt).

---

## 4. Brutally Honest Review

### Summary
Bài rất độc đáo về cách tiếp cận — áp dụng scaling law methodology vào hyperparameter optimization cho mixture ratio. Kết quả thuyết phục nhưng còn nhiều limitations về diversity và generalizability.

### Strengths
- ✅ **Vấn đề thực tế quan trọng:** Mixture ratio optimization là pain point thực sự cho practitioners; grid search rất tốn kém.
- ✅ **Mathematical rigor:** 4 requirements cho parameterization được chứng minh rõ ràng; formula derivation transparent.
- ✅ **High fitting accuracy:** R² > 0.97 và Huber loss < 0.02 là impressive.
- ✅ **Cross-domain extension:** DLC = K chỉ cần 1% training cost để compute → practical breakthrough.
- ✅ **3 usage scenarios** được validate thực tế với số liệu cụ thể (rd=0.924, rd=0.732).

### Weaknesses
- ❌ **Chỉ 1 model family (Qwen-1.5):** Law chưa được verify trên LLaMA, Mistral, Gemma → không biết có model-agnostic không.
- ❌ **6 domains, tất cả English:** Rất ít diversity; không test cross-lingual hay low-resource settings.
- ❌ **Power-law assumption không được justify đủ:** Chỉ empirically verify, không có theoretical motivation cho tại sao mixture ratio theo power law.
- ❌ **Cross-domain với chỉ 4+2 domains:** Sample size quá nhỏ để claim generalizability → risk overfit.
- ❌ **Không test multi-stage CPT:** Chỉ test 1 round of CPT (D₀ → D₁). Multi-stage có thể invalidate law.

### Questions
1. D-CPT Law có hold cho tiếng Việt legal domain (rd_optimal là bao nhiêu cho Vietnamese legal + Vietnamese general)?
2. Nếu domain data quality thay đổi (ví dụ, dữ liệu pháp lý có noise cao), DLC K có còn stable không?
3. Cross-Domain Law fit từ 4 English domains có thể predict cho Vietnamese legal domain không?

### Recommendation
**Accept (EMNLP/ICLR) — Borderline.** Ý tưởng mới và practical, nhưng cần validation rộng hơn với nhiều model families và đa ngôn ngữ trước khi claim broad generalizability.

---

## 5. Data & Metrics View Designer

### Tasks / Datasets / Baselines / Metrics
| | Details |
|---|---|
| **Domains** | Code, Math, Law, Chemistry, Music, Medical |
| **Models** | Qwen-1.5 {0.5B, 1.8B, 4B}; verified on 7B |
| **Mixture Ratios** | 9 ratios: {0:10, 1:9, 2:8, 3.3:6.7, 5:5, 6.7:3.3, 8:2, 9:1, 10:0} |
| **Training** | 20K steps; validate every 1K steps |
| **Primary Metric** | Validation loss (Lg = general, Ld = domain) |
| **Fitting Quality** | Huber loss (↓), R² (↑) |

### 3 Chart Proposals
1. **Predicted vs. Real Loss Curves (Figure 1 replica):** 2×2 grid: rows = Lg và Ld; cols = N=0.5B và N=1.8B. X-axis = dataset size D (B tokens); Y-axis = loss. Multiple lines per model = different rd ratios (0.2, 0.4, 0.6, 0.8). Overlay = predicted curve vs. real data points.
2. **Trade-off Surface:** 3D surface (or heatmap): X = rd (0 to 1), Y = D (dataset size), Z = Ld. Color = constraint region (Lg < threshold T). Highlight optimal rd at different T thresholds.
3. **Cross-Domain Generalizability:** Bar chart: X = 6 domains, Y = Huber loss. 2 bars per domain: In-domain fit (blue) vs. Cross-domain predicted (orange). Shows DLC generalizability.

### Table Structure — Key Numbers
| Scenario | N | D | Predicted rd | Real Optimal rd | Predicted Ld | Real Ld |
|---|---|---|---|---|---|---|
| Usage 1 (Trade-off, Chemistry) | 1.8B | 10B | **0.924** | **0.924** | 1.7284 | 1.7291 |
| Usage 2 (Limited data, Music) | 1.8B | Dd=5B | **0.732** | **0.732** | 0.7328 | 0.7309 |

### Raw Data Extraction
- **Fitting accuracy L3:** General: Huber=0.0048, R²=0.9968; Domain: Huber=0.0157, R²=0.9796.
- **Model size generalizability (cross-val):** Huber 0.0049 (D=domain), R²=0.9711 (G).
- **Mixture ratio generalizability (cross-val):** Huber 0.0019 (G), R²=0.9964.
- **Usage 1:** rd=0.924 exact match; threshold T=3% general degradation.
- **Usage 2 (music, Dd=5B):** rd=0.732 real Ld=0.7309 (vs Ld=0.7486 at rd=0.2, Ld=0.7398 at rd=0.8).

---

## 6. Key Takeaways & Relevance to VN-Legal-AI / CDNCTQ

| Takeaway | Relevance |
|---|---|
| **D-CPT Law dự đoán optimal mixture ratio** | Có thể dùng để tìm tỷ lệ Vietnamese legal corpus vs. Vietnamese general corpus tối ưu mà không cần grid search |
| **Usage 1: Trade-off control với threshold T** | Ví dụ: "Tôi chấp nhận mất tối đa 5% general Vietnamese" → law tính ra rd_optimal |
| **Usage 2: Limited domain data** | Corpus pháp lý tiếng Việt thường nhỏ (<5B tokens) → optimal mixing ratio từ law, không cần full grid search |
| **Cross-Domain DLC với 1% cost** | Khi có domain mới (ví dụ hình sự → dân sự), chỉ cần ~1% training để characterize domain mới |
| **Cần validate trên Vietnamese data** | Law chưa được test ngoài English domains và Qwen-1.5. Cần verify tính general |
