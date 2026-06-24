# W3 — Workflow Online: Từ truy vấn đến câu trả lời

> Luồng chạy lúc inference (base+LoRA cố định, KB cố định). Thứ tự khoá: **P (unlearn) → compliance gate → selective re-rank → reasoning + Q**.

---

## W3.1 Các bước (chi tiết, có input/output)

| Bước | Việc | Input → Output |
|------|------|----------------|
| S1 | Nhận query $q$ + suy ra **mốc thời gian** $t_q$ (mặc định "hiện hành") | $q$ → $(q, t_q)$ |
| S2 | **Retrieve** ứng viên theo ngữ nghĩa | $(q)$ → $C = \text{top-}M$ |
| S3 | **Unlearning gate (P)**: loại/ẩn entry theo forget-policy + $t_q$ | $C$ → $C'$ |
| S4 | **Compliance hard-gate**: tách $must = \{u: v_i{=}1, \text{khớp}(q,u)\}$ | $C'$ → $must, rest$ |
| S5 | **Selective re-rank** phần còn lại: $S = \mathrm{sim} + \beta I_i$ | $rest$ → $rest$ xếp hạng |
| S6 | Ghép **context** = $must \cup \text{top-}k(rest)$ | → context |
| S7 | **Reasoning** (base+LoRA) với ràng buộc $Q$ (không tiết lộ entry đã quên) | context → draft answer |
| S8 | **Compliance/privacy filter** + ghi **audit log** | draft → answer cuối |
| S9 | Cập nhật **access log** cho các unit đã dùng (phục vụ decay ở W4) | → log |

## W3.2 Xử lý regime theo thời gian (điểm tinh tế)
- $t_q$ = "hiện hành" → chỉ dùng unit có $v_i{=}1$ tại thời điểm hiện tại.
- $t_q$ = "tại 20XX" → dùng unit hợp lệ trong $[t_{start},t_{end}] \ni$ 20XX (kể cả luật nay đã hết hiệu lực) → **time-travel query**.

## W3.3 Sơ đồ luồng inference (TikZ, có nút quyết định)

> Preamble: `\usepackage{tikz}` · `\usetikzlibrary{arrows.meta, positioning, shapes.geometric}`

```latex
\begin{tikzpicture}[
  font=\small, node distance=0.75cm and 1.1cm,
  step/.style ={rectangle, rounded corners, draw, align=center,
                minimum height=0.8cm, text width=3.1cm},
  dec/.style  ={diamond, aspect=2, draw, align=center, inner sep=1pt,
                font=\footnotesize, text width=2.0cm},
  io/.style   ={trapezium, trapezium left angle=70, trapezium right angle=110,
                draw, align=center, minimum height=0.8cm},
  arr/.style  ={-{Stealth[length=2mm]}, thick}
]
\node[io]   (q)   {S1. Query $q$ (+ $t_q$)};
\node[step, below=of q]  (s2)  {S2. Retrieve top-$M$};
\node[step, below=of s2] (s3)  {S3. Unlearning gate (P)};
\node[dec,  below=of s3] (s4)  {S4. $v_i{=}1$ \& khớp?};
\node[step, below left=0.9cm and 0.2cm of s4] (must) {must-keep \\ (giữ bắt buộc)};
\node[step, below right=0.9cm and 0.2cm of s4] (rest) {S5. Re-rank $\mathrm{sim}+\beta I_i$};
\node[step, below=1.7cm of s4] (ctx) {S6. Ghép context top-$k$};
\node[step, below=of ctx] (rea) {S7. Reasoning (base+LoRA) + $Q$};
\node[step, below=of rea] (flt) {S8. Filter + Audit log};
\node[io,   below=of flt] (ans) {Câu trả lời};

\draw[arr] (q)--(s2); \draw[arr] (s2)--(s3); \draw[arr] (s3)--(s4);
\draw[arr] (s4) -- node[above left,font=\scriptsize]{có} (must);
\draw[arr] (s4) -- node[above right,font=\scriptsize]{khác} (rest);
\draw[arr] (must)--(ctx); \draw[arr] (rest)--(ctx);
\draw[arr] (ctx)--(rea); \draw[arr] (rea)--(flt); \draw[arr] (flt)--(ans);
\end{tikzpicture}
```

## W3.4 Pseudocode gọn (đối chiếu sơ đồ)
```
INFER(q):
    t_q   = infer_time(q)              # S1
    C     = retriever.top_M(q)         # S2
    C'    = unlearn_gate(C, q, t_q)    # S3 (P)
    must  = [u in C' if u.v==1 and match(u,q) and valid_at(u,t_q)]   # S4
    rest  = sort(C'\must, key = sim(q,u)+β*importance(u,t_q))        # S5
    ctx   = must + top_k(rest)         # S6
    ans   = LLM_base_lora(ctx, q, constraint=Q)                      # S7
    ans   = filter_and_log(ans)        # S8
    log_access(ctx)                    # S9
    return ans
```
