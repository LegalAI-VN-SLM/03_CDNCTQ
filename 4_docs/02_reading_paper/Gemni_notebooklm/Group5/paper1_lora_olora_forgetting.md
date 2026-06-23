# Phân tích Chi tiết: Mitigating Catastrophic Forgetting in Fine-Tuned Large Language Models: An Experimental Study of LoRA and O-LoRA

## 1. Tóm tắt cấp cao (High-level Summary)
Bài báo giải quyết hiện tượng quên thảm khốc (catastrophic forgetting) ở các Mô hình Ngôn ngữ Lớn (LLMs) trong quá trình tinh chỉnh liên tục theo hướng dẫn (continual instruction fine-tuning). Nhóm tác giả thực hiện các thực nghiệm sâu rộng để kiểm chứng khả năng bảo toàn kiến thức của phương pháp Thích ứng Hạng thấp (LoRA) cùng các kỹ thuật PEFT khác, đồng thời đánh giá O-LoRA (Orthogonal Low-Rank Adaptation) như một giải pháp giảm thiểu. Ý tưởng cốt lõi của O-LoRA là giới hạn các bản cập nhật trọng số cho mỗi tác vụ mới vào các không gian con hạng thấp và ép buộc các không gian con này trực giao với không gian gradient của các tác vụ cũ. Thử nghiệm trên mô hình LLaMA-2-7B cho thấy O-LoRA giúp giảm thiểu đáng kể chỉ số quên (Forgetting Gradient) trên các bài test tri thức và đọc hiểu, tuy nhiên hiệu năng của nó lại rất nhạy cảm với các siêu tham số (rank và alpha), đặc biệt là trên các tác vụ tính toán logic. Trong bối cảnh LLM cần liên tục tiếp thu kỹ năng mới mà không được tiếp cận lại dữ liệu lịch sử do giới hạn bộ nhớ hoặc quyền riêng tư, nghiên cứu này chỉ ra cả cơ hội lẫn các hạn chế thực tế của việc áp dụng ràng buộc trực giao.

---

## 2. Tóm tắt chi tiết từng phần (Section Summaries)
*   **Introduction (Giới thiệu):** Phần này nêu bật sự phát triển vượt bậc của LLMs và sự cần thiết của việc tinh chỉnh (fine-tuning) để mô hình thích ứng với các miền chuyên biệt. Tác giả giới thiệu các phương pháp hiệu quả tham số (PEFT) như LoRA nhưng chỉ ra nguy cơ quên thảm khốc khi mô hình học tuần tự. Bài báo đề xuất đánh giá O-LoRA như một chiến lược khắc phục không cần lưu trữ dữ liệu cũ (rehearsal-free) hay gradients lịch sử khổng lồ.
*   **Related Work (Nghiên cứu liên quan):** Tác giả hệ thống hóa các kỹ thuật PEFT phổ biến bao gồm Prefix-Tuning, P-Tuning, Prompt-Tuning và LoRA. Mỗi phương pháp được mô tả sơ lược về cơ chế hoạt động (ví dụ: chèn token ảo hoặc dùng ma trận tích hợp hạng thấp). Phần này làm rõ cách LoRA giảm số tham số huấn luyện tới 10,000 lần và bộ nhớ GPU tới 3 lần so với tinh chỉnh toàn bộ tham số.
*   **O-LoRA Method (Phương pháp O-LoRA):** Mô tả chi tiết giải pháp O-LoRA trong học liên tục. Bằng cách sử dụng các cặp ma trận LoRA ($A$ và $B$) mới cho mỗi tác vụ, mô hình đóng băng các adapter cũ và tối ưu hóa adapter mới dưới ràng buộc trực giao ma trận $A_t^T A_i = 0$. Ý tưởng này giúp các hướng cập nhật trọng số của tác vụ mới không can thiệp vào các biểu diễn hoặc loss landscape của các tác vụ cũ.
*   **Experimental Design and Results (Thiết kế thực nghiệm và Kết quả):** Nhóm tác giả thực hiện hai luồng thí nghiệm trên LLaMA-2-7B: (1) Đánh giá LoRA và O-LoRA dưới các thiết lập siêu tham số khác nhau trên Alpaca, đo lường trên TruthfulQA, BLiMP, MMLU và các bài toán số học; (2) Thiết lập học liên tục chuỗi 3 tác vụ (Simp -> Exp -> InqQG) để tính toán Forgetting Gradient (FG) trên Domain Knowledge, Reasoning và Reading Comprehension. Kết quả cho thấy O-LoRA giảm chỉ số quên cực tốt (có những chỗ FG âm, biểu thị hiệu năng tăng), nhưng lại dễ sụp đổ hiệu năng về 0% trên tác vụ tính toán (Arithmetic) khi tăng tham số tỷ lệ $\alpha$.
*   **Conclusions and Future Work (Kết luận và Hướng đi tương lai):** Xác nhận LoRA và PEFT bị quên kiến thức nghiêm trọng trong học liên tục. O-LoRA thể hiện tiềm năng giảm quên đáng kể nhưng nhạy cảm với việc chọn siêu tham số và không phải là giải pháp vạn năng. Hướng đi tương lai cần tập trung vào việc giảm sự phụ thuộc siêu tham số và tăng tính ổn định của O-LoRA trên các tập dữ liệu khác nhau.

