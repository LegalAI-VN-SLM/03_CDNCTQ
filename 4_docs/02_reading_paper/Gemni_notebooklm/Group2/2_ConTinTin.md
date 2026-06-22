# 2_ConTinTin.md: Continual Learning from Task Instructions (ConTinTin)

## 1. High-Level Summary
Trong lĩnh vực xử lý ngôn ngữ tự nhiên (NLP), các mô hình học máy truyền thống thường hoạt động dựa trên hai giả định: các tác vụ mục tiêu là cố định và việc học đòi hỏi một lượng lớn dữ liệu gán nhãn thủ công vốn rất tốn kém. Để vượt qua giới hạn này, nghiên cứu này đề xuất một bài toán mới mang tên **ConTinTin** (Continual Learning from Task Instructions), trong đó mô hình học một chuỗi liên tục các tác vụ mới chỉ thông qua việc đọc hướng dẫn bằng văn bản (textual instructions). Ý tưởng chính được hiện thực hóa qua mô hình **InstructionSpeak** (dựa trên BART) tích hợp hai chiến lược cốt lõi: học từ các chỉ dẫn phủ định ("Negative Training" - rút ra bài học từ các ví dụ sai trong prompt) và ôn tập chỉ dẫn cũ ("History Training" - xem lại mô hình hóa hướng dẫn của các tác vụ trước đó). Mô hình hoạt động trong bối cảnh giao thoa giữa Học liên tục (Continual Learning) và Học không nhãn qua chỉ dẫn (Zero-Shot Instruction Tuning). Bằng việc thử nghiệm trên tập dữ liệu NATURAL-INSTRUCTIONS gồm 61 tác vụ, nghiên cứu chứng minh hệ thống có thể chuyển giao tri thức hiệu quả theo chiều dọc (forward transfer) sang tác vụ mới và bảo toàn năng lực ở tác vụ cũ (backward transfer) mà không bị quên lãng thảm họa (catastrophic forgetting).

---

## 2. Section Summaries
*   **Introduction:** Nhóm tác giả chỉ ra điểm yếu chí mạng của mô hình học máy truyền thống là sự phụ thuộc nặng nề vào tài nguyên gán nhãn lớn và đắt đỏ. Để giảm thiểu gánh nặng này, họ đề xuất kết hợp hai nguồn giám sát thay thế: tri thức tích lũy từ các tác vụ trong quá khứ và các hướng dẫn ngôn ngữ tự nhiên mô tả mục tiêu tác vụ. Từ đó, họ định nghĩa bài toán ConTinTin đi kèm các tiêu chí khắt khe như dung lượng mô hình cố định, khả năng giữ vững tri thức và chuyển giao tri thức hai chiều (tiến/lùi).
*   **Method:** Khung làm việc của ConTinTin yêu cầu một pha khởi tạo (Initialization) trên một số ít tác vụ có nhãn để mô hình học cách đọc hiểu hướng dẫn, tiếp nối bằng pha tiến hóa (Evolution) học tuần tự các tác vụ chưa từng thấy mà không dùng nhãn huấn luyện. Phương pháp đề xuất **InstructionSpeak** giải quyết việc chuyển giao tiến bằng kỹ thuật **Negative Training**, chuyển các ví dụ sai trong chỉ dẫn thành mục tiêu tiền huấn luyện để mô hình nhận diện giới hạn đầu ra. Đồng thời, kỹ thuật **History Training** được đưa vào nhằm giảm thiểu quên lãng bằng cách học lại các chuỗi hướng dẫn của tác vụ cũ thay vì lưu trữ các bộ đệm dữ liệu nhãn khổng lồ.
*   **Experiments:** Các thí nghiệm được tiến hành trên tập dữ liệu NATURAL-INSTRUCTIONS được tái cấu trúc thành 61 tác vụ chia thành 6 nhóm danh mục. Các tác giả đánh giá thông qua hai chỉ số định lượng mới là điểm chuyển giao tiến ($g^\rightarrow_i$) và chuyển giao lùi ($g^\leftarrow_i$). Kết quả cho thấy InstructionSpeak vượt trội đáng kể so với các mô hình baseline cơ bản như Sequential Fine-tuning hay LAMOL, và các phân tích loại bỏ (ablation) chứng minh Negative Training giúp tăng lực chuyển giao tiến trong khi History Training đóng vai trò chủ chốt chống quên lãng thảm họa.
*   **Discussion:** Phần thảo luận kết luận rằng ConTinTin mở ra một hướng đi đầy hứa hẹn cho các mô hình ngôn ngữ lớn học tập liên tục thông qua hướng dẫn thay vì dữ liệu nhãn. Phân tích chi tiết theo từng loại tác vụ cho thấy các tác vụ dạng Phân loại (Classification) hay Tạo câu trả lời (Answer Generation) hưởng lợi nhiều nhất nhờ sự tương đồng cao trong chuỗi tác vụ, ngược lại các tác vụ mang tính Xác thực (Verification) gặp nhiều khó khăn trong việc cải thiện. Nghiên cứu này thiết lập một cột mốc quan trọng thúc đẩy các nghiên cứu tiếp theo về học tập trọn đời dựa trên chỉ dẫn.

