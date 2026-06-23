# Phân tích Chi tiết: Overview of the LegalSLM Shared Task: Evaluating Legal Reasoning of Vietnamese Small Language Models

## 1. Tóm tắt cấp cao (High-level Summary)
Bài báo cung cấp tổng quan về nhiệm vụ chia sẻ (shared task) LegalSLM được tổ chức tại Hội thảo Xử lý Ngôn ngữ và Tiếng nói Tiếng Việt (VLSP 2025). Nhóm tác giả giới thiệu một bộ tiêu chuẩn đánh giá (benchmark) được thiết kế đặc biệt nhằm đo lường năng lực suy luận pháp lý của các mô hình ngôn ngữ nhỏ (SLMs) bằng tiếng Việt. Bộ dữ liệu benchmark gồm 1,950 mẫu được chia thành ba tác vụ chính: Trắc nghiệm Kiến thức Pháp luật (MC), Suy luận Tự nhiên Pháp luật (NLI), và Suy luận Tam đoạn luận Pháp luật (Syllogism). Tiêu chuẩn này được xây dựng thông qua quy trình bốn bước kết hợp thu thập câu hỏi từ diễn đàn pháp luật thực tế, tạo dữ liệu tổng hợp bằng GPT-4.1, kiểm chứng chéo bằng ba LLMs lớn, và hiệu đính bởi con người. Kết quả từ 10 đội tham gia cùng các mô hình cơ sở cho thấy mặc dù các mô hình đạt hiệu năng tốt trên các tác vụ lựa chọn trực tiếp (MC và NLI), nhưng suy luận tam đoạn luận nhiều bước (Syllogism) vẫn là một thách thức cực kỳ lớn. Nghiên cứu cũng chứng minh việc tiếp tục tiền huấn luyện (continued pretraining) trên văn bản pháp lý giúp cải thiện rõ rệt năng lực suy luận của các mô hình từ 4B tham số trở lên, trong khi các mô hình nhỏ hơn (1.7B) gặp khó khăn trong việc cân bằng giữa ghi nhớ thực tế và suy luận.

---

## 2. Tóm tắt chi tiết từng phần (Section Summaries)
*   **Introduction (Giới thiệu):** Tác giả nêu tầm quan trọng của việc trang bị năng lực xử lý văn bản pháp luật cho LLM nhằm tăng khả năng tiếp cận pháp luật của người dân. Dù thế giới có nhiều bộ dữ liệu như LegalBench hay LexGLUE, tài nguyên tiếng Việt vẫn rất hạn chế và có tính đặc thù ngôn ngữ cao. Phần này nhấn mạnh tính cấp thiết của việc xây dựng một benchmark pháp luật tiếng Việt toàn diện để đánh giá mô hình.
*   **LegalSLM Challenge (Thử thách LegalSLM):** Chi tiết hóa ba tác vụ trong thử thách: (1) Multiple-Choice Legal Knowledge đánh giá kiến thức thực tế với câu hỏi trắc nghiệm 4 đáp án; (2) Legal NLI đánh giá quan hệ logic giữa tiền đề pháp lý và giả thuyết với nhãn nhị phân "Có"/"Không"; (3) Legal Syllogism Reasoning yêu cầu sinh câu trả lời tự do từ một tình huống thực tế dựa trên các điều luật cho sẵn.
*   **Baseline Model (Mô hình cơ sở):** Nhóm tổ chức giới thiệu hai baseline là Qwen-3-1.7B và Qwen-3-4B được tiếp tục tiền huấn luyện (continued pretraining) trên 96k văn bản pháp luật Việt Nam. Quá trình huấn luyện diễn ra trong 1 epoch với batch size 256 và learning rate $10^{-5}$ mà không tinh chỉnh trực tiếp trên các tác vụ đích để đảm bảo tính khách quan của benchmark.
*   **Task Data (Dữ liệu tác vụ):** Trình bày quy trình 4 giai đoạn tạo lập dữ liệu: (1) Cào dữ liệu hỏi đáp từ trang `thuvienphapluat.vn` (2023-2025); (2) GPT-4.1 tạo sinh các phiên bản tác vụ; (3) Lọc chéo tự động bằng 3 mô hình DeepSeek, Gemini, GPT-4.1 để chỉ giữ các mẫu có sự đồng thuận tối thiểu 2/3 mô hình; (4) Hai chuyên gia con người kiểm duyệt tính nhất quán của trích dẫn và độ rõ ràng của câu trả lời.
*   **Evaluation (Đánh giá):** Thuyết minh các phương thức đánh giá. Tác vụ MC và NLI sử dụng độ chính xác (Accuracy). Tác vụ Syllogism sử dụng cơ chế LLM-as-a-judge để chấm điểm tự động theo thang 4 tiêu chí nhị phân (Mức độ liên quan, Trích dẫn luật, Độ chính xác suy luận, Độ chính xác kết luận), mỗi tiêu chí đạt yêu cầu đóng góp 0.25 điểm.
*   **Results & Conclusions (Kết quả & Kết luận):** Tổng hợp kết quả của 10 đội tham gia và 4 cấu hình baseline. Bosch@AI dẫn đầu với điểm trung bình 0.8108, đạt điểm NLI/MC rất cao (0.9700/0.9267) nhưng điểm Syllogism chỉ ở mức trung bình (0.5358), trong khi URAx đạt điểm Syllogism cao nhất (0.5767). Nhóm tác giả kết luận rằng việc continued pretraining cải thiện đáng kể năng lực suy luận của mô hình 4B nhưng lại gây bất ổn cho mô hình 1.7B.

