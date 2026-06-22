# Phân tích Chi tiết: LoRA Learns Less and Forgets Less

## 1. Tóm tắt cấp cao (High-level Summary)
Bài báo nghiên cứu sâu sắc về khả năng thực tế và sự đánh đổi (trade-offs) giữa Low-Rank Adaptation (LoRA) và tinh chỉnh toàn phần (Full Finetuning) khi thích ứng các Mô hình Ngôn ngữ Lớn (LLMs) vào các miền tri thức phức tạp như lập trình và toán học [1, 2]. Nhóm tác giả thực hiện so sánh hai phương pháp này dưới hai thiết lập huấn luyện: Tiếp tục tiền huấn luyện (Continued Pretraining - CPT, quy mô tỷ token) và Tinh chỉnh chỉ thị (Instruction Finetuning - IFT, quy mô chục triệu token) [1, 2]. Nghiên cứu phát hiện ra rằng trong các cấu hình hạng thấp tiêu chuẩn, LoRA có hiệu năng học miền đích (target domain) kém hơn đáng kể so với tinh chỉnh toàn phần, đặc biệt là trong CPT [1, 3]. Tuy nhiên, LoRA lại có ưu thế vượt trội trong việc ngăn chặn hiện tượng quên thảm khốc (catastrophic forgetting), duy trì năng lực tri thức chung tốt hơn nhiều so với tinh chỉnh toàn phần hay các bộ điều chuẩn kinh điển [1, 4]. Kết quả phân tích SVD chỉ ra rằng tinh chỉnh toàn phần cập nhật trọng số với hạng (rank) cao gấp 10-100 lần so với LoRA, giải thích cho khoảng cách hiệu năng và dẫn đến các khuyến nghị tối ưu hóa tham số LoRA [1, 5].

---

## 2. Tóm tắt chi tiết từng phần (Section Summaries)
*   **Introduction (Giới thiệu):** Đặt vấn đề về sự tranh luận trong cộng đồng liên quan đến việc liệu LoRA có làm suy giảm chất lượng mô hình so với tinh chỉnh toàn phần hay không. Tác giả xem quá trình tinh chỉnh LLM như một dạng học liên tục, nơi việc tiếp thu kiến thức mới thường phải trả giá bằng việc quên đi khả năng cơ bản cũ. Bài báo hướng tới mục tiêu đo lường định lượng cả hai chiều: năng lực học miền đích (plasticity) và độ quên miền nguồn (stability) trong các tác vụ code và toán học.
*   **Method (Phương pháp thiết lập):** Sử dụng LLaMA-2-7B làm mô hình nền tảng, áp dụng LoRA lên toàn bộ các module tuyến tính của Transformer (attention và MLP). Dữ liệu CPT bao gồm StarCoder-Python và OpenWebMath; dữ liệu IFT bao gồm Magicoder-Evol-Instruct và MetaMathQA. Nhóm nghiên cứu phân tách rõ các metric đánh giá thành: Hiệu năng miền đích (HumanEval cho code, GSM8K cho toán) và Hiệu năng miền nguồn/quên (trung bình cộng độ chính xác của HellaSwag, WinoGrande và ARC-Challenge).
*   **Experiments (Thực nghiệm & Kết quả):** Kết quả thực nghiệm chứng minh LoRA không thể bắt kịp tinh chỉnh toàn phần trong chế độ CPT bất kể cấu hình rank lớn thế nào, nhưng rank cao (r=256) có thể tương đương tinh chỉnh toàn phần trong chế độ IFT. Quan trọng là LoRA duy trì bộ nhớ miền cũ ổn định hơn hẳn tinh chỉnh toàn phần trên mọi cấu hình. Các phân tích sâu hơn chỉ ra LoRA chống quên tốt hơn attention dropout và weight decay, đồng thời giữ được độ đa dạng của phân phối sinh từ (generation diversity) trong khi tinh chỉnh toàn phần dễ bị sụp đổ phân phối.
*   **Discussion & Conclusion (Thảo luận & Kết luận):** Sử dụng phân tích Phân rã trị riêng (SVD) trên ma trận $\Delta W$ của tinh chỉnh toàn phần để chứng minh các cập nhật toàn phần thực tế có hạng rất cao (cần rank ~2000 để giải thích 90% biến động). Từ đó, bài báo đưa ra các khuyến nghị thực hành: không dùng LoRA cho CPT, dùng rank tối thiểu 256 cho các tác vụ IFT phức tạp, scale $\alpha = 2r$, và tìm kiếm learning rate lớn hơn 10 lần so với tinh chỉnh toàn phần.

