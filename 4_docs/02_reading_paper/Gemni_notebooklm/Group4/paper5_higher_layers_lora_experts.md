# Phân tích Chi tiết: Higher Layers Need More LoRA Experts (MoLA)

## 1. Tóm tắt cấp cao (High-level Summary)
Bài báo giải quyết các giới hạn hiệu năng và tính dư thừa tham số trong các phương pháp Tinh chỉnh Hiệu quả Tham số (PEFT) khi kết hợp Low-Rank Adaptation (LoRA) với cấu trúc Mixture-of-Experts (MoE) [1, 2]. Trong khi các thiết kế LoRA-MoE hiện tại phân bổ đều một số lượng chuyên gia (experts) cố định cho tất cả các tầng Transformer, nghiên cứu này chỉ ra rằng cách tiếp cận đó gây lãng phí bộ nhớ ở các tầng dưới và hạn chế khả năng học ở các tầng trên [3, 4]. Nhóm tác giả đề xuất **MoLA (MoE-LoRA with Layer-wise Expert Allocation)**, một kiến trúc gán số lượng LoRA experts linh hoạt theo từng tầng [5, 6]. Thông qua phân tích thực nghiệm, bài báo chứng minh rằng các tầng thấp hơn (chuyên xử lý cú pháp cấp token cơ bản) có độ dư thừa expert rất cao, trong khi các tầng cao hơn (chuyên xử lý ngữ nghĩa trừu tượng, đặc thù tác vụ) cần nhiều experts hơn để đạt hiệu năng tối đa và chống quên thảm khốc trong học liên tục [7, 8].

---

## 2. Tóm tắt chi tiết từng phần (Section Summaries)
*   **Introduction (Giới thiệu):** Thảo luận về xu hướng kết hợp PEFT (LoRA) với MoE nhằm mở rộng khả năng biểu diễn của LLM mà không tăng tải chi phí tính toán. Tuy nhiên, các kiến trúc MoE thông thường thường gặp hiện tượng sụp đổ biểu diễn (representational collapse), dẫn đến sự dư thừa của nhiều expert. Nhóm tác giả đặt câu hỏi về mức độ dư thừa của các expert trong cấu trúc PEFT-MoE và đề xuất tìm kiếm sự phân bổ tối ưu số lượng expert theo tầng của Transformer.
*   **Method (Phương pháp):** Giới thiệu kiến trúc MoLA, thay thế các lớp tuyến tính thông thường bằng một bộ định tuyến (router) và một nhóm các LoRA experts song song. Nhóm tác giả thiết kế 4 cấu hình phân bổ expert cho mô hình 32 tầng (LLaMA-2-7B): Hình tam giác ($\triangle$, nhiều expert ở tầng thấp, giảm dần lên trên), Tam giác ngược ($\nabla$, ít expert ở tầng thấp, tăng dần lên trên), Đồng hồ cát ($\bowtie$, nhiều ở hai đầu, ít ở giữa), và Hình chữ nhật ($\square$, số lượng expert đều nhau ở mọi tầng). Để đảm bảo so sánh công bằng, tổng số tham số huấn luyện của tất cả các cấu hình được khống chế bằng nhau.
*   **Experiments (Thực nghiệm):** Đánh giá các cấu hình trên các benchmark NLU (MRPC, COLA, RTE) và Commonsense QA sau khi tiền huấn luyện nhẹ trên tập OpenOrca. Kết quả cho thấy cấu hình Tam giác ngược (MoLA-$\nabla$, ký hiệu là 2-4-6-8 experts) đạt hiệu năng cao nhất, vượt trội rõ rệt so với LoRA chuẩn và cấu hình Rectangle uniform truyền thống, dù sử dụng ít hơn 40% tham số so với baseline uniform đầy đủ.
*   **Discussion (Thảo luận):** Tiến hành định lượng mức độ dư thừa expert bằng cách tính toán chuẩn Frobenius giữa ma trận trọng số của các expert trong cùng một tầng. Kết quả xác nhận các expert ở tầng dưới có chuẩn Frobenius rất thấp (rất giống nhau/dư thừa nhiều), trong khi các expert ở tầng trên có chuẩn Frobenius cao (khác biệt lớn/phân hóa tốt). Ngoài ra, trong bài test học liên tục đa miền (Continual Domain Adaptation), MoLA-$\nabla$ duy trì độ suy giảm hiệu năng thấp nhất.

---

