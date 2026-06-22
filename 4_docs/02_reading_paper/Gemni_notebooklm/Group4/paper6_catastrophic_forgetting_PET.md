# Phân tích Chi tiết: Analyzing and Reducing Catastrophic Forgetting in Parameter Efficient Tuning (I-LoRA)

## 1. Tóm tắt cấp cao (High-level Summary)
Bài báo giải quyết vấn đề quên thảm khốc (catastrophic forgetting) trong các Mô hình Ngôn ngữ Lớn (LLMs) khi chúng được tinh chỉnh liên tục trên các tác vụ hạ nguồn thuộc nhiều chuyên ngành phức tạp khác nhau bằng các kỹ thuật thích ứng hiệu quả tham số (PEFT) [1]. Ý tưởng chính của nghiên cứu là khảo sát các liên kết hình học giữa các cực tiểu (minima) của các tác vụ khác nhau thông qua lăng kính **"liên kết chế độ" (mode connectivity)** — hiện tượng các cực tiểu cục bộ khác nhau của mạng neural có thể kết nối với nhau bằng một thung lũng có giá trị suy hao thấp (low-loss continuous valley) [1, 2]. Trong bối cảnh học liên tục vốn thường dựa vào các kỹ thuật phát lại dữ liệu cứng nhắc hoặc các ràng buộc điều chuẩn tham số, nghiên cứu này đề xuất phương pháp **I-LoRA (Interpolation-based LoRA)** [1, 3]. I-LoRA xây dựng một khung học kép (dual-memory framework) sử dụng một bộ "học nhanh" (fast learner) để thích ứng nhanh với tác vụ mới và một bộ "học chậm" (slow learner) để hợp nhất tri thức lâu dài thông qua cơ chế nội suy tuyến tính tham số (parameter interpolation) [1, 4].

---

## 2. Tóm tắt chi tiết từng phần (Section Summaries)
*   **Introduction (Giới thiệu):** Đặt vấn đề về sự đánh đổi giữa tính mềm dẻo (plasticity - khả năng học tác vụ mới nhanh) và tính ổn định (stability - giữ lại tri thức cũ) trong học liên tục (CL). Nhóm tác giả phê bình các phương pháp hiện tại (như EWC, GEM hay Replay thông thường) vì bỏ qua các tính chất hình học kết nối giữa các cực tiểu của các tác vụ trong không gian trọng số của LLM. Bằng cách phân tích mode connectivity, họ chỉ ra rằng các điểm nội suy tuyến tính ở giữa hai điểm cực tiểu của hai tác vụ liên tiếp thường mang lại điểm cân bằng tối ưu hơn cả hai điểm đầu cuối.
*   **Method (Phương pháp):** Khảo sát thực nghiệm tính chất liên kết chế độ tuyến tính (Linear Mode Connectivity) bằng cách dựng đường đi tham số $\phi(\lambda)$ nối giữa cực tiểu tác vụ cũ và tác vụ mới. Nhóm tác giả chứng minh bằng toán học và thực nghiệm rằng các điểm nội suy $\lambda$ nằm giữa hai điểm cực tiểu đạt hiệu năng trung bình trên cả tác vụ cũ và mới tốt hơn việc chỉ dùng một trong hai điểm. Từ đó, thuật toán I-LoRA được thiết kế: duy trì bộ nhớ làm việc (fast learner) cập nhật liên tục trên dữ liệu trộn lẫn và bộ nhớ dài hạn (slow learner) cập nhật chậm qua phép nội suy trọng số, kết hợp với hàm loss chưng cất tri thức (MSE distillation).
*   **Experiments (Thực nghiệm):** Đánh giá I-LoRA trên mô hình LLaMA-2-7B sử dụng chuỗi 8 tác vụ chuyên ngành phức tạp (như y học MedMCQA, tài chính FOMC, luật JEC-QA, tóm tắt tiếng Đức 20Minuten). So sánh với 9 baseline mạnh (như Seq-Train, Experience Replay, EWC, GEM, O-LoRA), I-LoRA chứng minh khả năng vượt trội trong việc giảm thiểu điểm quên thảm khốc (forgetting score) và bảo toàn tri thức chung tốt hơn hẳn các thuật toán truyền thống.
*   **Discussion (Thảo luận):** Giải thích lý do I-LoRA thành công thông qua ba góc nhìn hình học: Khoảng cách trọng số (Weight Distance), Căn chỉnh nhân trung tâm (Centered Kernel Alignment), và Biểu đồ độ chính xác trung bình (Mean Accuracy Landscape). Nhóm tác giả chỉ ra rằng việc tinh chỉnh LoRA thông thường khiến tham số bị kéo lệch hoàn toàn sang một cực tiểu cục bộ cô lập mới, gây mất mát tri thức cũ. Cơ chế nội suy của I-LoRA kéo trọng số neo đậu ở thung lũng kết nối giúp duy trì biểu diễn đặc trưng cũ ổn định.

