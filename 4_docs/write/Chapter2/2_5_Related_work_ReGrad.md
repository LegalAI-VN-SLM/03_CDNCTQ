# 2.5 Retrievable Gradients & Dynamic Weight Editing (ReGrad) — Planning & Reading Notes
↔ `2_chapters/2_Background/2_5_*.tex` (Group 5)

> **Vai trò: ĐỐI CHIẾU** — đại diện họ bán-tham-số "gradient bank". KHÔNG phải method của đề tài (đề tài RAG-centric). Dùng để so sánh và biện minh lựa chọn RAG.
> Paper: `su2026retrievablegradientscontinualposttraining` (ReGrad/REGRAD).

---

## 0. Vai trò & mạch viết
- **Mạch 3 đoạn:** (1) vấn đề **weight drift** tích luỹ trong CPT/CFT tuần tự → (2) cơ chế ReGrad (Gradient Bank + truy xuất động lúc inference) → (3) so ReGrad vs RAG → vì sao đề tài chọn RAG (độ trễ thấp, dễ unlearn).

## 1. Chiến lược Figure & Table
| ID | Loại | Nội dung | Nguồn |
|----|------|----------|-------|
| **F2.5-a** | TikZ | Pipeline ReGrad: offline gradient bank → retrieve → temporary weight add → rollback | Su2026 |
| **T2.5-a** | Bảng | **REGRAD performance across domains** (Law 84.87, zero weight drift) | Group5 synth T5 |
| **T2.5-b** | Bảng | So sánh trục **Memory Persistence**: CPT (vĩnh viễn) / ReGrad (tạm) / RAG (phi tham số) | tổng hợp |

---

## 2. Đọc sâu (template 7 câu)

### Su et al. 2026, *Retrievable Gradients: Continual Post-Training Without Cumulative Weight Drift* `su2026retrievablegradientscontinualposttraining`
1. **Câu hỏi:** cập nhật tri thức tài liệu mới ở mức tham số mà **không tích luỹ weight drift** (không quên) được không?
2. **Chia nhóm:** so các paradigm: Direct, Standard CPT, Instruction CPT, RAG (ICL), Fine-tuned-RAG, REGRAD.
3. **Method chính/phụ:** *chính* = **Gradient Bank** — tiền tính gradient LoRA của từng tài liệu, lưu offline; lúc inference **truy xuất gradient liên quan, cộng tạm vào $W$ đóng băng rồi rollback**. *Phụ* = REGRAD+ICL.
4. **Benchmark:** LLaMA (1B–8B), General/Medical/Law. Metric: Accuracy/F1, cross-domain.
5. **Kết luận lớn nhất:** đạt chất lượng cập nhật cấp tham số **mà không drift**; **REGRAD+ICL: Law 84.87%, Overall 67.26%** — cao nhất, **zero weight drift** (so Standard CPT drift nặng, Overall chỉ 48.18%).
6. **Gap:** **độ trễ inference cao** (áp gradient động lúc chạy) → khó triển khai thời gian thực; hạ tầng gradient bank phức tạp.
7. **Liên quan đề tài:** ⭐ **điểm đối chiếu chính**: ReGrad mạnh nhưng **nặng + khó unlearn + độ trễ cao**. Đề tài chọn **RAG** vì đơn giản hơn, độ trễ thấp, **unlearn = gỡ entry** (xem 2.7). Đây là lý do "vì sao không ReGrad".

---

## 3. So sánh trục Memory Persistence (luận điểm bản lề)
| Trục | CPT/CFT | **ReGrad** | **RAG (đề tài)** |
|------|---------|-----------|------------------|
| Thay đổi trọng số | vĩnh viễn | **tạm thời (rollback)** | **không (phi tham số)** |
| Weight drift / quên | có (nặng) | không | không |
| Độ trễ inference | nền | **cao** | nền + tra cứu |
| Unlearn | rất khó | khó | **dễ (gỡ entry)** |
| Cập nhật luật | train lại | thêm gradient | **sửa KB tức thì** |

> Kết luận đọc khi bị hỏi: ReGrad và RAG cùng tránh weight drift; nhưng RAG **rẻ hơn, nhanh hơn, dễ kiểm soát/unlearn hơn** ⇒ hợp bài toán luật biến động + "quên có chủ đích".

## 4. Figure (TikZ)
### F2.5-a — Pipeline ReGrad (đối chiếu)
```latex
\begin{tikzpicture}[
  font=\footnotesize, node distance=0.7cm and 1.0cm,
  b/.style={rectangle, rounded corners, draw, align=center, text width=2.7cm, minimum height=0.7cm},
  st/.style={cylinder, shape border rotate=90, aspect=0.2, draw, align=center, minimum height=0.9cm, minimum width=1.8cm},
  arr/.style={-{Stealth[length=2mm]}}
]
\node[b] (doc) {New document};
\node[b, right=of doc] (grad) {Pre-compute LoRA gradient};
\node[st, right=of grad] (bank) {Gradient Bank};
\node[b, below=1.0cm of bank] (q) {Query};
\node[b, left=of q] (ret) {Retrieve relevant gradient};
\node[b, left=of ret] (add) {Temp add to frozen $W$ $\to$ rollback};
\draw[arr](doc)--(grad); \draw[arr](grad)--(bank);
\draw[arr](q)--(ret); \draw[arr](bank)--(ret); \draw[arr](ret)--(add);
\end{tikzpicture}
```

## 5. Table (số liệu thật)
### T2.5-a — REGRAD performance across domains (Su 2026)
| Method | General | Medical | **Law** | Overall | Weight drift? |
|---|:--:|:--:|:--:|:--:|:--:|
| Direct baseline | 33.42 | 62.95 | 50.15 | 50.31 | No |
| Standard CPT | 25.67 | 62.03 | 48.20 | 48.18 | **Yes (severe)** |
| RAG (ICL) | 26.98 | 59.49 | 53.23 | 47.98 | No |
| Fine-tuned-RAG | 37.17 | 66.84 | 70.33 | 61.34 | **Yes (cumulative)** |
| **REGRAD** | 38.43 | 65.61 | 80.50 | 64.36 | **No** |
| **REGRAD+ICL** | **45.06** | **67.74** | **84.87** | **67.26** | **No** |

> Lưu ý dùng: bảng này cho thấy CPT thường drift + tụt General (25.67); ReGrad tránh drift. Đề tài lấy bài học "tránh drift" nhưng đi đường RAG thay vì gradient bank.

## 6. Dàn ý viết
1. Vấn đề weight drift tích luỹ (Standard CPT tụt General → 25.67).
2. Cơ chế ReGrad (Gradient Bank, **F2.5-a**) + kết quả (**T2.5-a**, Law 84.87, zero drift).
3. So Memory Persistence (CPT/ReGrad/RAG) → chốt: đề tài chọn RAG (rẻ, nhanh, dễ unlearn).
