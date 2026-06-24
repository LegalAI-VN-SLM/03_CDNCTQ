# W2 — Workflow Offline: Dựng KB có metadata + Huấn luyện LoRA

> Hai nhánh song song chạy offline: (A) xây Legal KB phi tham số; (B) huấn luyện LoRA (IFT) cho hành vi suy luận. **Không CPT** — facts đi vào RAG, không nhồi vào trọng số.

---

## W2.1 Nhánh A — Pipeline dựng KB (non-parametric)

| Bước | Việc | Input → Output |
|------|------|----------------|
| A1 | Thu thập văn bản quy phạm + án lệ công khai | nguồn → corpus thô |
| A2 | Chuẩn hoá về **đơn vị tri thức** (điều/khoản/án lệ) | corpus → units $u_i$ |
| A3 | Gán **metadata**: $v_i$ (hiệu lực), $[t_{start},t_{end}]$, loại văn bản, $\rho_i$ (rủi ro), $acl_i$ | units → units+meta |
| A4 | Dựng **citation graph** → tính $c_i$ (centrality) | units → graph + $c_i$ |
| A5 | **Embedding** từng unit | units → $e_i$ |
| A6 | Index hoá → **Legal KB** | $(e_i,\text{meta}) →$ KB |

## W2.2 Nhánh B — Huấn luyện LoRA (parametric, IFT)

| Bước | Việc | Ghi chú |
|------|------|---------|
| B1 | Chuẩn bị dữ liệu **legal instruction** (QA/NLI/syllogism) | `Le2025_LegalSLM`, `Nguyen2025_VLQA`, `Duong2026_ViLegalNLI` |
| B2 | Trộn **replay** dữ liệu chat tổng quát (0.25–1%) chống quên | `Scialom-etal-2022` |
| B3 | Train **LoRA hạng cao** ($r{\ge}256$) trên base đóng băng | `Biderman2024_LoRALearnsLess` (IFT mới hợp LoRA) |
| B4 | (tuỳ chọn) cô lập đa-ngành bằng O-LoRA/N-LoRA | `Wang2023_OrthogonalSubspace`, `Yang2024_ParameterCollision` |
| B5 | Xuất **LoRA adapter** | gắn vào base lúc inference |

> Lưu ý bảo vệ: LoRA ở đây học **hành vi tác vụ**, KHÔNG dùng để nhớ fact (fact ở KB). Đây là lý do tránh được giới hạn dung lượng SVD (~2000) của LoRA trong CPT.

## W2.3 Sơ đồ pipeline offline (TikZ)

> Preamble: `\usepackage{tikz}` · `\usetikzlibrary{arrows.meta, positioning, shapes.geometric, fit, backgrounds}`

```latex
\begin{tikzpicture}[
  font=\small, node distance=0.8cm and 1.0cm,
  step/.style ={rectangle, rounded corners, draw, align=center,
                minimum height=0.8cm, text width=2.7cm},
  store/.style={cylinder, shape border rotate=90, aspect=0.22, draw,
                align=center, minimum height=1.0cm, minimum width=1.9cm},
  arr/.style  ={-{Stealth[length=2mm]}, thick},
  grp/.style  ={rectangle, rounded corners, draw, dashed, inner sep=6pt}
]
% Branch A
\node[step] (a1) {A1. Thu thập luật + án lệ};
\node[step, right=of a1] (a2) {A2. Chuẩn hoá đơn vị $u_i$};
\node[step, right=of a2] (a3) {A3. Gán metadata $(v,[t],\rho,acl)$};
\node[step, below=of a3] (a4) {A4. Citation graph $\to c_i$};
\node[step, left=of a4]  (a5) {A5. Embedding $e_i$};
\node[store, left=of a5] (kb) {Legal KB};

\draw[arr] (a1)--(a2); \draw[arr] (a2)--(a3); \draw[arr] (a3)--(a4);
\draw[arr] (a4)--(a5); \draw[arr] (a5)--(kb);

% Branch B
\node[step, below=1.4cm of kb] (b1) {B1. Legal instruction data};
\node[step, right=of b1] (b2) {B2. + Replay 0.25--1\%};
\node[step, right=of b2] (b3) {B3. Train LoRA ($r{\ge}256$), base frozen};
\node[step, right=of b3] (b5) {B5. LoRA adapter};

\draw[arr] (b1)--(b2); \draw[arr] (b2)--(b3); \draw[arr] (b3)--(b5);

\begin{scope}[on background layer]
  \node[grp, fit=(a1)(a2)(a3)(a4)(a5)(kb), label=above:{\footnotesize Nhánh A — Non-parametric KB}] {};
  \node[grp, fit=(b1)(b2)(b3)(b5), label=below:{\footnotesize Nhánh B — LoRA (behavior, IFT)}] {};
\end{scope}
\end{tikzpicture}
```

## W2.4 Đầu ra offline
- **Legal KB** (embedding + metadata + citation graph) — đầu vào cho W3/W4.
- **LoRA adapter** — gắn base lúc reasoning ở W3.