---

## 3. Methodology
*   **Framework Description:** Hệ thống hoạt động qua 2 giai đoạn:
    1.  *Initialization Process:* Mô hình ($M$) được huấn luyện trên một số lượng rất nhỏ các tác vụ huấn luyện ($S$, với $k=5$) có đầy đủ hướng dẫn và dữ liệu gán nhãn nhằm dạy mô hình cách tương tác và hiểu cấu trúc của một bản hướng dẫn.
    2.  *Evolution Process:* Mô hình sau đó đối mặt với một luồng tác vụ chưa biết ($U$) xuất hiện tuần tự. Với mỗi tác vụ $u_i$, mô hình chỉ được đọc phần hướng dẫn mô tả $d_{ui}$ mà không có dữ liệu huấn luyện, và phải sinh ra đầu ra chính xác cho dữ liệu kiểm tra.
*   **Key Technical Ideas:**
    *   **Negative Training:** Thay vì tối thiểu hóa xác suất sinh các câu trả lời sai (gây lỗi giải mã decoder), mô hình được tiền huấn luyện để chủ động sinh ra các đầu ra sai này (từ phần "Things to avoid" trong chỉ dẫn). Việc này giúp mô hình nắm bắt được các ràng buộc về mặt định dạng trước khi tinh chỉnh trên dữ liệu đúng.
    *   **History Training:** Để tránh quên lãng thảm họa mà không cần lưu trữ dữ liệu nhãn thô (tốn bộ nhớ), mô hình ôn tập lại các văn bản hướng dẫn của các tác vụ trước đó trong một batch với tốc độ học thấp (learning rate nhỏ) trước khi học tác vụ mới.
*   **Data, Setup & Metrics:**
    *   Dữ liệu dựa trên NATURAL-INSTRUCTIONS gồm 61 tác vụ thuộc 6 nhóm (Tạo câu hỏi, Tạo câu trả lời, Phân loại, Tạo câu trả lời sai, Chỉnh sửa tối thiểu, Xác thực). Dùng $k=5$ tác vụ để khởi tạo, 56 tác vụ còn lại làm chuỗi tiến hóa tuần tự.
    *   Mô hình nền tảng là BART-base. Tốc độ học 5e-5 cho pha huấn luyện thường và 5e-6 cho History Training (1 epoch).
    *   Metrics: Chuyển giao tiến ($g^\rightarrow_i$) đo hiệu quả tăng thêm khi học tác vụ đích sau $i$ tác vụ trung nguồn; Chuyển giao lùi ($g^\leftarrow_i$) đo mức độ suy giảm/cải thiện năng lực trên tác vụ cũ sau khi đã học thêm $i$ tác vụ mới. Đo bằng điểm ROUGE-L.
*   **3 Key Assumptions:**
    1.  Mô hình được khởi tạo trên một tập tác vụ cực nhỏ ($k=5$) có thể học được khả năng tổng quát hóa để dịch các hướng dẫn dạng văn bản bất kỳ thành logic thực thi cho các tác vụ hoàn toàn mới.
    2.  Việc huấn luyện mô hình sinh ra các kết quả sai ("negative outputs") trong pha tiền huấn luyện hoạt động như một bộ điều hòa (regularizer) hữu ích giúp định hình không gian đầu ra, thay vì tiêm nhiễm các kiến thức sai lệch vào mô hình.
    3.  Chỉ cần xem lại phần hướng dẫn dạng văn bản thuần túy của các tác vụ cũ là đủ để cung cấp tín hiệu giám sát chống quên lãng thảm họa, loại bỏ hoàn toàn nhu cầu về bộ đệm lưu trữ dữ liệu mẫu.

---

