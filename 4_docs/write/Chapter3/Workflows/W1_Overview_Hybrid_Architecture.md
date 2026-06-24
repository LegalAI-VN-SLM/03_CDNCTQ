# W1 — Workflow tổng quan: Kiến trúc Hybrid (RAG + LoRA)

> Bám khung hybrid đã chốt ở `2_1_Surveys_CL.tex` (RAG phi tham số cho tri thức + LoRA tham số cho hành vi) và `2_3_PEFT.tex` (LoRA hợp cho IFT/behavior, KHÔNG dùng nhớ fact).
> Nguyên tắc phân vai: **Sự thật pháp lý (facts) → RAG KB** · **Kỹ năng/hành vi suy luận → LoRA** · **Base weights → đóng băng**.

---

## W1.1 Hai hệ bộ nhớ, hai vai trò

| Hệ | Thành phần | Chứa gì | Cập nhật/Quên |
|----|-----------|---------|----------------|
| **Non-parametric** | Legal KB + Retriever + Selective Memory + Unlearning gate | điều luật/án lệ + metadata (hiệu lực, citation, rủi ro) | sửa KB tức thì; unlearn = gỡ/ẩn entry |
| **Parametric** | Frozen Base LLM + **LoRA adapter** | kỹ năng đọc–hiểu–lập luận pháp lý (IFT) | huấn luyện LoRA offline; base không đổi |

> Lý do phân vai (đọc khi bị hỏi): facts thay đổi nhanh + cần unlearn ⇒ để ở RAG (rẻ, đảo ngược được); LoRA dung lượng thấp (`Biderman2024_LoRALearnsLess`) hợp học *hành vi* tác vụ chứ không hợp nhớ khối lượng fact lớn ⇒ đúng vai.

## W1.2 Bốn workflow của hệ thống (map sang các file W2–W4)

1. **Offline** — dựng KB có metadata + huấn luyện LoRA (IFT) → `W2`.
2. **Online inference** — query → retrieve → unlearn gate → selective + compliance → (base+LoRA) reasoning → answer → `W3`.
3. **Unlearning** — xử lý forget request (P+Q) → `W4`.
4. **Memory maintenance** — logging → importance → decay → tier migration → `W4`.

## W1.3 Sơ đồ kiến trúc tổng quan (TikZ)

> Yêu cầu preamble: `\usepackage{tikz}` · `\usetikzlibrary{arrows.meta, positioning, shapes.geometric, fit, backgrounds}`

```latex
\begin{tikzpicture}[
  font=\small,
  node distance=0.9cm and 1.3cm,
  box/.style   ={rectangle, rounded corners, draw, align=center,
                 minimum height=0.85cm, minimum width=2.6cm},
  store/.style ={cylinder, shape border rotate=90, aspect=0.22, draw,
                 align=center, minimum height=1.0cm, minimum width=2.0cm},
  io/.style    ={trapezium, trapezium left angle=70, trapezium right angle=110,
                 draw, align=center, minimum height=0.8cm},
  arr/.style   ={-{Stealth[length=2mm]}, thick},
  grp/.style   ={rectangle, rounded corners, draw, dashed, inner sep=6pt}
]
% ---- input ----
\node[io] (q) {Truy vấn $q$ \\ (+ mốc $t_q$ tuỳ chọn)};

% ---- non-parametric pipeline (RAG) ----
\node[store, below=of q]            (kb)   {Legal KB \\ (+ metadata)};
\node[box, right=of kb]             (ret)  {Retriever \\ $\mathrm{sim}(q,e_i)$};
\node[box, right=of ret]            (unl)  {Unlearning \\ gate (P)};
\node[box, right=of unl]            (sel)  {Selective Memory \\ + Compliance gate};
\node[box, right=of sel]            (ctx)  {Context \\ (top-$k$)};

% ---- parametric model ----
\node[box, above=1.3cm of ctx]      (lora) {LoRA adapter \\ (behavior, IFT)};
\node[box, left=of lora]            (base) {Frozen \\ Base LLM};

% ---- reasoning + output ----
\node[box, below=1.0cm of ctx]      (rea)  {Reasoning engine \\ (base+LoRA) + ràng buộc $Q$};
\node[io, below=of rea]             (ans)  {Câu trả lời};
\node[store, right=1.3cm of rea]    (log)  {Audit log};

% ---- edges ----
\draw[arr] (q) -- (ret);
\draw[arr] (kb) -- (ret);
\draw[arr] (ret) -- (unl);
\draw[arr] (unl) -- (sel);
\draw[arr] (sel) -- (ctx);
\draw[arr] (ctx) -- (rea);
\draw[arr] (base) -- (rea);
\draw[arr] (lora) -- (rea);
\draw[arr] (rea) -- (ans);
\draw[arr] (rea) -- (log);

% ---- group boxes ----
\begin{scope}[on background layer]
  \node[grp, fit=(kb)(ret)(unl)(sel)(ctx), label=above:{\footnotesize Non-parametric memory (RAG)}] {};
  \node[grp, fit=(base)(lora), label=above:{\footnotesize Parametric model (frozen base + LoRA)}] {};
\end{scope}
\end{tikzpicture}
```

## W1.4 Ghi chú thứ tự (quan trọng)
- **Unlearning gate (P) chạy TRƯỚC** selective re-rank: entry bị cấm không bao giờ vào candidate.
- **Compliance gate** nằm trong Selective Memory: giữ luật còn hiệu lực dù importance thấp.
- **Ràng buộc Q** áp ở bước reasoning (chống rò rỉ entry đã quên qua parametric).