---

## 3. Methodology Deep-Dive
### Khung hệ thống & Thuật toán
Hệ thống đánh giá của LegalSLM tích hợp ba dạng bài toán có độ khó tăng dần để kiểm tra toàn diện khả năng xử lý ngôn ngữ pháp lý. Điểm đặc sắc nhất là thuật toán chấm điểm cho tác vụ sinh văn bản tự do **Legal Syllogism Reasoning** (Suy luận tam đoạn luận pháp lý) thông qua cơ chế **LLM-as-a-judge**:

Mỗi câu trả lời do mô hình sinh ra được đánh giá dựa trên 4 tiêu chí nhị phân $c_{i,k} \in \{0, 1\}$ (với $k \in \{1, 2, 3, 4\}$):
1.  *Relevance (Liên quan)*: Câu trả lời có tập trung giải quyết câu hỏi đặt ra không.
2.  *Legal citation (Trích dẫn pháp lý)*: Trích dẫn đúng điều khoản/điểm luật cụ thể và chính xác.
3.  *Reasoning accuracy (Độ chính xác suy luận)*: Lập luận pháp lý hợp lệ dựa trên các điều khoản đã trích dẫn.
4.  *Conclusion accuracy (Độ chính xác kết luận)*: Kết luận logic rút ra đúng từ tiền đề và các sự kiện thực tế.

Điểm số của mẫu thứ $i$ được tính bằng trung bình cộng 4 tiêu chí: $s_i = \frac{1}{4} \sum_{k=1}^{4} c_{i,k}$. Điểm tổng thể của mô hình trên toàn bộ $N$ mẫu là:
$$\bar{s} = \frac{1}{4N} \sum_{i=1}^{N} \sum_{k=1}^{4} c_{i,k}$$

### Dữ liệu, Thiết lập & Metrics
*   **Dữ liệu:** Tổng cộng 1,950 mẫu (800 trắc nghiệm MC, 850 cặp NLI, và 300 câu hỏi Tam đoạn luận Syllogism).
*   **Baselines:** Qwen-3-1.7B Base, Qwen-3-4B Base, Qwen-3-1.7B-ours (sau tiếp tục tiền huấn luyện), Qwen-3-4B-ours (sau tiếp tục tiền huấn luyện), và các giải pháp tinh chỉnh của 10 đội thi.
*   **Metrics:** Accuracy (%) cho MC và NLI; Điểm đánh giá kết hợp dựa trên rubric (Rubric-based score) cho Syllogism.

### 3 Giả định kỹ thuật quan trọng
1.  **Tính chính xác của LLM-as-a-judge:** Giả định rằng giám khảo LLM (ở đây sử dụng GPT-4.1) có khả năng đánh giá công bằng, chính xác và không bị thiên kiến (bias) đối với các câu trả lời tự luận tiếng Việt so với chuyên gia luật là con người.
2.  **Độ tin cậy của dữ liệu tổng hợp (Synthetic Data):** Giả định quy trình lọc tự động bằng 3 LLM phối hợp cùng 2 annotator con người đủ chặt chẽ để loại bỏ hoàn toàn các lỗi ảo giác (hallucination) và sai sót pháp lý từ dữ liệu do GPT-4.1 sinh ra ở giai đoạn 2.
3.  **Tác động tích cực của Continued Pretraining:** Giả định việc tiếp tục tiền huấn luyện không giám sát trên 96k văn bản pháp luật tiếng Việt (mục tiêu dự đoán token tiếp theo) sẽ tự động chuyển hóa thành khả năng suy luận logic nhiều bước ở hạ nguồn mà không cần căn chỉnh (alignment) chuyên biệt.