---

## 3. Methodology Deep-Dive
### Khung hệ thống & Thiết lập PEFT
Nghiên cứu đánh giá LLaMA-2-7B trên hai kịch bản huấn luyện:
*   **Continued Pretraining (CPT):** StarCoder-Python (20 tỷ token) và OpenWebMath (14.7 tỷ token).
*   **Instruction Finetuning (IFT):** Magicoder-Evol-Instruct-110K và MetaMathQA.

Trọng số thích ứng được thiết lập là LoRA toàn diện, áp dụng lên toàn bộ các ma trận trọng số tuyến tính:
$$\{W_q, W_k, W_v, W_o, W_{gate}, W_{up}, W_{down}\}$$
Hệ số tỉ lệ của LoRA được cố định theo công thức $\alpha = 2r$. Trong Peft library của HuggingFace, scale factor được tính bằng $\frac{\alpha}{r}$, điều này vô hình trung làm giảm độ lớn cập nhật khi rank $r$ tăng lên nếu giữ nguyên $\alpha$. Việc khóa $\alpha = 2r$ đảm bảo giữ nguyên tỷ lệ tác động của adapter khi tăng rank.

### Phân tích SVD trên cập nhật trọng số
Để hiểu bản chất vật lý của $\Delta W$, nhóm tác giả thực hiện phân rã trị riêng (SVD) trên ma trận thay đổi trọng số sau khi tinh chỉnh toàn phần:
$$\Delta W = U \Sigma V^T$$
Họ vẽ biểu đồ phổ trị riêng (singular value spectrum) và đếm số lượng trị riêng cần thiết để giải thích 90% phương sai của $\Delta W$. Kết quả cho thấy $\Delta W$ của tinh chỉnh toàn phần có hạng nội tại rất cao ($r \approx 2000$ trên kích thước ma trận $4096 \times 4096$).

### Dữ liệu & Metrics
*   **Metric Học (Target):** HumanEval pass@1 (đo khả năng code), GSM8K strict match (đo khả năng toán), MT-Bench (đo khả năng hội thoại tổng hợp).
*   **Metric Quên (Source):** Điểm trung bình cộng (Average score) của ba tập: HellaSwag, WinoGrande, và ARC-Challenge.

---

## 4. Phản biện học thuật (Brutally Honest Review)

### Summary
Paper thực hiện đánh giá thực nghiệm toàn diện so sánh LoRA và tinh chỉnh toàn phần (Full FT) trên các bài toán code và toán học, phân chia rõ rệt giữa hai chế độ CPT và IFT. Nghiên cứu chỉ ra rằng LoRA bị giới hạn hiệu năng trong CPT do các cập nhật của tinh chỉnh toàn phần có hạng rất cao, nhưng LoRA lại là bộ điều chuẩn chống quên thảm khốc và giữ độ đa dạng đầu ra xuất sắc. Bài báo cung cấp các hướng dẫn cấu hình hyperparameter giá trị cho việc triển khai thực tế.

### Strengths
1.  **Phân định rạch ròi CPT vs IFT:** Giải quyết triệt để các kết luận mâu thuẫn trong các nghiên cứu trước đây bằng cách chỉ ra LoRA hoạt động tốt ở IFT nhưng kém ở CPT.
2.  **Đồ thị Pareto trực quan:** Biểu diễn trực quan hóa xuất sắc mối quan hệ đánh đổi giữa "Học miền đích" và "Quên miền nguồn" giúp người đọc nắm bắt ngay bản chất của các phương pháp.
3.  **Khuyến nghị hyperparameter thực tiễn:** Công thức $\alpha=2r$ và hướng dẫn LR cao hơn 10 lần là những kinh nghiệm kỹ thuật vô giá cho cộng đồng.
4.  **Phân tích độ đa dạng sinh từ (Diversity):** Việc chứng minh tinh chỉnh toàn phần làm sụp đổ sự đa dạng của câu trả lời trong khi LoRA bảo tồn được nó là một phát hiện chất lượng cho các ứng dụng thực tế.

