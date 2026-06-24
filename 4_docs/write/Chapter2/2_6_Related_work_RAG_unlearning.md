# Machine Unlearning in RAG  ↔ `2_chapters/2_Background/2_6_Related_work_RAG_unlearning.tex`

> Vai trò: **trụ chính 2** của đề tài — nền lý thuyết cho method 3.3 (RAG Machine Unlearning). | Nhóm nguồn: Group 5

## Ý lớn cần viết

### 1. Unlearning là gì & vì sao cần ở miền luật
- Tóm tắt: yêu cầu "quên có chủ đích" — luật hết hiệu lực, sealed records, quyền được lãng quên; model-level unlearning tốn kém và rủi ro.
- Paper nguồn: `Wang2026_RAGUnlearning`
- Cần viết: đặt vấn đề unlearning trong bối cảnh pháp lý → động lực dùng RAG thay vì sửa weight.

### 2. Cơ chế RAG-based unlearning (P + Q)
- Tóm tắt: mô hình hoá tri thức cần quên gồm thành phần truy xuất P + ràng buộc Q (prompt constraints); mô phỏng "quên" mà KHÔNG đổi tham số; hiệu quả cả với model đóng (GPT-4o, Gemini).
- Paper nguồn: `Wang2026_RAGUnlearning`
- Cần viết: mô tả khung tối ưu có ràng buộc trên KB; minh hoạ với case luật hết hiệu lực.

### 3. Tiêu chí đánh giá unlearning
- Tóm tắt: effectiveness, universality, harmlessness, simplicity, robustness; chỉ số ROUGE-L, USR, TPR@1%FPR, HOP.
- Paper nguồn: `Wang2026_RAGUnlearning`
- Cần viết: liệt kê tiêu chí + chỉ số → dùng lại ở Ch4 (đánh giá quên chính xác mà không hại câu trả lời khác).

## Khoảng trống / câu hỏi mở
- Đo "harmlessness" (không ảnh hưởng truy vấn không liên quan) trong miền luật VN chưa có chuẩn → CQ3.
