# 3.4 LoRA / Parameter-Efficient Behavioural Learning  ↔ `3_Methods_Models/3_4_LoRA_Behavioural_Adapters.tex` (MỚI)

> **Trụ method 3** — tầng **parametric** của khung hybrid. Cân lại Chương 3 (vốn nặng external memory). | Nguồn: `hu2021loralowrankadaptationlarge`, `biderman2024loralearnsforgets`, `wang-etal-2023-orthogonal` (O-LoRA), `yang2024parametercollisionhinderingcontinual` (N-LoRA), `han-etal-2025-slim` (SLIM).

---

## 3.4.0 Vai trò & nguyên tắc phân vai
> **RAG quyết định "DÙNG điều luật nào"; LoRA quyết định "LẬP LUẬN với điều luật đó thế nào".**
- LoRA học **behavior/policy**: đọc context truy xuất → lập luận theo định dạng pháp lý (tam đoạn luận: đại tiền đề/tiểu tiền đề/kết luận) → trích dẫn điều luật → tôn trọng ràng buộc Q (không viện dẫn entry đã quên).
- LoRA **KHÔNG** dùng để nhớ facts luật (facts ở KB) — vì giới hạn dung lượng (xem 3.4.2).

## 3.4.1 Cấu hình LoRA (đề xuất, sẽ tune)
| Tham số | Giá trị đề xuất | Lý do |
|---|---|---|
| Base | decoder-only **instruct** (Qwen/LLaMA VN) frozen | kháng quên + đệm SFT (`luo2025empirical...`) |
| Rank r | 256 | IFT hợp rank cao (`biderman2024...`) |
| α | 512 (α=2r) | scaling chuẩn |
| Target | all linear modules | bao phủ behavior |
| Replay | trộn ~0.5% corpus VN chung | giữ zero-shot (`scialom-etal-2022-fine`) |
| Optimizer | AdamW (tuỳ chọn **SAM** flat-minima) | giảm sharp-minima quên (`li2024revisiting...`) |

## 3.4.2 Vì sao LoRA cho behavior, KHÔNG cho facts (luận cứ bảo vệ)
- SVD của ΔW: **CPT cần rank ~2000** (LoRA r≤256 không đủ) nhưng **IFT chỉ cần ≤32** → LoRA ngang Full-FT ở IFT (`biderman2024loralearnsforgets`).
- ⇒ nhồi facts luật vào LoRA sẽ *thiếu dung lượng + gây quên*; để facts ở RAG là đúng vai.
- LoRA "learns less, forgets less" → là **regularizer** bảo tồn năng lực chung.

## 3.4.3 Dữ liệu instruction (legal IFT)
- Cặp instruction–response cho QA / NLI / **syllogism** (định dạng đại–tiểu tiền đề–kết luận), dựng từ phong cách VLQA/ViLegalNLI/LegalSLM.
- Định dạng prompt **tách bạch task** (tránh xung đột nhãn — `yin-etal-2022-contintin`).
- Bao gồm mẫu "đọc context RAG rồi trả lời" để LoRA học **policy dùng RAG** (không chỉ trả lời chay).

## 3.4.4 Học nhiều phân ngành luật tuần tự (tuỳ chọn)
Nếu train tuần tự (hình sự → dân sự → doanh nghiệp):
- **O-LoRA** (chiếu trực giao) hoặc **N-LoRA** (sparsity) chống parameter collision (`wang-etal-2023-orthogonal`, `yang2024...`).
- Hoặc **SLIM** (route query theo ngành tới expert) (`han-etal-2025-slim`).
- Lưu ý: O-LoRA nhạy siêu tham số (sụp đổ trên task logic) → tune cẩn thận.

## 3.4.5 Tương tác LoRA ↔ RAG (lúc inference)
```
context (top-k điều luật, đã selective + unlearn) ─┐
query q ──────────────────────────────────────────┼─► [Base LLM + LoRA] ─► lập luận pháp lý
ràng buộc Q (no forgotten grounding) ──────────────┘                         (syllogism + cite)
```

## 3.4.6 Figure (TikZ — dán vào .tex)
```latex
\begin{tikzpicture}[
  font=\footnotesize, node distance=0.7cm and 1.1cm,
  b/.style={rectangle, rounded corners, draw, align=center, text width=2.8cm, minimum height=0.8cm},
  frz/.style={rectangle, rounded corners, draw, align=center, text width=2.6cm, fill=blue!6},
  lora/.style={rectangle, rounded corners, draw, very thick, align=center, text width=2.6cm, fill=green!10},
  arr/.style={-{Stealth[length=2mm]}, thick}
]
\node[b] (ctx) {Retrieved context (selective + unlearned)};
\node[b, below=of ctx] (q) {Query $q$ + constraint $Q$};
\node[frz, right=of ctx] (base) {Frozen Base LLM};
\node[lora, below=of base] (lo) {LoRA adapter (behavior, IFT)};
\node[b, right=2.0cm of base] (out) {Legal reasoning (syllogism + citation)};
\draw[arr] (ctx) -- (base); \draw[arr] (q) -- (lo);
\draw[arr] (base) -- (out); \draw[arr] (lo) -- (out);
\end{tikzpicture}
```

## 3.4.7 Q&A phản biện
**Q: LoRA vẫn sửa tham số, sao nói "frozen base"?**
A: Base weights **đóng băng hoàn toàn**; LoRA là ma trận low-rank *thêm vào*, có thể tách rời/tắt; nó học *behavior* (cách lập luận), không nhớ facts → facts vẫn cập nhật/unlearn qua RAG mà không đụng LoRA.
**Q: Sao không chỉ prompt engineering cho rẻ?**
A: LegalSLM cho thấy lập luận tam đoạn luận khó (~0.5); LoRA học *định dạng + policy* lập luận pháp lý ổn định hơn prompt thuần, nhưng vẫn giữ base frozen.
**Q: LoRA có gây quên không?**
A: LoRA "learns less, forgets less" + replay 0.5% + (tuỳ chọn) SAM/O-LoRA → quên tối thiểu; facts không ở LoRA nên đổi luật không cần retrain LoRA.

---
> **Cross-ref:** cấu hình train chi tiết ở workflow `Chapter3/Workflows/W2_Offline_Indexing_and_LoRA.md`; vai trò trong pipeline ở `W1`/`W3`.