## 3. Methodology Deep-Dive
### Khung hệ thống & Thuật toán
MoLA được xây dựng trên một Transformer backbone đóng băng. Với tầng thứ $j$, mô hình tạo ra $N_j$ cặp ma trận LoRA hạng thấp (experts). Bộ định tuyến $S_{i}^{jt}(x)$ nhận đầu vào $x$, tính toán trọng số bằng Softmax qua một ma trận học được $W_r$, và chọn ra top-$K$ experts để xử lý:
$$S_{i}^{jt}(x) = \text{TopK}(\text{Softmax}(W_r^{jt} x), K)_i$$
Đầu ra của tầng $j$ được biểu diễn bằng công thức:
$$h_{jt} = W_0^{jt} x + \sum_{i=1}^{K} S_i^{jt}(x) A_i^{jt} B_i^{jt} x$$

### Ý tưởng kỹ thuật chính
1.  **Phân bổ Expert theo tầng (Layer-wise Allocation):** Thay vì gán cố định số lượng expert, mô hình chia 32 tầng thành 4 block (mỗi block 8 tầng) và gán số lượng expert tăng dần (2 $\rightarrow$ 4 $\rightarrow$ 6 $\rightarrow$ 8) từ thấp lên cao (cấu hình MoLA-$\nabla$).
2.  **Đo lường độ dư thừa bằng Frobenius Norm:** 
    $$\text{Redundancy}_j = \text{Mean}(\|W_{\text{expert}_a} - W_{\text{expert}_b}\|_F)$$
    Đo khoảng cách ma trận giữa các expert trong cùng một tầng sau huấn luyện để chứng minh giả thuyết về sự sụp đổ biểu diễn ở tầng thấp.

### Dữ liệu, Thiết lập & Metrics
*   **Dữ liệu:** Huấn luyện trên 50,000 mẫu OpenOrca. Đánh giá trên MRPC, COLA, RTE, ScienceQA, CommonsenseQA, OpenbookQA. Thí nghiệm học liên tục trên 5 miền khoa học (Sinh học, Vật lý, Hóa học, Kinh tế, Khoa học Trái đất).
*   **Thiết lập:** LLaMA-2-7B. Optimizer AdamW, lr=3e-4, batch size 128, rank $r=8$, $\alpha=16$. Sử dụng Top-2 routing ($K=2$).
*   **Thước đo:** Accuracy (%), Overall Performance (OP - độ chính xác trung bình sau khi học liên tục), Performance Drop (PD - mức độ suy giảm trung bình của tri thức cũ).

### 3 Giả định kỹ thuật quan trọng
1.  **Sự phân hóa đặc trưng theo chiều sâu mạng:** Các tầng dưới của Transformer chỉ học các đặc trưng cú pháp và token cơ bản mang tính phổ quát, trong khi các tầng trên chịu trách nhiệm xử lý ngữ nghĩa trừu tượng mang tính đặc thù của tác vụ.
2.  **Độ tương đồng ma trận phản ánh sự lãng phí:** Nếu khoảng cách chuẩn Frobenius giữa hai ma trận LoRA của expert trong cùng một lớp rất nhỏ, chúng đang thực hiện phép biến đổi gần như trùng lặp, khiến tài nguyên tham số bị lãng phí.
3.  **Hạng LoRA nhỏ đủ để làm expert độc lập:** Giả định rằng ma trận hạng thấp ($r=8$) có đủ dung lượng biểu diễn để hoạt động như một expert chuyên biệt trong cấu trúc MoE, thay vì cần các tầng FFN dày đặc truyền thống.

---

## 4. Phản biện học thuật (Brutally Honest Review)

### Summary
Paper đề xuất cấu trúc MoLA nhằm tối ưu hóa việc phân bổ các LoRA experts theo chiều sâu của Transformer. Qua việc phân tích Frobenius Norm, nhóm tác giả chỉ ra các expert ở tầng dưới của LLM bị sụp đổ biểu diễn và trở nên dư thừa. Thực nghiệm trên LLaMA-2-7B chứng minh rằng việc gán ít expert ở tầng dưới và tăng dần số lượng expert ở tầng trên (cấu hình Tam giác ngược) giúp mô hình đạt hiệu năng vượt trội và giảm thiểu hiện tượng quên thảm khốc khi học liên tục.

### Strengths
1.  **Phát hiện kiến trúc thực tế và trực quan:** Kết luận "tầng cao cần nhiều expert hơn tầng thấp" rất nhất quán với các lý thuyết interpretability của Transformer và dễ dàng triển khai.
2.  **Phân tích định lượng xuất sắc:** Việc sử dụng chuẩn Frobenius để chứng minh trực quan sự dư thừa của expert ở tầng dưới (Hình 4) mang lại độ tin cậy khoa học rất cao cho giả thuyết của bài báo.
3.  **Khả năng chống quên tốt trong Continual Learning:** Đưa ra chỉ số PD cải tiến rõ rệt chứng minh việc tối ưu hóa phân bổ expert giúp củng cố tính ổn định của mô hình khi tiếp nhận luồng dữ liệu mới.

