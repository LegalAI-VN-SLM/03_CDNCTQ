**Kế hoạch thực hiện chi tiết — 6 tháng / 24 tuần**
Đề tài: *Học liên tục & Quên thảm khốc trong Mô hình Ngôn ngữ cho Pháp luật Việt Nam* — HV: Hoàng Đình Quý Vũ (252805008) · GVHD: PGS.TS. Lê Anh Cường

> **WP1** Selective RAG Memory (crawl, index, importance/decay, compliance + temporal gating P+Q) · **WP2** Continual-Ingestion Forgetting Study (CPT/CIT, ReGrad, nạp snapshot, BWT) · **WP3** Benchmark & Validation (VLQA/ViLegalNLI/LegalSLM, ablation, viết báo cáo).

---

# Tháng 1 — Hạ tầng dữ liệu (crawl → KB v1)

### Tuần 1 — Thu thập corpus (WP1)
- Crawl văn bản luật từ cổng Chính phủ & công báo
- Ưu tiên 2 sub-domain biến động nhiều: lao động, thuế
- Lập sổ nguồn: URL · ngày ban hành/hiệu lực · loại văn bản
- **Đầu ra —** Corpus thô ≥ 1.500 văn bản + sổ nguồn
- **Note —** Chọn nguồn có mốc sửa đổi rõ để dựng "legal time-regime"

### Tuần 2 — Cắt đơn vị & chuẩn hoá (WP1)
- Viết parser cắt văn bản → đơn vị (Điều/Khoản/đoạn án)
- Chuẩn hoá định dạng, khử nhiễu OCR
- Chốt schema metadata `⟨e, v, [t], c, ρ, H, acl⟩`
- **Đầu ra —** ~50k đơn vị đã segment; schema metadata khoá
- **Note —** Schema sai ở đây sẽ kéo lỗi xuống toàn pipeline → review kỹ

### Tuần 3 — Metadata & citation graph (WP1)
- Gắn metadata: hiệu lực `v`, khoảng thời gian `[t]`, phân quyền `acl`
- Dựng đồ thị trích dẫn → tính citation-centrality `c`
- LLM tự kiểm + spot-check 5% mẫu metadata
- **Đầu ra —** KB-meta v0.5 (đơn vị đủ tag) + đồ thị trích dẫn
- **Note —** ⭐ **Mốc M1**: corpus thu thập & chuẩn hoá xong

### Tuần 4 — Embedding & vector index (WP1)
- Sinh embedding bằng bi-encoder tiếng Việt
- Dựng vector index (FAISS) + gắn `c`, `ρ` vào đơn vị
- Test truy vấn semantic top-N
- **Đầu ra —** KB v1 truy vấn được (semantic retrieve)
- **Note —** So vài bi-encoder VN, chọn theo Recall@K sơ bộ

---

# Tháng 2 — Selective Memory + bộ đánh giá

### Tuần 5 — Khoá KB & retrieval baseline (WP1)
- Tinh chỉnh chunking/embedding; đo Recall@K thô
- Đóng băng phiên bản KB để thí nghiệm tái lập được
- **Đầu ra —** Báo cáo retrieval baseline; **KB v1 khoá**
- **Note —** ⭐ **Mốc M2**: embedding + index hoàn tất

### Tuần 6 — Importance + decay (WP1)
- Cài điểm importance `I = w₁ĉ + w₂v + w₃ρ + w₄g(H,t)`
- Cài decay Ebbinghaus `g(H,t)`; phân tier Active/Archive
- **Đầu ra —** Module rerank selective chạy được
- **Note —** `w₁..w₄` để rule-based trước, tune sau (là biến của RQ/H3)

### Tuần 7 — Compliance + temporal gating (WP1)
- Compliance hard-gate: luật còn hiệu lực luôn giữ
- Temporal gating P + logic supersession (`v=0`, `t_end`)
- **Đầu ra —** Pipeline Selective + Compliance hoàn chỉnh
- **Note —** "Quên" = giảm nhiễu xếp hạng, KHÔNG mất coverage

### Tuần 8 — Bộ đánh giá 3 task (WP3)
- Dựng eval harness: Recall@K, MRR@10, EM/F1, NLI-acc, syllogism-score
- Wiring 3 bộ dữ liệu VLQA / ViLegalNLI / LegalSLM
- **Đầu ra —** Evaluator tự động chạy được cả 3 task
- **Note —** Decontaminate train/test (VLQA vừa là KB vừa là eval)

---

# Tháng 3 — Baseline RAG + baseline tham số

### Tuần 9 — Baseline static RAG (WP3)
- Dựng static RAG: cùng base + LoRA + KB, tắt selective/unlearning
- Đo điểm chuẩn 3 task làm mốc so sánh
- **Đầu ra —** Baseline static RAG cho điểm trên 3 task
- **Note —** ⭐ **Mốc M3**: baseline static RAG hoạt động

### Tuần 10 — LoRA behavioural (WP1)
- Train adapter hành vi (template QA/NLI/Syll + replay 0.5%)
- Cấu hình `r=256, α=512`, base frozen 1–4B
- **Đầu ra —** Adapter hành vi v1 (sinh có trích dẫn, đúng format)
- **Note —** LoRA học hành vi, KHÔNG nhồi facts (rank thấp đủ cho IFT)

