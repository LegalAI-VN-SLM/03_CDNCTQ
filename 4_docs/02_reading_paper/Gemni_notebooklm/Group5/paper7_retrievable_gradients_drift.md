# Phân tích Chi tiết: Retrievable Gradients: Continual Post-Training Without Cumulative Weight Drift

## 1. Tóm tắt cấp cao (High-level Summary)
Bài báo giải quyết thế lưỡng nan giữa Tinh chỉnh sau huấn luyện liên tục (Continual Post-Training - CPT) và Truy xuất tăng cường thế sinh (Retrieval-Augmented Generation - RAG). CPT giúp mô hình đồng hóa tri thức mới trực tiếp vào trọng số nhưng làm tích tụ độ trôi tham số (weight drift) dẫn đến quên thảm khốc, trong khi RAG bảo toàn được năng lực mô hình nhưng tri thức nằm ngoài tham số và bị giới hạn bởi context window. Nhóm tác giả đề xuất **REGRAD** (Retrievable Gradients), một phương pháp coi các gradient cụ thể của tài liệu như các đơn vị tri thức có thể truy xuất. REGRAD tính toán trước gradient của từng tài liệu ngoại tuyến (offline) trên không gian LoRA và lưu vào một "Ngân hàng Gradient" (Gradient Bank). Khi chạy suy luận (inference), mô hình truy xuất các gradient liên quan đến câu hỏi, tạm thời cộng dồn và cập nhật vào trọng số để trả lời, sau đó hủy bỏ cập nhật này để khôi phục mô hình gốc. Để đảm bảo gradient học không giám sát có khả năng trả lời câu hỏi thực tế, tác giả giới thiệu thuật toán meta-learning hai cấp (bi-level meta-learning) để tối ưu hóa việc tạo hướng gradient nhằm mục tiêu suy luận nghiệp vụ thay vì tái cấu trúc từ vựng bề nổi.

---

## 2. Tóm tắt chi tiết từng phần (Section Summaries)
*   **Introduction (Giới thiệu):** Đặt vấn đề về sự bất ổn định của CPT do việc ghi đè liên tục lên tham số chung gây trôi trọng số toàn cục. RAG là giải pháp thay thế tốt nhưng hoạt động ngoài tham số (in-context), không tận dụng được cơ chế lưu trữ tri thức sâu của LLM. Tác giả giới thiệu REGRAD như một cách dung hòa: cập nhật tham số tạm thời tại thời điểm suy luận bằng các gradient được tính sẵn và lưu trữ.
*   **Related Work (Nghiên cứu liên quan):** Định vị REGRAD trong mối tương quan với các lĩnh vực: CPT, RAG truyền thống, hiệu chỉnh tri thức (Knowledge Editing), và Huấn luyện tại thời điểm kiểm thử (Test-time Training). REGRAD khác biệt ở chỗ nó không sửa đổi vĩnh viễn mô hình gốc và cũng tránh được chi phí tính toán lan truyền ngược (backpropagation) trực tuyến lúc chạy bằng cách truy xuất gradient đã lưu.
*   **The REGRAD Framework (Khung hệ thống REGRAD):** Thuyết minh vòng đời 3 giai đoạn của REGRAD. (1) Meta-learning: huấn luyện mô hình MAML để tìm trọng số khởi tạo LoRA ($\theta$) và tỷ lệ học $\alpha$ sao cho gradient không giám sát giúp trả lời QA. (2) Xây dựng Gradient Bank: pre-compute gradient của toàn bộ corpus ngoại tuyến. (3) Inference trực tuyến: truy xuất top-K gradient, cộng dồn tuyến tính để cập nhật $\theta_q = \theta^* - \alpha^* \odot g(q)$, sinh câu trả lời và khôi phục lại trạng thái ban đầu.
*   **Experimental Setup (Thiết lập thực nghiệm):** Chạy thực nghiệm trên LLaMA-3.2-1B/3B và LLaMA-3.1-8B. Đánh giá trên 3 lĩnh vực: Wikipedia chung (2WQA, CWQ, HotpotQA), Y học (PubMedQA, MedQA, BioASQ) và Pháp luật (CaseHOLD, Learned Hands, HousingQA). So sánh với CPT tiêu chuẩn, Instruction CPT, RAG truyền thống, và Parametric RAG.
*   **Results, Ablations & Discussion (Kết quả & Thảo luận):** REGRAD (chỉ dùng tham số) vượt trội hơn hẳn các baseline CPT và PRAG. Khi kết hợp với RAG truyền thống (REGRAD + ICL), mô hình đạt hiệu năng cao nhất trên mọi benchmark. Thử nghiệm nạp liên tục 10K tài liệu chứng minh hiệu năng của CPT đi xuống (quên thảm khốc) còn REGRAD tăng tiến đều đặn. Các ablation study chứng minh meta-learning là bắt buộc, và gradient mang thông tin ngữ nghĩa thực sự thay vì chỉ kích hoạt QA mode.

