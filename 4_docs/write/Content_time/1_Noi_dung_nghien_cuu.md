# Nội dung nghiên cứu (Research Content)

> Những nội dung cụ thể cần thực hiện để đáp ứng mục tiêu đã đề ra.
> Bám 3 mục tiêu cụ thể (RAG-centric): **(MT1)** Legal memory importance / Selective RAG · **(MT2)** RAG-based legal unlearning · **(MT3)** Đánh giá & benchmark.
> Quy ước: **NDNC = Nội dung nghiên cứu**. Cột "Đáp ứng MT" ánh xạ về mục tiêu cụ thể.

---

## Bảng tổng quan

| NDNC | Tên nội dung | Đáp ứng MT | Sản phẩm chính |
|------|--------------|:----------:|----------------|
| NDNC1 | Lược khảo & xây dựng nền tảng lý thuyết | nền chung | Chương 2 hoàn chỉnh, khung khái niệm |
| NDNC2 | Thu thập & tiền xử lý corpus pháp lý VN + gán metadata | MT1, MT2, MT3 | Corpus chuẩn hoá + KB có metadata |
| NDNC3 | Xây dựng kiến trúc RAG bán-tham-số tổng thể (baseline) | MT1, MT3 | Pipeline RAG nền + static-RAG baseline |
| NDNC4 | Selective RAG Memory (importance + consolidation/decay) | MT1 | Module memory chọn lọc |
| NDNC5 | RAG-based Legal Unlearning (P+Q + filtering) | MT2 | Module unlearning có kiểm soát |
| NDNC6 | Thiết kế thực nghiệm, bộ metric & đánh giá | MT3 | Benchmark + báo cáo kết quả |
| NDNC7 | Tổng hợp, phân tích, viết & công bố | tất cả | Luận văn/đề cương + bài báo + mã nguồn |

---

## NDNC1 — Lược khảo & xây dựng nền tảng lý thuyết
**Đáp ứng:** nền chung cho cả 3 mục tiêu.

Công việc cụ thể:
- Tổng hợp survey CL cho LLM theo năm; phân biệt CL vs RAG vs Model Editing (`Wu2024_CL_Survey`, `Shi2024_CL_Survey`, `Zheng2024_LL_Survey`, `Guo2025_CL_GenAI`).
- Hệ thống hoá 3 paradigm CPT/CIT/CA + động lực học catastrophic forgetting + benchmark (`Kalajdzievski2024_ScalingForgetting`, `Li2024_RevisitingCF`, `Wang2023_TRACE`).
- Khảo sát PEFT/LoRA và giới hạn → biện minh hướng bán-tham-số (`Hu2021_LoRA`, `Biderman2024_LoRALearnsLess`, `Wang2023_OrthogonalSubspace`).
- Khảo sát đối chiếu ReGrad + nền RAG memory & unlearning (`Su2026_ReGrad`, `Sumida2025_LUFY`, `Wang2026_RAGUnlearning`, `Zheng2026_AgentLifelongSurvey`).
- Rà soát dataset/benchmark pháp lý VN (`Nguyen2025_VLQA`, `Duong2026_ViLegalNLI`, `Le2025_LegalSLM`).

Đầu ra: bản thảo Chương 2; xác định rõ khoảng trống nghiên cứu (gap) cho từng mục tiêu.

## NDNC2 — Thu thập & tiền xử lý corpus pháp lý VN + gán metadata
**Đáp ứng:** MT1, MT2, MT3.

Công việc cụ thể:
- Thu thập văn bản quy phạm + án lệ công khai (luật, nghị định, thông tư, quyết định) giai đoạn ~2005→nay.
- Chuẩn hoá cấu trúc pháp lý về **đơn vị tri thức** (điều/khoản/án lệ).
- Gán metadata cho mỗi đơn vị: **hiệu lực** (còn/hết, ngày), **thời gian ban hành**, **citation/tham chiếu chéo**, **loại văn bản**, **mức rủi ro/áp dụng**.
- Xây **citation graph** giữa các đơn vị (đầu vào cho importance score).
- Xác định vài mốc sửa đổi lớn để tạo **"regime pháp lý"** (trước/sau sửa) phục vụ kiểm thử chuyển regime.
- Tích hợp/đối chiếu VLQA (KB 59k điều luật) làm nguồn truy xuất.

Đầu ra: KB pháp lý có metadata + citation graph + tập "regime" mẫu.

## NDNC3 — Kiến trúc RAG bán-tham-số tổng thể (baseline)
**Đáp ứng:** MT1, MT3.

