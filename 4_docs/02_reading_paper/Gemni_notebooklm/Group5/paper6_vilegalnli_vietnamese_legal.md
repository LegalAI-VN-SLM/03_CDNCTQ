# Phân tích Chi tiết: ViLegalNLI: Natural Language Inference for Vietnamese Legal Texts

## 1. Tóm tắt cấp cao (High-level Summary)
Bài báo giới thiệu **ViLegalNLI**, bộ dữ liệu Suy luận Ngôn ngữ Tự nhiên (Natural Language Inference - NLI) quy mô lớn đầu tiên dành riêng cho lĩnh vực pháp luật bằng tiếng Việt. Bộ dữ liệu chứa 42,012 cặp Tiền đề - Giả thuyết (Premise - Hypothesis) được xây dựng từ các văn bản quy phạm pháp luật đang có hiệu lực tại Việt Nam, gán nhãn nhị phân suy luận (Entailment và Non-entailment). Nhóm tác giả đề xuất một quy trình xây dựng bán tự động tích hợp: trích xuất tiền đề bằng luật luật lệ, tạo giả thuyết bằng Gemini-2.5 Flash, lọc nhãn chéo bằng ba LLMs lớn (GPT-4o, DeepSeek-R1, LLaMA-4 Scout), và loại bỏ định kiến từ vựng (artifact mitigation) thông qua điểm PMI (Pointwise Mutual Information). Thực nghiệm trên nhiều nhóm mô hình (đa ngôn ngữ, đơn ngôn ngữ tiếng Việt như PhoBERT/CafeBERT, và LLMs) chỉ ra rằng phương pháp few-shot prompting với LLMs lớn (như Qwen2.5) đạt hiệu năng tốt nhất với độ chính xác 90.72%, đồng thời chỉ ra tính phức tạp và thách thức lớn trong việc chuyển giao năng lực suy luận giữa các lĩnh vực luật khác nhau.

---

## 2. Tóm tắt chi tiết từng phần (Section Summaries)
*   **Introduction (Giới thiệu):** Định nghĩa tác vụ NLI và nêu tầm quan trọng của nó trong việc xác minh tính hợp lệ của các luận điểm pháp lý dựa trên luật định. Chỉ ra sự thiếu hụt nghiêm trọng các tài nguyên gán nhãn NLI cho tiếng Việt pháp luật, trong khi các bộ dữ liệu tiếng Anh (như ContractNLI) không tương thích với cấu trúc luật Việt Nam. Tác giả giới thiệu ViLegalNLI nhằm lấp đầy khoảng trống này.
*   **Related Works (Nghiên cứu liên quan):** Điểm qua lịch sử phát triển của các bộ dữ liệu NLI chung (SNLI, MultiNLI) và chuyên ngành (ContractNLI, LawngNLI). Hệ thống hóa các bộ dữ liệu NLI bằng tiếng Việt (ViNLI, ViANLI, ViHealthNLI) để làm nổi bật tính duy nhất của ViLegalNLI khi là bộ dữ liệu pháp lý tiếng Việt đầu tiên được tạo từ văn bản luật chính thức. Phân loại các mô hình NLI thành 4 nhóm kiến trúc chính.
*   **Task Definition (Định nghĩa tác vụ):** Định nghĩa toán học bài toán Legal NLI tiếng Việt dưới dạng phân loại nhị phân. Đầu vào là một cặp câu $(p, h)$ trong đó Premise ($p$) là văn bản luật chính thức và Hypothesis ($h$) là một phát biểu pháp lý cần kiểm chứng. Đầu ra là nhãn $y \in \{0, 1\}$ tương ứng với Entailment (1 - giả thuyết được suy luận logic từ luật) hoặc Non-entailment (0 - giả thuyết mâu thuẫn hoặc không đủ cơ sở pháp lý để suy luận).
*   **Dataset (Bộ dữ liệu ViLegalNLI):** Trình bày quy trình 7 bước tạo lập dữ liệu. Từ 168 văn bản luật đang có hiệu lực (tính đến tháng 2/2025), nhóm tác giả trích xuất 20,860 tiền đề. Sử dụng Gemini-2.5 Flash để sinh giả thuyết theo 10 quy tắc thiết lập sẵn. Điểm nhấn là bước giảm thiểu lỗi từ vựng (artifact mitigation) bằng cách tính PMI để lọc bỏ các từ "báo hiệu nhãn" (như từ "khi" báo hiệu Entailment, "không cần" báo hiệu Non-entailment) và viết lại giả thuyết thông qua các từ đồng nghĩa.
*   **Methodology & Experiments (Phương pháp luận & Thực nghiệm):** Trình bày thiết lập thực nghiệm. Huấn luyện các mô hình trong 5 epochs sử dụng Adam optimizer với learning rate $10^{-5}$. Thực hiện so sánh hiệu năng của 13 cấu hình mô hình thuộc các nhóm: đa ngôn ngữ (XLM-R, mBERT, InfoXLM), monolingual tiếng Việt (PhoBERT, viBERT, CafeBERT), DeBERTa V3 cải tiến, và LLMs (Gemma, Qwen) dưới các dạng zero-shot, few-shot và tinh chỉnh tham số.

