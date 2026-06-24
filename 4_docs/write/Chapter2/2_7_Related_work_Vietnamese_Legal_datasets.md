# Vietnamese Legal NLP Datasets & Benchmarks  ↔ `2_chapters/2_Background/2_7_Related_work_Vietnamese_Legal_datasets.tex`

> Vai trò: nền dữ liệu/benchmark cho case study tiếng Việt — chốt phễu Ch2, nối sang đánh giá ở Ch4. | Nhóm nguồn: Group 5 (+ Group 3 D-CPT law domain)

## Ý lớn cần viết

### 1. VLQA — QA pháp luật tiếng Việt
- Tóm tắt: bộ dữ liệu QA đầu tiên gán nhãn chuyên gia, 59k điều luật + 3.1k QA; chỉ số Recall@K, MRR@10, ROUGE, BLEU; baseline ~59%.
- Paper nguồn: `Nguyen2025_VLQA`
- Cần viết: giới thiệu dataset + dùng làm KB cho RAG (kết nối method 3.2).

### 2. ViLegalNLI — suy luận pháp lý tiếng Việt
- Tóm tắt: bộ NLI luật lớn nhất, 42k cặp câu; loại bỏ lexical shortcut bằng PMI để đo đúng năng lực logic; baseline ~96%.
- Paper nguồn: `Duong2026_ViLegalNLI`
- Cần viết: nêu vai trò đánh giá lập luận + bài học chống lexical bias.

### 3. LegalSLM — đánh giá SLM tiếng Việt (QA/NLI/Syllogism)
- Tóm tắt: shared task VLSP 2025; CPT luật cải thiện model 4B nhưng hại model 1.7B; ~98% trắc nghiệm/NLI nhưng tụt ~50–59% ở tam đoạn luận tự luận.
- Paper nguồn: `Le2025_LegalSLM`
- Cần viết: nêu khoảng cách "truy xuất tốt nhưng lập luận kém" → động lực cho selective RAG + reasoning.

### 4. CPT đặc thù miền luật (D-CPT cho luật)
- Tóm tắt: D-CPT Law tính tỷ lệ trộn tối ưu cho domain luật; tiếng Việt là Strong Shift cần replay 15–25%.
- Paper nguồn: `Que2024_DCPTLaw`
- Cần viết: nối nền dữ liệu với chiến lược huấn luyện (nếu có pha CPT).

## Khoảng trống / câu hỏi mở
- SLM tiếng Việt thất bại ở tam đoạn luận pháp lý dù đã CPT → khoảng cách giữa truy xuất bộ nhớ và lập luận chủ động.
- Chưa có corpus gắn metadata hiệu lực/thời gian đầy đủ để mô hình hoá "regime pháp lý".
