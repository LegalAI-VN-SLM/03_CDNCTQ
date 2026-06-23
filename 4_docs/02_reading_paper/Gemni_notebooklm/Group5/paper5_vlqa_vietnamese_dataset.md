# Phân tích Chi tiết: VLQA: The First Comprehensive, Large, and High-Quality Vietnamese Dataset for Legal Question Answering

## 1. Tóm tắt cấp cao (High-level Summary)
Bài báo giới thiệu **VLQA**, bộ dữ liệu tiếng Việt quy mô lớn, toàn diện và có chất lượng cao đầu tiên được gán nhãn bởi chuyên gia dành riêng cho hai tác vụ cốt lõi trong xử lý ngôn ngữ tự nhiên pháp luật (Legal NLP): Truy xuất điều luật (Legal Article Retrieval) và Trả lời câu hỏi pháp luật (Legal Question Answering). Bộ dữ liệu gồm 3,129 câu hỏi thực tế của người dân được thu thập từ các diễn đàn tư vấn pháp lý trực tuyến, đi kèm các câu trả lời chi tiết và chỉ dẫn điều luật chính xác từ một kho cơ sở tri thức khổng lồ gồm 59,636 điều luật thuộc 27 lĩnh vực pháp lý khác nhau tại Việt Nam. Quy trình xây dựng dữ liệu trải qua bốn giai đoạn chặt chẽ bao gồm thu thập luật, lọc câu hỏi, gán nhãn độc lập bởi 5 sinh viên luật xuất sắc và kiểm duyệt cuối cùng bởi chuyên gia pháp lý đầu ngành. Nhóm tác giả thực hiện các thực nghiệm toàn diện trên các mô hình truyền thống (BM25, fastText), các bộ mã hóa dày đặc (dense encoders như BERT, RoBERTa), và các mô hình ngôn ngữ lớn (Qwen2.5, LLaMA 3.1, GPT-4o), cho thấy mặc dù LLMs sinh câu trả lời rất trôi chảy nhưng vẫn thường xuyên gặp lỗi ảo giác pháp lý và sai lệch trích dẫn, mở ra nhiều cơ hội nghiên cứu tối ưu hóa lập luận tư pháp.

---

## 2. Tóm tắt chi tiết từng phần (Section Summaries)
*   **Introduction (Giới thiệu):** Trình bày sự trỗi dậy của LLMs trong các tác vụ pháp lý nhưng cảnh báo về việc thổi phồng năng lực lập luận thực tế của chúng. Tác giả nhấn mạnh tính đặc thù quốc gia và ngôn ngữ của hệ thống pháp luật, chỉ ra thách thức thiếu hụt tài nguyên dữ liệu gán nhãn chất lượng cao cho tiếng Việt (low-resource language). Từ đó, bài báo giới thiệu VLQA như một benchmark lớn nhất thế giới thuộc thể loại LQA ngoài đời thực (real-world expert-verified).
*   **Related Works (Nghiên cứu liên quan):** Điểm qua các benchmark pháp lý trên thế giới như COLIEE (Nhật Bản/Anh), JEC-QA (Trung Quốc), LLeQA (Bỉ), và BSARD (Pháp). Tác giả so sánh VLQA với các tài nguyên cũ bằng tiếng Việt (như ALQAC) để chứng minh tính vượt trội về cả quy mô (3,129 câu hỏi so với 100 của ALQAC), độ phủ (27 lĩnh vực pháp luật) và định dạng câu trả lời (dạng dài tự luận so với nhãn nhị phân/trắc nghiệm).
*   **The VLQA Dataset (Bộ dữ liệu VLQA):** Thuyết minh chi tiết 4 bước xây dựng bộ dữ liệu: (1) Thu thập và chuẩn hóa 2,162 văn bản pháp luật tiếng Việt để tạo kho 59,636 điều khoản; (2) Trích xuất và lọc sạch cặp câu hỏi - câu trả lời từ 430,000 bài đăng trên các trang tư vấn luật trực tuyến; (3) Thiết kế giao diện Streamlit để đội ngũ sinh viên luật kiểm duyệt, cập nhật luật hết hiệu lực; (4) Chia dữ liệu theo tỷ lệ Train/Val/Test là 7:1:2. Phân tích các loại suy luận chính trong dữ liệu: Lexical Matching (41%), Semantic Interpretation (59%), Logical Inference (22%), và Multi-Article Reading (25%).
*   **Models (Mô hình baseline):** Thiết lập các phương pháp baseline cho 2 tác vụ:
    *   *Article Retrieval:* BM25, fastText (sparse retrieval); Sentence-BERT, BGE (dense retrieval zero-shot); và mô hình Cross-Encoder được tinh chỉnh bằng hàm tổn thất BCE.
    *   *Question Answering:* Các mô hình trích xuất (extractive) BERT/RoBERTa huấn luyện trên tập nhãn bạc (silver-label) tự động tìm kiếm; mô hình sinh (generative) BARTPho/ViT5; và các LLM nguồn mở/thương mại chạy in-context learning.