---

## 3. Methodology Deep-Dive
### Khung hệ thống & Thuật toán
REGRAD hoạt động dựa trên cơ chế cập nhật tham số tạm thời (dynamic test-time parameter adaptation) mà không cần tính lan truyền ngược online.

1.  **Giai đoạn 1: Meta-Learning tối ưu hóa gradient (Bi-level Optimization):**
    *   *Inner Loop (Không giám sát - Tải tri thức tài liệu):* Với tài liệu $C$, tính gradient language-modeling thông thường:
        $$g_C = \nabla_\theta \mathcal{L}_{LM}(C; \theta)$$
        Thử nghiệm cập nhật tạm thời: $\theta_C = \theta - \alpha \odot g_C$ (với $\alpha$ là vector tỷ lệ học cho từng tensor).
    *   *Outer Loop (Có giám sát - Kiểm tra năng lực QA):* Đánh giá $\theta_C$ trên bài test QA $(q, a)$. Điều quan trọng là tài liệu $C$ bị loại bỏ khỏi context prompt của LLM. Mô hình bắt buộc phải dùng tri thức đã nhúng trong $\theta_C$ để trả lời $q$:
        $$\mathcal{L}_{QA}^C(\theta_C) = - \frac{1}{L} \sum_{t=1}^{L} \log p(at | q, a_{<t})$$
    *   *Hàm mục tiêu Meta-learning:*
        $$\min_{\theta, \alpha} \mathbb{E} \left[ \mathcal{L}_{QA}^C \left( \theta - \alpha \odot g_C(\theta) \right) \right]$$
        Quá trình này tối ưu hóa điểm khởi đầu $\theta^*$ và tốc độ cập nhật $\alpha^*$ sao cho việc học từ vựng không giám sát ở inner loop tự động chuyển hóa thành năng lực trả lời câu hỏi logic ở outer loop.
2.  **Giai đoạn 2 & 3: Xây dựng Gradient Bank và Suy luận trực tuyến:**
    *   *Gradient Bank:* Tính sẵn $g_i = \nabla_\theta \mathcal{L}_{LM}(d_i; \theta^*)$ cho từng văn bản $d_i$ trong corpus rồi lưu lại.
    *   *Inference:* Với câu hỏi $q$, truy xuất top-K tài liệu, lấy các gradient tương ứng $\{g_{ik}\}_{k=1}^K$ và cộng dồn:
        $$g(q) = \sum_{k=1}^K g_{ik}, \quad \theta_q = \theta^* - \alpha^* \odot g(q)$$
        Cập nhật trọng số LoRA tạm thời sang $\theta_q$ để sinh câu trả lời, sau đó hủy bỏ cập nhật.

### Dữ liệu, Thiết lập & Metrics
*   **Cơ sở tri thức:** DPR Wikipedia dump (General), PubMed Abstracts (Medicine), Pile-of-Law (Law).
*   **Metrics:** Accuracy (%) cho các tác vụ lựa chọn khép kín (như CaseHOLD); Token-level F1-score (%) cho trả lời câu hỏi tự do.

### 3 Giả định kỹ thuật quan trọng
1.  **Tính cộng dồn tuyến tính của gradient (Gradient Additivity):** Giả định các gradient của nhiều tài liệu khác nhau được tính tại cùng một điểm khởi tạo $\theta^*$ có thể cộng trực tiếp với nhau một cách tuyến tính để tạo ra một hướng cập nhật chung hiệu quả cho nhiều tài liệu.
2.  **Sự ổn định của không gian hạng thấp (LoRA subspace stabilization):** Giả định việc giới hạn cập nhật trong không gian LoRA nhỏ gọn là đủ để mô hình hấp thụ tri thức của tài liệu và không làm phá vỡ các liên kết logic sâu trong mạng Transformer gốc.
3.  **Khả năng tổng quát hóa của Meta-Initialization:** Giả định cặp siêu tham số khởi tạo $(\theta^*, \alpha^*)$ học trên tập meta-training có thể tổng quát hóa tốt cho các tài liệu mới xuất hiện trong tương lai mà không cần chạy lại meta-learning.

---

