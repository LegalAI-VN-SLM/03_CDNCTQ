# 2.8 Vietnamese Legal NLP Datasets & Benchmarks — Planning & Reading Notes
↔ `2_chapters/2_Background/2_8_*.tex` (Group 5)

> Nền dữ liệu/benchmark cho case study tiếng Việt — chốt phễu Ch2, nối sang đánh giá Ch4.
> Paper: VLQA `nguyen2025vlqacomprehensivelargehighquality`; ViLegalNLI `duong2026vilegalnlinaturallanguageinference`; LegalSLM `le-etal-2025-overview`; (D-CPT law domain `que2024dcptlawdomainspecificcontinual` — đã ở 2.3).

---

## 0. Vai trò & mạch viết
- **Mạch 4 đoạn:** (1) VLQA — KB QA pháp lý + phân bố topic → (2) ViLegalNLI — suy luận + chống lexical shortcut → (3) LegalSLM — leaderboard, "truy xuất tốt nhưng tam đoạn luận kém" → (4) khoảng trống dữ liệu (metadata hiệu lực, regime thời gian) → đề tài.

## 1. Chiến lược Figure & Table
| ID | Loại | Nội dung | Nguồn |
|----|------|----------|-------|
| **T2.8-a** | Bảng | **LegalSLM leaderboard** (Qwen-3-4B CPT 0.8010; syllo ~0.59) | Group5 synth T1 |
| **T2.8-b** | Bảng | **VLQA topic distribution** (Administrative 14.67%, 59k articles) | Group5 synth T2 |
| **T2.8-c** | Bảng | **ViLegalNLI model performance** (Qwen-2.5 90.72% test) | Group5 synth T3 |

---

## 2. Đọc sâu (template 7 câu)

### 2.A — Nguyen et al. 2025, *VLQA* `nguyen2025vlqacomprehensivelargehighquality`
1. **Câu hỏi:** thiếu bộ dữ liệu QA pháp lý tiếng Việt chất lượng cao có nhãn chuyên gia?
2. **Chia nhóm:** theo topic pháp lý (9 nhóm); retrieval + generation.
3. **Method chính/phụ:** *chính* = **dataset VLQA** (59k điều luật, 3.1k QA, gán nhãn chuyên gia). *Phụ* = đánh giá LLM hiện đại.
4. **Benchmark/Metric:** Recall@K, MRR@10, ROUGE, BLEU.
5. **Kết luận lớn nhất:** bộ QA pháp lý VN đầu tiên toàn diện; mật độ quy phạm cao nhất ở **Administrative System (14.67%)**.
6. **Gap:** chưa gắn metadata hiệu lực/thời gian đầy đủ.
7. **Liên quan đề tài:** ⭐ **KB truy xuất** cho Selective RAG (method 3.2); nguồn corpus chính.

### 2.B — Duong et al. 2026, *ViLegalNLI* `duong2026vilegalnlinaturallanguageinference`
1. **Câu hỏi:** đo năng lực **suy luận** pháp lý tiếng Việt mà không bị lexical shortcut?
2. **Chia nhóm:** NLI (entailment) trên premise luật; loại bỏ shortcut bằng PMI.
3. **Method chính/phụ:** *chính* = **dataset NLI** (42k cặp) + lọc PMI. *Phụ* = benchmark nhiều model.
4. **Benchmark/Metric:** Accuracy, F1.
5. **Kết luận lớn nhất:** bộ NLI luật VN lớn nhất; **Qwen-2.5 (72B) đạt 90.72% test**; encoder VN (CafeBERT 87.49%) cạnh tranh.
6. **Gap:** chỉ entailment; chưa reasoning đa bước.
7. **Liên quan đề tài:** đánh giá **lập luận** + bài học chống lexical bias (cho thiết kế test Ch4).

### 2.C — Le et al. 2025, *LegalSLM Shared Task* `le-etal-2025-overview`
1. **Câu hỏi:** SLM tiếng Việt suy luận pháp lý tới đâu (QA/NLI/Syllogism)?
2. **Chia nhóm:** 3 task; so team + baseline CPT.
3. **Method chính/phụ:** *chính* = **shared task + leaderboard**. *Phụ* = phân tích CPT giúp/hại theo size.
4. **Benchmark/Metric:** vi-law-nli (Acc), vi-law-qa (Acc), vilaw-syllo (Score), Average.
5. **Kết luận lớn nhất:** **~98% NLI/QA nhưng tam đoạn luận chỉ ~0.53–0.60**; CPT cải thiện 4B (0.769→0.801) **nhưng hại 1.7B** (0.6076→0.6050).
6. **Gap:** khoảng cách lớn giữa **truy xuất/phân loại** và **lập luận chủ động** (syllogism).
7. **Liên quan đề tài:** ⭐ động lực: selective RAG cải thiện *context*, nhưng reasoning bị chặn bởi base → chọn base đủ mạnh + nêu limitation; baseline so sánh ở Ch4.

---

## 3. Khoảng trống → đề tài
- SLM VN **thất bại ở tam đoạn luận** dù CPT → khoảng cách truy xuất ↔ lập luận chủ động.
- Chưa có corpus gắn **metadata hiệu lực/thời gian** đầy đủ → đề tài bổ sung (cho regime + compliance).
- CPT giúp/hại tuỳ size → cẩn trọng khi chọn mô hình.

## 4. Tables (số liệu thật)
### T2.8-a — LegalSLM leaderboard (rút gọn)
| Rank | Model | nli | qa | syllo | Avg |
|:--:|---|:--:|:--:|:--:|:--:|
| 1 | Bosch@AI | 0.970 | 0.927 | 0.536 | 0.811 |
| 2 | **Qwen-3-4B (CPT)** | 0.960 | 0.850 | **0.595** | 0.801 |
| 6 | Qwen-3-4B-Base | 0.970 | 0.821 | 0.516 | 0.769 |
| 8 | Qwen-3-1.7B-Base | 0.562 | 0.793 | 0.468 | 0.608 |
| 9 | Qwen-3-1.7B (CPT) | 0.645 | 0.787 | 0.384 | 0.605 |

> Đọc: syllogism là điểm yếu chung (~0.5); CPT giúp 4B nhưng hại 1.7B.

### T2.8-b — VLQA topic distribution (rút gọn)
| Topic | Articles | Q density |
|---|:--:|:--:|
| Administrative System | 8,566 | 14.67% |
| Business & Corporate | 3,816 | 7.30% |
| Commerce | 4,748 | 6.80% |
| Labor & Wages | 2,440 | 5.20% |
| Civil Rights | 2,218 | 5.00% |
| Criminal Liability | 2,197 | 4.50% |

### T2.8-c — ViLegalNLI model performance (rút gọn)
| Model | Size | Test Acc | Test F1 |
|---|:--:|:--:|:--:|
| PhoBERT-large | 370M | 84.98 | 84.78 |
| CafeBERT (VN) | 110M | 87.49 | 87.36 |
| InfoXLM-large | 550M | 87.98 | 87.85 |
| Qwen-2.5 (few-shot) | 72B | **90.72** | **90.64** |

## 5. Dàn ý viết
1. VLQA (KB + topic, **T2.8-b**) → nguồn retrieval.
2. ViLegalNLI (suy luận + PMI, **T2.8-c**).
3. LegalSLM (**T2.8-a**): truy xuất tốt, syllogism kém → động lực.
4. Khoảng trống dữ liệu (metadata hiệu lực) → đề tài bổ sung; chốt Ch2.