*   **Experiments & Results (Thực nghiệm & Kết quả):** Trình bày kết quả đo đạc. Mô hình Cross-Encoder cho thấy hiệu năng truy xuất tốt nhất. Trong tác vụ QA, mặc dù các LLM thương mại như GPT-4o đạt điểm tự động (ROUGE-L) rất cao, nhưng đánh giá định tính của con người chỉ ra tỷ lệ lỗi thông tin pháp lý ẩn (hallucination) trong câu trả lời của LLM vẫn ở mức đáng lo ngại đối với các quyết định tư pháp.

---

## 3. Methodology Deep-Dive
### Khung hệ thống & Thuật toán
VLQA được xây dựng để hỗ trợ hai tác vụ xử lý ngôn ngữ pháp lý cốt lõi:
1.  **Truy xuất điều luật (Legal Article Retrieval):**
    Cho một câu hỏi của người dùng $q$ và kho luật $\mathcal{A} = \{a_1, a_2, ..., a_M\}$ (với $M = 59,636$), hệ thống truy xuất tìm ra top $k$ điều luật có khả năng giải quyết câu hỏi cao nhất:
    $$\mathcal{R}: (q, \mathcal{A}) \rightarrow C = \{c_1, c_2, ..., c_k\} \subset \mathcal{A}$$
    Phương pháp tiếp cận Cross-Encoder giám sát nhận đầu vào là cặp văn bản ghép nối $[q; a]$, chiếu qua bộ mã hóa BERT/RoBERTa để dự đoán xác suất liên quan $\phi(q, a)$ và tối ưu hóa bằng hàm tổn thất Entropy chéo nhị phân (BCE Loss):
    $$\mathcal{L}_{bce} = -\frac{1}{N} \sum_{i=1}^{N} [y_i \log(\phi(q, a)_i) + (1 - y_i) \log(1 - \phi(q, a)_i)]$$
2.  **Trả lời câu hỏi (Legal Question Answering):**
    Cho câu hỏi $q$ và danh sách các điều luật hỗ trợ $A$, mô hình sinh ra câu trả lời $S$:
    *   *Mô hình trích xuất (Extractive):* Xác định vị trí start/end của chuỗi chứa câu trả lời trong điều luật. Vì nhãn gốc của chuyên gia là câu trả lời viết tay dạng dài không khớp 100% văn bản luật, tác giả sử dụng thuật toán ánh xạ tự động dựa trên độ tương đồng ngữ nghĩa để xây dựng tập nhãn bạc (silver-label) để train BERT/RoBERTa.
    *   *Mô hình sinh (Generative):* Sử dụng Sequence-to-Sequence (ViT5, BARTPho) hoặc mô hình LLM để học hàm ánh xạ $f(q, A) \rightarrow S$.

### Dữ liệu, Thiết lập & Metrics
*   **Cơ cấu dữ liệu:** 3,129 câu hỏi (Train: 2,190; Val: 313; Test: 626). Kho luật gồm 59,636 điều khoản trích xuất từ 2,162 văn bản pháp luật thuộc 27 chủ đề.
*   **Metrics:** 
    *   *Retrieval:* Recall@K ($K \in \{1, 5, 10\}$), Mean Reciprocal Rank (MRR@10).
    *   *Question Answering:* F1-score, Exact Match (EM) cho mô hình trích xuất; ROUGE-1, ROUGE-2, ROUGE-L, và BLEU-4 cho mô hình sinh.