## 4. Phản biện học thuật (Brutally Honest Review)

### Summary
Paper đề xuất REGRAD, một phương pháp học liên tục và RAG mới sử dụng gradient của tài liệu làm đơn vị tri thức có thể truy xuất. Thông qua meta-learning hai cấp, REGRAD biến các gradient language-modeling không giám sát thành các bản cập nhật trọng số LoRA tạm thời để hỗ trợ trả lời câu hỏi ở thời điểm suy luận mà không gây trôi trọng số vĩnh viễn. Kết quả thực nghiệm trên các mô hình họ LLaMA cho thấy REGRAD cải thiện hiệu năng so với CPT và RAG, đồng thời đạt hiệu quả cộng hưởng khi kết hợp với in-context RAG.

### Strengths
1.  **Ý tưởng đột phá khoa học (Conceptual novelty):** Việc coi gradient như một "đơn vị tri thức có thể truy xuất và lưu trữ" (retrievable knowledge atom) thay vì chỉ là tín hiệu tối ưu tạm thời là một tư duy rất mới lạ và mở ra một hướng đi khác cho mảng Parametric RAG.
2.  **Khắc phục triệt để hiện tượng quên vĩnh viễn:** Nhờ cơ chế gán trọng số tạm thời và phục hồi ngay lập tức, mô hình hoàn toàn tránh được hiện tượng tích tụ trôi trọng số (weight drift), giúp giữ vững Plasticity của mô hình qua hàng nghìn lượt cập nhật (Bảng 2).
3.  **Chi phí suy luận trực tuyến cực thấp:** Tránh được việc chạy lan truyền ngược online (test-time training) nhờ tính sẵn gradient offline. Thời gian cập nhật trực tuyến chỉ mất 6-15 ms, hoàn toàn khả thi cho các ứng dụng thực tế.

### Weaknesses
1.  **Chi phí lưu trữ Gradient Bank khổng lồ:** Lưu trữ gradient LoRA (ngay cả khi giới hạn rank thấp) cho hàng triệu tài liệu là một gánh nặng lưu trữ rất lớn. Tác giả chưa thảo luận chi tiết về dung lượng đĩa cứng cần thiết (GB/TB) để lưu Gradient Bank cho các corpus quy mô lớn và các giải pháp nén gradient.
2.  **Độ nhạy cảm với chất lượng của bộ truy xuất (Retriever dependency):** REGRAD phụ thuộc hoàn toàn vào kết quả của bộ truy xuất (ở đây dùng BM25). Nếu retriever lấy sai tài liệu, mô hình sẽ cộng dồn các gradient nhiễu (distractor gradients) trực tiếp vào trọng số, dẫn đến hiện tượng can thiệp gradient (gradient interference) và làm sụt giảm nghiêm trọng khả năng suy luận của mô hình (Hình 4 chỉ ra hiệu năng đi xuống khi tăng K lên 10).
3.  **Meta-initialization phụ thuộc chặt chẽ vào miền dữ liệu (Domain dependency):** Kết quả ở Bảng 3 chứng minh cấu hình `General-init` chạy trên miền Y học hay Luật pháp cho kết quả rất tệ, thậm chí tệ hơn cả mô hình gốc. Nghĩa là chúng ta bắt buộc phải chạy lại meta-learning hai cấp rất tốn kém tài nguyên cho từng miền chuyên biệt.
4.  **Thiếu so sánh với các kỹ thuật tối ưu hóa tham số cục bộ khác:** Paper bỏ qua việc so sánh với các phương pháp hiệu chỉnh tri thức (Knowledge Editing) mạnh như ROME, MEMIT hay GRACE, vốn cũng có khả năng cập nhật tham số nhanh chóng.
5.  **Chi phí tính toán offline ban đầu rất cao:** Để tạo Gradient Bank, mô hình phải chạy backward pass cho mọi tài liệu trong corpus. Với corpus lớn hàng triệu văn bản, đây là một chi phí năng lượng và tính toán khổng lồ.

### Questions for Authors
1.  Dung lượng lưu trữ trung bình (MB/tài liệu) của một gradient LoRA trong Gradient Bank là bao nhiêu? Chi phí lưu trữ này tăng tiến thế nào khi kích thước mô hình tăng từ 1B lên 8B?
2.  Khi gặp các tài liệu nhiễu (distractors) do retriever lấy sai, làm thế nào để REGRAD phát hiện và triệt tiêu các gradient độc hại này trước khi chúng can thiệp vào trọng số $\theta$?
3.  Có thể sử dụng một mạng nén gradient (gradient compression network) để giảm dung lượng của Gradient Bank mà không làm suy hao năng lực thích ứng của tham số không?