### Weaknesses
1.  **Thiết kế phân bổ mang tính thủ công (Hardcoded Grid):** Các cấu hình phân bổ (như 2-4-6-8) được chia cứng theo các block 8 tầng. Một thiết kế tối ưu hơn nên sử dụng cơ chế Dynamic Pruning hoặc Differentiable Architecture Search để mô hình tự động điều chỉnh số lượng expert tối ưu ở từng lớp.
2.  **Giới hạn ở quy mô mô hình nhỏ:** Thực nghiệm chỉ được thực hiện trên LLaMA-2-7B. Ở các quy mô lớn hơn (70B+), hành vi định tuyến của MoE và sự dư thừa tham số có thể thay đổi rất nhiều, khiến kết luận chưa đảm bảo tính tổng quát hóa tuyệt đối.
3.  **Tập dữ liệu huấn luyện quá nhỏ:** Việc chỉ sử dụng 50,000 mẫu từ OpenOrca để tinh chỉnh chỉ thị có thể chưa đủ để kích hoạt tối đa năng lực của các expert ở tầng thấp. Nếu huấn luyện trên tập dữ liệu hàng triệu mẫu, có khả năng các tầng thấp cũng sẽ bắt đầu phân hóa và cần nhiều expert hơn.
4.  **Thiếu đánh giá về throughput (độ trễ thời gian thực):** Dù tiết kiệm tham số, bộ định tuyến MoE ở mỗi tầng Transformer vẫn tạo ra overhead tính toán. Tác giả không cung cấp số liệu so sánh thời gian huấn luyện và tốc độ suy luận (tokens/sec) thực tế so với LoRA truyền thống.
5.  **Vấn đề router ở các tầng thấp:** Ở cấu hình MoLA-$\nabla$ (2-4-6-8) với Top-2 routing, các tầng 1-8 chỉ có 2 experts. Điều này nghĩa là bộ định tuyến ở các tầng này không hoạt động (luôn chọn cả 2 experts), làm mất đi tính thưa (sparsity) của MoE và gây lãng phí tính toán router.

### Questions for Authors
1.  Tại sao không sử dụng một hàm phạt thưa (sparsity regularization) trực tiếp lên router để mô hình tự động tắt/bỏ các expert dư thừa trong quá trình train thay vì chia block thủ công?
2.  Tốc độ suy luận (tokens/second) của cấu hình MoLA-$\nabla$ thay đổi như thế nào so với LoRA chuẩn khi deploy trên cùng một phần cứng?
3.  Khi sử dụng cấu hình Tam giác ngược với Top-2 routing ở 8 tầng đầu tiên (chỉ có 2 experts), router luôn chọn cả 2. Cơ chế load balancing loss ở các tầng này được xử lý như thế nào khi không có sự lựa chọn?

### Recommendation
**Accept.** Mặc dù thiết kế phân bổ còn mang tính kinh nghiệm thủ công, MoLA mang lại đóng góp quan trọng trong việc chỉ ra sự dư thừa tham số của PEFT-MoE. Phân tích Frobenius Norm rất rõ ràng và kết quả thực nghiệm cải tiến cả downstream task lẫn khả năng chống quên là đủ thuyết phục.

---

## 5. Data & Metrics View Designer

### Tóm tắt dữ liệu & Baselines
*   **Datasets:** OpenOrca (Instruction Tuning), MRPC, COLA, RTE, ScienceQA, CommonsenseQA, OpenbookQA (Direct Fine-tuning). Continual Learning: 5 miền khoa học (Bio, Phy, Chem, Econ, Earth).
*   **Baselines:** Full-Parameter FT, LoRA, MoLA-$\square$ (Uniform), MoLA-$\triangle$ (Triangle), MoLA-$\bowtie$ (Hourglass).

### Đề xuất trực quan hóa (Charts)
1.  **Line Chart biểu diễn Frobenius Norm theo tầng:**
    *   *Trục X:* Chỉ số tầng Transformer (1 đến 32)
    *   *Trục Y:* Frobenius Norm trung bình giữa các expert
    *   *Nhóm (Series):* MoLA-$\nabla$, MoLA-$\triangle$
    *   *Mục đích:* Tái hiện Hình 4 để chứng minh các expert ở tầng cao có sự khác biệt lớn (norm cao), trong khi tầng thấp rất giống nhau (norm thấp).
