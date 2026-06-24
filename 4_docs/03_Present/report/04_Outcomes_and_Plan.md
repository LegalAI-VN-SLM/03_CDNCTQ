# 04 — Expected Outcomes & Plan

## Slide 1 — Deliverables ↔ acceptance thresholds (đo được)
- **Obj 1 — Selective memory module:** active index **giảm ≥30%** vs static RAG; **Recall@10 trong ±2%** so full-index.
- **Obj 2 — Unlearning gate (P+Q):** **ROUGE-L ≤0.15** trên forget set; **harmlessness ≥95%** baseline; **TPR ≤1% @1%FPR** dưới jailbreak.
- **Obj 3 — Evaluation suite:** chạy được trên **VLQA / ViLegalNLI / LegalSLM**, instantiate trên testbed **lao động (primary) + thuế (secondary)**; thêm eval **cross-sector tuần tự lao động→thuế** đo Backward Transfer (target near-zero factual forgetting vs parametric baseline).

💬 Kể: "Mỗi mục tiêu gắn ngưỡng số → nghiệm thu khách quan."
📌 `nguyen2025vlqa...`, `duong2026vilegalnli...`, `le-etal-2025-overview`

## Slide 2 — Academic & open-source outputs
- 1 hội nghị (PACLIC/KSE/VLSP) + 1 tạp chí ISI/Scopus.
- Hỗ trợ 1 luận văn ThS + 2 đồ án ĐH.
- GitHub: code + eval scripts + metadata-augmented index (tái lập được).

## Slide 3 — Beneficiaries (Who)
- **Cộng đồng NLP/AI pháp lý:** benchmark + metric chuẩn để so các retrieval/memory paradigm.
- **Lập trình hệ thống pháp lý:** tái dùng selective memory + unlearning gate (giảm chi phí đảm bảo compliance).
- **Cơ quan tuân thủ/bảo mật:** ngân hàng, toà án, công ty luật — đảm bảo không rò rỉ/không viện dẫn thông tin mật hoặc luật hết hiệu lực.

## Slide 4 — Work packages & timeline (6 tháng)
- **WP1 Selective RAG memory** (crawl/parse/index, citation graph, importance + decay) — crawl **scoped 2 ngành**: lao động full + thuế slice → khả thi trong 6 tháng.
- **WP2 Unlearning gate** (P+Q, compliance filter, audit log).
- **WP3 Benchmark & validation** (static-RAG baseline, ablation, VLQA/ViLegalNLI/LegalSLM).
- 8 milestone theo tuần (corpus W3 → KB W5 → baseline W9 → modules W12 → tuning W15 → unlearning W18 → benchmark W21 → report W24).

💬 Kể: dependency WP1→WP3, WP2→WP3.

## Slide 5 — Budget & risks
- Tổng ~**1,25 tỷ VND (~$50k)**: nhân sự / compute (A100 thuê + workstation) / dữ liệu+expert validation / công bố.
- **Rủi ro chính:** metadata sai (→ double-pass verify 5%); retrieval recall (→ hybrid dense+BM25); compute (→ pre-compute embedding, Bayesian search); access-control leakage (→ automated audit).

💬 Kể: nhấn rủi ro metadata = rủi ro số 1, đã có biện pháp.

## Slide 6 — Closing (3 câu hỏi proposal phải trả lời)
- **Why needed:** luật biến động + phải "quên có chủ đích"; parametric → quên thảm khốc / khó unlearn.
- **Why now:** data VN mới (2025–2026) + quy định siết → nền thực nghiệm + nhu cầu hội tụ.
- **Why me:** đã làm **khóa luận về RAG** (miền thông tin trường ĐH) → nắm nền tảng, nay chuyển/mở rộng sang **miền luật** (khó hơn), dưới hướng dẫn GV chuyên NLP, dùng dataset VN công khai.
- **Value:** zero-weight-drift + compliance-aware forgetting + reproducible protocol — novel về KH và có giá trị thực tiễn cho legal-AI VN.

💬 Kể: slide chốt — đọc 4 gạch này là hội đồng nắm trọn giá trị + thấy bạn đủ năng lực.
🔖 Liêm chính: AI chỉ hỗ trợ tìm/cấu trúc/ngôn ngữ; ý tưởng + framework + phân tích là của tác giả (xem Declaration).