---

## 3. Methodology Deep-Dive
### Khung hệ thống & Thuật toán
O-LoRA kế thừa cấu trúc của LoRA bằng cách thêm các nhánh ma trận tích hợp hạng thấp vào các lớp Transformer (cụ thể là các ma trận chiếu Attention $W_q$ và $W_v$), đồng thời đóng băng trọng số gốc $W$. 

Khi mô hình học một chuỗi tác vụ tuần tự, với mỗi tác vụ $t$, một cặp ma trận thích ứng mới $A_t$ và $B_t$ được khởi tạo. Để ngăn chặn các cập nhật của tác vụ mới làm hỏng kiến thức cũ, O-LoRA đưa vào một hàm phạt trực giao (Orthogonal Regularization Loss) nhằm ép ma trận $A_t$ của tác vụ hiện tại phải trực giao với các ma trận $A_i$ ($i < t$) của các tác vụ trước đó. Ràng buộc toán học được thể hiện qua việc giảm thiểu tích chéo $A_i^T A_t \approx 0$. 

### Dữ liệu, Thiết lập & Metrics
*   **Dữ liệu:** Tinh chỉnh hướng dẫn trên tập Alpaca (52k mẫu). Đánh giá khả năng học liên tục qua chuỗi tác vụ: Text Simplification (Simp), Explanation Generation (Exp), và Inquisitive Question Generation (InqQG).
*   **Setup:** Sử dụng mô hình nền tảng LLaMA-2-7B. Đánh giá khả năng giữ kiến thức tổng quát trên các benchmark: MMLU (Domain Knowledge), HellaSwag, Winogrande, PIQA, MathQA, Mutual (Reasoning) và RACE-high/middle (Reading Comprehension). Các baseline bao gồm LoRA, Prefix-Tuning, P-Tuning, Prompt-Tuning, và NEFTune (kết hợp nhiễu embedding).
*   **Thước đo (Metrics):** Sử dụng chỉ số Forgetting Gradient (FG):
    $$FGi = \frac{1}{|Ei|} \sum_{e \in Ei} \frac{1}{n} \sum_{m=1}^{n} \frac{R0_e - Rm_e}{R0_e} \times 100\%$$
    Trong đó $R0_e$ là điểm số ban đầu của mô hình gốc, và $Rm_e$ là điểm số sau khi học tác vụ thứ $m$.

### 3 Giả định kỹ thuật quan trọng
1.  **Tính trực giao của ma trận $A$ tương đương với trực giao gradient:** Giả định rằng ràng buộc trực giao trên ma trận chiếu đầu vào $A$ của LoRA là đủ để đảm bảo các bản cập nhật trọng số $\Delta W = BA$ không can thiệp vào không gian gradient của các tác vụ cũ.
2.  **Sự độc lập của các tác vụ (Task Independence):** Giả định các tác vụ học tuần tự có các không gian biểu diễn độc lập và việc ép trực giao tuyệt đối không triệt tiêu khả năng chuyển giao kiến thức tích cực (forward transfer) giữa các tác vụ tương đồng ngữ nghĩa.
3.  **Khả năng mở rộng không gian trực giao:** Giả định rằng không gian vector của LLM (ở đây là LLaMA-2-7B) đủ rộng để chứa nhiều không gian con trực giao hạng thấp mà không bị cạn kiệt khi số lượng tác vụ tăng lên.

---

## 4. Phản biện học thuật (Brutally Honest Review)

