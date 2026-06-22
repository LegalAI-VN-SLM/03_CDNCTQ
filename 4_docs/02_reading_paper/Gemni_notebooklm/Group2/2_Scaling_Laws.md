# 2_Scaling_Laws.md: Scaling Laws for Forgetting When Fine-Tuning Large Language Models

## 1. High-Level Summary
Nghiên cứu này giải quyết bài toán định lượng và dự đoán hiện tượng quên lãng thảm họa trong các mô hình ngôn ngữ lớn (LLMs) khi được tinh chỉnh hiệu quả tham số (PEFT) bằng phương pháp LoRA. Cho đến nay, mối quan hệ chính xác giữa sự quên lãng, quy mô tham số tinh chỉnh, và số bước huấn luyện vẫn chưa được làm rõ về mặt toán học. Ý tưởng chính của bài báo là thiết lập các "luật tỷ lệ" (scaling laws) mô tả sự sụt giảm tri thức nền tảng dưới dạng một hàm số mũ lũy thừa dịch chuyển (shifted power law) phụ thuộc vào số lượng tham số được cập nhật ($P$) và số bước tối ưu hóa ($N$). Các tác giả phát hiện ra một mối quan hệ tuyến tính nghịch đảo rất mạnh: mức độ quên lãng tỷ lệ thuận với mức độ học được tác vụ mới (đo bằng fine-tuning loss). Từ đó, nghiên cứu đưa ra kết luận bi quan rằng các chiến thuật thông thường như dừng sớm (early stopping) hay giảm số tham số tinh chỉnh (giảm rank của LoRA) không thể giúp ngăn chặn hiện tượng quên lãng nếu mô hình vẫn đạt được độ tối ưu cao trên tác vụ mới, đòi hỏi phải có những cơ chế kháng quên chuyên biệt.

---

