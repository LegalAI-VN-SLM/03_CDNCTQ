# Research Plan and Implementation Strategy  ↔ `2_chapters/4_Expected_Outcomes_and_Plan.tex` → `\section{Research Plan and Implementation Strategy}`

> Vai trò: kế hoạch triển khai + tiến độ + (kinh phí nếu cần). | Framing của bạn (cần bạn quyết).

## Ý lớn cần viết

### 1. Work packages & tiến độ (timeline)
- WP1: xây Legal Selective RAG memory (importance + consolidation/decay).
- WP2: cơ chế RAG-based legal unlearning (P+Q + filtering).
- WP3: chuẩn bị dữ liệu + benchmark + đánh giá (VLQA/ViLegalNLI/LegalSLM).
- Cần viết: bảng Gantt/mốc theo tháng; phụ thuộc giữa WP. **(cần bạn chốt mốc)**

### 2. Chiến lược triển khai & rủi ro
- Tóm tắt: thứ tự ưu tiên trụ method; baseline trước (static RAG) → selective RAG → +unlearning; phương án dự phòng dữ liệu.
- Paper nguồn (chiến lược dữ liệu/CPT nếu dùng): `Que2024_DCPTLaw`, `Ibrahim2024_StrategiesCPT`
- Cần viết: nêu rủi ro (dữ liệu luật, compute) + cách giảm thiểu.

### 3. Dự trù kinh phí (How much) — nếu đề cương yêu cầu
- Tóm tắt: nhân sự · compute/GPU + storage (gradient/vector store) · thu thập-làm sạch dữ liệu luật · (user study nếu có).
- Cần viết: bảng ước tính theo mục. **(cần bạn quyết có đưa vào đây hay bỏ)**

## Khoảng trống / câu hỏi mở
- **(cần bạn quyết)** kinh phí + timeline chi tiết tới đâu là đủ cho cấp đề cương này?