---

## 3. Methodology Deep-Dive
### Khung hệ thống & Thuật toán
Quy trình xây dựng và chuẩn hóa bộ dữ liệu ViLegalNLI bao gồm các thành phần công nghệ chính:

1.  **Trích xuất Tiền đề tự động (Premise Extraction):**
    Sử dụng các biểu thức chính quy (regular expressions) để quét cấu trúc văn bản luật, nhận dạng các từ khóa chuyển tiếp (như "sau đây", "như sau", "bao gồm", "nghiêm cấm") để tách các điều, khoản, điểm thành các câu tiền đề độc lập kèm siêu dữ liệu (metadata) truy vết nguồn gốc pháp lý.
2.  **Tạo giả thuyết và Lọc đồng thuận (Consensus Filtering):**
    *   *Sinh giả thuyết:* Gemini-2.5 Flash sinh các câu giả thuyết dựa trên 10 quy tắc biến đổi ngữ nghĩa (ví dụ: chuyển chủ động/bị động, thay thế thực thể bằng thuật ngữ tương đương, đảo ngược điều kiện).
    *   *Lọc chéo:* Ba mô hình độc lập (GPT-4o, DeepSeek-R1, LLaMA-4 Scout) gán nhãn lại cho toàn bộ tập dữ liệu. Mẫu dữ liệu chỉ được giữ lại nếu nhãn của ít nhất 2 trong 3 mô hình này trùng khớp với nhãn gốc.
3.  **Giảm thiểu lỗi từ vựng (Artifact Mitigation):**
    Tính chỉ số PMI (Pointwise Mutual Information) giữa từ $w$ và nhãn $y$:
    $$PMI(w, y) = \log \frac{P(w, y)}{P(w)P(y)}$$
    Nếu một từ có điểm PMI quá cao với một nhãn (ví dụ "không cần" có PMI = 0.95 với nhãn Non-entailment), nó sẽ bị coi là trigger-word gây nhiễu cho mô hình. Hệ thống sẽ ép buộc LLM viết lại giả thuyết bằng cách thay thế các trigger-words này bằng các cụm từ đồng nghĩa (ví dụ: thay "không cần" bằng "không nhất thiết phải") để triệt tiêu các phím tắt học tập (shortcut learning) của mô hình.

### Dữ liệu, Thiết lập & Metrics
*   **Cấu trúc phân bổ dữ liệu:** Tổng cộng 42,012 mẫu (Train: 34,121; Dev: 4,160; Test: 3,731). Chia dữ liệu dựa trên Premise để tránh rò rỉ thông tin (data leakage). Độ dài trung bình của Premise là 43.08 tokens, của Hypothesis là 43.08 tokens.
*   **Metrics:** Accuracy (%) và Macro F1-score (%) để đánh giá hiệu năng phân loại nhị phân.