## 2. Section Summaries
*   **Introduction:** Việc thích ứng các mô hình pre-trained lớn sang các tác vụ hạ nguồn qua tinh chỉnh chỉ dẫn (fine-tuning) rất phổ biến nhưng thường phá hủy các năng lực tổng quát vốn có của mô hình. Trong khi các luật tỷ lệ trong pha tiền huấn luyện (pre-training scaling laws) đã được chứng minh rõ ràng, hành vi của mô hình trong quá trình tinh chỉnh tham số hiệu quả (PEFT như LoRA) vẫn chưa được mô hình hóa toán học. Nghiên cứu này đặt giả thuyết rằng việc thay đổi quy mô tham số tinh chỉnh và thời gian huấn luyện sẽ tuân theo các quy luật lũy thừa có thể dự đoán được.
*   **Method (Methods and Experimental Setup):** Để đo lường hiện tượng quên lãng một cách liên tục và độc lập với độ chính xác ban đầu của mô hình gốc, tác giả đề xuất sử dụng hàm mất mát cross-entropy tính toán giữa dự đoán của mô hình đã tinh chỉnh ($M'$) so với phân phối xác suất đầu ra (logits) của mô hình pre-trained gốc ($M$). Họ sử dụng LLaMA-2 7B Chat làm mô hình nền tảng, áp dụng cấu trúc LoRA cải tiến có hệ số cân bằng rank (rank-stabilized scaling factor $\gamma_r = 1/\sqrt{r}$) trên tất cả các lớp linear. Việc tinh chỉnh được thực hiện trên hai tập dữ liệu (OpenOrca và News) trong 260 bước huấn luyện, sử dụng tập WikiText-103 để đo lường sự quên lãng tri thức tổng quát.
*   **Experiments:** Kết quả thực nghiệm chỉ ra rằng mức độ quên lãng ($L_f$) có mối tương quan tuyến tính cực kỳ chặt chẽ với hàm mất mát tinh chỉnh ($L_{ft}$), nghĩa là mô hình lớn quên nhiều hơn chủ yếu vì nó học tác vụ mới tốt hơn (loss thấp hơn). Nhóm tác giả đã khớp thành công hệ phương trình power-law mô tả chung $L_f$ theo hai biến số là số lượng tham số cập nhật ($P$) và số bước gradient ($N$). Các thực nghiệm định tính chứng minh rằng ngay cả các mô hình LoRA rank nhỏ nhất (rank 8) cũng quên mất cách từ chối các yêu cầu độc hại (AdvBench) và mất đi khả năng lập luận trên các câu hỏi tư duy (ARC).
*   **Discussion/Conclusion:** Nhóm tác giả khẳng định quá trình tinh chỉnh tuân theo các luật tỷ lệ tương tự như pha tiền huấn luyện. Vì hiện tượng quên lãng bị khóa chặt với hiệu năng học tác vụ mới qua mối quan hệ tuyến tính nghịch đảo, các giải pháp can thiệp thông thường như giảm số lượng tham số cập nhật là không có tác dụng bảo vệ tri thức. Nghiên cứu nhấn mạnh tầm quan trọng của việc thiết kế các thuật toán tinh chỉnh mới có thể bẻ gãy mối ràng buộc tuyến tính này để bảo vệ các ranh giới an toàn và logic của mô hình.

---

## 3. Methodology
*   **Framework Description:** Một mô hình pre-trained gốc ($M$) được tinh chỉnh trên tập dữ liệu hạ nguồn $D'$ bằng phương pháp LoRA để tạo ra mô hình tinh chỉnh ($M'$). Mức độ quên lãng được định lượng bằng cross-entropy loss của mô hình $M'$ khi dự đoán trên tập dữ liệu kiểm tra tổng quát (WikiText-103), sử dụng trực tiếp các logits đầu ra của mô hình gốc $M$ làm nhãn mục tiêu (thay vì nhãn ground-truth của dữ liệu).
*   **Key Technical/Architecture Ideas:**
    *   **Rank-Stabilized LoRA (rsLoRA):** Sử dụng hệ số tỷ lệ $\gamma_r = 1/\sqrt{r}$ (thay vì $1/r$ truyền thống). Kỹ thuật này giúp giải phóng hiệu năng của LoRA khỏi các bất ổn định toán học khi tăng rank $r$, cho phép tác giả cô lập chính xác ảnh hưởng của số lượng tham số tinh chỉnh ($P$, vốn tỷ lệ thuận với rank $r$) lên hiện tượng quên lãng.
    *   **Logit-based Forgetting Metric ($L_f$):** Việc so sánh cross-entropy logits giúp loại bỏ nhiễu từ các yếu tố như: mô hình gốc vốn dĩ đã đoán sai từ trước, hoặc hiện tượng chuyển giao tích cực làm thay đổi nhãn ground-truth nhưng thực chất không phải quên lãng.
*   **Data & Setup:**
    *   *Dữ liệu huấn luyện:* OpenOrca (tinh chỉnh chỉ dẫn tư duy) và "News" (100 bài báo thu thập vào tháng 9/2023, đảm bảo thông tin hoàn toàn nằm ngoài phân phối pre-training của LLaMA-2).
    *   *Setup:* Tối ưu hóa bằng Adafactor, batch size = 32, context length = 512, huấn luyện trong 260 bước (bỏ qua 50 bước khởi động warmup đầu tiên để khớp đường cong). Thử nghiệm trên các rank $r \in \{8, 16, 32, 64, 128, 256\}$.
*   **Evaluation Metrics:** Forgetting Loss ($L_f$), Fine-Tuning Loss ($L_{ft}$). Đánh giá định tính bằng Accuracy trên ARC Challenging và số lượt từ chối yêu cầu độc hại trên AdvBench (50 mẫu).
*   **3 Key Assumptions:**
    1.  Độ lệch logits (cross-entropy đối chứng mô hình gốc) là một đại diện hoàn hảo cho sự quên lãng, coi bất kỳ sự dịch chuyển logits nào khỏi mô hình ban đầu đều là tiêu cực bất kể mô hình gốc đưa ra câu trả lời đúng hay sai.
    2.  Việc thay đổi rank $r$ của LoRA cô lập chính xác tác động của số lượng tham số cập nhật ($P$) mà không thay đổi tính chất động lực học nội tại của mô hình.
    3.  Tập dữ liệu WikiText-103 là đại diện hoàn chỉnh để đo lường sự suy hao tri thức bách khoa đa lĩnh vực của LLM.

---