### Weaknesses
1.  **Giới hạn ở quy mô mô hình nhỏ (7B):** Toàn bộ thực nghiệm chỉ chạy trên LLaMA-2-7B. Trên các mô hình lớn hơn nhiều (ví dụ 70B tham số), tỷ lệ dung lượng dư thừa lớn hơn có thể giúp LoRA hạng thấp ($r=64$) học tốt hơn trong chế độ CPT, làm giảm tính tổng quát của kết luận CPT.
2.  **Bỏ qua việc unfreeze các tầng Embeddings:** Tác giả đóng băng hoàn toàn Input/Output Embeddings khi chạy LoRA trong CPT. Đối với các miền dữ liệu mới có nhiều từ vựng chuyên ngành, việc huấn luyện thêm embeddings là bắt buộc và có thể thu hẹp đáng kể khoảng cách CPT.
3.  **Thiếu so sánh với các biến thể PEFT nâng cao:** Bài báo chỉ so sánh vanilla LoRA với Full FT. Việc thiếu các đối thủ mạnh như DoRA (Weight Decomposition) hay AdaLoRA (Dynamic Rank) khiến kết luận về giới hạn của PEFT hạng thấp chưa thực sự trọn vẹn.
4.  **Metric đo độ quên tương đối hạn chế:** Sử dụng trung bình cộng 3 tập trắc nghiệm đa lựa chọn (HellaSwag, ARC, WinoGrande) là một proxy khá hẹp. Nó không phản ánh được sự suy giảm trong khả năng tuân thủ định dạng chỉ thị (instruction-following) hay tính an toàn (safety alignment).
5.  **Giải thích SVD chưa hoàn toàn thuyết phục:** Việc $\Delta W$ có hạng cao trong tinh chỉnh toàn phần có thể chỉ là do nhiễu sinh ra bởi thuật toán tối ưu hóa (optimizer noise) tích lũy qua các step, chứ không chắc chắn là tất cả 2000 chiều đó đều chứa thông tin học hữu ích cho tác vụ.

### Questions for Authors
1.  Nếu chúng ta unfreeze và huấn luyện thêm các tầng Input/Output Embeddings kết hợp với LoRA, khoảng cách hiệu năng trong CPT có được thu hẹp đáng kể không?
2.  Liệu hiện tượng LoRA dưới cơ Full FT trong CPT có lặp lại trên các mô hình có kích thước lớn hơn như LLaMA-3-70B hay không?
3.  Làm thế nào để phân biệt phần hạng thực tế chứa tri thức hữu ích và phần hạng chỉ chứa nhiễu ngẫu nhiên tích lũy của optimizer trong ma trận cập nhật $\Delta W$ của Full FT?

### Recommendation
**Strong Accept.** Đây là một nghiên cứu thực nghiệm mẫu mực, đính chính nhiều nhận định chưa chuẩn xác về LoRA trong cộng đồng. Những phát hiện về sự đánh đổi Pareto giữa học-quên cùng các chỉ dẫn hyperparameter có giá trị ứng dụng thực tiễn rất cao.

---

## 5. Data & Metrics View Designer

### Tóm tắt dữ liệu & Baselines
*   **Datasets:** StarCoder-Python, OpenWebMath, Magicoder-Evol-Instruct, MetaMathQA, Tülu-v2-mix.
*   **Baselines:** Full Finetuning, Attention Dropout, Weight Decay.

### Đề xuất trực quan hóa (Charts)
1.  **Line Chart so sánh hiệu năng theo Rank trong CPT:**
    *   *Trục X:* Số lượng token huấn luyện (từ 0.25 tỷ đến 20 tỷ tokens)
    *   *Trục Y:* Target Accuracy (HumanEval hoặc GSM8K)
    *   *Nhóm (Series):* Full FT, LoRA (r=16), LoRA (r=64), LoRA (r=256)
    *   *Mục đích:* Tái hiện Hình 1 để chứng minh LoRA bị hụt hơi trong kịch bản CPT dài hạn bất kể tăng rank.
