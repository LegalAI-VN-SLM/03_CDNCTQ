# Research Objectives  ↔ `2_chapters/1_Introduction/1_2_Research_objectives.tex`

> Vai trò: mục tiêu chung + mục tiêu cụ thể. | Phần lớn là framing của bạn (cần bạn quyết); căn chỉnh theo hướng RAG-centric (đã bỏ ReGrad khỏi method).

## Ý lớn cần viết

### Mục tiêu chung
- Tóm tắt: thiết kế & đánh giá framework **RAG bán-tham-số cho LLM pháp lý tiếng Việt**, kết hợp Selective RAG memory + cơ chế unlearning có kiểm soát; không sửa weight base.
- Cần viết: 1 câu mục tiêu tổng. **(cần bạn chốt câu chữ)**

### Mục tiêu cụ thể (gắn từng trụ method)
1. **Legal memory importance**: định nghĩa & học thước đo importance cho đơn vị điều luật/án lệ (citation, hiệu lực, rủi ro) + consolidation/decay. → method 3.2
   - Paper nguồn: `Sumida2025_LUFY`, `Nguyen2025_VLQA`
2. **RAG-based legal unlearning**: cơ chế quên có chủ đích cho case nhạy cảm (P + Q ràng buộc). → method 3.3
   - Paper nguồn: `Wang2026_RAGUnlearning`
3. **Đánh giá**: benchmark VN (VLQA, ViLegalNLI, LegalSLM) với accuracy + forgetting (FGT/BWT) + compliance hiệu lực + chi phí.
   - Paper nguồn: `Wang2023_TRACE`, `Le2025_LegalSLM`

## Khoảng trống / câu hỏi mở
- **(cần bạn quyết)** có giữ mục tiêu liên quan ReGrad/gradient bank không? Hiện đã chuyển ReGrad sang đối chiếu (2.4) → đề xuất bỏ khỏi mục tiêu cụ thể.
