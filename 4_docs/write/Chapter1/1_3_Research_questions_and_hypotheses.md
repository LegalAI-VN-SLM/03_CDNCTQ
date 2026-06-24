# Research Questions and Hypotheses  ↔ `2_chapters/1_Introduction/1_3_Research_questions_and_hypotheses.tex`

> Vai trò: câu hỏi nghiên cứu + giả thuyết. Đặt ngay sau objectives. | Framing của bạn (cần bạn quyết câu chữ).

## Ý lớn cần viết

### Câu hỏi nghiên cứu
- **CQ1 (Selective memory):** thước đo importance/legal weight nào (citation, hiệu lực, rủi ro) hiệu quả nhất cho selective legal memory?
- **CQ2 (RAG vs alternatives):** Selective RAG cải thiện gì so với RAG thường / CPT-CFT thuần về accuracy, forgetting, compliance, chi phí?
- **CQ3 (Unlearning):** hệ thống xử lý yêu cầu unlearning thực tế (right to be forgotten, sealed judgments) tới mức nào mà không hại câu trả lời khác?

### Giả thuyết
- **H1:** importance-based selective memory (consolidate điều luật quan trọng, decay phần ít liên quan) giữ/cải thiện QA-reasoning + giảm kích thước index mà không mất compliance.
  - Paper nguồn: `Sumida2025_LUFY`
- **H2:** RAG (không sửa weight) giảm catastrophic forgetting năng lực tổng quát so với CPT/CFT, đồng thời chuyển "regime pháp lý" (trước/sau sửa luật) có kiểm soát.
  - Paper nguồn: `Wu2024_CL_Survey`, `Kalajdzievski2024_ScalingForgetting`
- **H3:** mô hình hoá unlearning như tối ưu có ràng buộc trên KB đạt mức "quên" tương đương/tốt hơn model-level unlearning với chi phí thấp hơn, ít ảnh hưởng năng lực không liên quan.
  - Paper nguồn: `Wang2026_RAGUnlearning`

## Khoảng trống / câu hỏi mở
- Mỗi giả thuyết cần gắn 1 chỉ số kiểm chứng cụ thể ở Ch4 (map H↔metric).