### Recommendation
**Strong Accept.** Đây là một công trình có tính học thuật rất cao, đề xuất một giải pháp kỹ thuật thanh lịch và hiệu quả để dung hòa CPT và RAG. Các thực nghiệm được thiết kế chặt chẽ và kết quả chứng minh tính bảo toàn năng lực của mô hình gốc là vô cùng thuyết phục.

---

## 5. Data & Metrics View Designer

### Tóm tắt dữ liệu & Baselines
*   **Datasets & Corpora:** DPR Wikipedia dump (Wikipedia), PubMed Abstracts (Medicine), Pile-of-Law (Law). Đánh giá trên: 2WQA, CWQ, HotpotQA, PubMedQA, MedQA, BioASQ, CaseHOLD, Learned Hands, HousingQA.
*   **Baselines:** Direct Generation, Standard CPT, Instruction CPT, Standard RAG, Prompt-Engineered RAG (PE-RAG), Parametric RAG (PRAG), cùng các biến thể tinh chỉnh tương ứng (Fine-tuned-Direct, Fine-tuned-RAG, Fine-tuned-PRAG).
*   **Metrics:** Accuracy (cho closed-ended), Token-level F1-score (cho open-ended QA).

### Đề xuất trực quan hóa (Charts)
1.  **Line Chart (Độ ổn định năng lực khi tăng quy mô tài liệu nạp vào):**
    *   *Trục X:* Số lượng tài liệu liên tục nạp vào (2.5K, 5K, 7.5K, 10K tài liệu)
    *   *Trục Y:* F1 Score (%) trung bình trên các tập test (2WQA, CWQ, HotpotQA)
    *   *Nhóm (Series):* Continual Post-Training (CPT) vs. REGRAD (RG)
    *   *Mục đích:* Chứng minh CPT bị sụp đổ hiệu năng (decay) khi học quá nhiều tài liệu do weight drift, trong khi REGRAD tăng trưởng ổn định nhờ cơ chế tạm thời.
2.  **Grouped Bar Chart (So sánh REGRAD với các Baseline trên 3 miền):**
    *   *Trục X:* Ba miền đánh giá (General, Medicine, Law)
    *   *Trục Y:* Average Performance (%)
    *   *Nhóm (Series):* Direct, PE-RAG, Fine-tuned-PRAG, REGRAD, REGRAD + ICL
    *   *Mục đích:* Làm nổi bật hiệu năng vượt trội của REGRAD cả ở phiên bản chỉ dùng tham số và phiên bản kết hợp in-context.
3.  **Bar Chart (Phân tích Ablation về nguồn gốc Gradient):**
    *   *Trục X:* Các tác vụ (2WQA, CWQ, HotpotQA)
    *   *Trục Y:* F1 Score (%) trên mô hình LLaMA-8B
    *   *Nhóm (Series):* Direct, Raw Gradient, Meta-learned Gradient
    *   *Mục đích:* Cho thấy raw gradient (chưa qua shaper) làm sụt giảm hiệu năng, chứng minh vai trò sinh tử của meta-learning.

### Thiết kế bảng tổng hợp đề xuất
Bảng nên phân cấp theo kích thước mô hình (1B, 3B, 8B) và thể hiện điểm số trên từng domain:
`[Kích thước mô hình | Phương pháp | General Avg (%) | Medical Avg (%) | Law Avg (%) | Overall Average (%) | Trôi trọng số? (Y/N)]`
*Highlight:* Bold cho kết quả tốt nhất ở mỗi khối mô hình.

### Số liệu thô trích xuất từ Paper

**Bảng A: Hiệu năng F1/Acc (%) trên các benchmark (Top-1000 setting, LLaMA-8B)**

