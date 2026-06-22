# Phân tích Chi tiết: Progressive Prompts: Continual Learning for Language Models

## 1. Tóm tắt cấp cao (High-level Summary)
Bài báo giải quyết hai thách thức cốt lõi trong Học liên tục (Continual Learning - CL) đối với các Mô hình Ngôn ngữ Lớn (LLMs): loại bỏ sự quên thảm khốc (catastrophic forgetting) và thúc đẩy khả năng chuyển giao kiến thức tiến (forward transfer) [1, 2]. Nhóm tác giả đề xuất phương pháp mang tên **"Progressive Prompts"**, một cơ chế điều chỉnh hiệu quả tham số (PEFT) trong đó mô hình ngôn ngữ nền tảng được giữ đóng băng hoàn toàn [1, 3]. Với mỗi tác vụ mới đến, mô hình sẽ học một soft prompt mới, sau đó soft prompt này được nối chuỗi tuần tự với các soft prompt đã đóng băng của các tác vụ trước đó để tạo thành chuỗi tiền tố (prefix) cho đầu vào [1, 3]. So với các phương pháp CL truyền thống phải lưu trữ lượng lớn dữ liệu cũ để phát lại (experience replay) hoặc áp dụng các hình phạt điều chuẩn (regularization) làm giảm năng lực học, Progressive Prompts mang lại giải pháp kiến trúc cực kỳ nhẹ [4]. Nhờ huấn luyện dưới 0.1% tổng số tham số, mô hình loại bỏ hoàn toàn sự quên theo thiết kế toán học, đồng thời giúp các tác vụ cũ hỗ trợ tích cực cho việc học các tác vụ mới cùng miền [5, 6].

---