### Summary
Paper thực hiện nghiên cứu thực nghiệm nhằm đánh giá hiện tượng quên thảm khốc của các phương pháp PEFT (đặc biệt là LoRA) trên LLaMA-2-7B và khảo sát hiệu quả của O-LoRA. Tác giả chỉ ra rằng việc tinh chỉnh hướng dẫn thông thường khiến LLM quên nhanh các kiến thức nền tảng (Domain Knowledge, Reasoning, Reading Comprehension). Nhóm tác giả claim rằng ràng buộc trực giao của O-LoRA có thể kiềm chế hiệu quả hiện tượng này, đưa chỉ số quên (FG) về mức rất thấp hoặc thậm chí âm, mặc dù phương pháp này vẫn bị ảnh hưởng nặng nề bởi sự nhạy cảm của các siêu tham số $r$ và $\alpha$.

### Strengths
1.  **Đánh giá thực nghiệm chi tiết về siêu tham số:** Việc quét (sweep) chi tiết hệ số $\alpha$ và rank $r$ của LoRA giúp phơi bày hiện tượng sụp đổ hiệu năng (catastrophic drop) trên các tác vụ logic/số học khi tỷ lệ $\alpha/r$ quá lớn.
2.  **Áp dụng chỉ số FG chuẩn xác:** Sử dụng công thức Forgetting Gradient (FG) chuẩn hóa theo thang đo của mô hình gốc ($R0$) giúp định lượng chính xác phần trăm năng lực bị suy giảm qua từng bước học liên tục.
3.  **Đo lường chi phí phần cứng thực tế:** Paper cung cấp số liệu so sánh trực tiếp về bộ nhớ GPU đỉnh (peak memory usage) giữa LoRA (30.49 GB) và O-LoRA (36.26 GB), cho thấy chi phí phụ trội của việc tính toán trực giao.

### Weaknesses
1.  **Thiếu đóng góp mới về mặt thuật toán:** Toàn bộ thuật toán O-LoRA được lấy nguyên bản từ bài báo gốc năm 2023 của Wang et al. [21]. Paper này thực chất chỉ là một bài báo kiểm chứng thực nghiệm (experimental study) chứ không đề xuất bất kỳ cải tiến cấu trúc hay toán học nào mới cho O-LoRA.
2.  **Nhầm lẫn hoặc thiếu sót trong thiết lập thực nghiệm O-LoRA:** Trong thuật toán O-LoRA chuẩn, mỗi tác vụ tuần tự sẽ có một adapter LoRA riêng và ép trực giao với các adapter trước đó. Tuy nhiên, ở thí nghiệm tinh chỉnh trên tập Alpaca (Bảng 6), Alpaca là một tập dữ liệu chỉ thị đơn lẻ (single instruction dataset) chứ không phải chuỗi tác vụ continual learning. Việc tác giả áp dụng "OLoRA" với các giá trị $\lambda$ khác nhau trên Alpaca mà không làm rõ cơ chế chia tác vụ hoặc tại sao cần ép dịch chuyển ở đây là một điểm cực kỳ mơ hồ và thiếu tính thuyết phục.
3.  **Hiện tượng sụp đổ hiệu năng toán học (Arithmetic collapse):** Trong Bảng 6, khi $\alpha \ge 32$ với $r=64$ hoặc $\alpha \ge 64$ với $r=128$, hiệu suất làm toán 2 chữ số và 4 chữ số của O-LoRA lập tức rơi về $0.0000\%$. Tác giả chỉ nhận xét chung chung là "nhạy cảm với siêu tham số" mà hoàn toàn không giải thích được cơ chế toán học đằng sau sự sụp đổ đột ngột này (ví dụ: do gradient bị triệt tiêu hay do ma trận tích chéo bị phạt quá nặng dẫn đến việc adapter không học được gì).
4.  **Thiếu các baseline Continual Learning mạnh khác:** Nghiên cứu chỉ so sánh O-LoRA với LoRA, Prefix-Tuning, P-Tuning và Prompt-Tuning. Tác giả hoàn toàn bỏ qua các baseline học liên tục không cần dữ liệu cũ (rehearsal-free) rất mạnh khác dành cho LLM như L2P, Progressive Prompts, hay SLIM mới hơn.
5.  **Chi phí bộ nhớ tăng cao:** Việc O-LoRA tốn thêm gần 20% bộ nhớ GPU (từ 30.49 GB lên 36.26 GB) là một điểm trừ lớn đối với các phương pháp PEFT vốn ưu tiên sự nhẹ nhàng. Tác giả không thảo luận giải pháp để tối ưu hóa lượng bộ nhớ phụ trội này.