Công việc cụ thể:
- Chọn & cấu hình base LLM đóng băng (Qwen/LLaMA tiếng Việt — *cần chốt*).
- Dựng pipeline RAG nền: indexing → retriever → reasoning → answer; không sửa weight base.
- Thiết lập **static-RAG baseline** (chưa selective, chưa unlearning) để làm mốc so sánh.
- Định nghĩa schema đơn vị tri thức + giao diện truy xuất dùng chung cho NDNC4/NDNC5.

Đầu ra: pipeline RAG nền chạy được + baseline.

## NDNC4 — Selective RAG Memory (importance + consolidation/decay)
**Đáp ứng:** MT1 (gắn CQ1/H1).

Công việc cụ thể:
- Thiết kế **importance score** cho đơn vị luật/án lệ: citation graph, hiệu lực, mức rủi ro, tần suất truy vấn (mở rộng 6-metric của `Sumida2025_LUFY` sang miền luật).
- So sánh phương án **rule-based vs LLM-estimated** importance.
- Cơ chế **consolidation/decay**: cập nhật importance theo lịch sử truy vấn; giữ top-k trong long-term semantic memory, phần còn lại archive/tạm quên.
- **Ràng buộc compliance**: hard-constraint giữ điều luật còn hiệu lực dù importance thấp.

Đầu ra: module Selective RAG Memory + cấu hình importance.

## NDNC5 — RAG-based Legal Unlearning (P+Q + filtering)
**Đáp ứng:** MT2 (gắn CQ3/H3).

Công việc cụ thể:
- Định nghĩa **taxonomy forget request**: luật hết hiệu lực · sealed case · xoá dữ liệu cá nhân (right to be forgotten).
- Hiện thực khung **constrained optimization (P + Q)** trên KB: ẩn/loại entry (P) + ràng buộc retrieval/prompt (Q), không đổi tham số (`Wang2026_RAGUnlearning`).
- Lớp **privacy & compliance filtering** + log audit để không trả lời dựa trên entry đã unlearn.
- Đảm bảo **harmlessness**: không ảnh hưởng truy vấn không liên quan.

Đầu ra: module RAG Unlearning có kiểm soát.

## NDNC6 — Thiết kế thực nghiệm, bộ metric & đánh giá
**Đáp ứng:** MT3 (gắn CQ2/H2).

Công việc cụ thể:
- Thiết kế **ma trận thực nghiệm**: baseline (static RAG, CPT/CFT đối chiếu) → selective RAG → selective RAG + unlearning.
- Bộ **metric**: accuracy/F1 (VLQA/ViLegalNLI/LegalSLM); forgetting (FGT/BWT/FWT); **compliance** (tỉ lệ dùng đúng luật hiệu lực, tránh luật bãi bỏ); **cost** (kích thước index, thời gian truy xuất).
- Metric **unlearning**: effectiveness, universality, harmlessness, simplicity, robustness (`Wang2026_RAGUnlearning`).
- **Ablation**: thành phần importance, ngưỡng decay, regime trước/sau sửa luật.
- Chạy thực nghiệm + thu thập kết quả + kiểm định giả thuyết H1/H2/H3.

Đầu ra: bảng kết quả + phân tích nghiệm chứng giả thuyết.

## NDNC7 — Tổng hợp, phân tích, viết & công bố
**Đáp ứng:** tất cả.

Công việc cụ thể:
- Viết hoàn chỉnh Ch3 (method) + Ch4 (kết quả mong đợi & kế hoạch) trong đề cương; chuẩn bị Ch Experiments/Conclusion cho luận văn đầy đủ.
- Đóng gói **mã nguồn + bộ metric/benchmark** để công bố.
- Soạn bản thảo bài báo/hội nghị (nếu có).

Đầu ra: luận văn/đề cương hoàn chỉnh + sản phẩm phụ.

---

## Ma trận Mục tiêu × Nội dung × Câu hỏi nghiên cứu

| Mục tiêu cụ thể | NDNC liên quan | CQ/Giả thuyết |
|-----------------|----------------|----------------|
| MT1 — Legal memory importance / Selective RAG | NDNC2, NDNC3, NDNC4 | CQ1 / H1 |
| MT2 — RAG-based legal unlearning | NDNC2, NDNC5 | CQ3 / H3 |
| MT3 — Đánh giá & benchmark | NDNC2, NDNC3, NDNC6 | CQ2 / H2 |

> Các chỗ cần bạn chốt: base LLM, danh mục forget request trong phạm vi, mốc "regime pháp lý", ngưỡng nghiệm thu định lượng.