---

## 4. Phản biện học thuật (Brutally Honest Review)

### Summary
Paper trình bày thiết kế và kết quả của thử thách LegalSLM tại VLSP 2025. Thử thách này giới thiệu một benchmark tiếng Việt gồm 1,950 câu hỏi phân bổ trên 3 tác vụ từ dễ đến khó để đánh giá mô hình ngôn ngữ nhỏ. Nhóm tác giả claim rằng dữ liệu được kiểm duyệt chặt chẽ qua quy trình lọc LLM và con người, đồng thời cung cấp các kết quả thực nghiệm chứng minh continued pretraining giúp nâng cao năng lực suy luận pháp luật trên các mô hình quy mô ~4B tham số.

### Strengths
1.  **Quy trình tạo dữ liệu chất lượng cao:** Việc kết hợp dữ liệu thực tế từ diễn đàn thuvienphapluat.vn, tạo sinh bằng LLM, lọc chéo đồng thuận (minimum 2-of-3 LLM agreement) và kiểm duyệt cuối bởi con người (human-in-the-loop) là một thiết kế rất chuẩn mực để tạo benchmark nhanh nhưng vẫn đảm bảo độ chính xác.
2.  **Thiết kế tác vụ tiệm cận thực tế:** Tác vụ Syllogism (Tam đoạn luận) yêu cầu cấu trúc lập luận chặt chẽ (Tiền đề lớn -> Tiền đề nhỏ -> Kết luận) rất sát với tư duy xử lý vụ việc của luật sư thực tế, vượt trội hơn các tác vụ QA thông thường.
3.  **Đánh giá đa dạng kích thước SLM:** Thử nghiệm trực tiếp trên phân khúc mô hình nhỏ (1.7B và 4B) giúp định vị rõ khả năng triển khai thực tế của các mô hình này trên thiết bị cá nhân hoặc máy chủ cấu hình thấp.

### Weaknesses
1.  **Thiếu kiểm chứng độ tin cậy của LLM Judge:** Tác giả sử dụng LLM làm giám khảo cho tác vụ Syllogism nhưng hoàn toàn không cung cấp nghiên cứu tương quan (human-LLM agreement correlation) để chứng minh điểm số từ LLM Judge khớp với đánh giá của chuyên gia con người đến mức nào.
2.  **Kích thước tập dữ liệu Syllogism quá nhỏ:** Tác vụ Syllogism chỉ có 300 mẫu. Với một tập dữ liệu sinh tự do có phân bố kết quả rộng, kích thước 300 mẫu là quá ít để đảm bảo tính ổn định thống kê và rất dễ dẫn đến nhiễu đánh giá từ LLM Judge.
3.  **Baseline cực kỳ yếu và thiếu thông tin huấn luyện:** Các baseline "ours" của BTC chỉ được huấn luyện 1 epoch trên 96k tài liệu mà không qua bất kỳ bước tinh chỉnh chỉ thị (SFT) nào trên các định dạng MC, NLI, Syllogism. Việc đánh giá zero-shot các mô hình chỉ mới qua continued pretraining (chưa học cách trả lời theo format) so với các mô hình của các đội tham gia (vốn đã được tối ưu hóa SFT) là một sự so sánh thiếu công bằng và không mang nhiều giá trị khoa học.
4.  **Mâu thuẫn hiệu năng của mô hình 1.7B:** Kết quả trong Bảng 3 cho thấy Qwen-3-1.7B sau khi tiếp tục pretrain thì điểm Syllogism bị giảm mạnh từ 0.468 xuống 0.384 (-8.40%). Tác giả chỉ nhận định sơ sài là "smaller models struggle to balance factual recall and reasoning" mà không đưa ra phân tích sâu hơn về việc liệu pretraining dữ liệu luật có gây nhiễu biểu diễn hay sụp đổ phân bố của mô hình nhỏ hay không.
5.  **Thiếu minh bạch về cấu trúc các mô hình tham gia:** Paper không mô tả chi tiết phương pháp tinh chỉnh, kiến trúc cụ thể hay các kỹ thuật căn chỉnh (như LoRA, RAG, Prompt Engineering) mà 10 đội tham gia đã sử dụng. Điều này làm giảm giá trị của báo cáo như một tài liệu tham khảo công nghệ cho cộng đồng.

