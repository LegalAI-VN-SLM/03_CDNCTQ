# 2.7 Machine Unlearning in RAG — Planning & Reading Notes
↔ `2_chapters/2_Background/2_7_*.tex` (Group 5)

> **Trụ chính 2** của đề tài — nền cho method RAG Machine Unlearning. Group 5.
> Paper: Wang et al. 2026, *When Machine Unlearning Meets RAG* `11207222`.

---

## 0. Vai trò & mạch viết
- **Mạch 3 đoạn:** (1) vì sao cần unlearning ở miền luật (luật hết hiệu lực, sealed, right-to-be-forgotten) + vì sao model-level unlearning tệ → (2) cơ chế RAG-based (P+Q) không đổi tham số → (3) tiêu chí đánh giá + bằng chứng định lượng (bảng) → dẫn sang method.

## 1. Chiến lược Figure & Table
| ID | Loại | Nội dung | Nguồn |
|----|------|----------|-------|
| **F2.7-a** | TikZ | Khung P (retrieval suppress) + Q (output constraint) trên KB | Wang2026 |
| **T2.7-a** | Bảng | **RAG-based vs model-level unlearning** (ROUGE-L↓, USR↑, TPR↓, utility) — số thật | Group5 synth T4 |

---

## 2. Đọc sâu (template 7 câu)

### Wang et al. 2026, *When Machine Unlearning Meets RAG: Keep Secret or Forget Knowledge?* `11207222`
1. **Câu hỏi:** có thể "quên" tri thức nhạy cảm bằng RAG (không sửa trọng số) đạt hiệu quả tương đương/tốt hơn model-level unlearning không?
2. **Chia nhóm:** so các phương pháp: Gradient Ascent (GA), In-context Unlearning, μ-Unlearning, **RAG-based**; trên 2 task (Concept, Sample).
3. **Method chính/phụ:** *chính* = mô hình hoá tri thức cần quên thành cặp **P** (thành phần truy xuất cần ẩn/loại) + **Q** (ràng buộc đầu ra); constrained optimization trên KB, không đổi tham số. *Phụ* = áp được cả model đóng (GPT-4o, Gemini).
4. **Benchmark:** Tiny-nq, Wikipedia topics, Harmful topics. Metric: ROUGE-L, USR, TPR@1%FPR (MIA), General Utility (ARC).
5. **Kết luận lớn nhất:** **RAG-based vượt trội**: ROUGE-L 0.10 (vs GA 61.30), **USR 99.8%** (vs GA 35.9%), TPR 1.3%, **utility được bảo toàn 53.8%** (các phương pháp parametric làm degrade utility).
6. **Gap:** là **behavioral unlearning** — không xoá tri thức khỏi trọng số base; rò rỉ parametric vẫn cần đo (đề tài thừa nhận ở Ch3).
7. **Liên quan đề tài:** ⭐ nền trực tiếp cho method 3.3; 5 tiêu chí (effectiveness, universality, harmlessness, simplicity, robustness) dùng lại ở Ch4; áp cho 3 loại forget request pháp lý.

---

## 3. Tiêu chí đánh giá (dùng lại ở Ch4)
| Tiêu chí | Ý nghĩa | Chỉ số |
|---|---|---|
| Effectiveness | có thật sự quên? | USR↑, ROUGE-L↓ |
| Universality | quên qua mọi cách diễn đạt | paraphrase test |
| Harmlessness | không hại truy vấn khác | utility (ARC) giữ |
| Robustness | chống jailbreak/MIA | TPR@1%FPR↓ |
| Simplicity | chi phí thấp, không train | thời gian |

## 4. Figure (TikZ)
### F2.7-a — Khung P + Q
```latex
\begin{tikzpicture}[
  font=\footnotesize, node distance=0.7cm and 1.1cm,
  b/.style={rectangle, rounded corners, draw, align=center, text width=2.8cm, minimum height=0.7cm},
  st/.style={cylinder, shape border rotate=90, aspect=0.2, draw, align=center, minimum height=0.9cm, minimum width=1.7cm},
  arr/.style={-{Stealth[length=2mm]}}
]
\node[b] (req) {Forget request};
\node[st, right=of req] (kb) {Legal KB};
\node[b, below=0.9cm of kb] (p) {\textbf{P}: suppress / hide / gate entries};
\node[b, right=of kb] (q) {\textbf{Q}: output constraint (no cite / no reveal)};
\node[b, below=0.9cm of q] (ans) {Answer (no forgotten grounding)};
\draw[arr](req)--(kb); \draw[arr](kb)--(p); \draw[arr](kb)--(q);
\draw[arr](p)--(ans); \draw[arr](q)--(ans);
\end{tikzpicture}
```

## 5. Table (số liệu thật)
### T2.7-a — RAG-based vs model-level unlearning (Wang 2026, Llama-2-7b-chat)
| Task | Method | ROUGE-L↓ | USR↑ | TPR@1%FPR↓ | Utility ARC |
|---|---|:--:|:--:|:--:|:--:|
| Concept | Gradient Ascent | 61.30 | 35.9% | 2.2% | 40.2% (degraded) |
| Concept | In-context | 75.60 | 13.8% | 2.4% | 52.4% |
| Concept | μ-Unlearning | 80.20 | 43.4% | 2.0% | 43.1% (degraded) |
| Concept | **RAG-based** | **0.10** | **99.8%** | **1.3%** | **53.8% (preserved)** |
| Sample | Gradient Ascent | 32.70 | 75.8% | 2.0% | 38.5% (degraded) |
| Sample | **RAG-based** | **0.00** | **100.0%** | **1.2%** | **53.8% (preserved)** |

> Đắt giá: RAG-based quên gần tuyệt đối (USR ~100%) **mà KHÔNG hại utility**, trong khi GA/μ-Unlearning làm tụt utility. Luận cứ mạnh cho hướng unlearning của đề tài.

## 6. Dàn ý viết
1. Nhu cầu unlearning pháp lý + model-level tệ (degrade utility, **T2.7-a**).
2. Cơ chế P+Q (**F2.7-a**), áp cả model đóng.
3. Tiêu chí 5 chiều + kết quả → dẫn sang method 3.3.
