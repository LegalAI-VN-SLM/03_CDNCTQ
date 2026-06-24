# Surveys on Continual Learning for LLMs  ↔ `2_chapters/2_Background/2_1_Surveys_CL.tex`

> Vai trò: mở đầu phễu Ch2 — điểm các survey theo năm, định hình khái niệm, dẫn dắt vì sao đề tài chọn hướng bán-tham-số/RAG. | Nhóm nguồn: Group 1

## Ý lớn cần viết

### 1. CL cho LLM tiến hoá từ deep learning truyền thống → LLM tạo sinh
- Tóm tắt: chuyển từ mô hình encoder/phân loại sang generative LLM; các survey hệ thống hoá lại toàn bộ lĩnh vực 2024→2026.
- Paper nguồn: `Wu2024_CL_Survey`, `Shi2024_CL_Survey`, `Zheng2024_LL_Survey`, `Guo2025_CL_GenAI`, `Zheng2025_Awesome`
- Cần viết: điểm survey theo mốc thời gian; nêu động lực vì sao CL trở nên cấp thiết với LLM (tri thức thay đổi nhanh).

### 2. Phân biệt CL với RAG và Model Editing (ranh giới khái niệm cốt lõi)
- Tóm tắt: RAG = bộ nhớ ngoài tạm qua context (không đổi trọng số); Model Editing (ROME/MEMIT) = vá lỗi sự kiện cục bộ; CL = tích hợp tri thức vào trọng số, tổng quát hoá.
- Paper nguồn: `Zheng2024_LL_Survey`, `Guo2025_CL_GenAI`, `Wu2024_CL_Survey`
- Cần viết: làm rõ 3 ranh giới → đây là bản lề lập luận để biện minh hướng RAG/bán-tham-số của đề tài (không sửa weight base).

### 3. Bốn trường phái chống quên (taxonomy)
- Tóm tắt: Rehearsal/Replay · Regularization · Architecture/PEFT · External Memory (RAG phi tham số).
- Paper nguồn: `Wu2024_CL_Survey`, `Shi2024_CL_Survey`
- Cần viết: bảng/đoạn phân loại 4 paradigm — làm khung để các section sau (2.3 PEFT, 2.4 ReGrad, 2.5 RAG) gắn vào.

### 4. Mở rộng biên giới: từ mô hình tĩnh → Lifelong Agent (Perception–Memory–Action)
- Tóm tắt: survey 2026 nâng CL lên cấp Agent (POMDP có điều kiện mục tiêu); phân loại bộ nhớ Working/Episodic/Semantic/Parametric.
- Paper nguồn: `Zheng2026_AgentLifelongSurvey`
- Cần viết: nêu xu hướng mới nhất, đặt nền khái niệm "bộ nhớ ngoài" cho 2.5.

## Khoảng trống / câu hỏi mở
- Thiếu nghiên cứu đa ngôn ngữ / tài nguyên thấp → động lực trực tiếp cho case study tiếng Việt.
- Đánh giá Agent lifelong còn nặng định tính, thiếu benchmark định lượng chuẩn hoá.