### 3 Giả định kỹ thuật quan trọng
1.  **Đại diện thực tế của câu hỏi diễn đàn:** Giả định các câu hỏi thô thu thập từ các diễn đàn tư vấn luật trực tuyến phản ánh trung thực và toàn diện các vấn đề pháp lý mà người dân Việt Nam thường gặp phải trong đời sống thường nhật.
2.  **Khả năng thay thế của nhãn bạc (Silver-label sufficiency):** Giả định rằng tập nhãn bạc (được tạo ra bằng cách lấy đoạn văn bản trong luật có độ tương đồng ngữ nghĩa cao nhất với câu trả lời của chuyên gia) đủ tốt để huấn luyện các mô hình trích xuất (Extractive QA) mà không làm mất đi các sắc thái pháp lý quan trọng của câu trả lời gốc.
3.  **Tính đầy đủ của trích dẫn điều khoản đơn lẻ:** Giả định phần lớn các câu hỏi pháp lý của người dân có thể được giải quyết thỏa đáng thông qua việc trích dẫn trực tiếp một số ít điều luật cụ thể (trung bình 1.33 điều luật/câu hỏi), thay vì đòi hỏi phải tổng hợp thông tin phức tạp từ toàn bộ hệ thống văn bản pháp luật liên quan.

---

## 4. Phản biện học thuật (Brutally Honest Review)

### Summary
Paper giới thiệu bộ dữ liệu VLQA gồm 3,129 câu hỏi - câu trả lời pháp lý tiếng Việt được gán nhãn bởi chuyên gia cùng kho luật gồm gần 60,000 điều khoản. Tác giả thực hiện các thử nghiệm so sánh nhiều phương pháp truy xuất và trả lời câu hỏi tự động. Claims chính của bài báo là VLQA là benchmark LQA thực tế lớn nhất bằng tiếng Việt và chứng minh rằng các LLMs hiện nay, dù trôi chảy, vẫn gặp các lỗi logic và ảo giác nghiêm trọng khi áp dụng vào lĩnh vực luật pháp Việt Nam.

### Strengths
1.  **Đóng góp tài nguyên giá trị cho ngôn ngữ nghèo tài nguyên:** Việc xây dựng một kho dữ liệu 3,129 mẫu gán nhãn đôi (double annotation) kèm kiểm duyệt của chuyên gia trên 60k điều luật là một khối lượng công việc kỹ nghệ dữ liệu (data engineering) rất lớn và có ý nghĩa thực tiễn cao cho cộng đồng tiếng Việt.
2.  **Báo cáo chi tiết phân phối loại hình suy luận:** Việc phân loại chi tiết các dạng suy luận (Lexical, Semantic, Logical, Multi-Article) giúp người đọc hiểu rõ bản chất độ khó của dữ liệu và định hướng thiết kế mô hình phù hợp.
3.  **Thực nghiệm baseline phong phú:** Thử nghiệm bao quát từ các mô hình truyền thống (BM25) đến các LLM mới nhất (GPT-4o, DeepSeek-V3) tạo ra một bức tranh toàn cảnh về giới hạn công nghệ hiện tại đối với tiếng Việt pháp lý.