### Questions for Authors
1.  Trong thực nghiệm tinh chỉnh trên tập Alpaca đơn lẻ (Bảng 6), các tác giả đã định nghĩa các "tác vụ tuần tự" như thế nào để áp dụng ràng buộc trực giao của O-LoRA? Nếu không chia tác vụ, ma trận $A_t$ được ép trực giao với cái gì?
2.  Tại sao hiệu suất số học (arithmetic_2ds và arithmetic_4ds) lại sụp đổ hoàn toàn về đúng $0.0000\%$ khi tăng hệ số $\alpha$ lên cao? Hiện tượng này có liên quan gì đến độ lớn của loss phạt trực giao $\lambda$ không?
3.  Tại sao chi phí bộ nhớ của O-LoRA lại tăng đáng kể (thêm ~5.77 GB GPU) so với LoRA thông thường? Liệu có cách nào để tối ưu hóa việc lưu trữ các ma trận khóa trực giao trong quá trình huấn luyện không?

### Recommendation
**Borderline Reject.** Mặc dù cung cấp một góc nhìn thực nghiệm thực tế về độ nhạy siêu tham số của O-LoRA trên LLaMA-2-7B, bài báo thiếu tính mới về mặt phương pháp luận và có những thiết lập thực nghiệm rất mơ hồ (như việc chạy O-LoRA trên một tập dữ liệu Alpaca đơn lẻ mà không giải thích cơ chế phân tách tác vụ). Nếu tác giả không làm rõ được cấu trúc chia tác vụ trong thí nghiệm Alpaca và cơ chế toán học gây ra sự sụp đổ toán học về 0%, bài báo chưa đủ tiêu chuẩn khoa học để chấp nhận tại các hội nghị hàng đầu.

---

## 5. Data & Metrics View Designer

### Tóm tắt dữ liệu & Baselines
*   **Datasets & Tasks:**
    *   Tác vụ đơn: Alpaca (52k instructions) tinh chỉnh hướng dẫn.
    *   Tác vụ chuỗi: Text Simplification (Simp), Explanation Generation (Exp), Inquisitive Question Generation (InqQG).
    *   Tập đánh giá: TruthfulQA (MC1, MC2), BLiMP Causative, MMLU GlobalFacts, Arithmetic Subtraction (2-digit & 4-digit).
    *   Tập đánh giá CL: MMLU (STEM, Social, Human, Other), Reasoning (BoolQ, PIQA, Winogrande, HellaSwag, MathQA, Mutual), RACE (high/middle).
*   **Baselines:** Fine-Tuning (FT), LoRA (các cấu hình $r$ và $\alpha$), Prefix-Tuning, P-Tuning, Prompt-Tuning, NEFTune+LoRA.
*   **Metrics:** Accuracy (cho các tập trắc nghiệm và số học), Forgetting Gradient (FG %) cho học liên tục.

### Đề xuất trực quan hóa (Charts)
1.  **Line Chart (Độ nhạy của $\alpha$ đối với tác vụ toán học):**
    *   *Trục X:* Giá trị scaling $\alpha$ (1, 16, 32, 64, 128)
    *   *Trục Y:* Accuracy (%) trên tác vụ số học 2 chữ số (arithmetic_2ds)
    *   *Nhóm (Series):* LoRA (r=64) vs O-LoRA (r=64, $\lambda=0.5$)
    *   *Mục đích:* Làm nổi bật điểm sụp đổ hiệu năng (cliff-edge drop) của cả hai phương pháp khi $\alpha$ tăng quá cao.
2.  **Bar Chart so sánh chỉ số quên (Forgetting Gradient Comparison):**
    *   *Trục X:* Ba nhóm năng lực (Domain Knowledge, Reasoning, Reading Comprehension)
    *   *Trục Y:* Forgetting Gradient (FG %) - Càng thấp càng tốt (giá trị âm nghĩa là cải thiện)
    *   *Nhóm (Series):* Full FT, LoRA (r=64, $\alpha=1$), Prefix-Tuning, O-LoRA (r=64, $\lambda=0.5$)
    *   *Mục đích:* Minh họa trực quan khả năng chống quên vượt trội của O-LoRA so với các kỹ thuật PEFT truyền thống.
3.  **Scatter Plot (Memory vs Performance Trade-off):**
    *   *Trục X:* Peak Memory Usage (GB)
    *   *Trục Y:* Average Accuracy trên các tác vụ tổng quát (%) hoặc chỉ số bảo toàn kiến thức
    *   *Nhóm (Series):* LoRA vs O-LoRA
    *   *Mục đích:* Giúp người thiết kế hệ thống lựa chọn cấu hình tối ưu giữa giới hạn phần cứng và độ ổn định tri thức.