2.  **Grouped Bar Chart so sánh các cấu hình:**
    *   *Trục X:* Các tập dữ liệu đánh giá (MRPC, COLA, RTE, ScienceQA, CSQA, OBQA)
    *   *Trục Y:* Accuracy (%)
    *   *Nhóm (Series):* LoRA, MoLA-$\square$ (uniform 8888), MoLA-$\nabla$ (tam giác ngược 2468)
    *   *Mục đích:* Nổi bật việc MoLA-$\nabla$ vượt trội hơn LoRA và đạt kết quả tương đương hoặc hơn uniform 8888 dù dùng ít tham số hơn.
3.  **Scatter Plot Pareto Frontier:**
    *   *Trục X:* Số lượng tham số huấn luyện (Trainable Parameters)
    *   *Trục Y:* Average Accuracy (%)
    *   *Nhóm (Series):* Các cấu hình MoLA
    *   *Mục đích:* Trực quan hóa hiệu quả sử dụng tham số của cấu hình Tam giác ngược (nằm ở góc trên bên trái tối ưu nhất).

### Thiết kế bảng tổng hợp đề xuất
Bảng tổng hợp nên tích hợp cả kết quả tinh chỉnh thông thường và kết quả học liên tục để làm nổi bật ưu thế của MoLA-$\nabla$ trên cả hai khía cạnh.

### Số liệu thô trích xuất từ Paper

| Model / Configuration | Trainable Params Ratio | Continual OP | Continual PD | MRPC | COLA | RTE | ScienceQA | CSQA | OBQA |
| :--- | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Full-Parameter FT | 100% | - | - | 87.13% | 86.29% | 87.73% | 93.12% | 77.48% | 80.4% |
| LoRA (Rank 64) | 1.5% | 78.67% | -2.17% | 84.41% | 84.95% | 84.48% | 91.01% | 74.61% | 76.6% |
| MoLA-$\square$ (Uniform 8888) | 2.4% | - | - | 84.23% | 85.72% | 87.36% | 92.13% | 77.15% | 78.4% |
| MoLA-$\square$ (Uniform 5555) | 1.5% | 88.80% | -0.60% | 84.93% | 84.56% | 88.81% | 91.73% | 75.92% | 77.6% |
| MoLA-$\triangle$ (Triangle 8642) | 1.5% | 88.84% | -2.10% | 84.46% | 85.23% | 89.17% | 91.41% | 76.33% | 78.8% |
| MoLA-$\bowtie$ (Hourglass 8228) | 1.5% | 83.82% | -3.92% | 84.35% | 84.85% | 87.72% | 91.41% | 75.02% | 77.4% |
| **MoLA-$\nabla$ (Tam giác ngược 2468)** | **1.5%** | **89.82%** | **-0.47%** | **85.45%** | **86.19%** | **89.17%** | **92.36%** | **77.15%** | **78.4%** |

---

## 6. Khả năng áp dụng cho VN-Legal-AI / CDNCTQ
*   **Thiết kế Adapter MoE tối ưu tham số:** Khi xây dựng kiến trúc đa chuyên gia (Multi-Expert) để giải quyết các tác vụ luật khác nhau (ví dụ: Soạn thảo văn bản, Tra cứu án lệ, Phân tích hợp đồng) dựa trên mô hình tiếng Việt nền tảng, chúng ta không nên phân bổ số lượng adapter giống nhau ở mọi tầng. Áp dụng phát hiện của MoLA, ta nên giữ các tầng Transformer dưới cực kỳ đơn giản (chỉ cần 1-2 adapter hoặc thậm chí đóng băng hoàn toàn không dùng adapter) và dồn tài nguyên GPU để huấn luyện nhiều adapter chuyên biệt ở các tầng trên cùng của mô hình để phân tách hành vi logic luật tốt hơn.
*   **Chống quên khi học liên tục đa luật:** Kết quả thử nghiệm Continual Learning của MoLA-$\nabla$ cho thấy chỉ số PD rất thấp (-0.47%). Khi chúng ta liên tục huấn luyện mô hình qua các luật mới phát hành theo năm, việc phân bổ nhiều expert hơn ở tầng cao sẽ giúp mô hình lưu trữ kiến thức mới vào các nhánh chuyên biệt ở tầng trên mà không ghi đè vào các biểu diễn cú pháp tiếng Việt dùng chung ở tầng dưới.
*   **Lưu ý khi thiết lập Top-K:** Trong dự án VN-Legal-AI, nếu quyết định dùng cấu hình tam giác ngược, chúng ta cần tránh việc lãng phí tính toán ở các tầng thấp khi số lượng expert bằng đúng Top-K (ví dụ dùng Top-2 routing ở block chỉ có 2 experts). Nên đóng băng hoặc tắt hẳn router ở các block này để tăng tốc độ suy luận cho hệ thống.