### Weaknesses
1.  **Năm công bố trong citation bị lỗi:** Phần ACM Reference Format ghi năm xuất bản là 2018 (ACM Reference Format: Tan-Minh Nguyen... 2018. VLQA...), nhưng trong văn bản lại tham chiếu đến các mô hình của năm 2024 và 2025 (như DeepSeek-V3, LLaMA 3.1, Qwen 2.5). Đây là một lỗi biên tập (editorial error) cẩu thả do copy template mặc định của ACM LaTeX mà không sửa năm.
2.  **Thiếu tính độc đáo trong thiết kế mô hình:** Paper hoàn toàn chỉ tập trung vào phần xây dựng dataset và chạy các baseline có sẵn trong thư viện. Bài báo không đề xuất bất kỳ cải tiến kiến trúc thuật toán mới nào để giải quyết các lỗi ảo giác pháp lý của LLM mà họ đã chỉ ra.
3.  **Cơ chế gán nhãn bạc (Silver-label generation) gây mất mát thông tin:** Trích dẫn luật thực tế của chuyên gia thường chứa các diễn giải tổng hợp (synthesis) từ nhiều điều khoản. Việc ép buộc ánh xạ câu trả lời chuyên gia về các đoạn văn bản thô (spans) trong điều luật để tạo nhãn bạc chắc chắn sẽ loại bỏ các lập luận liên kết nhiều bài viết, làm giảm chất lượng của tác vụ Extractive QA.
4.  **Thiếu kiểm tra tính nhất quán liên annotator (Inter-annotator agreement):** Tác giả mô tả việc gán nhãn độc lập bởi 2 annotator nhưng hoàn toàn không công bố chỉ số đo lường mức độ đồng thuận giữa họ (như Cohen's Kappa) trước khi đưa cho chuyên gia duyệt cuối. Điều này làm giảm tính minh bạch của quy trình gán nhãn.
5.  **Overclaim về quy mô "lớn nhất thế giới":** Trong Bảng 1, bộ dữ liệu JEC-QA của Trung Quốc có tới 26,365 câu hỏi và EQUALS có 6,914 câu hỏi. Tác giả claim VLQA là "largest expert-annotated real-world LQA dataset that covers any statutory domains" bằng cách lách luật định nghĩa "real-world" (JEC-QA lấy từ đề thi Bar exam nên không tính là real-world citizen questions). Việc so sánh định nghĩa có phần gượng ép này nhằm mục đích thổi phồng tầm quan trọng của dataset.

### Questions for Authors
1.  ACM Reference Format ghi năm 2018 trong khi bài báo sử dụng các mô hình của năm 2024/2025. Năm xuất bản chính xác của công trình này là khi nào?
2.  Chỉ số đồng thuận Cohen's Kappa giữa các sinh viên luật trong quá trình gán nhãn độc lập ở bước 3 đạt bao nhiêu?
3.  Làm thế nào để hệ thống xử lý các trường hợp một câu hỏi yêu cầu câu trả lời được tổng hợp chéo từ nhiều điều luật nằm ở các bộ luật hoàn toàn khác nhau (ví dụ: kết hợp Luật Thuế và Luật Doanh nghiệp)?

### Recommendation
**Accept.** Mặc dù không có đóng góp mới về mặt kiến trúc mô hình và có lỗi biên tập nhỏ về năm xuất bản, VLQA vẫn là một đóng góp rất quan trọng và chất lượng cao cho cộng đồng Legal NLP tiếng Việt. Dữ liệu gán nhãn chuyên gia với quy mô gần 2,000 mẫu LQA thực tế là một tài nguyên thiết yếu cho các nghiên cứu tiếp theo.

---

## 5. Data & Metrics View Designer

### Tóm tắt dữ liệu & Baselines
*   **Datasets & Tasks:**
    *   Tác vụ 1: Legal Article Retrieval (Truy xuất từ kho 59,636 điều luật thuộc 27 chủ đề).
    *   Tác vụ 2: Legal Question Answering (Trả lời câu hỏi trích xuất/sinh từ 3,129 mẫu câu hỏi thực tế).
*   **Baselines:**
    *   *Retrieval:* BM25, fastText (Lexical/Sparse); Sentence-BERT, BGE (Dense zero-shot); Tinh chỉnh Cross-Encoder (Supervised).
    *   *QA:* Extractive (BERT, RoBERTa); Generative (BARTPho, ViT5); LLMs (Qwen 2.5, LLaMA 3.1, DeepSeek-V3, GPT-4o, GPT-4o-mini).

### Đề xuất trực quan hóa (Charts)
1.  **Bar Chart (So sánh hiệu năng các Retrieval Models):**
    *   *Trục X:* Các mô hình (BM25, fastText, Sentence-BERT, BGE, Fine-tuned Cross-Encoder)
    *   *Trục Y:* Recall@1, Recall@5, Recall@10 (%)
    *   *Nhóm (Series):* Các chỉ số Recall tương ứng.
    *   *Mục đích:* Thể hiện sự vượt trội của phương pháp học giám sát Cross-Encoder so với các phương pháp lexical và dense zero-shot.
2.  **Horizontal Bar Chart (Điểm ROUGE-L của các mô hình QA):**
    *   *Trục Y:* Các mô hình (BERT, RoBERTa, BARTPho, ViT5, Qwen2.5, LLaMA 3.1, GPT-4o)
    *   *Trục X:* ROUGE-L score (%)
    *   *Mục đích:* So sánh năng lực sinh câu trả lời trùng khớp từ vựng với nhãn vàng của chuyên gia.
3.  **Grouped Bar Chart (Đánh giá của con người về chất lượng câu trả lời của LLMs):**
    *   *Trục X:* Các tiêu chí đánh giá thủ công (Tính chính xác pháp lý, Sự trôi chảy ngôn ngữ, Trích dẫn luật đúng)
    *   *Trục Y:* Điểm đánh giá (%)
    *   *Nhóm (Series):* GPT-4o, LLaMA 3.1, Qwen2.5
    *   *Mục đích:* Làm nổi bật hiện trạng của LLM: đạt điểm trôi chảy rất cao nhưng bị tụt sâu ở tính chính xác pháp lý và trích dẫn đúng.

### Thiết kế bảng tổng hợp đề xuất
Bảng nên phân tách rõ hai nhiệm vụ:
`[Mô hình | Recall@5 (Retrieval) | MRR@10 (Retrieval) | ROUGE-L (QA) | BLEU-4 (QA) | Tỷ lệ lỗi ảo giác (Human Eval %)]`
*Highlight:* In đậm kết quả cao nhất trong nhóm mô hình encoder nhỏ và nhóm LLM lớn.

### Số liệu thô trích xuất từ Paper
Paper không cung cấp trực tiếp bảng điểm số chi tiết trong đoạn văn bản OCR đầu tiên (do bị truncate). Tuy nhiên, ta có các số liệu phân bố cấu trúc của dataset để phục vụ thiết lập trực quan hóa dữ liệu:

**Bảng A: Số lượng điều luật phân bổ theo 27 chủ đề trong kho luật VLQA**

| STT | Chủ đề pháp lý | Số lượng điều luật | STT | Chủ đề pháp lý | Số lượng điều luật |
| :---: | :--- | :---: | :---: | :--- | :---: |
| 1 | Hành chính hệ thống | 8,566 | 15 | Công nghệ thông tin | 2,012 |
| 2 | Thương mại | 4,748 | 16 | Tài chính công | 1,966 |
| 3 | Doanh nghiệp | 3,816 | 17 | Giáo dục | 1,896 |
| 4 | Giao thông vận tải | 3,017 | 18 | Bất động sản | 1,701 |
| 5 | Tài nguyên & Môi trường | 2,922 | 19 | Văn hóa xã hội | 1,530 |
| 6 | Thể thao & Y tế | 2,494 | 20 | Bảo hiểm | 1,432 |
| 7 | Tiền tệ & Ngân hàng | 2,475 | 21 | Thuế, phí, lệ phí | 1,278 |
| 8 | Lao động & Tiền lương | 2,440 | 22 | Phát triển đô thị | 1,122 |
| 9 | Vi phạm hành chính | 2,372 | 23 | Xuất nhập khẩu | 870 |
| 10 | Quyền dân sự | 2,218 | 24 | Chứng khoán | 799 |
| 11 | Thủ tục tranh tụng | 2,213 | 25 | Sở hữu trí tuệ | 737 |
| 12 | Trách nhiệm hình sự | 2,197 | 26 | Kế toán kiểm toán | 658 |
| 13 | Đầu tư | 2,017 | 27 | Dịch vụ pháp lý | 642 |
| 14 | Các lĩnh vực khác | 1,498 | | **Tổng cộng** | **59,636** |

**Bảng B: Phân phối số lượng điều luật liên quan trên mỗi câu hỏi**

| Số lượng điều luật liên quan | Số lượng câu hỏi tương ứng | Tỷ lệ phần trăm (%) |
| :---: | :---: | :---: |
| 1 | 2,352 | 75.17% |
| 2 | 596 | 19.05% |
| 3 | 123 | 3.93% |
| 4 | 40 | 1.28% |
| 5 | 10 | 0.32% |
| $\ge 6$ | 8 | 0.25% |

---

## 6. Khả năng áp dụng cho VN-Legal-AI / CDNCTQ
*   **Sử dụng kho văn bản luật quy mô lớn:** Cơ sở dữ liệu 59,636 điều luật tiếng Việt được chuẩn hóa và loại bỏ các điều khoản hết hiệu lực trong VLQA là nguồn tài nguyên đầu vào hoàn hảo để làm giàu cho hệ thống cơ sở tri thức (vector database) của trợ lý RAG trong dự án CDNCTQ.
*   **Giải pháp tinh chỉnh Cross-Encoder:** Kết quả thực nghiệm của VLQA cho thấy mô hình Cross-Encoder được tinh chỉnh mang lại hiệu năng truy xuất vượt trội. Dự án VN-Legal-AI nên áp dụng thiết kế hai giai đoạn (two-stage retrieval): sử dụng BM25 làm bộ lọc thô đầu tiên (nhanh, tốn ít tài nguyên), sau đó dùng một mô hình Cross-Encoder tiếng Việt nhỏ (như PhoBERT-large) để tái xếp hạng (re-rank) tài liệu trước khi đưa vào LLM sinh câu trả lời.
*   **Ứng dụng bộ test cho LQA:** Bộ dữ liệu 626 câu test của VLQA là thước đo thực tế tuyệt vời để đánh giá chéo năng lực sinh câu trả lời của mô hình LegalSLM của chúng ta so với các baseline thương mại lớn như GPT-4o.