### 3 Giả định kỹ thuật quan trọng
1.  **Tính độc lập của các điểm luật đơn lẻ:** Giả định rằng một điểm hoặc khoản luật khi được trích xuất ra khỏi ngữ cảnh của toàn bộ điều luật vẫn giữ nguyên vẹn giá trị ngữ nghĩa pháp lý để làm tiền đề suy luận độc lập.
2.  **Đồng thuận LLM tương đương chuyên gia:** Giả định sự đồng thuận nhãn giữa ba mô hình ngôn ngữ lớn (GPT-4o, DeepSeek-R1, LLaMA-4 Scout) có độ tin cậy tương đương với việc dán nhãn thủ công của các chuyên gia luật con người.
3.  **PMI phản ánh chính xác lỗi hệ thống:** Giả định rằng mối tương quan từ vựng - nhãn được đo lường bằng PMI là nguồn gốc chính gây ra hiện tượng "học vẹt" (shortcut learning) của các mô hình Transformer, và việc loại bỏ các từ này sẽ ép buộc mô hình phải suy luận ngữ nghĩa thực sự.

---

## 4. Phản biện học thuật (Brutally Honest Review)

### Summary
Paper đề xuất bộ dữ liệu ViLegalNLI gồm 42,012 cặp tiền đề - giả thuyết tiếng Việt pháp lý để đánh giá năng lực suy luận ngôn ngữ tự nhiên. Tác giả áp dụng quy trình bán tự động kết hợp LLM sinh dữ liệu, lọc đồng thuận đa mô hình và kiểm soát độ khó bằng cách giảm thiểu nhiễu từ vựng PMI. Thực nghiệm so sánh rộng rãi giữa các bộ mã hóa Transformer và các LLM vài lượt (few-shot) chứng minh tính khả thi của dữ liệu và chỉ ra các giới hạn suy luận pháp lý hiện tại.

### Strengths
1.  **Quy trình Artifact Mitigation rất bài bản:** Đây là điểm sáng nhất của paper. Việc nhận diện trigger expressions bằng PMI và ép LLM viết lại bằng các từ đồng nghĩa (như thay "không cần" bằng "không nhất thiết") giúp bộ dữ liệu sạch, ngăn chặn mô hình đạt điểm cao nhờ học vẹt từ vựng.
2.  **Quy mô dữ liệu lớn và cấu trúc chặt chẽ:** 42,012 mẫu là quy mô rất lớn đối với dữ liệu pháp lý tiếng Việt. Thiết kế chia split dựa trên Premise để tránh data leak là rất chuẩn mực khoa học.
3.  **Thực nghiệm bao quát, hệ thống:** Việc so sánh cả các mô hình encoder nhỏ (PhoBERT, CafeBERT) đã được tinh chỉnh với các LLMs lớn chạy few-shot cung cấp cái nhìn thực tế về chi phí và hiệu năng khi ứng dụng thực tế.

### Weaknesses
1.  **Lạm dụng hoàn toàn nhãn của LLM (LLM-only annotation):** Mặc dù tác giả mô tả đây là quy trình "bán tự động" (semi-automatic), nhưng thực tế 100% việc dán nhãn và tạo giả thuyết đều do LLMs thực hiện (Gemini sinh, GPT-4o/DeepSeek/LLaMA duyệt). Paper hoàn toàn không có sự tham gia hiệu đính trực tiếp của các luật sư hay chuyên gia con người (human-in-the-loop) để kiểm tra tính đúng đắn pháp lý của các giả thuyết được tạo ra. điều này cực kỳ nguy hiểm trong miền pháp luật.
2.  **Fleiss' Kappa cao bất thường ở các vòng cuối:** Điểm Fleiss' Kappa đạt tới 0.87 ở vòng 6 (chỉ ra sự đồng thuận gần như tuyệt đối giữa các mô hình). Tuy nhiên, vì cả 4 mô hình tham gia (Gemini, GPT-4o, DeepSeek, LLaMA) đều được huấn luyện trên các tập dữ liệu tương đồng và có xu hướng thiên lệch (bias) giống nhau, sự đồng thuận cao này có thể chỉ phản ánh "sự đồng thuận sai lệch" của AI (AI consensus bias) chứ không phải độ chính xác pháp lý thực tế.
3.  **Thiếu đánh giá chi tiết về lỗi phân loại sai (Error Analysis):** Paper trình bày kết quả số liệu rất đẹp nhưng thiếu một phần phân tích định tính chi tiết về các mẫu mà mô hình đoán sai. Người đọc không thể biết các mô hình thường thất bại ở dạng suy luận nào (ví dụ: phủ định kép hay loại trừ điều kiện).
4.  **Sự sụt giảm hiệu năng khi cross-domain chưa được làm rõ:** Tác giả có nhắc đến việc "cross-domain evaluations reveal challenges of generalizing legal inference", nhưng không cung cấp một ma trận thử nghiệm chéo (cross-domain matrix) chi tiết giữa 27 lĩnh vực luật để chứng minh nhận định này.
5.  **Thiếu benchmark với con người (Human baseline):** Paper không đo lường xem con người (ví dụ: sinh viên luật hoặc người dân thường) đạt độ chính xác bao nhiêu % trên tập test của ViLegalNLI để làm mốc so sánh (human ceiling) với AI.

