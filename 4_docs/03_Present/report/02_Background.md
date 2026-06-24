# 02 — Background (Survey arc → Evidence → 4 Gaps)

## Slide 1 — The survey arc (2024 → 2026)
- 5 landmark surveys chart the field: stage-aligned (Wu) → dual-axis (Shi) → internal/external (Zheng) → cross-modality (Guo) → lifelong **agents** (2026).
- Consensus: lifecycle **CPT → CIT → CA**; stability–plasticity dilemma; shift **parametric → external (RAG)**.

💬 Kể: "Lĩnh vực tiến hoá dần về **bộ nhớ ngoài** — đúng hướng đề tài."
📌 `wu2024...`, `shi2024...`, `zheng2024lifelong...`, `guo2025...`, `11328884`

## Slide 2 — Catastrophic forgetting is a *law*, not a bug
- **Inverse Linear Law**: học mới càng sâu ⇒ quên cũ càng nhiều; giảm rank/early-stop **không cứu**.
- Hình học: OOD tuning → **sharp minima** → quên; SAM → flat minima.
- Decoder-only + SFT-buffer kháng quên tốt hơn.

💬 Kể: "Mọi tinh chỉnh trọng số đều quên theo *tỷ lệ* — nên ta đóng băng base."
📌 `kalajdzievski2024...`, `li2024revisiting...`, `luo2025empirical...`

## Slide 3 — Cheap stabilizers + benchmark evidence
- **Replay 0.25–1%** đủ giữ zero-shot (rẻ).
- **O-LoRA giảm Forgetting Gradient ~40×** so LoRA thường (FG ~0.77 vs ~42).
- Benchmark (TRACE/ConTinTin): safety bền, **reasoning dễ quên nhất**.

📌 `scialom-etal-2022-fine`; FG% từ `repec:axf:aidtaa:v:3:y:2026:i:1:p:52-61`; `wang2023trace...`, `yin-etal-2022-contintin`

## Slide 4 — CPT mechanics & scaling laws
- **Stability gap** (do optimizer) → cần **re-warming** LR.
- Replay theo shift: weak ~5% → **strong/đa ngôn ngữ 15–25%** (tiếng Việt = strong shift).
- **D-CPT Law**: tính được tỷ lệ trộn tối ưu; **Law domain r ≈ 0.64** (64% luật + 36% general), R²>0.97.

💬 Kể: "Nếu có pha pretrain luật, đã có công thức tính % trộn — nhưng đề tài chính vẫn frozen+RAG."
📌 `gupta2023...`, `ibrahim2024...`, `zheng-etal-2024-breaking`, `que2024dcpt...`

## Slide 5 — Semi-parametric & RAG memory/unlearning
- **ReGrad** (gradient bank, đối chiếu): Law 84.87%, **zero weight drift** — nhưng độ trễ cao.
- **LUFY**: quên 90% lượt ít quan trọng → **+17% retrieval** (cảm hứng importance + decay).
- **RAG-based unlearning** (Wang): **USR 99.8%, ROUGE-L 0.10, utility giữ 53.8%** vs gradient-ascent quên kém + hại utility.

💬 Kể: "Bằng chứng mạnh nhất cho hướng RAG: unlearn gần tuyệt đối **mà không hại** năng lực khác."
📌 `su2026retrievablegradients...`, `Sumida_2025`, `11207222`

## Slide 6 — Vietnamese legal resources & the reasoning gap
- **VLQA** (59k điều luật) = KB; **ViLegalNLI** (Qwen-2.5 90.72%); **LegalSLM**.
- Phát hiện then chốt: NLI/QA ~0.95 nhưng **syllogism chỉ ~0.5–0.6**; CPT giúp 4B nhưng **hại 1.7B**.

💬 Kể: "Truy xuất/phân loại tốt rồi, nhưng **lập luận tam đoạn luận còn yếu** — đây là khoảng trống."
📌 `nguyen2025vlqa...`, `duong2026vilegalnli...`, `le-etal-2025-overview`

## Slide 7 — Four research gaps (chốt chương → dẫn vào method)
1. **Low-resource VN** chưa được nghiên cứu CL đầy đủ.
2. **Controllable forgetting / right-to-be-forgotten** under-explored.
3. **Selective legal memory** (importance đặc thù luật) chưa ai làm.
4. Thiếu **benchmark legal-CL VN**.

💬 Kể: "4 khoảng trống này = 4 lý do tồn tại của đề tài."