### Tuần 11 — Baseline tham số CPT/CIT (WP2)
- Dựng pipeline Continual Pre-Training / Instruction Tuning
- Chạy cùng cỡ model 1–4B để so sánh công bằng
- **Đầu ra —** CPT/CIT chạy được trên corpus luật
- **Note —** Cố định learning-rate/schedule để khử confound

### Tuần 12 — ReGrad đối chiếu + snapshot (WP2)
- Dựng ReGrad (gradient bank) làm nhánh bán-tham số
- Chốt lịch nạp snapshot 25 / 50 / 75 / 100%
- **Đầu ra —** Khung 4 nhánh (CPT · ReGrad · vanilla · selective) sẵn sàng
- **Note —** ⭐ **Mốc M4**: selective+gating + baseline (CPT/ReGrad) chạy

---

# Tháng 4 — Thí nghiệm forgetting chính

### Tuần 13 — Nạp 25%→50% (WP2)
- Chạy nạp liên tục mức 25% rồi 50% cho cả 4 nhánh
- Đo perf 3 task ở mỗi mức nạp
- **Đầu ra —** Bảng perf theo lượng nạp (mức thấp)
- **Note —** Lưu checkpoint mỗi mức để vẽ đường liên tục

### Tuần 14 — Nạp 75%→100% + BWT (WP2)
- Nạp tiếp mức 75% và 100%
- Đo Backward Transfer (BWT) + Forgetting Gradient
- **Đầu ra —** Đường forgetting sơ bộ (CPT sụp vs RAG ổn)
- **Note —** Kỳ vọng H1 (CPT âm mạnh) & H2 (RAG ≈ 0) lộ rõ ở mức 100%

### Tuần 15 — Selective vs vanilla + tune (WP1 + WP3)
- Đo selective vs vanilla khi kho phình: Recall@K, kích thước index
- Tune `w₁..w₄`, `λ` trên dev set
- **Đầu ra —** Selective giữ recall; index giảm; **tham số chốt**
- **Note —** ⭐ **Mốc M5**: tinh chỉnh selective xong (đây là H3)

### Tuần 16 — Ablation (WP3)
- Ablation: từng feature importance · `λ` · P-only vs P+Q
- Sửa lỗi pipeline phát sinh
- **Đầu ra —** Bảng ablation cô lập đóng góp từng module
- **Note —** Mỗi dòng ablation phải trỏ về một quyết định thiết kế

---

# Tháng 5 — Phân tích & kiểm định giả thuyết

### Tuần 17 — Đường forgetting R1 (WP2 + WP3)
- Chạy RAG-vs-parametric đầy đủ trên 3 task
- Hoàn chỉnh đường forgetting (kết quả R1)
- **Đầu ra —** Figure R1 + số liệu BWT chốt
- **Note —** Đây là "trái tim" kết quả → ưu tiên độ sạch số liệu

### Tuần 18 — Δ theo task / trần lập luận R3 (WP3)
- Tính Δ cải thiện QA → NLI → Syllogism
- Đối chiếu ReGrad (hiệu năng vs chi phí)
- **Đầu ra —** Figure R3 + bằng chứng trần lập luận
- **Note —** ⭐ **Mốc M6**: chốt forgetting + robustness (H4 ở đây)

### Tuần 19 — Unlearning (phụ) (WP1)
- Forget↔retain matched; đo USR, ROUGE-L↓, harmlessness
- Đo rò rỉ TPR@1%FPR dưới câu hỏi jailbreak
- **Đầu ra —** Bảng unlearning + đánh giá rò rỉ
- **Note —** Định vị là phần phụ; KHÔNG claim xoá khỏi trọng số

### Tuần 20 — Tổng hợp & kiểm định H1–H4 (WP3)
- Tổng hợp kết quả; error analysis ca syllogism sai
- Kiểm định thống kê 4 giả thuyết
- **Đầu ra —** Bảng kết quả + kết luận chấp nhận/bác bỏ H1–H4
- **Note —** Chuẩn bị sẵn diễn giải cho cả trường hợp NULL

---

# Tháng 6 — Đạt ngưỡng, viết & nộp

### Tuần 21 — Chốt ngưỡng nghiệm thu R2 (WP3)
- Kiểm tra Recall trong ±2%, active index giảm ≥30%
- Chạy lại robustness để xác nhận ổn định
- **Đầu ra —** Đạt mục tiêu benchmark định lượng
- **Note —** ⭐ **Mốc M7**: đạt điểm benchmark mục tiêu

### Tuần 22 — Viết kết quả + đóng gói GitHub (WP3)
- Viết chương Kết quả + Thảo luận
- Đóng gói repo: code + eval scripts + index tái lập được
- **Đầu ra —** Bản thảo chương 4–5 + repo công khai
- **Note —** Repo tái lập = một đóng góp được hội đồng đánh giá cao

### Tuần 23 — Hoàn thiện toàn văn (WP3)
- Hoàn thiện luận văn; rà soát trích dẫn; biên tập
- Chuẩn bị slide bảo vệ
- **Đầu ra —** Bản thảo luận văn hoàn chỉnh + slide
- **Note —** Chừa buffer cho phản hồi của GVHD

### Tuần 24 — Nộp & hội nghị (WP3)
- Nộp báo cáo cuối
- Phấn đấu 01 báo cáo hội nghị trong nước (VLSP/KSE)
- **Đầu ra —** Nộp luận văn + bản nháp bài hội nghị
- **Note —** ⭐ **Mốc M8**: nộp báo cáo