## 4. Brutally Honest Review
*   **Summary:** Bài báo thiết lập các luật tỷ lệ (scaling laws) định lượng hiện tượng quên lãng thảm họa khi tinh chỉnh LLM (LLaMA-2 7B) bằng LoRA. Bằng cách đo lường độ lệch logits trên WikiText-103, tác giả chứng minh mức độ quên lãng có mối quan hệ tuyến tính nghịch đảo chặt chẽ với fine-tuning loss. Nghiên cứu cảnh báo các kỹ thuật PEFT thông thường không thể tránh được sự suy giảm năng lực tư duy và an toàn khi mô hình đạt độ tối ưu cao trên tác vụ mới.
*   **Strengths:**
    1.  **Metric đo lường tri thức sáng tạo:** Việc sử dụng cross-entropy đối chứng logits gốc thay vì ground-truth accuracy là một đóng góp rất thông minh, giúp cô lập chính xác độ trôi tham số (parameter drift) khỏi năng lực ban đầu của mô hình.
    2.  **Khái quát hóa toán học tốt:** Đưa ra hệ phương trình lũy thừa cụ thể để dự báo mức độ quên lãng theo số tham số ($P$) và số bước huấn luyện ($N$), mang lại nền tảng lý thuyết vững chắc vốn đang thiếu trong mảng PEFT.
    3.  **Đập tan quan niệm cũ về PEFT:** Chứng minh thực nghiệm rằng việc giảm rank LoRA không giúp chống quên lãng (nó chỉ làm mô hình học kém đi, nên quên ít đi) đã bác bỏ một niềm tin phổ biến nhưng sai lầm của nhiều kỹ sư thực hành.
*   **Weaknesses:**
    1.  **Thiếu hụt nghiêm trọng về độ đa dạng mô hình (Severity: High):** Toàn bộ hệ thống "Luật tỷ lệ" của bài báo được xây dựng dựa trên duy nhất một mô hình LLaMA-2 7B Chat. Không có thử nghiệm đối chứng trên các quy mô khác (như 70B) hay các kiến trúc khác (như Mixture of Experts - MoE, Encoder-Decoder). Việc gọi các phát hiện này là "Laws" (Định luật) khi chỉ thử nghiệm trên một mô hình duy nhất là quá vội vã và thiếu tính thuyết phục khoa học.
    2.  **Môi trường huấn luyện siêu vi mô (Severity: High):** Các phương trình lũy thừa được khớp dựa trên các thí nghiệm cực kỳ ngắn (chỉ 260 bước gradient) và tập dữ liệu rất nhỏ (News chỉ có 100 bài báo). Trong thực tế công nghiệp, quá trình tinh chỉnh thường kéo dài hàng chục nghìn bước trên nhiều epoch. Việc ngoại suy các đường cong lũy thừa từ một vài trăm bước huấn luyện sang các tiến trình thực tế là rất thiếu tin cậy.
    3.  **Bỏ qua các baseline thuật toán học liên tục (Severity: Medium):** Bài báo chỉ ra vấn đề nghiêm trọng nhưng không thử nghiệm bất kỳ phương pháp CL kinh điển nào (như EWC, Replay, hay Orthogonal Projection) để xem liệu chúng có thể bẻ gãy hoặc làm giảm hệ số tương quan tuyến tính nghịch đảo kia hay không.
    4.  **Khả năng mở rộng kém sang các phương pháp PEFT khác (Severity: Medium):** Trong phần phụ lục, mối quan hệ tuyến tính $L_f(L_{ft})$ bị sụp đổ hoàn toàn khi áp dụng cho phương pháp tinh chỉnh IA3 ($R^2$ giảm thê thảm xuống 0.18). Điều này cho thấy "Luật tỷ lệ" đề xuất thực chất bị thiên vị nặng nề theo cơ chế hoạt động của LoRA chứ không phải là quy luật chung của mọi phương pháp fine-tuning.
    5.  **WikiText-103 chưa phản ánh đủ các khía cạnh quên lãng (Severity: Low):** Việc dùng WikiText-103 làm thước đo định lượng duy nhất cho $L_f$ có xu hướng thiên vị cho việc quên kiến thức dạng ghi nhớ sự thật (factual knowledge), chưa phản ánh được sự suy hao ở các mạch tư duy logic phức tạp hay khả năng tuân thủ chỉ dẫn.
