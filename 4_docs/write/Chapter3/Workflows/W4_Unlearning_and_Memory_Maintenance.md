# W4 — Workflow Unlearning + Bảo trì bộ nhớ (decay/consolidation)

> Hai workflow vận hành liên tục trên KB: (1) **Unlearning** theo yêu cầu (P+Q); (2) **Memory maintenance** tự động (importance → decay → tier).

---

## W4.1 Workflow Unlearning (P + Q)

| Bước | Việc | Output |
|------|------|--------|
| U1 | Nhận **forget request** | mục tiêu cần quên |
| U2 | **Phân loại**: Temporal / Sealed / Personal | loại + cường độ |
| U3 | Áp **P** (retrieval): `delete` (personal) · `temporal_gate` (luật hết hiệu lực) · `acl_gate` (sealed) | KB policy cập nhật |
| U4 | Áp **Q** (đầu ra): ràng buộc không viện dẫn/không tiết lộ lớp mục tiêu | system prompt cập nhật |
| U5 | **Verify**: effectiveness (USR/ROUGE-L↓), harmlessness (retain set), robustness (TPR@1%FPR) | pass / fail |
| U6 | Nếu fail → mở rộng tập "entry liên quan" hoặc siết Q → lặp U3 | — |
| U7 | Ghi **audit log** (đảo ngược được, trừ hard-delete) | log |

### Sơ đồ Unlearning (TikZ)
> Preamble: `\usepackage{tikz}` · `\usetikzlibrary{arrows.meta, positioning, shapes.geometric}`
```latex
\begin{tikzpicture}[
  font=\small, node distance=0.8cm and 1.0cm,
  step/.style={rectangle, rounded corners, draw, align=center,
               minimum height=0.8cm, text width=2.7cm},
  dec/.style ={diamond, aspect=2.2, draw, align=center, inner sep=1pt,
               font=\footnotesize, text width=2.0cm},
  arr/.style ={-{Stealth[length=2mm]}, thick}
]
\node[step] (u1) {U1. Forget request};
\node[dec, right=of u1] (u2) {U2. Loại?};
\node[step, above right=0.6cm and 1.0cm of u2] (t) {Temporal\\ $\to$ temporal\_gate};
\node[step, right=1.0cm of u2] (s) {Sealed\\ $\to$ acl\_gate};
\node[step, below right=0.6cm and 1.0cm of u2] (p) {Personal\\ $\to$ delete};
\node[step, right=5.6cm of u2] (pq) {U3+U4. Áp P + Q};
\node[dec, below=1.0cm of pq] (v) {U5. Verify\\ pass?};
\node[step, left=of v] (fix) {U6. Mở rộng / siết Q};
\node[step, below=of v] (ok) {U7. Audit log (reversible)};

\draw[arr] (u1)--(u2);
\draw[arr] (u2)--(t); \draw[arr] (u2)--(s); \draw[arr] (u2)--(p);
\draw[arr] (t)--(pq); \draw[arr] (s)--(pq); \draw[arr] (p)--(pq);
\draw[arr] (pq)--(v);
\draw[arr] (v) -- node[above,font=\scriptsize]{no} (fix);
\draw[arr] (fix) |- (pq);
\draw[arr] (v) -- node[right,font=\scriptsize]{yes} (ok);
\end{tikzpicture}
```

## W4.2 Workflow Memory maintenance (vòng lặp định kỳ)

| Bước | Việc |
|------|------|
| M1 | Đọc **access log** (từ S9 của W3) |
| M2 | **Recompute importance** $I_i = w_1 c_i + w_2 v_i + w_3 \rho_i + w_4 g(H_i,t)$ |
| M3 | **Decay** $g(H_i,t)=\sum_{\tau} e^{-\lambda(t-\tau)}$ (consolidation khi truy vấn lặp) |
| M4 | **Tier migration**: $I_i \ge \theta \to$ Active; $< \theta \to$ Archive (down-rank, **không xoá**) |
| M5 | **Compliance guard**: $v_i{=}1 \Rightarrow$ luôn truy xuất được dù ở Archive |

### Sơ đồ vòng lặp maintenance (TikZ)
```latex
\begin{tikzpicture}[
  font=\small, node distance=1.0cm and 1.2cm,
  step/.style={rectangle, rounded corners, draw, align=center,
               minimum height=0.8cm, text width=2.8cm},
  guard/.style={rectangle, draw, dashed, align=center, text width=2.8cm, font=\footnotesize},
  arr/.style ={-{Stealth[length=2mm]}, thick}
]
\node[step] (m1) {M1. Access log};
\node[step, right=of m1] (m2) {M2. Recompute $I_i$};
\node[step, right=of m2] (m3) {M3. Decay $g(H_i,t)$};
\node[step, below=of m3] (m4) {M4. Tier migration\\ Active $\leftrightarrow$ Archive};
\node[guard, left=of m4] (m5) {M5. Compliance guard\\ ($v_i{=}1$ luôn truy xuất)};
\draw[arr] (m1)--(m2); \draw[arr] (m2)--(m3); \draw[arr] (m3)--(m4);
\draw[arr] (m4)--(m5); \draw[arr] (m5) |- (m1);
\end{tikzpicture}
```

## W4.3 Bất biến cần nhớ (đọc khi bị hỏi)
- **Decay không bao giờ xoá**; chỉ hard-delete khi Personal-data unlearning.
- **$v_i{=}1$ (còn hiệu lực) là bất biến cứng**: không bị decay loại, không bị quên nhầm.
- Mọi thao tác unlearn/maintenance **ghi log, đảo ngược được** (trừ hard-delete) → giải trình pháp lý.