| Method / Benchmark | 2WQA | CWQ | HQA | CaseHOLD | LH | Housing | PubMed | MedQA | BioASQ | Avg |
| :--- | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Direct | 33.42 | 42.73 | 26.34 | 61.30 | 54.10 | 46.00 | 91.20 | 16.46 | 81.20 | 50.31 |
| Standard CPT | 25.67 | 40.24 | 23.51 | 58.10 | 53.80 | 46.20 | 90.30 | 15.59 | 80.20 | 48.18 |
| Instruction CPT | 36.31 | 46.58 | 27.44 | 63.80 | 54.00 | 49.50 | 38.70 | 19.24 | 60.60 | 44.02 |
| RAG (ICL) | 26.98 | 31.47 | 35.23 | 66.30 | 39.90 | 53.50 | 80.10 | 17.88 | 80.50 | 47.98 |
| PE-RAG | 29.61 | 36.58 | 38.70 | 69.70 | 51.40 | 60.20 | 80.60 | 14.15 | 81.00 | 51.33 |
| PRAG | 31.63 | 40.96 | 27.18 | 60.30 | 52.00 | 46.20 | 92.40 | 15.93 | 82.10 | 49.86 |
| Fine-tuned-Direct | 32.18 | 49.67 | 28.84 | 56.90 | 84.20 | 80.20 | 92.80 | 22.89 | 81.40 | 58.79 |
| Fine-tuned-RAG | 37.17 | 54.21 | 39.16 | 59.60 | 91.90 | 69.50 | 92.70 | 23.22 | 84.60 | 61.34 |
| Fine-tuned-PRAG | 35.71 | 51.26 | 32.97 | 66.20 | 78.60 | 83.00 | 91.80 | 21.37 | 82.60 | 60.39 |
| **REGRAD (Ours)** | 38.43 | 56.41 | 36.31 | 70.30 | **96.20** | 84.80 | 93.20 | 21.65 | 81.90 | **64.36** |
| **REGRAD + ICL (Ours)** | **45.06** | **57.52** | **45.27** | **74.10** | 94.80 | **85.40** | **98.10** | **19.83** | **85.30** | **67.26** |

**Bảng B: Khả năng bảo toàn năng lực (F1 score %) khi nạp tri thức liên tục (LLaMA-8B)**

| Số lượng tài liệu nạp vào | Method | 2Wiki | CWQ | HotpotQA | Average |
| :---: | :--- | :---: | :---: | :---: | :---: |
| 2.5K | CPT | 27.14 | 43.32 | 21.73 | 30.73 |
| | **REGRAD (RG)** | **33.95** | **55.91** | **23.26** | **37.71** |
| 5K | CPT | 28.26 | 44.55 | 21.43 | 31.41 |
| | **REGRAD (RG)** | **34.12** | **57.16** | **27.65** | **39.64** |
| 7.5K | CPT | 27.43 | 45.72 | 22.46 | 31.87 |
| | **REGRAD (RG)** | **36.12** | **59.84** | **31.84** | **42.60** |
| 10K | CPT | 20.25 | 45.89 | 17.26 | 27.80 |
| | **REGRAD (RG)** | **37.70** | **60.10** | **34.35** | **44.05** |

---

## 6. Khả năng áp dụng cho VN-Legal-AI / CDNCTQ
*   **Giải pháp cập nhật văn bản pháp luật không gây drift:** Trong hệ thống VN-Legal-AI, khi có các văn bản pháp luật mới cập nhật (ví dụ: các Nghị định, Thông tư ban hành hàng ngày), thay vì chạy Continual SFT liên tục lên mô hình chính (gây nguy cơ quên kiến thức luật cũ hoặc ngôn ngữ tiếng Việt phổ thông), chúng ta có thể áp dụng REGRAD. Chúng ta tính sẵn gradient LoRA cho mỗi văn bản luật mới và lưu vào Gradient Bank. Khi người dùng hỏi về một nghị định mới, hệ thống truy xuất gradient của nghị định đó và cập nhật tạm thời để trả lời.
*   **Thiết lập Meta-learning trên tập luật Việt Nam:** Để áp dụng REGRAD cho tiếng Việt pháp lý, chúng ta cần thực hiện giai đoạn Meta-learning (Stage 1). Chúng ta có thể sử dụng tập dữ liệu **VLQA** (Paper 5) làm tập dữ liệu meta-training $(C, q, a)$ để định hình trọng số khởi đầu LoRA $\theta^*$ và step size $\alpha^*$. Việc này sẽ shape các gradient của văn bản luật Việt Nam từ dạng reconstruction (học vẹt từ ngữ pháp luật) thành dạng suy luận lập luận tư pháp thực tế.
*   **Kết hợp Hybrid REGRAD + ICL cho hệ thống RAG:** Hệ thống RAG hiện tại của dự án CDNCTQ có thể nâng cấp thành phiên bản Hybrid. Khi người dùng hỏi, hệ thống vừa lấy văn bản luật thô đưa vào context prompt của LLM (In-Context Learning), vừa lấy gradient của văn bản luật đó cập nhật vào tham số LoRA (Parametric adaptation). Thử nghiệm của paper cho thấy sự kết hợp này giúp tăng tối đa độ chính xác của câu trả lời, vượt qua cả giới hạn của RAG đơn thuần.
