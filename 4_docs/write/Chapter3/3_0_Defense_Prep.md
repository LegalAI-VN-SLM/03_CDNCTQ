# 3.0 Defense Prep — Tổng hợp câu hỏi xoáy & cách trả lời

> Dùng để ôn trước buổi bảo vệ đề cương. Mỗi câu: **trả lời ngắn gọn + bằng chứng/luận cứ + (nếu có) thừa nhận giới hạn**.
> Nguyên tắc vàng: **hiểu giới hạn và nói thẳng** > vòng vo. Hội đồng hỏi xoáy để xem mình có hiểu mình làm gì không.

---

## A. Nhóm "Novelty & lý do tồn tại của đề tài"

**A1. Đề tài mới ở đâu? RAG/unlearning/selective memory đã có người làm.**
→ Đóng góp = **kết hợp + thích ứng miền luật VN + đánh giá**, KHÔNG claim thuật toán RAG mới:
(1) importance/forgetting đặc thù pháp lý (citation graph + hiệu lực + rủi ro) + **compliance gate theo hiệu lực**;
(2) hợp nhất selective memory + controllable unlearning, xử lý **regime pháp lý theo thời gian**;
(3) **metric/benchmark legal-CL tiếng Việt**.

**A2. Sao không CPT/fine-tune cho model thuộc luật?**
→ CPT/CFT gây **catastrophic forgetting + weight drift** (`Kalajdzievski2024_ScalingForgetting`: inverse linear law; `Gupta2023_CPT_Warmup`: stability gap), tốn compute, và **không unlearn được**. Mục tiêu = cập nhật liên tục + quên có chủ đích → RAG hợp hơn.

**A3. Tại sao bỏ ReGrad dù đã đọc kỹ?**
→ ReGrad (parametric tạm) tránh weight drift nhưng **độ trễ inference cao** (áp gradient runtime) và phức tạp. Đề tài ưu tiên độ trễ thấp + dễ unlearn → giữ ReGrad làm **đối chiếu** (2.4), không làm method. *(Thể hiện đã cân nhắc, không phải bỏ vì không biết.)*

## B. Nhóm "Kiến trúc & frozen LLM"

**B1. Frozen LLM thì reasoning pháp lý có đủ? LegalSLM cho thấy tụt ~50% ở tam đoạn luận.**
→ Đúng, là **giới hạn đã biết** (`Le2025_LegalSLM`). Selective RAG cải thiện *chất lượng context* (đúng điều, đúng hiệu lực) → giảm lỗi retrieve; reasoning vẫn bị chặn bởi base → chọn base instruct decoder-only mạnh + reasoning prompting + nêu ở Limitations.

**B2. Phụ thuộc retriever — retrieve sai thì sao?**
→ Lỗi retrieval → lỗi câu trả lời (GĐ2). Giảm thiểu: hybrid retrieval, compliance gate giữ luật hiệu lực, đo Recall@K/MRR như chỉ số trung gian.

## C. Nhóm "Selective Memory (3.2)"

**C1. Decay làm mất luật quan trọng nhưng hiếm tra?** → **Validity là hard gate**; luật còn hiệu lực luôn giữ; decay chỉ down-rank archive *vẫn truy xuất*, không xoá.
**C2. Trọng số importance ad hoc?** → tune trên validation (Recall@K/MRR) hoặc LLM-estimated; có **ablation**.
**C3. Citation centrality có đáng tin?** → 1 trong nhiều feature, proxy có cơ sở, ablation kiểm chứng, đã nêu giới hạn.

## D. Nhóm "Unlearning (3.3)" — phần dễ bị hỏi nặng nhất

**D1. (KILLER) Tri thức trong weight thì gỡ KB có thật sự quên?**
→ Đây là **retrieval-level unlearning**: đảm bảo không truy xuất/grounding/trích dẫn (đủ cho compliance). KHÔNG xoá khỏi weight; rò rỉ parametric được giảm bằng ràng buộc Q + filter và **đo định lượng** (TPR@1%FPR, HOP). Đảm bảo mạnh ở mức weight cần model-level unlearning — **ngoài phạm vi**. *(Nói thẳng phạm vi.)*
**D2. Khác gì DELETE database?** → khó ở harmlessness + universality + temporal regime + robustness, là constrained optimization P+Q, không phải xoá đơn thuần.
**D3. Metadata hiệu lực sai thì sao?** → rủi ro chính; QC thủ công tập trọng yếu; ghi ở Limitations + timeline.
**D4. Unlearn nhầm có đảo ngược?** → Có (trừ hard-delete personal data); audit log truy vết.

## E. Nhóm "Đánh giá & nghiệm chứng" (xem Ch4 / NDNC6)

**E1. Đo 'compliance' khách quan thế nào?**
→ Trên test có nhãn vàng hiệu lực: tỉ lệ (a) trích dẫn luật còn hiệu lực, (b) không viện dẫn luật bãi bỏ khi hỏi hiện hành, (c) xử lý đúng query theo mốc thời gian.
**E2. Baseline công bằng không?** → cùng base LLM + cùng KB, chỉ khác module selective/unlearning → cô lập đóng góp.
**E3. Giả thuyết H1/H2/H3 kiểm bằng chỉ số nào?**
→ H1: accuracy + index size + Recall@K (selective vs static RAG). H2: forgetting FGT/BWT năng lực chung (RAG vs CPT/CFT). H3: USR/ROUGE-L↓ + harmlessness + TPR@1%FPR (unlearning).
**E4. Dữ liệu/compute thiếu thì sao?** → ưu tiên VLQA/ViLegalNLI trước, LegalSLM sau; chạy quy mô nhỏ; phương án fallback đã ghi ở timeline.

## F. Nhóm "Phạm vi & khả thi"

**F1. 6 tháng làm hết nổi không?** → có timeline tuần chi tiết (`2_Timeline_6_thang.md`), 8 milestone, bảng rủi ro; phạm vi giới hạn QA/NLI/reasoning, luật VN, không generation dài.
**F2. Cần chốt gì để chạy?** → base LLM, danh mục forget request, mốc regime pháp lý, ngưỡng nghiệm thu (đã đánh dấu trong các file).

---

### Checklist tự tin trước bảo vệ
- [ ] Vẽ lại được sơ đồ kiến trúc 3.1.3 từ trí nhớ.
- [ ] Giải thích được vì sao validity là hard gate (C1).
- [ ] Trả lời gọn câu D1 mà không vòng vo (nói rõ phạm vi).
- [ ] Đọc thuộc 3 đóng góp (A1) và 3 giả thuyết + chỉ số (E3).
- [ ] Biết 4 thứ cần chốt (F2) và rủi ro chính (D3).