### Questions for Authors
1.  Tại sao bạn không đưa một tỷ lệ nhỏ mẫu (ví dụ 5% tập test) cho các luật sư con người đánh giá thủ công để kiểm chứng độ chính xác pháp lý của nhãn đồng thuận LLM?
2.  Quy trình lọc PMI có làm giảm tính tự nhiên của ngôn ngữ pháp luật không? Có trường hợp nào sau khi paraphrase, giả thuyết bị thay đổi luôn nhãn logic ban đầu không?
3.  Bạn có thể cung cấp ma trận chi tiết về hiệu năng cross-domain (huấn luyện trên miền luật này và test trên miền luật khác) để làm rõ mức độ suy giảm hiệu năng không?

### Recommendation
**Accept.** Mặc dù thiếu bước kiểm duyệt của con người đối với nhãn LLM, phương pháp khử nhiễu từ vựng bằng PMI là một đóng góp kỹ thuật rất tốt. Quy mô và độ sạch của ViLegalNLI đủ để làm benchmark tiêu chuẩn cho các nghiên cứu xử lý ngôn ngữ pháp luật tiếng Việt trong nhiều năm tới.

---

## 5. Data & Metrics View Designer

### Tóm tắt dữ liệu & Baselines
*   **Datasets:** ViLegalNLI (42,012 mẫu, chia Train/Dev/Test = 34,121 / 4,160 / 3,731). Gồm 27 miền luật Việt Nam.
*   **Baselines:**
    *   *Multilingual:* XLM-R (base/large), mBERT, InfoXLM (base/large).
    *   *Vietnamese:* PhoBERT (base/large), viBERT, CafeBERT.
    *   *Architectural:* DeBERTa V3 (base/large).
    *   *LLMs:* Gemma-3 (zero-shot/few-shot), Qwen2.5 (zero-shot/few-shot), Gemma-2 (Fine-tuned).
*   **Metrics:** Accuracy (%), Macro F1-score (%).

### Đề xuất trực quan hóa (Charts)
1.  **Grouped Bar Chart (So sánh hiệu năng của Encoder Models vs. Few-shot LLMs):**
    *   *Trục X:* Các mô hình tiêu biểu (PhoBERT-large, CafeBERT, InfoXLM-large, Gemma-3 few-shot, Qwen2.5 few-shot)
    *   *Trục Y:* Score (%)
    *   *Nhóm (Series):* Accuracy vs. F1-score trên tập Test.
    *   *Mục đích:* Nổi bật khoảng cách hiệu năng giữa nhóm encoder tinh chỉnh truyền thống và nhóm LLM few-shot.
2.  **Scatter Plot (Lexical Overlap vs. Model Prediction Error):**
    *   *Trục X:* Jaccard Similarity (%) hoặc điểm overlap từ vựng của cặp câu.
    *   *Trục Y:* Tỷ lệ đoán sai của mô hình CafeBERT (%).
    *   *Nhóm (Series):* Entailment vs. Non-entailment.
    *   *Mục đích:* Trực quan hóa kết quả phân tích xem mô hình có xu hướng đoán sai nhiều hơn ở các mẫu có overlap từ vựng thấp hay không.