## 2. Tóm tắt chi tiết từng phần (Section Summaries)
*   **Introduction (Giới thiệu):** Khái quát hiện trạng các mô hình ngôn ngữ lớn bị sụp đổ hiệu năng trên các tác vụ cũ khi học tuần tự. Các giải pháp truyền thống gặp khó khăn trong việc cân bằng giữa chống quên và chuyển giao kiến thức tiến (tận dụng tri thức cũ để học cái mới nhanh hơn). Tác giả giới thiệu Progressive Prompts lấy cảm hứng từ Progressive Networks nhưng tối ưu bộ nhớ hơn nhiều, bằng cách chỉ học thêm một số lượng nhỏ các token ảo cho mỗi tác vụ và nối chúng lại với nhau.
*   **Method (Phương pháp):** Chi tiết hóa khung làm việc với trọng số mô hình gốc hoàn toàn đóng băng. Khi tác vụ mới $T_k$ xuất hiện, chỉ có prompt $P_k$ tương ứng được khởi tạo và huấn luyện dựa trên hàm loss log-likelihood, sử dụng chuỗi đầu vào dạng $[P_k, P_{k-1}, \dots, P_1, x]$. Để giải quyết sự mất ổn định đặc trưng của prompt tuning, nhóm nghiên cứu đề xuất cơ chế "Residual MLP Reparameterization" ($P'_k = MLP(P_k) + P_k$) giúp tăng tốc độ hội tụ và độ ổn định của gradient, sau đó có thể loại bỏ lớp MLP này khi hoàn tất huấn luyện để tiết kiệm bộ nhớ.
*   **Experiments (Thực nghiệm):** Đánh giá hiệu năng trên Standard 5-task CL Benchmark và một Long CL Benchmark gồm 15 tác vụ (tích hợp thêm GLUE, SuperGLUE và IMDB). Thử nghiệm trên BERT-base và T5-large cho thấy Progressive Prompts vượt trội hoàn toàn so với các phương pháp replay, EWC, hay các kỹ thuật prompt tuning khác như LFPT5. Đặc biệt ở kịch bản ít dữ liệu (few-shot 20 samples/class), phương pháp đạt cải tiến hiệu năng vượt bậc, lần lượt là +21.9% trên T5 và +33.3% trên BERT so với các SOTA trước đó.
*   **Discussion / Conclusion (Thảo luận / Kết luận):** Đặt hiệu quả của Progressive Prompts vào bức tranh chung của các kỹ thuật CL, chỉ ra rằng phương pháp này loại bỏ được các rủi ro bảo mật dữ liệu và gánh nặng bộ nhớ của replay buffer cũng như hiện tượng quên thảm khốc của regularization trên chuỗi tác vụ dài. Tác giả kết luận phương pháp đã thiết lập một tiêu chuẩn mới cho học liên tục hiệu quả tham số bằng khả năng tái sử dụng tri thức mà không làm tổn hại đến hiệu năng cũ.

---

## 3. Methodology Deep-Dive
### Khung hệ thống & Thuật toán
Với chuỗi gồm $m$ tác vụ, các tham số mô hình ngôn ngữ $\theta$ được giữ đóng băng. Khi học tác vụ thứ $k$, mô hình chỉ tối ưu hóa các tham số $\theta_{P_k}$ của soft prompt $P_k$. Văn bản đầu vào $x$ được biểu diễn dưới dạng:
$$[P_k, P_{k-1}, \dots, P_1, x]$$
Trong đó tất cả các prompt $P_i$ ($i < k$) đã được huấn luyện ở quá khứ sẽ bị khóa cứng (frozen).

Hàm mục tiêu tối ưu hóa cho tác vụ $T_k$ là:
$$\min_{\theta_{P_k}} L(\theta_{P_k}) = - \sum_{x,y \in T_k} \log p(y | [P_k, \dots, P_1, x]; \theta, \theta_{P_1}, \dots, \theta_{P_k})$$

### Ý tưởng kỹ thuật chính
1.  **Nối chuỗi Prompt lũy tiến (Progressive Concatenation):** Cho phép các token ảo của tác vụ mới tương tác trực tiếp với các token ảo cũ qua cơ chế Self-Attention của Transformer, từ đó kế thừa tri thức biểu diễn đã học.
2.  **Reparameterization qua MLP phần dư (Residual MLP):** 
    $$P'_k = MLP(P_k) + P_k$$
    Sử dụng MLP 2 lớp với kết nối tắt (skip connection) để chiếu các token prompt trước khi đưa vào Transformer. Việc này giúp ổn định quá trình lan truyền ngược và tránh hiện tượng MLP học phép chiếu đồng nhất (identity mapping). Lớp MLP này sẽ bị loại bỏ sau khi kết thúc huấn luyện tác vụ.

### Dữ liệu, Thiết lập & Metrics
*   **Dữ liệu:** 5 tác vụ chuẩn (AG News, Amazon, Yelp, DBpedia, Yahoo) và 15 tác vụ chuỗi dài (thêm MNLI, QQP, RTE, SST2, WiC, CB, COPA, MultiRC, BoolQ, IMDB).
*   **Thiết lập:** BERT-base (prompt length = 20) và T5-large (prompt length = 10 cho chuỗi dài, 50 cho chuỗi Standard). Đánh giá dưới 3 kịch bản: Few-shot (20 mẫu/lớp), Medium (200 mẫu/lớp) và Full-shot (1000 mẫu hoặc toàn bộ tập dữ liệu).
*   **Thước đo:** Average Accuracy, Forward Transfer (FWT - đo mức độ tri thức cũ giúp ích cho tác vụ mới), và Backward Transfer (BWT - đo mức độ quên tác vụ cũ).

### 3 Giả định kỹ thuật quan trọng
1.  **Năng lực biểu diễn của Soft Prompt:** Các soft prompt liên tục có đủ không gian biểu diễn để điều hướng thành công mô hình ngôn ngữ gốc (đã bị đóng băng hoàn toàn) giải quyết các tác vụ mới.
2.  **Đặc trưng hỗ trợ tiến (Forward Transfer Synergy):** Các prompt của tác vụ trước chứa các đặc trưng mang tính chất ngữ nghĩa chung hoặc miền liên quan, hỗ trợ trực tiếp cho việc học tác vụ tiếp theo thay vì đóng vai trò như nhiễu đối kháng.
3.  **Giới hạn ngữ cảnh (Context Window Bounds):** Tổng chiều dài của tất cả các prompt nối chuỗi cộng với văn bản đầu vào không được vượt quá giới hạn cửa sổ ngữ cảnh tối đa của Transformer gốc.

---

## 4. Phản biện học thuật (Brutally Honest Review)

### Summary
Paper đề xuất phương pháp Progressive Prompts cho phép học liên tục hiệu quả tham số mà không bị quên tri thức cũ. Bằng cách chèn soft prompt mới ở mỗi tác vụ và nối nó trực tiếp với chuỗi các prompt cũ đã được khóa lại, phương pháp loại bỏ hiện tượng quên thảm khốc theo thiết kế. Các thực nghiệm trên T5 và BERT chứng minh tính ưu việt của phương pháp, đặc biệt trong các kịch bản few-shot và chứng minh sự tồn tại của forward transfer ngữ nghĩa.

### Strengths
1.  **Loại bỏ hiện tượng quên tuyệt đối:** Do mô hình gốc và các prompt cũ được đóng băng 100%, không có bất kỳ rủi ro nào về việc ghi đè hoặc phá hủy trọng số cũ, giải quyết triệt để bài toán quên thảm khốc.
2.  **Tận dụng Forward Transfer tự nhiên:** Việc nối chuỗi cho phép Attention của Transformer liên kết các token ảo mới với các token ảo cũ, giúp mô hình tái sử dụng tri thức tương đồng ngữ nghĩa một cách tự động.
3.  **Tối ưu hóa reparameterization thực tế:** Việc thiết kế MLP có kết nối phần dư giúp ổn định hóa quá trình huấn luyện prompt tuning (vốn rất nhạy cảm và dễ phân kỳ) là một đóng góp kỹ thuật chất lượng.

### Weaknesses
1.  **Cạn kiệt cửa sổ ngữ cảnh (Context Window Exhaustion):** Đây là điểm yếu nghiêm trọng nhất. Do chiều dài prompt tăng tuyến tính theo số lượng tác vụ (ví dụ mỗi tác vụ cần 20 token, sau 100 tác vụ sẽ cần tới 2000 token tiền tố), mô hình sẽ nhanh chóng làm cạn kiệt cửa sổ ngữ cảnh của các Transformer truyền thống.
2.  **Độ trễ suy luận tăng phi tuyến (Quadratic Latency Scaling):** Do chi phí tính toán Attention của Transformer tăng theo bình phương chiều dài chuỗi ($O(L^2)$), việc liên tục kéo dài chuỗi prompt sẽ khiến thời gian sinh từ tăng vọt ở các tác vụ cuối chuỗi.
3.  **Yêu cầu Task-ID khi Inference:** Để xây dựng đúng chuỗi prompt $[P_k, P_{k-1}, \dots, P_1, x]$, mô hình bắt buộc phải biết chính xác nhãn tác vụ hiện tại (Task-ID) lúc suy luận. Điều này làm hạn chế khả năng ứng dụng trong các tác vụ task-agnostic thực tế.
4.  **Nhạy cảm với thứ tự tác vụ (Task Ordering Sensitivity):** Sự chuyển giao tiến phụ thuộc mạnh mẽ vào các tác vụ đi trước. Nếu một tác vụ có chất lượng kém hoặc chứa nhiều nhiễu được học sớm, nó có thể phá hỏng biểu diễn ngữ cảnh của toàn bộ chuỗi prompt phía sau.
5.  **Chỉ đánh giá trên tác vụ phân loại:** Dù sử dụng T5 (vốn là mô hình sinh), toàn bộ thực nghiệm chỉ đánh giá dựa trên độ chính xác của các bài toán phân loại văn bản, bỏ ngỏ hiệu năng trên các tác vụ sinh văn bản tự do (NLG).

### Questions for Authors
1.  Làm thế nào để xử lý giới hạn cứng của cửa sổ ngữ cảnh LLM khi số lượng tác vụ trong vòng đời học liên tục tiến tới hàng trăm hoặc hàng ngàn?
2.  Mô hình có bắt buộc phải biết Task-ID tại thời điểm suy luận để nối chuỗi prompt không, và làm cách nào để thích ứng phương pháp này sang kịch bản hoàn toàn task-agnostic?
3.  Bạn đã thử nghiệm chèn một tác vụ có phân phối hoàn toàn lệch chuẩn (out-of-domain noise) vào giữa chuỗi chưa, và nó ảnh hưởng thế nào đến khả năng forward transfer của các tác vụ sau đó?

### Recommendation
**Accept.** Đóng góp của bài báo về việc thiết lập một cơ chế CL hoàn toàn miễn nhiễm với forgetting mà không cần lưu trữ dữ liệu cũ là rất rõ ràng và thực tiễn. Mặc dù gặp rào cản về giới hạn ngữ cảnh khi scale dài hạn, hiệu năng vượt trội trong chế độ ít dữ liệu (few-shot) vẫn chứng minh giá trị học thuật cao của nghiên cứu này.

---

## 5. Data & Metrics View Designer

### Tóm tắt dữ liệu & Baselines
*   **Datasets:** 15 tập dữ liệu (AG News, Amazon, Yelp, DBpedia, Yahoo, MNLI, QQP, RTE, SST2, WiC, CB, COPA, MultiRC, BoolQ, IMDB).
*   **Baselines:** Finetune, Replay, Prompt Tuning, Per-task Prompts, IDBR, LFPT5, Multi-task Learning (MTL).

### Đề xuất trực quan hóa (Charts)
1.  **Line Chart biểu diễn Forward Transfer theo lượng dữ liệu:**
    *   *Trục X:* Số lượng dữ liệu huấn luyện mỗi lớp (2, 5, 20, All)
    *   *Trục Y:* Average Score (%)
    *   *Nhóm (Series):* Prompt Tuning vs Progressive Prompts
    *   *Mục đích:* Thể hiện khả năng tận dụng tri thức cũ cực tốt của Progressive Prompts khi lượng dữ liệu huấn luyện cực kỳ ít (few-shot).
2.  **Heatmap Attention liên prompt (Inter-prompt Attention):**
    *   *Trục X:* Các tác vụ cũ trong chuỗi (Tác vụ 1 đến 14)
    *   *Trục Y:* Tác vụ hiện tại đang suy luận (Tác vụ 2 đến 15)
    *   *Mục đích:* Tái hiện Hình 2 của paper để chứng minh các tác vụ tương đồng (ví dụ Yelp và Amazon) có điểm chú ý (attention) rất cao với nhau.
3.  **Grouped Bar Chart so sánh Average Accuracy trên chuỗi 15 tác vụ:**
    *   *Trục X:* Số lượng samples/class (20, 200, 1000)
    *   *Trục Y:* Average Accuracy (%)
    *   *Nhóm (Series):* Replay, LFPT5, ProgPrompt, MTL
    *   *Mục đích:* Thể hiện khoảng cách hiệu năng vượt trội (>20%) của Progressive Prompts so với các phương pháp CL khác trên T5-Large.

### Thiết kế bảng tổng hợp đề xuất
Bảng tổng hợp nên so sánh trực tiếp hiệu năng trên mô hình T5-Large và BERT-base qua 3 mức giới hạn dữ liệu (20, 200, 1000 samples) để người đọc dễ dàng thấy được sự vượt trội trong điều kiện ít dữ liệu.

### Số liệu thô trích xuất từ Paper (T5-Large trên Long-Sequence Order 8)

| Method | 20 Samples/Class | 200 Samples/Class | 1000 Samples/Class |
| :--- | :---: | :---: | :---: |
| Finetune (Full model) | 9.3 | 8.9 | 7.4 |
| Experience Replay | 46.0 | 45.0 | 55.2 |
| Prompt Tuning (Shared) | 9.7 | 8.4 | 8.2 |
| Per-task Prompts (No Transfer) | 69.9 | 75.2 | 77.0 |
| LFPT5 (Previous SOTA) | 54.7 | 61.6 | 70.4 |
| **ProgPrompt (Ours)** | **75.4** | **79.1** | **79.5** |
| Multi-Task Learning (Upper Bound) | 70.7 | 72.5 | 76.3 |

---

## 6. Khả năng áp dụng cho VN-Legal-AI / CDNCTQ
*   **Học chuyển giao giữa các bộ luật:** Trong miền Luật Việt Nam, các văn bản pháp luật thường có tính kế thừa hoặc liên quan chặt chẽ với nhau (ví dụ: Luật Đất đai liên quan đến Luật Nhà ở và Luật Kinh doanh Bất động sản). Áp dụng Progressive Prompts cho phép chúng ta huấn luyện prompt cho Luật Đất đai trước, sau đó khi huấn luyện prompt cho Luật Nhà ở, mô hình có thể trực tiếp tham chiếu (attend) sang prompt Đất đai cũ để hiểu ngữ cảnh nhanh hơn mà không cần học lại từ đầu.
*   **Tính ổn định nhờ Residual MLP:** Khi huấn luyện các prompt tiếng Việt chuyên biệt cho hệ thống hỏi đáp pháp luật, sự mất ổn định của prompt tuning là một vấn đề lớn. Việc áp dụng công thức Residual MLP reparameterization trong pha train sẽ giúp quá trình hội tụ nhanh hơn và tránh hiện tượng mô hình sinh ra các token vô nghĩa.
*   **Cảnh báo giới hạn ngữ cảnh pháp lý:** Do văn bản luật Việt Nam thường rất dài (các điều khoản, nghị định có thể lên tới hàng ngàn từ), việc chuỗi prompt bị kéo dài liên tục theo số lượng tác vụ (bộ luật) sẽ nhanh chóng chiếm dụng hết cửa sổ ngữ cảnh hữu dụng của mô hình, làm giảm khả năng tiếp nhận câu hỏi dài từ phía người dùng. Do đó, phương pháp này chỉ nên áp dụng cho các chuỗi tác vụ ngắn hoặc cần kết hợp thêm kỹ thuật nén prompt (prompt compression).