### Thiết kế bảng tổng hợp đề xuất
Bảng nên tổng hợp hiệu năng giữ kiến thức và chi phí tài nguyên:
`[Phương pháp | Domain Knowledge FG (%) | Reasoning FG (%) | Reading Comprehension FG (%) | Peak Memory (GB) | Tốc độ huấn luyện (steps/s)]`
*Highlight:* Bold cho FG thấp nhất (ít quên nhất), in nghiêng các cấu hình bị sụp đổ hiệu năng trên tác vụ logic.

### Số liệu thô trích xuất từ Paper

**Bảng A: Chỉ số quên FG (%) của các phương pháp trên LLaMA-2-7B**
*(Giá trị FG càng thấp thể hiện mức độ quên càng ít; giá trị âm thể hiện hiệu năng tăng sau khi học liên tục)*

| Method / Setting | Domain Knowledge FG | Reasoning FG | Reading Comprehension FG |
| :--- | :---: | :---: | :---: |
| Full Fine-Tuning | 42.45 | 37.90 | 42.29 |
| LoRA ($r=64, \alpha=1$) | 30.79 | 20.03 | 19.69 |
| LoRA ($r=128, \alpha=1$) | 38.87 | 40.46 | 43.90 |
| LoRA ($r=64, \alpha=16$) | 40.43 | 40.73 | 43.66 |
| LoRA ($r=64, \alpha=128$) | 42.71 | 40.90 | 44.95 |
| Prefix-Tuning | 37.15 | 38.54 | 42.05 |
| P-Tuning | 36.51 | 38.54 | 42.05 |
| Prompt-Tuning | 38.39 | 37.39 | 41.81 |
| NEFTune + LoRA (noise=1) | 40.55 | 40.84 | 43.90 |
| NEFTune + LoRA (noise=5) | 42.40 | 40.94 | 42.69 |
| **O-LoRA** ($r=64, \lambda=0.5$) | **1.11** | **0.43** | **-1.20** |
| **O-LoRA** ($r=128, \lambda=0.5$) | **0.77** | **0.25** | **-0.24** |
| **O-LoRA** ($r=64, \alpha=16, \lambda=0.5$) | **3.49** | **-0.16** | **-0.88** |
| **O-LoRA** ($r=64, \alpha=128, \lambda=0.5$) | **1.70** | **0.51** | **1.20** |

---

## 6. Khả năng áp dụng cho VN-Legal-AI / CDNCTQ
*   **Chiến lược tinh chỉnh luật liên tục:** Khi chúng ta Continual Pre-train hoặc Fine-tune mô hình LegalSLM qua các văn bản pháp luật mới cập nhật (ví dụ: các nghị định mới ban hành năm 2026), việc sử dụng O-LoRA có thể giúp mô hình hấp thụ nhanh kiến thức luật mới mà không làm mất đi khả năng đọc hiểu ngôn ngữ và hành văn pháp lý cơ bản của mô hình gốc (thể hiện qua việc FG Reading Comprehension đạt giá trị âm).
*   **Nguy cơ sụp đổ tư duy logic pháp lý:** Cảnh báo từ thực nghiệm số học của paper chỉ ra rằng O-LoRA rất nhạy cảm siêu tham số và có thể phá hủy hoàn toàn khả năng suy luận logic/tính toán của mô hình. Trong bài toán suy luận pháp lý (Legal Reasoning), vốn đòi hỏi tính logic cực cao, việc chọn sai tỷ lệ $\alpha/r$ hoặc phạt trực giao quá mức có thể khiến mô hình đưa ra các suy luận vô nghĩa ($0\%$ chính xác). Do đó, cần kiểm thử chặt chẽ trên các tập suy luận như Legal Syllogism trước khi triển khai thực tế.
*   **Đánh giá chi phí GPU:** Dự án cần cân đối chi phí hạ tầng. O-LoRA tăng khoảng $19\%$ dung lượng VRAM yêu cầu so với LoRA tiêu chuẩn trên cùng cấu hình ($r=64$, $\alpha=16$). Khi huấn luyện các mô hình lớn hơn (như Qwen-7B hoặc LLaMA-13B), lượng VRAM tăng thêm này có thể vượt quá giới hạn của một GPU đơn lẻ (ví dụ A100 40GB or RTX 3090/4090 24GB), đòi hỏi phải phân tán mô hình (model parallelism) phức tạp hơn.