## 4. Brutally Honest Review
*   **Summary:** Bài báo đề xuất bài toán ConTinTin kết hợp giữa Học liên tục và Tuning chỉ dẫn. Phương pháp InstructionSpeak áp dụng pretraining trên các ví dụ sai và dùng kỹ thuật lưu trữ/ôn tập lại hướng dẫn cũ để giảm thiểu quên lãng thảm họa. Thử nghiệm trên NATURAL-INSTRUCTIONS cho thấy mô hình hoạt động hiệu quả trên các độ dài luồng học khác nhau, vượt qua baseline Sequential Fine-Tuning và LAMOL dựa trên các chỉ số chuyển giao tự định nghĩa.
*   **Strengths:**
    1.  **Ý tưởng mới mẻ:** Chuyển dịch trọng tâm học liên tục từ dữ liệu nhãn sang học từ hướng dẫn văn bản là một đóng góp rất thời thượng và có giá trị thực tiễn cao cho LLMs.
    2.  **Tiết kiệm bộ nhớ vượt trội:** Cơ chế History Training chỉ lưu trữ văn bản hướng dẫn (instruction) ngắn gọn thay vì lưu trữ hàng nghìn mẫu dữ liệu cũ, giải quyết triệt để bài toán bộ nhớ đệm của Continual Learning.
    3.  **Hệ thống số đo chặt chẽ:** Cách xây dựng công thức toán học và thuật toán cho $g^\rightarrow_i$ và $g^\leftarrow_i$ cung cấp một phương pháp đo lường tường minh về mức độ chuyển giao tri thức theo khoảng cách học.
*   **Weaknesses:**
    1.  **Logic của "Negative Training" thiếu tính tổng quát (Severity: High):** Kỹ thuật tiền huấn luyện sinh câu trả lời sai hoạt động được chủ yếu do các ví dụ sai trong tập NATURAL-INSTRUCTIONS chỉ chứa các lỗi định dạng đơn giản (như viết thừa từ giải thích). Nếu áp dụng vào các tác vụ suy luận phức tạp nơi ví dụ sai chứa các lỗi logic hoặc sai lệch thông tin thực tế, việc ép mô hình sinh đầu ra sai sẽ trực tiếp dạy mô hình các kiến thức sai lệch (hallucination).
    2.  **Hệ thống Baseline quá yếu (Severity: Medium):** Việc chỉ so sánh với Sequential Fine-Tuning và LAMOL (vốn được thiết kế cho GPT-2 và phải đổi sang BART để so sánh) là không đủ thuyết phục. Bài báo thiếu các baseline hiện đại dựa trên Parameter-Efficient Fine-Tuning (LoRA, Adapters) hoặc các phương pháp CL dựa trên prompt-pool.
    3.  **Ranh giới tác vụ nhân tạo (Severity: Medium):** Tập dữ liệu NATURAL-INSTRUCTIONS được tạo ra bằng cách chia nhỏ các bước thu thập dữ liệu đám đông của các tập dữ liệu gốc thành các tiểu tác vụ độc lập. Do đó, các tác vụ có độ tương đồng rất cao. Hiện tượng "chuyển giao tiến" tốt thực chất có thể chỉ là do mô hình ghi nhớ các phần trùng lặp trong miền phân phối, chứ không phải khả năng tổng quát hóa tác vụ thực sự.
    4.  **Khả năng mở rộng của History Training (Severity: Low):** Thuật toán yêu cầu mô hình phải học lại toàn bộ $i-2$ hướng dẫn của tất cả các tác vụ trước đó trong một batch lớn. Khi chuỗi tác vụ tiến hóa lên hàng nghìn, việc ôn tập toàn bộ này sẽ biến đổi học liên tục thành học đa tác vụ (multi-task learning) và tiêu tốn chi phí tính toán khổng lồ.
    5.  **Chưa giải quyết được vấn đề clashing label space (Severity: Low):** Tác giả thừa nhận các tác vụ phân loại (CF) bị sụt giảm hiệu năng nghiêm trọng ở chiều chuyển giao lùi do không gian nhãn bị chồng chéo khi học liên tục các tác vụ mới, nhưng không đề xuất giải pháp kỹ thuật nào để khắc phục.