---

## 3. Methodology Deep-Dive
### Khung hệ thống & Thuật toán
I-LoRA duy trì hai tập tham số LoRA song song: bộ nhớ làm việc $\theta_w$ (fast learner) đóng vai trò thích ứng nhanh, và bộ nhớ dài hạn $\theta_l$ (slow learner) lưu trữ tri thức bền vững. Phương pháp hoạt động kết hợp với một bộ đệm phát lại (experience replay buffer) $M$ quy mô nhỏ chứa một số ít mẫu dữ liệu của các tác vụ cũ.

Trong mỗi bước huấn luyện của tác vụ $t$:
1.  Mẫu dữ liệu hiện tại $x_t \in D_t$ được trộn với dữ liệu cũ $x_m \in M$ lấy từ bộ đệm.
2.  Bộ học nhanh $\theta_w$ tính toán hàm loss phân loại chuẩn (Cross-Entropy Loss):
    $$L_{CE} = \text{CrossEntropy}(f(x; \theta_w), y)$$
3.  Để giữ tính ổn định, mô hình tính thêm hàm loss chưng cất MSE (Distillation Loss) giữa đầu ra của bộ học nhanh và đầu ra của bộ học chậm $\theta_l$:
    $$L_{MSE} = \text{MeanSquaredError}(f_o(x_m; \theta_w), f_o(x_m; \theta_l))$$
4.  Cập nhật bộ học nhanh $\theta_w$ bằng lan truyền ngược với loss tổng hợp: $L = L_{CE} + \gamma L_{MSE}$.
5.  Bộ học chậm $\theta_l$ được cập nhật bằng phép nội suy tuyến tính tham số (Exponential Moving Average):
    $$\theta_l^k \leftarrow \lambda \theta_l^{k-1} + (1-\lambda)\theta_w^k$$

### Dữ liệu, Thiết lập & Metrics
*   **Dữ liệu:** 8 tập dữ liệu chuyên biệt đa dạng: C-STANCE, FOMC (tài chính), MeetingBank (tóm tắt cuộc họp), ScienceQA, NumGLUE-cm (toán học), 20Minuten (tiếng Đức), MedMCQA (y học), JEC-QA (luật pháp).
*   **Thiết lập:** LLaMA-2-7B. GPU RTX 4090. LoRA áp dụng trên $W_q, W_v$ với rank $r=8$, $\alpha=16$. Mỗi tác vụ lấy 5000 mẫu train và 500 mẫu test.
*   **Thước đo:** Average Accuracy ($Acc_T$ - trung bình độ chính xác của các tác vụ sau khi học xong chuỗi) và Backward Transfer ($BWT_T$ - điểm quên trung bình).

### 3 Giả định kỹ thuật quan trọng
1.  **Sự tồn tại của thung lũng liên kết (Mode Connectivity):** Không gian suy hao không lồi (non-convex landscape) của LLM trong không gian PEFT hạng thấp chứa các thung lũng tuyến tính có giá trị loss thấp kết nối các cực tiểu cục bộ của các tác vụ học tuần tự.
2.  **Tính tối ưu của điểm nội suy:** Tồn tại một hệ số nội suy $\lambda$ phù hợp sao cho điểm nằm giữa hai cực tiểu $\theta_t$ và $\theta_{t+1}$ đạt được sự cân bằng tối ưu giữa việc ghi nhớ tác vụ cũ và học tác vụ mới.
3.  **Hiệu quả của chưng cất MSE:** Việc áp dụng MSE chưng cất logits giữa mô hình học nhanh và mô hình học chậm là đủ để neo giữ bộ học nhanh không bị lệch quá xa vào vùng không gian trọng số gây quên thảm khốc.

---

## 4. Phản biện học thuật (Brutally Honest Review)

### Summary
Paper khảo sát hiện tượng liên kết chế độ tuyến tính (Linear Mode Connectivity) trong quá trình thích ứng hiệu quả tham số của LLM. Tác giả đề xuất I-LoRA sử dụng cấu trúc bộ nhớ kép (fast/slow learner) kết hợp chưng cất tri thức MSE và nội suy tuyến tính tham số để giữ trọng số mô hình nằm trong thung lũng có giá trị suy hao thấp giữa các tác vụ. Thử nghiệm trên LLaMA-2-7B với chuỗi 8 tác vụ chuyên sâu cho thấy hiệu quả cải tiến rõ rệt so với các CL baselines.

