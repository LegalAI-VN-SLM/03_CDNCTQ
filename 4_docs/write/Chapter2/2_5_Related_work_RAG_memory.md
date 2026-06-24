# Selective RAG & Long-term Memory  ↔ `2_chapters/2_Background/2_5_Related_work_RAG_memory.tex`

> Vai trò: **trụ chính 1** của đề tài — nền lý thuyết cho method 3.2 (Selective RAG Memory). | Nhóm nguồn: Group 5 (+ Group 1 agent memory)

## Ý lớn cần viết

### 1. Bộ nhớ ngoài trong Lifelong Agent (taxonomy)
- Tóm tắt: Working / Episodic / Semantic / Parametric memory; RAG = semantic/episodic phi tham số.
- Paper nguồn: `Zheng2026_AgentLifelongSurvey`
- Cần viết: định vị RAG memory trong khung Perception–Memory–Action; làm nền khái niệm.

### 2. Quên có chọn lọc theo tâm lý học nhận thức (LUFY)
- Tóm tắt: tích hợp đường cong quên của con người; quên 90% lượt thoại ít quan trọng → +17% độ chính xác truy xuất, giảm tải bộ nhớ/context clutter; dùng 6 thước đo importance.
- Paper nguồn: `Sumida2025_LUFY`
- Cần viết: ý lõi cho method — "importance score + consolidation/decay"; chuyển từ hội thoại sang đơn vị điều luật/án lệ.

### 3. Từ "memory hội thoại" → "memory pháp lý" (gap & cơ hội)
- Tóm tắt: LUFY làm cho chatbot; đề tài mở rộng sang văn bản luật/án lệ với importance đặc thù (citation graph, hiệu lực, mức rủi ro).
- Paper nguồn: `Sumida2025_LUFY` (đối chiếu), nối `Nguyen2025_VLQA`
- Cần viết: nêu khoảng trống → đóng góp của đề tài (legal importance metric).

## Khoảng trống / câu hỏi mở
- Chưa có thước đo importance cho đơn vị pháp lý (vs hội thoại) → câu hỏi nghiên cứu CQ1.
- Cân bằng giảm kích thước index vs giữ compliance (không vô tình quên điều luật còn hiệu lực).