*   **Questions for authors:**
    1.  Làm thế nào để đảm bảo "Negative Training" không gây ra lỗi suy luận sai lệch (hallucination) ở các tác vụ phức tạp khi ví dụ sai mang tính sai lệch logic chứ không chỉ là lỗi định dạng?
    2.  Tại sao nhóm tác giả không so sánh với các giải pháp Parameter-Isolation (như LoRA hay Adapters chuyên biệt cho từng tác vụ) vốn là chuẩn mực hiện nay để chống catastrophic forgetting?
    3.  Nếu luồng tác vụ tăng lên 10,000 tác vụ, chi phí tính toán cho History Training (phải duyệt qua toàn bộ hướng dẫn cũ trước mỗi bước học mới) sẽ tăng trưởng như thế nào và có khả thi không?
*   **Recommendation:** **Weak Accept**
    *Giải thích:* Đóng góp khái niệm cốt lõi của bài báo là rất tốt: chuyển dịch học liên tục sang hướng dẫn văn bản. Mặc dù Negative Training mang tính "hack" số liệu trên tập dữ liệu cụ thể và thiếu tính tổng quát, cơ chế History Training bằng cách lưu trữ hướng dẫn thay vì dữ liệu thô là một hướng đi thông minh. Đánh giá thực nghiệm chi tiết và hệ thống metric chặt chẽ đủ để bù đắp các điểm yếu về baseline.

---

## 5. Data & Metrics View Designer
*   **Datasets & Tasks:** NATURAL-INSTRUCTIONS (61 tác vụ thuộc các nhóm QG, AG, CF, IAG, MM, VF).
*   **Metrics:** ROUGE-L, Forward-transfer ($g^\rightarrow_i$), Backward-transfer ($g^\leftarrow_i$).
*   **Baselines:** Seq-finetune, LAMOL (adapted to BART), Multi-task.

### Propose Charts:
1.  **Chart 1: Line Chart (Độ bền chuyển giao theo khoảng cách)**
    *   *Trục X:* Khoảng cách chuyển giao (Transferring Distance $i \\in \\{1, 10, 20, 30, 40\\}$)
    *   *Trục Y:* Điểm Forward Transfer ($g^\rightarrow_i$)
    *   *Series:* Seq-finetune, LAMOL, InstructionSpeak
    *   *Mục đích:* Trực quan hóa khả năng tích lũy tri thức vượt trội của InstructionSpeak khi khoảng cách chuỗi tác vụ tăng lên.
2.  **Chart 2: Bar Chart (Hiệu năng chi tiết theo danh mục tác vụ)**
    *   *Trục X:* 6 nhóm danh mục tác vụ (QG, AG, CF, IAG, MM, VF)
    *   *Trục Y:* Điểm ROUGE-L trung bình
    *   *Series:* Forward-transfer vs. Backward-transfer của InstructionSpeak
    *   *Mục đích:* Chỉ rõ sự sụt giảm nghiêm trọng của tác vụ Phân loại (CF) trong chiều chuyển giao lùi do chồng chéo không gian nhãn.
3.  **Chart 3: Line Chart (Ảnh hưởng của kích thước khởi tạo $k$)**
    *   *Trục X:* Số lượng tác vụ khởi tạo ($k \\in \\{1, 5, 10, 20\\}$)
    *   *Trục Y:* Điểm Forward-transfer trung bình ($g^\rightarrow_{40}$)
    *   *Series:* Single line representing InstructionSpeak
    *   *Mục đích:* Minh chứng cho giả thuyết: huấn luyện khởi tạo với nhiều hướng dẫn giúp mô hình tăng tốc khả năng đọc hiểu hướng dẫn mới ở các bước sau.

### Summary Tables:
1.  **Table 1: Main CL Results on ConTinTin Benchmark**
    *   *Cột:* Phương pháp, các cột $g^\rightarrow$ ($1, 10, 20, 30, 40$), các cột $g^\leftarrow$ ($1, 10, 20, 30, 40$). Bold cho best score, kèm độ lệch chuẩn.
2.  **Table 2: Category Breakdown on NATURAL-INSTRUCTIONS**
    *   *Cột:* Phương pháp, QG, AG, CF, IAG, MM, VF, Mean score.

### Raw Data (Markdown Format):

