# 04 — Expected Outcomes & Plan

## Slide 1 — Kết quả mong đợi ↔ tiêu chí đo được
- **R1 — Đường forgetting (nạp liên tục):** CPT **sụp** ở mức nạp cao (BWT âm, như Bảng B ReGrad); **RAG không quên** (perf ổn/tăng). Vẽ trên cả 3 task.
- **R2 — Selective vs vanilla RAG:** kho phình → vanilla **tụt Recall@K**; selective giữ **±2%** + active index **↓≥30%**.
- **R3 — Δ cải thiện theo task:** **QA > NLI > Syllogism** → bằng chứng **trần lập luận** (retrieval không thay được suy luận). Đối chiếu **ReGrad** (bán-tham số).
- *(Phụ)* unlearning = temporal gating khi supersession.

💬 Kể: "Mỗi mục tiêu gắn ngưỡng số → nghiệm thu khách quan."
📌 `nguyen2025vlqa...`, `duong2026vilegalnli...`, `le-etal-2025-overview`

## Slide 2 — Academic & open-source outputs (student-scale)
- **Hoàn thành luận văn thạc sĩ** này (đây là output chính).
- (Phấn đấu) **1 báo cáo hội nghị/workshop trong nước** (VLSP/KSE) — không cam kết tạp chí ISI/Scopus trong 6 tháng.
- **GitHub**: code + eval scripts + index tái lập được → đóng góp tái lập cho legal-CL VN.

## Slide 3 — Beneficiaries (Who)
- **Cộng đồng NLP/AI pháp lý:** benchmark + metric chuẩn để so các retrieval/memory paradigm.
- **Lập trình hệ thống pháp lý:** tái dùng selective memory + unlearning gate (giảm chi phí đảm bảo compliance).
- **Cơ quan tuân thủ/bảo mật:** ngân hàng, toà án, công ty luật — đảm bảo không rò rỉ/không viện dẫn thông tin mật hoặc luật hết hiệu lực.

## Slide 4 — Work packages & timeline (6 tháng)
- **WP1 Xây kho RAG chọn lọc** (crawl/parse/index, citation graph, importance + decay, compliance gate) — crawl ưu tiên sub-domain biến động nhiều (lao động, thuế).
- **WP2 Thí nghiệm forgetting** (nạp liên tục × 4 arm CPT/ReGrad/vanilla-RAG/selective-RAG × 3 task).
- **WP3 Phân tích & benchmark** (đường forgetting, Δ theo độ khó task, VLQA/ViLegalNLI/LegalSLM).
- 8 milestone theo tuần (corpus W3 → KB W5 → baseline W9 → selective W12 → continual-loading W15 → đo forgetting W18 → benchmark W21 → report W24).

💬 Kể: dependency WP1→WP3, WP2→WP3.

## Slide 5 — Budget & risks
- Tổng **~20 triệu VND (~$800)** — quy mô **đồ án sinh viên** (lương = 0): Colab Pro+ (~4,8tr) · cloud GPU RunPod/L4/4090 (~1,2tr) · thù lao SV luật tình nguyện (~4tr) · OCR API (~2,5tr) · in ấn/đi lại/workshop. *(khớp Bảng 4_4)*
- **Rủi ro chính:** metadata sai (→ double-pass verify 5%); retrieval recall (→ hybrid dense+BM25); compute (→ pre-compute embedding, mô hình nhỏ 1–4B).

💬 Kể: nhấn rủi ro metadata = rủi ro số 1; budget **student-scale**, không cần A100.

## Slide 6 — Closing (3 câu hỏi proposal phải trả lời)
- **Why needed:** luật biến động + phải "quên có chủ đích"; parametric → quên thảm khốc / khó unlearn.
- **Why now:** data VN mới (2025–2026) + quy định siết → nền thực nghiệm + nhu cầu hội tụ.
- **Why me:** đã làm **khóa luận về RAG** (miền thông tin trường ĐH) → nắm nền tảng, nay chuyển/mở rộng sang **miền luật** (khó hơn), dưới hướng dẫn GV chuyên NLP, dùng dataset VN công khai.
- **Value:** no factual (weight) drift + compliance-aware forgetting + reproducible protocol — novel về KH và có giá trị thực tiễn cho legal-AI VN.

💬 Kể: slide chốt — đọc 4 gạch này là hội đồng nắm trọn giá trị + thấy bạn đủ năng lực.
🔖 Liêm chính: AI chỉ hỗ trợ tìm/cấu trúc/ngôn ngữ; ý tưởng + framework + phân tích là của tác giả (xem Declaration).