### Questions for Authors
1.  Độ tương đồng (agreement rate) giữa giám khảo LLM và các chuyên gia luật con người trên 300 mẫu Syllogism là bao nhiêu? Bạn có tính chỉ số Cohen's Kappa không?
2.  Tại sao các mô hình baseline tiếp tục pretrain không được tinh chỉnh hướng dẫn (SFT) nhẹ để làm quen với cấu trúc đầu ra (như chọn nhãn {0,1,2,3} hay {Có, Không}) trước khi đo đạc Accuracy?
3.  Hiện tượng suy giảm điểm Syllogism của mô hình Qwen-3-1.7B (-8.40%) có phải do lỗi quá khớp (overfitting) với tập pretrain hay do sự suy giảm năng lực ngôn ngữ chung (alignment tax)?

### Recommendation
**Weak Accept.** Mặc dù còn thiếu các phân tích sâu về LLM-as-a-judge và baseline chưa được căn chỉnh SFT tốt, đây vẫn là một công trình rất có giá trị đối với cộng đồng NLP Việt Nam. Việc công bố một benchmark pháp luật tiếng Việt có kiểm duyệt của con người với cấu trúc suy luận tam đoạn luận là một đóng góp thiết thực cho mảng VN-Legal-AI.

---

## 5. Data & Metrics View Designer

### Tóm tắt dữ liệu & Baselines
*   **Datasets & Tasks:**
    *   Multiple-Choice Legal Knowledge (MC): 800 mẫu, nhãn $\{0,1,2,3\}$. Metric: Accuracy.
    *   Legal Natural Language Inference (NLI): 850 mẫu, nhãn $\{\text{"Có"}, \text{"Không"}\}$. Metric: Accuracy.
    *   Legal Syllogism Reasoning: 300 mẫu, sinh tự luận (4 tiêu chí chấm điểm). Metric: Rubric-based average score.
    *   *Tổng kích thước:* 1,950 mẫu, dữ liệu giai đoạn 2023 - 2025.
*   **Baselines:** Qwen-3-1.7B/4B Base, Qwen-3-1.7B/4B Continual Pretrained (ours), cùng giải pháp của 10 đội tham gia (Bosch@AI, MinLegal, URAx, Innovation-LLM, LICTU, NHK, PSLV-Warrior, Abe, NLPhi, Nguyen Quang Thao).

### Đề xuất trực quan hóa (Charts)
1.  **Grouped Bar Chart (Hiệu năng các đội dẫn đầu trên 3 tác vụ):**
    *   *Trục X:* Các đội thi/mô hình tiêu biểu (Bosch@AI, MinLegal, URAx, Qwen-3-4B ours)
    *   *Trục Y:* Score (%)
    *   *Nhóm (Series):* vi-law-nli, vi-law-qa, vilaw-syllo
    *   *Mục đích:* So sánh trực quan sự phân hóa hiệu năng; làm nổi bật việc các mô hình đạt điểm rất cao ở NLI/QA nhưng lại tụt dốc ở Syllogism.
2.  **Scatter Plot (Sự đánh đổi giữa Suy luận tam đoạn luận và Lựa chọn đáp án):**
    *   *Trục X:* Điểm trung bình của hai tác vụ lựa chọn (NLI & QA)
    *   *Trục Y:* Điểm tác vụ tự luận (Syllogism)
    *   *Nhóm (Series):* Các đội tham gia và baseline
    *   *Mục đích:* Trực quan hóa nhận định của tác giả về sự đánh đổi (trade-off) giữa năng lực suy luận suy diễn (deductive reasoning) và khả năng trích xuất thông tin trực tiếp.
3.  **Bar Chart (Hiệu quả của Continued Pretraining trên các kích thước tham số):**
    *   *Trục X:* Cấu hình mô hình (Qwen-3-1.7B vs Qwen-3-4B)
    *   *Trục Y:* Điểm trung bình (Avg Score) hoặc điểm Syllogism
    *   *Nhóm (Series):* Base (Original) vs Continual Pretrained (Ours)
    *   *Mục đích:* Thể hiện rõ ràng tác động trái chiều của pretraining dữ liệu luật lên mô hình kích thước nhỏ (1.7B - giảm hiệu năng) so với mô hình lớn hơn (4B - tăng hiệu năng).