### Strengths
1.  **Góc nhìn hình học độc đáo:** Việc đưa khái niệm Mode Connectivity vốn phổ biến trong thị giác máy tính vào PEFT cho LLM là một hướng tiếp cận lý thuyết rất mới mẻ và có giá trị định hướng cao.
2.  **Benchmark thực nghiệm chất lượng cao:** Sử dụng 8 tập dữ liệu chuyên ngành có độ khó cao (như y học, luật, toán học, đa ngôn ngữ) mang lại độ tin cậy thực tế lớn hơn nhiều so với các benchmark phân loại văn bản đơn giản.
3.  **Cơ chế nội suy đơn giản:** Phép cập nhật EMA trọng số rất dễ cài đặt, không cần sửa đổi kiến trúc mô hình gốc và có thể tích hợp trực tiếp vào các thư viện PEFT hiện hành.

### Weaknesses
1.  **Sự tương đồng thuật toán (Trùng lặp ý tưởng):** Cơ chế cốt lõi của I-LoRA — duy trì một mô hình giáo viên di chuyển chậm bằng EMA và dùng hàm loss MSE để kiểm soát mô hình học sinh — thực chất là cấu trúc "Mean Teacher" rất kinh điển trong học bán giám sát. Paper gán nhãn đây là "nội suy liên kết chế độ", nhưng về mặt thuật toán không có sự cải tiến đột phá nào so với Mean Teacher áp dụng cho trọng số LoRA.
2.  **Tăng tải chi phí bộ nhớ và tính toán:** I-LoRA bắt buộc phải lưu trữ đồng thời hai tập tham số LoRA ($\theta_w$ và $\theta_l$) trong VRAM và thực hiện thêm một lượt forward pass cho mô hình slow learner để tính MSE loss. Tác giả chưa phân tích kỹ lượng VRAM tăng thêm này ảnh hưởng thế nào đến khả năng chạy trên các phần cứng giới hạn.
3.  **Vẫn phụ thuộc vào Replay Buffer:** Mặc dù động lực thiết kế dựa trên các tính chất hình học của trọng số, thuật toán vẫn cần một buffer lưu trữ dữ liệu cũ $M$ để chưng cất. Điều này làm giảm giá trị ứng dụng trong các kịch bản học liên tục có ràng buộc nghiêm ngặt về quyền riêng tư dữ liệu (rehearsal-free).
4.  **Thiếu phân tích độ nhạy của siêu tham số $\lambda$:** Hệ số nội suy $\lambda$ quyết định tốc độ cập nhật của slow learner. Bài báo thiếu các phân tích ablation sâu để xem khi các tác vụ kế tiếp có độ lệch phân phối (domain distance) quá lớn thì $\lambda$ có cần phải thay đổi động hay không.
5.  **Chỉ kiểm chứng ở quy mô nhỏ:** Các thực nghiệm chỉ giới hạn ở LLaMA-2-7B với rank LoRA rất thấp ($r=8$). Hiện tượng mode connectivity tuyến tính có thể không còn giữ được tính chất thung lũng loss thấp khi tăng rank LoRA lên cao hoặc scale mô hình lên trên 70B tham số.

### Questions for Authors
1.  Sự khác biệt cốt lõi giữa quy tắc cập nhật nội suy của I-LoRA và cơ chế Mean Teacher truyền thống trong học chưng cất là gì?
2.  Chi phí tài nguyên bộ nhớ VRAM và thời gian huấn luyện (training time overhead) tăng thêm bao nhiêu % so với baseline Experience Replay thông thường?
3.  I-LoRA có thể duy trì được hiệu quả chống quên nếu hoàn toàn loại bỏ bộ đệm dữ liệu phát lại ($M = 0$)?

### Recommendation
**Accept.** Mặc dù thuật toán có sự trùng lắp ý tưởng với cơ chế Mean Teacher, việc lý giải và áp dụng nó dưới góc nhìn hình học của Mode Connectivity trong không gian tham số LoRA mang lại giá trị đóng góp học thuật tốt. Kết quả thực nghiệm vượt trội trên một tập benchmark domain-specific đa dạng là minh chứng rõ ràng cho tính thực tiễn của bài báo.

---

## 5. Data & Metrics View Designer

### Tóm tắt dữ liệu & Baselines
*   **Datasets:** 8 tập chuyên biệt (C-STANCE, FOMC, MeetingBank, ScienceQA, NumGLUE-cm, 20Minuten, MedMCQA, JEC-QA).
*   **Baselines:** Zero-shot inference (ZSI), Sequence Fine-tuning (Seq-Train), Experience Replay (ER), EWC, GEM, A-GEM, MBPA++, L2P, LFPT5, O-LoRA.