2.  **Scatter Plot Pareto Frontier (Học vs Quên):**
    *   *Trục X:* Forgetting Score (Average HellaSwag, ARC, WinoGrande - Càng cao càng tốt)
    *   *Trục Y:* Target Accuracy (HumanEval / GSM8K)
    *   *Nhóm (Series):* Full FT, LoRA các ranks
    *   *Mục đích:* Tái hiện Hình 3 để thể hiện rõ ràng: LoRA nằm ở vùng "Học ít hơn nhưng quên ít hơn".
3.  **Histogram phân bố độ đa dạng sinh từ:**
    *   *Trục X:* Số lượng đáp án duy nhất (Unique Answers) sinh ra trong 50 lần thử
    *   *Trục Y:* Tần suất xuất hiện (Count)
    *   *Nhóm (Series):* Base LLaMA-2, Full FT, LoRA
    *   *Mục đích:* Minh họa hiện tượng sụp đổ độ đa dạng (collapse of diversity) của Full FT so với LoRA.

### Thiết kế bảng tổng hợp đề xuất
Bảng nên phân loại rõ rệt theo miền dữ liệu (Code vs Math) và theo chế độ huấn luyện (CPT vs IFT) để thể hiện sự dịch chuyển điểm số của target domain và mức độ bảo toàn tri thức chung.

### Số liệu thô trích xuất từ Paper

| Regime & Domain | Method Configuration | Tokens / Epochs | Target Domain Acc | Source Domain Acc (No Forgetting) |
| :--- | :--- | :--- | :---: | :---: |
| **Code CPT** | LoRA (r=16) | 20 Billion Tokens | 16.2% | 63.5% |
| **Code CPT** | LoRA (r=64) | 20 Billion Tokens | 19.6% | 62.6% |
| **Code CPT** | LoRA (r=256) | 20 Billion Tokens | 22.4% | 61.7% |
| **Code CPT** | Full Finetuning | 20 Billion Tokens | **26.3%** | 54.5% |
| **Code IFT** | LoRA (r=16) | 16 Epochs | 32.4% | 60.9% |
| **Code IFT** | LoRA (r=64) | 16 Epochs | 40.5% | 51.0% |
| **Code IFT** | LoRA (r=256) | 16 Epochs | **46.6%** | 51.7% |
| **Code IFT** | Full Finetuning | 16 Epochs | 41.6% | 41.4% |
| **Math IFT** | LoRA (r=16) | 16 Epochs | 56.9% | 59.6% |
| **Math IFT** | LoRA (r=256) | 16 Epochs | 59.4% | 56.7% |
| **Math IFT** | Full Finetuning | 16 Epochs | **59.9%** | 55.9% |

---

## 6. Khả năng áp dụng cho VN-Legal-AI / CDNCTQ
*   **Lựa chọn kịch bản Continued Pretraining cho Luật Việt Nam:** Đây là cảnh báo cực kỳ quan trọng cho dự án VN-Legal-AI. Khi thực hiện Continual Pre-Training trên lượng lớn văn bản luật Việt Nam (quy mô hàng tỷ token văn bản thô), chúng ta **không nên dùng LoRA** vì hiệu năng học thuật ngữ và cấu trúc pháp lý mới sẽ bị hạn chế nghiêm trọng. Nên ưu tiên chạy Full Fine-Tuning (hoặc ít nhất là unfreeze các tầng Embeddings và MLP) để hấp thụ tối đa tri thức mới.
*   **Ứng dụng LoRA trong pha tinh chỉnh chỉ thị (IFT):** Ở pha sau, khi huấn luyện mô hình đã có nền tảng luật để thực hiện các tác vụ cụ thể (như Q&A pháp luật, soạn thảo đơn từ), ta nên chuyển sang dùng LoRA với cấu hình rank cao ($r=256$) và thiết lập $\alpha=2r$. Việc này giúp mô hình học nhanh các mẫu hội thoại luật mà không quên đi khả năng suy luận logic chung và bảo tồn được độ đa dạng của câu trả lời.
*   **Tối ưu hóa hyperparameter khi huấn luyện:** Khi tinh chỉnh LoRA cho mô hình luật, cần thiết lập learning rate lớn hơn khoảng một cấp số (order of magnitude, ví dụ LR $\approx 2e-4$) so với khi chạy tinh chỉnh toàn phần (LR $\approx 2e-5$) để adapter hội tụ tốt nhất, tránh việc học hời hợt.