*   **Questions for authors:**
    1.  Hệ số của luật tỷ lệ thay đổi như thế nào khi áp dụng trên các mô hình lớn hơn nhiều (ví dụ LLaMA-2 70B)? Liệu quy mô mô hình lớn có tự động làm chậm tốc độ quên lãng tuyệt đối không?
    2.  Với việc mối tương quan $L_f(L_{ft})$ thất bại trên IA3 ($R^2 = 0.18$), làm thế nào để khẳng định đây là một "Định luật" chung cho mọi tiến trình fine-tuning thay vì chỉ là đặc trưng của LoRA?
    3.  Tại sao nhóm tác giả không tích hợp một baseline sử dụng Experience Replay (phát lại dữ liệu) để kiểm tra xem liệu phương pháp này có thể phá vỡ mối liên kết tuyến tính giữa fine-tuning loss và forgetting loss không?
*   **Recommendation:** **Borderline**
    *Giải thích:* Đóng góp về mặt toán học và metric đối chứng logits của bài báo là rất tốt và đáng ghi nhận. Tuy nhiên, việc khẳng định các phát hiện này là "Scaling Laws" (Luật tỷ lệ) mang tính phổ quát trong khi thực nghiệm chỉ giới hạn trên một mô hình 7B, 260 bước huấn luyện, và bị sụp đổ trên phương pháp PEFT khác (IA3) là một sự vội vã. Bài báo cần được điều chỉnh và bổ sung thêm các thử nghiệm đa dạng hơn để xứng đáng được accept tại các hội nghị lớn.

---

## 5. Data & Metrics View Designer
*   **Datasets & Tasks:** OpenOrca (Instruction tuning), News (scraped web text), WikiText-103 (general distribution), ARC (reasoning), AdvBench (safety).
*   **Metrics:** Forgetting Loss ($L_f$), Fine-tuning Loss ($L_{ft}$), Accuracy, Rejection Count.
*   **Baselines:** Un-tuned LLaMA-2 7B Chat.

### Propose Charts:
1.  **Scatter Plot (Mối quan hệ Tuyến tính Nghịch đảo):**
    *   *Trục X:* Fine-tuning Loss ($L_{ft}$)
    *   *Trục Y:* Forgetting Loss ($L_f$)
    *   *Series:* LoRA Ranks ($r \in \{8, 16, 32, 64, 128, 256\}$) cho OpenOrca và News
    *   *Mục đích:* Minh họa trực quan tương quan tuyến tính chặt chẽ ($R^2 > 0.94$), chứng minh quên lãng tỷ lệ thuận với mức độ học được tác vụ mới.
2.  **Line Chart (Diễn biến Forgetting Loss theo số bước huấn luyện):**
    *   *Trục X:* Training Steps (N)
    *   *Trục Y:* Forgetting Loss ($L_f$)
    *   *Series:* LoRA Ranks (8, 16, 32, 64, 128, 256)
    *   *Mục đích:* Chỉ ra các rank lớn hơn (nhiều tham số $P$ hơn) có tốc độ quên lãng nhanh hơn và sâu sắc hơn qua các bước huấn luyện.
3.  **Grouped Bar Chart (Suy giảm Khả năng Từ chối độc hại):**
    *   *Trục X:* Phiên bản mô hình (Base, News-FT, OpenOrca-FT)
    *   *Trục Y:* Số lượt từ chối thành công trên AdvBench (tối đa 50)
    *   *Series:* LoRA Rank 8 models
    *   *Mục đích:* Làm nổi bật sự suy giảm nghiêm trọng hàng rào an toàn của mô hình sau khi tinh chỉnh, đặc biệt là phiên bản tinh chỉnh chỉ dẫn (OpenOrca).

### Summary Tables:
1.  **Table 1: Parameter Values for Scaling Law Fits**
    *   *Cột:* Dataset, các tham số lũy thừa ($a_f, \alpha_f, b_f, \beta_f, \rho, s_{ft}$, v.v.).
2.  **Table 2: Qualitative Forgetting Metrics on Reasoning and Safety**
    *   *Cột:* Model Checkpoint, ARC Acc vs. Ground Truth, ARC Acc vs. Base Model, AdvBench Refusal Rate.