### Đề xuất trực quan hóa (Charts)
1.  **Line Chart biểu diễn Mode Connectivity Valley:**
    *   *Trục X:* Hệ số nội suy $\lambda$ (chạy từ 0.0 đến 1.0)
    *   *Trục Y:* Accuracy (%)
    *   *Nhóm (Series):* Tác vụ cũ ($A_p$), Tác vụ mới ($A_n$), Trung bình ($A_{all}$)
    *   *Mục đích:* Tái hiện Hình 1 của paper để chứng minh trực quan việc các điểm nằm ở giữa (ví dụ $\lambda=0.5$) đạt hiệu suất tốt hơn hai đầu mút $\theta_t$ và $\theta_{t+1}$.
2.  **Bar Chart so sánh điểm quên (Forgetting Rate):**
    *   *Trục X:* Các phương pháp CL (Seq-Train, EWC, ER, O-LoRA, I-LoRA)
    *   *Trục Y:* Forgetting score (%) trên NumGLUE-cm hoặc 20Minuten
    *   *Mục đích:* Nổi bật khả năng kiểm soát quên thảm khốc của I-LoRA (giảm quên xuống 9.1% so với mức quên >20% của EWC).
3.  **Line Chart theo dõi độ ổn định qua chuỗi tác vụ:**
    *   *Trục X:* Các mốc thời gian học (Sau tác vụ 1, Sau tác vụ 2, ..., Sau tác vụ 8)
    *   *Trục Y:* Accuracy (%) trên Tác vụ 1 (C-STANCE)
    *   *Nhóm (Series):* Seq-Train vs ER vs I-LoRA
    *   *Mục đích:* Xem tốc độ suy giảm hiệu năng của tác vụ đầu tiên qua các bước học liên tục.

### Thiết kế bảng tổng hợp đề xuất
Bảng tổng hợp nên so sánh độ chính xác của I-LoRA với các baseline trên cả 8 tập dữ liệu chuyên ngành sau khi kết thúc chuỗi huấn luyện tác vụ để thấy rõ độ bền bộ nhớ.

### Số liệu thô trích xuất từ Paper

| Task / Dataset | C-STANCE | FOMC | MeetingBank | ScienceQA | NumGLUE-cm | 20Minuten | MedMCQA | JEC-QA |
| :--- | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| **Single Task FT (Plasticity Upper Bound)** | 0.388 | 0.342 | 0.164 | 0.324 | 0.308 | 0.014 | 0.328 | 0.324 |
| **Linear Comb. (Fast/Slow Interpolation)** | 0.276 | 0.488 | 0.032 | 0.252 | 0.004 | 0.014 | 0.236 | 0.272 |
| **Seq-Train (After T8)** | 21.0 | 29.2 | 12.3 | 23.8 | 17.3 | 38.9 | 26.2 | 26.8 |
| **ER (After T8)** | 16.4 | 49.0 | 7.9 | 26.2 | 27.2 | - | - | - |

*(Lưu ý: Các số liệu thô trên được trích xuất từ phần phân tích fine-grained và Bảng 14, 15 trong phụ lục của paper [17, 18])*

---

## 6. Khả năng áp dụng cho VN-Legal-AI / CDNCTQ
*   **Nội suy tham số khi cập nhật văn bản luật mới:** Trong dự án VN-Legal-AI, khi chúng ta liên tục tinh chỉnh mô hình nền tảng qua các văn bản luật bổ sung (ví dụ cập nhật các Nghị định hướng dẫn thi hành mới của năm 2026), chúng ta có thể áp dụng cơ chế I-LoRA. Cụ thể, ta huấn luyện một bộ LoRA làm việc nhanh ($\theta_w$) trên dữ liệu mới, sau đó thay vì dùng trực tiếp mô hình này, ta thực hiện nội suy tuyến tính trọng số của nó với bộ LoRA dài hạn cũ ($\theta_l$) để tạo ra mô hình chạy thực tế. Phép nội suy này giúp mô hình vừa cập nhật được luật mới, vừa không bị sụp đổ khả năng hiểu luật cũ đã học.
*   **Chống quên ngôn ngữ thông qua MSE chưng cất:** Hàm loss chưng cất MSE giữa fast learner và slow learner là một giải pháp rất tốt để kiểm soát sự trôi lệch biểu diễn ngữ nghĩa. Chúng ta có thể dùng một lượng nhỏ dữ liệu tiếng Việt phổ thông (ER buffer) để chưng cất MSE, giữ cho các cập nhật luật mới không làm méo mó cấu trúc câu tiếng Việt phổ thông của mô hình nền tảng.
*   **Quản lý tài nguyên huấn luyện:** Cần lưu ý rằng I-LoRA đòi hỏi giữ 2 bản sao adapter trong quá trình train. Nếu chạy trên các GPU giới hạn (như RTX 3090), chúng ta cần tối ưu hóa mã nguồn để bộ nhớ slow learner được đẩy sang RAM hệ thống (CPU offloading) khi không tính toán chưng cất, tránh gây ra lỗi Out-Of-Memory (OOM).