### Thiết kế bảng tổng hợp đề xuất
Bảng nên tổng hợp phân loại theo nguồn gốc mô hình (Baseline của BTC vs Mô hình của các đội thi):
`[Mô hình / Đội thi | vi-law-nli (Acc) | vi-law-qa (Acc) | vilaw-syllo (Rubric) | Average | Loại hình SFT/Pretrain]`
*Highlight:* In đậm kết quả cao nhất ở mỗi cột, thêm dòng biểu thị mức chênh lệch trung bình giữa nhóm Baseline và nhóm Đội thi để thấy khoảng cách công nghệ.

### Số liệu thô trích xuất từ Paper

**Bảng A: Kết quả xếp hạng của các đội tham gia trên Leaderboard**

| Hạng | Đội thi | vi-law-nli | vi-law-qa | vilaw-syllo | Avg |
| :---: | :--- | :---: | :---: | :---: | :---: |
| 1 | Bosch@AI Team | 0.9700 | 0.9267 | 0.5358 | 0.8108 |
| 2 | MinLegal | 0.9800 | 0.8733 | 0.5308 | 0.7947 |
| 3 | URAx | 0.9450 | 0.8333 | **0.5767** | 0.7850 |
| 4 | Innovation-LLM | 0.9567 | 0.8367 | 0.5417 | 0.7784 |
| 5 | LICTU | 0.8467 | 0.8067 | 0.5375 | 0.7303 |
| 6 | NHK | 0.9333 | 0.8683 | 0.3275 | 0.7097 |
| 7 | PSLV-Warrior | 0.8517 | 0.7483 | 0.5250 | 0.7083 |
| 8 | Abe | 0.8200 | 0.8400 | 0.2875 | 0.6492 |
| 9 | NLPhi | 0.6517 | 0.8150 | 0.4792 | 0.6486 |
| 10 | Nguyen Quang Thao | 0.5816 | 0.8217 | 0.3800 | 0.5944 |

**Bảng B: So sánh hiệu năng của các mô hình Baseline**

| Hạng | Mô hình baseline | vi-law-nli | vi-law-qa | vilaw-syllo | Avg |
| :---: | :--- | :---: | :---: | :---: | :---: |
| 1 | Qwen 3 - 4B (ours) | 0.9600 | 0.8500 | **0.5950** | **0.8010** |
| 2 | Qwen 3 - 4B - Base (original) | **0.9700** | 0.8210 | 0.5160 | 0.7690 |
| 3 | Qwen 3 - 1.7B - Base (original) | 0.5617 | **0.7933** | 0.4680 | 0.6076 |
| 4 | Qwen 3 - 1.7B (ours) | 0.6450 | 0.7867 | 0.3840 | 0.6050 |

---

## 6. Khả năng áp dụng cho VN-Legal-AI / CDNCTQ
*   **Sử dụng trực tiếp bộ Benchmark:** Đây là tài nguyên tiếng Việt vô cùng quý giá cho dự án. Chúng ta có thể dùng 1,950 mẫu này (đặc biệt là 300 mẫu Syllogism) làm tập đánh giá cục bộ (local validation/test set) để đo lường năng lực của mô hình LegalSLM mà chúng ta đang phát triển.
*   **Nhận thức về giới hạn kích thước mô hình:** Kết quả pretraining của BTC cảnh báo rằng các mô hình quá nhỏ (dưới 3B tham số, ví dụ Qwen-3-1.7B) khó có thể tự hội tụ năng lực suy luận phức tạp chỉ bằng pretraining dữ liệu luật. Đối với CDNCTQ, nếu mục tiêu là triển khai mô hình hoạt động ổn định trên thiết bị cấu hình thấp, chúng ta nên ưu tiên chọn kích thước tối thiểu từ 3B đến 4B (như Qwen-3-4B hoặc Phi-3-medium) thay vì cố gắng ép các mô hình 1.5B - 1.7B.
*   **Thiết lập quy trình tinh chỉnh SFT sau Pretrain:** Để tránh việc mô hình sau pretrain bị tụt điểm do lệch định dạng trả lời (như trường hợp Qwen-3-1.7B), dự án bắt buộc phải thiết kế một giai đoạn tinh chỉnh hướng dẫn (SFT) ngắn với các dữ liệu chỉ thị định dạng đầu ra chuẩn trước khi đưa vào đánh giá benchmark.