3.  **Horizontal Bar Chart (Fleiss' Kappa qua các vòng tối ưu hóa Prompt):**
    *   *Trục Y:* Các vòng tinh chỉnh (Round 1 đến Round 6)
    *   *Trục X:* Fleiss' Kappa score
    *   *Mục đích:* Thể hiện trực quan tiến trình cải thiện độ đồng thuận nhãn giữa các mô hình thông qua tối ưu hóa prompt.

### Thiết kế bảng tổng hợp đề xuất
Bảng nên nhóm theo phân loại kiến trúc mô hình:
`[Kiến trúc mô hình | Tên mô hình | Dev Acc (%) | Dev F1 (%) | Test Acc (%) | Test F1 (%) | Số lượng tham số (Parameters)]`
*Highlight:* Bold cho best score trong từng nhóm kiến trúc (Multilingual, Monolingual, LLM).

### Số liệu thô trích xuất từ Paper

**Bảng A: Kết quả thực nghiệm của các mô hình trên ViLegalNLI**

| Nhóm kiến trúc | Mô hình | Dev Accuracy (%) | Dev F1-score (%) | Test Accuracy (%) | Test F1-score (%) |
| :--- | :--- | :---: | :---: | :---: | :---: |
| **Multilingual Encoder** | mBERT | 80.41 | 79.83 | 79.36 | 78.75 |
| | XLM-R (base) | 84.34 | 84.18 | 83.65 | 83.43 |
| | XLM-R (large) | 86.98 | 86.81 | 86.37 | 86.19 |
| | InfoXLM (base) | 83.96 | 83.65 | 83.25 | 82.85 |
| | **InfoXLM (large)** | **89.24** | **89.15** | **87.98** | **87.85** |
| **Vietnamese Monolingual** | viBERT | 75.00 | 74.24 | 74.53 | 73.54 |
| | PhoBERT (base) | 85.14 | 84.84 | 84.46 | 84.13 |
| | PhoBERT (large) | 86.13 | 85.97 | 84.98 | 84.78 |
| | **CafeBERT** | **87.56** | **87.44** | **87.49** | **87.36** |
| **Architectural Improved** | DeBERTa V3 (base) | 76.15 | 75.56 | 76.01 | 75.52 |
| | **DeBERTa V3 (large)** | **86.16** | **85.98** | **85.35** | **85.18** |
| **Large Language Models** | Gemma-3 (Zero-shot) | 81.56 | 81.52 | 80.76 | 80.72 |
| | Qwen2.5 (Zero-shot) | 80.65 | 79.15 | 79.62 | 77.83 |
| | Gemma-2 (Fine-tuned) | 84.11 | 82.46 | 83.74 | 81.80 |
| | Gemma-3 (Few-shot) | 89.49 | 89.49 | 88.92 | 88.86 |
| | **Qwen2.5 (Few-shot)** | **90.89** | **90.78** | **90.72** | **90.64** |

---

## 6. Khả năng áp dụng cho VN-Legal-AI / CDNCTQ
*   **Ứng dụng mô hình NLI để xác thực câu trả lời (Fact-checking):** Trong hệ thống tư vấn pháp luật VN-Legal-AI, mô hình NLI được sử dụng ở giai đoạn hậu xử lý (post-processing). Khi LLM sinh ra câu trả lời cho người dùng kèm các điều luật được RAG truy xuất, chúng ta có thể dùng một mô hình NLI (như CafeBERT tinh chỉnh trên ViLegalNLI) để kiểm tra xem câu phát biểu trong câu trả lời (Hypothesis) có thực sự được suy luận hợp lệ từ điều luật gốc (Premise) hay không. Nếu nhãn trả về là Non-entailment, hệ thống sẽ cảnh báo lỗi ảo giác pháp lý.
*   **Áp dụng quy trình loại bỏ lỗi từ vựng (Artifact Mitigation):** Khi dự án tự xây dựng dữ liệu tinh chỉnh hướng dẫn (SFT) hoặc RAG bằng các mô hình sinh tự động, bắt buộc phải áp dụng thuật toán lọc PMI như paper này đề xuất để tránh việc mô hình LegalSLM của chúng ta học các mẫu từ vựng bề nổi (như phím tắt "chỉ cần", "không cần") thay vì học cách lập luận logic pháp lý thực tế.
*   **Sử dụng Qwen2.5 few-shot làm baseline mạnh:** Kết quả của paper chỉ ra Qwen2.5 few-shot đạt hiệu năng cực cao (90.72% test acc). Chúng ta có thể cấu hình prompt few-shot cho Qwen2.5 trong hệ thống để làm module thẩm định suy luận pháp lý một cách nhanh chóng mà không cần tốn chi phí huấn luyện lại các mô hình lớn.