```markdown
# ConTinTin Main Results (Table 2 in Paper)
| Method | g->1 | g->10 | g->20 | g->30 | g->40 | g<-1 | g<-10 | g<-20 | g<-30 | g<-40 |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| Seq-finetune | 1.44 | 3.28 | -3.74 | 2.90 | -0.36 | 1.57 | 0.04 | -0.19 | -6.48 | -9.46 |
| LAMOL | -1.34 | 1.41 | 3.31 | -5.40 | -0.03 | 2.67 | 2.21 | 9.42 | 6.33 | 7.21 |
| InstructionSpeak | **2.16** | **5.06** | **2.29** | **4.07** | **4.39** | 1.44 | **5.21** | **7.33** | **14.99** | **12.31** |
| w/o NEGATIVE TRAINING | -2.89 | 1.06 | 1.33 | 2.21 | 1.78 | 2.21 | 3.37 | 11.44 | 10.36 | 8.94 |
| w/o HISTORY TRAINING | 1.88 | 3.32 | 4.41 | 3.22 | 2.97 | **4.74** | -2.78 | -0.83 | 1.35 | 3.49 |
| Multi-task (upperbound) | 7.98 | - | - | - | - | - | - | - | - | - |

# Category-wise ROUGE-L Results (Table 3 in Paper)
| Method | QG | AG | CF | IAG | MM | VF | mean |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| Paper Report (Mishra) | 52.00 | 30.00 | 50.00 | 25.00 | 47.00 | 8.00 | 35.33 |
| Reimplement (Mishra) | 53.55 | 17.45 | 63.79 | 11.06 | 82.86 | 7.40 | 39.35 |
| Seq-finetune forward | 49.61 | 21.46 | 48.74 | 9.70 | 57.31 | 7.61 | 32.40 |
| Seq-finetune backward | 47.09 | 21.17 | 7.45 | 9.61 | 88.84 | 14.98 | 31.52 |
| LAMOL forward | 52.23 | 20.45 | 67.74 | 8.81 | 82.29 | 8.83 | 40.05 |
| LAMOL backward | 52.14 | 22.76 | 7.98 | 8.33 | 88.45 | 9.91 | 31.59 |
| InstructionSpeak w/o CL | 51.07 | 23.40 | 70.68 | 11.43 | 88.13 | 6.22 | 41.82 |
| InstructionSpeak forward | 51.30 | 24.89 | 70.96 | 9.36 | 90.41 | 10.70 | 42.93 |
| InstructionSpeak backward | 53.04 | 24.93 | 7.51 | 8.56 | 88.09 | 13.86 | 32.66 |
```

---

## 6. Figure & Table Highlights (For hightlight.md)
*   **Table 1:** Định nghĩa và giải nghĩa 5 thuộc tính mong muốn (desiderata) của bài toán ConTinTin (Instruction-driven supervision, Fixed model capacity, Knowledge maintenance, Forward transfer, Backward transfer).
*   **Figure 1:** Sơ đồ quy trình hoạt động của ConTinTin. Trực quan hóa Giai đoạn Khởi tạo (huấn luyện trên $k$ tác vụ) và Giai đoạn Tiến hóa (học tuần tự từ tác vụ chưa biết $u_1 \dots u_i$ chỉ qua hướng dẫn).
*   **Algorithms 1 & 2:** Mô tả chi tiết thuật toán tính toán hai chỉ số chuyển giao tiến ($g^\rightarrow_i$) và lùi ($g^\leftarrow_i$) bằng các vòng lặp ngẫu nhiên vị trí tác vụ trong chuỗi tiến hóa.
*   **Figure 2:** Minh họa một bản hướng dẫn đầy đủ từ NATURAL-INSTRUCTIONS cho tác vụ trả lời câu hỏi khoa học, tô màu xanh cho các tín hiệu tích cực (định nghĩa, ví dụ đúng) và màu đỏ cho tín hiệu phủ định cần tránh ( Things to avoid, ví dụ sai).
*   **Table 2 (Bảng kết quả chính):** So sánh InstructionSpeak với các baseline khác ở các khoảng cách từ 1 đến 40. Khẳng định hiệu quả của hai chiến lược đề xuất.
*   **Table 3 (Bảng kết quả phân rã):** Thể hiện điểm ROUGE-L chi tiết cho 6 loại tác vụ. Minh chứng rõ rệt cho việc nhóm Phân loại (CF) bị quên lãng cực nặng (điểm giảm từ ~70 xuống ~7) khi học thêm các tác vụ khác.
*   **Figure 3 (Ảnh hưởng của k):** Biểu đồ đường chứng minh việc tăng số tác vụ khởi tạo $k$ từ 1 lên 20 giúp cải thiện mạnh mẽ khả năng forward transfer.
*   **Figure 4 (Transferability curves):** Hai biểu đồ đường biểu diễn khả năng chuyển giao tiến (a) và lùi (b) của 6 loại tác vụ, chỉ ra nhóm Classification, AG, QG hoạt động rất tốt trong khi MM và VF sụt giảm hoặc đi ngang.