### Raw Data (Markdown Format):

```markdown
# Approximate Parameter Values for Scaling Law Equations (Table 1 in Paper)
| Parameter | OpenOrca Fit | News Fit |
| :--- | :--- | :--- |
| $a_f$ | 0.0388 × 10^7 | 0.0011 × 10^7 |
| $a_{ft}$ | 0.0007 × 10^7 | 0.0022 × 10^7 |
| $b_f$ | 23.6418 | 97.8466 |
| $b_{ft}$ | 72.9186 | 54.8678 |
| $c_{ft}$ | 0.0020 | 0.0028 |
| $c_{f,ft}$ | 1.7334 | 1.0615 |
| $s_{ft}$ | 0.6126 | 1.9253 |
| $s_{f,ft}$ | 2.0481 | 3.1285 |
| $\\alpha_f$ | 0.0351 | 0.0458 |
| $\\alpha_{ft}$ | 0.0424 | 0.0383 |
| $\\beta_f$ | 0.1468 | 0.1044 |
| $\\beta_{ft}$ | 0.1219 | 0.1161 |
| $\\rho$ | 7.6885 | 7.5996 |

# Qualitative Forgetting Evaluation (From text figures 4 & 5)
| Model Checkpoint (LoRA Rank 8) | ARC Accuracy vs. Ground Truth | ARC Accuracy vs. Base Model | AdvBench Refusals (out of 50) |
| :--- | :--- | :--- | :--- |
| Pre-trained LLaMA-2 7B Chat | 54.1% | 100.0% | 32 |
| News-Fine-Tuned (N=260) | 48.0% | 65.0% | 24 |
| OpenOrca-Fine-Tuned (N=260) | 52.0% | 67.2% | 16 |
```

---

## 6. Figure & Table Highlights (For hightlight.md)
*   **Figure 1 (Ví dụ định tính):** So sánh các câu trả lời của mô hình gốc và mô hình tinh chỉnh LoRA rank 8. Thể hiện 3 hiện tượng: học được tri thức mới (Pixel 8), quên tri thức cũ (ARC) và quên hàng rào an toàn (AdvBench).
*   **Figure 2 (Đồ thị Scatter tương quan):** Hai biểu đồ phân tán biểu diễn mối tương quan tuyến tính nghịch đảo giữa fine-tuning loss và forgetting loss trên OpenOrca và News. Khẳng định quên lãng là hệ quả của việc học tốt tác vụ mới.
*   **Figure 3 (Đường cong trajectories):** Các biểu đồ đường theo dõi tiến trình loss tinh chỉnh và quên lãng thực tế so với đường cong dự đoán bằng phương trình lũy thừa đề xuất qua 260 bước.
*   **Figure 4 (Đánh giá trên ARC):** Hai biểu đồ đường chứng minh việc đánh giá độ chính xác của mô hình tinh chỉnh đối chứng với mô hình gốc (đường màu xanh lá) phản ánh hiện tượng quên lãng nhạy bén hơn nhiều so với đối chứng ground-truth thông thường (đường màu xanh lam).
*   **Figure 5 (Mất an toàn trong thực tế):** Đoạn chat minh họa mô hình tinh chỉnh OpenOrca viết mã khai thác bảo mật Windows 10, trong khi mô hình gốc từ chối thẳng thừng.
*   **Table 1 (Bảng tham số tối ưu):** Tổng hợp các hệ số và số mũ lũy thừa thu được sau khi khớp tối ưu hóa các đường cong thực nghiệm cho hai tập dữ liệu.
*   **Figure 6 (Appendix - IA3 Failure):** Đồ thị phân tán chỉ ra phương trình tuyến tính $L_f(L_{ft})$ hoạt động rất kém trên phương pháp PEFT khác là IA3 ($R^2$ chỉ đạt 0.18), chứng tỏ luật tỷ lệ này chưa đạt tính phổ quát toàn diện.
*   **Figures 7 & 8 (Appendix - Qualitative detail):** Tổng hợp hàng loạt câu trả lời bị lỗi tư duy và các câu trả lời vi phạm nghiêm trọng tính an toàn (hướng dẫn viết virus, tự tử, bạo lực) của mô hình LoRA rank 8 sau khi tinh chỉnh.
