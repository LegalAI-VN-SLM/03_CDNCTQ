# 2.6 Selective RAG & Long-term Memory — Planning & Reading Notes
↔ `2_chapters/2_Background/2_6_*.tex` (Group 5)

> **Trụ chính 1** của đề tài — nền lý thuyết cho method (Selective RAG Memory). Group 5.
> Paper: LUFY (psychological forgetting RAG) `[sumida2025... — key cần xác nhận trong references.bib]`; agent memory taxonomy `11328884`.

---

## 0. Vai trò & mạch viết
- **Mạch 3 đoạn:** (1) bộ nhớ ngoài trong lifelong agent (taxonomy 4 loại; RAG=Semantic) → (2) quên có chọn lọc theo tâm lý học nhận thức (LUFY: importance + decay) → (3) gap: chưa có importance đặc thù *pháp lý* → đóng góp đề tài.

## 1. Chiến lược Figure & Table
| ID | Loại | Nội dung | Nguồn |
|----|------|----------|-------|
| **F2.6-a** | TikZ | LUFY: importance scoring + consolidation/decay (đường cong quên) | LUFY |
| **T2.6-a** | Bảng | 6 importance metrics của LUFY → ánh xạ sang đơn vị pháp lý | LUFY + đề tài |

---

## 2. Đọc sâu (template 7 câu)

### 2.A — Sumida et al. 2025, *LUFY: Long-term RAG Chatbot with Psychological Forgetting* `[key cần xác nhận]`
1. **Câu hỏi:** chatbot RAG dài hạn có nên **quên** các lượt hội thoại ít quan trọng không?
2. **Chia nhóm:** 6 thước đo importance; cơ chế consolidation/decay theo đường cong quên người.
3. **Method chính/phụ:** *chính* = **importance-based selective forgetting** (quên ~90% lượt ít quan trọng). *Phụ* = user study UX.
4. **Benchmark:** CANDOR, LUFY-Dataset. Metric: human ratings, GPT-4o UX, precision.
5. **Kết luận lớn nhất:** quên 90% lượt ít quan trọng → **+17% độ chính xác truy xuất** + giảm tải bộ nhớ/context clutter.
6. **Gap:** làm cho **hội thoại** với importance generic; không có validity/citation/temporal của luật; không compliance constraint.
7. **Liên quan đề tài:** ⭐ ý lõi cho method — chuyển importance + decay từ hội thoại sang **đơn vị điều luật/án lệ**; thêm **legal importance (citation graph + hiệu lực + rủi ro)** + **compliance hard-gate**.

### 2.B — Zheng et al. 2026, Agent memory taxonomy `11328884`
1. **Câu hỏi:** bộ nhớ của lifelong agent gồm những loại nào?
2. **Chia nhóm:** Working / Episodic / **Semantic** / Parametric.
3. **Method/kết luận:** RAG = **semantic memory** phi tham số; là nơi đặt selective forgetting dựa hiệu lực + centrality.
4. **Liên quan đề tài:** định vị RAG memory trong khung P-M-A (đã dùng ở 2.1); nền khái niệm cho 2.6.

---

## 3. Khoảng trống → đề tài
- Chưa có **thước đo importance cho đơn vị pháp lý** (vs hội thoại) → câu hỏi nghiên cứu CQ1.
- Cân bằng **giảm index vs giữ compliance** (không vô tình quên điều luật còn hiệu lực) → **compliance hard-gate** (đóng góp).

## 4. Figure (TikZ)
### F2.6-a — Importance scoring + decay
```latex
\begin{tikzpicture}[
  font=\footnotesize, node distance=0.6cm and 1.0cm,
  b/.style={rectangle, rounded corners, draw, align=center, text width=2.7cm, minimum height=0.7cm},
  arr/.style={-{Stealth[length=2mm]}}
]
\node[b] (u) {Legal unit $u_i$};
\node[b, right=of u] (imp) {Importance $I_i$\\ (citation, validity, risk, freq)};
\node[b, right=of imp] (dec) {Consolidation / decay $g(H_i,t)$};
\node[b, below=0.9cm of dec] (act) {Active (top-$k$)};
\node[b, left=of act] (arc) {Archive (down-rank, not deleted)};
\draw[arr](u)--(imp); \draw[arr](imp)--(dec);
\draw[arr](dec)--(act); \draw[arr](dec)--(arc);
\end{tikzpicture}
```

## 5. Table
### T2.6-a — Importance metrics: LUFY → pháp lý
| LUFY (hội thoại) | Tương ứng pháp lý (đề tài) |
|---|---|
| recency / frequency | tần suất truy vấn điều luật |
| relevance | citation centrality (đồ thị tham chiếu) |
| (mới) | hiệu lực $v_i$ (hard-gate) |
| (mới) | mức rủi ro / áp dụng $\rho_i$ |

## 6. Dàn ý viết
1. Taxonomy bộ nhớ agent (RAG=Semantic, `11328884`).
2. LUFY: importance + decay (**F2.6-a**), +17% retrieval khi quên 90% ít quan trọng.
3. Gap → legal importance + compliance gate (đóng góp, dẫn sang method Ch3).
