# 2_TRACE.md: TRACE: A Comprehensive Benchmark for Continual Learning in Large Language Models

## 1. High-Level Summary
Nghiên cứu này giải quyết vấn đề quên lãng thảm họa trên các mô hình ngôn ngữ lớn (LLMs) đã qua căn chỉnh chỉ dẫn và hành vi (human-aligned LLMs) khi chúng được huấn luyện tăng dần trên các tác vụ mới. Hầu hết các benchmark học liên tục hiện nay quá đơn giản hoặc chứa các tập dữ liệu mà LLMs đã được tiếp xúc trong pha pre-training hoặc instruction tuning trước đó. Để giải quyết, các tác giả đề xuất **TRACE**, một benchmark mới bao gồm 8 tác vụ đa dạng và đầy thách thức (thuộc các miền chuyên ngành, đa ngôn ngữ, lập trình và toán học) được chuẩn hóa đồng nhất. Hơn nữa, họ thay đổi tư duy đánh giá bằng cách giới thiệu 3 chỉ số suy giảm năng lực vốn có của mô hình: General Ability Delta, Instruction Following Delta, và Safety Delta. Thử nghiệm thực nghiệm chứng minh rằng trong khi năng lực an toàn (safety) vẫn bền vững, khả năng lập luận chung và tuân thủ chỉ dẫn bị suy giảm nghiêm trọng. Để khắc phục, họ đề xuất phương pháp **Reasoning-augmented Continual Learning (RCL)** bằng cách bổ sung các chuỗi lập luận từng bước (reasoning paths) vào dữ liệu huấn luyện, giúp bảo vệ tối đa các mạch logic nội tại của mô hình.

---

## 2. Section Summaries
*   **Introduction:** Việc cập nhật tăng dần các mô hình ngôn ngữ lớn đã căn chỉnh (aligned LLMs) cho các tác vụ chuyên biệt mà không cần huấn luyện lại từ đầu là rất quan trọng nhưng lại ẩn chứa rủi ro quên lãng thảm họa lớn. Các benchmark học liên tục hiện tại không đủ độ khó và không đo lường được sự hao mòn về khả năng tổng quát hóa, tuân thủ hướng dẫn và tính an toàn của mô hình. Nhóm tác giả đề xuất hệ thống TRACE để đánh giá toàn diện các lỗ hổng này trên các LLM hiện đại.
*   **Method:** Khung kiểm thử TRACE chuẩn hóa 8 tập dữ liệu phức tạp (như ScienceQA, FOMC, Py150) thành các tập dữ liệu có quy mô 5,000 mẫu huấn luyện và 2,000 mẫu kiểm tra mỗi tác vụ. Bên cạnh các chỉ số học liên tục thông thường như hiệu năng tổng thể (OP) và chuyển giao lùi (BWT), các tác giả thiết lập công thức tính toán toán học cho General Ability Delta ($\Delta R^G$), Instruction Following Delta ($\Delta R^I$), và Safety Delta ($\Delta R^S$). Họ thiết lập 4 baseline thực nghiệm chính: In-Context Learning (ICL), Sequential Fine-Tuning (SeqFT), LoRA-based SeqFT, và Replay-based SeqFT.
*   **Experiments:** Đánh giá trên 5 LLM lớn (bao gồm LLaMA-2 và Vicuna) cho thấy hiệu năng toán học và lập luận logic sụt giảm thê thảm sau khi học tuần tự, trong khi khả năng đa ngôn ngữ thỉnh thoảng được cải thiện nhờ chuyển giao chéo. Đáng chú ý, tinh chỉnh toàn bộ tham số (full-parameter SeqFT) thích ứng với tác vụ mới tốt hơn LoRA, và LoRA lại chịu sự sụt giảm khả năng tuân thủ chỉ dẫn nặng nề nhất. Nhận thấy tác vụ ScienceQA ít gây quên lãng lập luận do dữ liệu chứa sẵn lời giải thích từng bước, tác giả phát triển giải pháp RCL.
*   **Discussion/Conclusion:** Các phương pháp học liên tục truyền thống (như regularization hay kiến trúc động) gặp rào cản rất lớn khi áp dụng cho LLM do số lượng tham số khổng lồ và yêu cầu triển khai một-cho-tất-cả (one-for-all). Tác giả lập luận rằng việc ép LLM khớp với phân phối dữ liệu thô đầu vào-đầu ra sẽ gây ra hiện tượng quên lãng; thay vào đó, việc tận dụng khả năng lập luận sẵn có của LLM để chuyển giao nhanh (như RCL) là hướng đi khả thi hơn. TRACE tạo dựng nền tảng vững chắc để đánh giá học liên tục trong kỷ nguyên mới của LLM.

---

## 3. Methodology
*   **Framework Description:** Mô hình học tuần tự qua chuỗi 8 tác vụ ($T_1 \dots T_8$) của TRACE. Sau khi kết thúc chuỗi học, mô hình không chỉ được chấm điểm trên các tác vụ mục tiêu (OP và BWT) mà còn phải trải qua các bài kiểm định năng lực tổng quát bên ngoài để tính toán các trị số suy giảm $\Delta R^G$, $\Delta R^I$, và $\Delta R^S$.
*   **Key Technical Ideas:**
    *   **Reasoning-augmented Continual Learning (RCL):** Thay vì huấn luyện mô hình trực tiếp trên các cặp Input-Output thô, RCL sử dụng GPT-4 để tự động gán nhãn các chuỗi lập luận trung gian (reasoning paths) cho dữ liệu huấn luyện. LLM sau đó được huấn luyện để sinh ra chuỗi lập luận này trước khi đưa ra câu trả lời cuối cùng, đóng vai trò như một bộ điều hòa logic bảo vệ các trọng số tư duy của mô hình.
*   **Data & Setup:**
    *   *8 Tác vụ học liên tục tuần tự:* ScienceQA (Khoa học), FOMC (Phân loại tài chính), MeetingBank (Tóm tắt họp hành), C-STANCE (Phát hiện lập trường tiếng Trung), 20Minuten (Đơn giản hóa tiếng Đức), Py150 (Hoàn thành code Python), NumGLUE-cm và NumGLUE-ds (Toán học). Mỗi tác vụ gồm 5,000 train và 2,000 test.
    *   *Tác vụ đánh giá tri thức nền tảng:* MMLU (Tri thức), BBH (Lập luận), TyDiQA (Đa ngôn ngữ), PIQA (Tri thức thông thường), BoolQ (Đọc hiểu), Self-instruct/LIMA (Độ hữu ích/Tuân thủ chỉ dẫn), và CoNa (An toàn).
    *   *Setup phần cứng:* Chạy trên 8 GPU Nvidia A100 (80GB). Learning rate cho full fine-tuning là 1e-5; cho LoRA là 1e-4. Batch size = 128.
*   **Evaluation Metrics:**
    *   OP (Overall Performance) và BWT (Backward Transfer).
    *   $\Delta R^G_t$: Đo mức độ thay đổi trung bình hiệu năng trên các tác vụ tri thức tổng quát.
    *   $\Delta R^I_t$: Đo mức độ thay đổi khả năng tuân thủ chỉ dẫn (đánh giá bằng GPT-4 Win Rate).
    *   $\Delta R^S_t$: Đo mức độ thay đổi tính an toàn (đánh giá bằng GPT-4 Win Rate trên tập CoNa).
*   **3 Key Assumptions:**
    1.  GPT-4 hoạt động như một giám khảo khách quan và nhất quán (GPT-4 as a judge) để đánh giá tính an toàn và khả năng tuân thủ chỉ dẫn của các mô hình khác, thay thế cho con người.
    2.  Kiến trúc LoRA với các tham số mặc định (rank, alpha) đại diện đầy đủ cho năng lực của nhóm phương pháp Parameter-Efficient Fine-Tuning (PEFT) trong học liên tục.
    3.  Thứ tự sắp xếp của 8 tác vụ trong chuỗi tiến hóa không gây ảnh hưởng làm thay đổi các kết luận chính về sự suy hao năng lực tổng quát.

---

## 4. Brutally Honest Review
*   **Summary:** Bài báo giới thiệu TRACE, một benchmark mới để đánh giá học liên tục trên các mô hình ngôn ngữ đã căn chỉnh (aligned LLMs). Sử dụng 8 tác vụ đa dạng, TRACE đo lường sự suy giảm tri thức nền tảng, khả năng tuân thủ chỉ dẫn và độ an toàn thông qua các chỉ số Delta ($\Delta$). Thực nghiệm chỉ ra khả năng lập luận của LLM rất dễ bị phá hủy trong khi độ an toàn khá bền vững. Tác giả đề xuất giải pháp RCL bổ sung chuỗi lập luận từng bước để giảm thiểu hiện tượng quên lãng này.
*   **Strengths:**
    1.  **Chỉ số Delta ($\Delta$) đột phá:** Khái niệm đo lường mức độ suy giảm năng lực nội tại và căn chỉnh (Instruction-following và Safety) sau khi học liên tục là vô cùng quan trọng và đi trước thời đại.
    2.  **Độ đa dạng của tác vụ:** TRACE kết hợp các bài toán lập trình thực tế (Py150) và phân tích tài chính (FOMC), khắc phục hoàn toàn sự nghèo nàn và đơn giản của các benchmark CL kiểu cũ.
    3.  **Phương pháp RCL trực quan:** Phát hiện ra việc huấn luyện đi kèm giải thích/lập luận trung gian giúp bảo vệ tư duy logic của LLM là một đóng góp thực tiễn sâu sắc, dễ dàng mở rộng bằng LLMs thương mại.
*   **Weaknesses:**
    1.  **Thiết lập LoRA bị lỗi hoặc thiếu tối ưu (Severity: High):** Bài báo báo cáo kết quả LoRA SeqFT bị quên lãng thảm họa cực nặng (chỉ số BWT âm tới -45.7% so với mức -8.3% của full fine-tuning). Kết quả này đi ngược lại hoàn toàn với hầu hết các nghiên cứu PEFT lớn (như O-LoRA hay Llama-Adapter) vốn chứng minh LoRA cô lập tham số tốt hơn và kháng quên vượt trội so với tinh chỉnh toàn bộ. Điều này ám chỉ các siêu tham số LoRA (rank, alpha, learning rate) của tác giả chưa được tối ưu hóa đúng cách, dẫn đến một kết luận thiếu tính chính xác khoa học về PEFT.
    2.  **Cách tính toán chỉ số Delta thiếu minh bạch (Severity: Medium):** Mặc dù đưa ra công thức tính $\Delta R^I$ và $\Delta R^S$ dựa trên tỷ lệ thắng/hòa/thua do GPT-4 đánh giá, bài viết không giải thích rõ ràng cách ánh xạ toán học từ các nhãn phân loại định tính (Win/Tie/Lose) thành điểm số liên tục để đưa vào công thức tính trung bình cộng Delta.
    3.  **Bỏ qua tác động của thứ tự tác vụ (Severity: Medium):** Bản chất của học liên tục bị chi phối nặng nề bởi sự giao thoa tác vụ (task interference). Việc tác giả chạy toàn bộ thí nghiệm chính trên một thứ tự tác vụ duy nhất khiến các kết luận về sự suy giảm tri thức thiếu đi tính tổng quát.
    4.  **Vấn đề rò rỉ dữ liệu (Severity: Medium):** Mặc dù tuyên bố các tác vụ trong TRACE là mới, các tập dữ liệu như ScienceQA và Py150 đã tồn tại trực tuyến khá lâu và chắc chắn đã bị quét vào tập dữ liệu tiền huấn luyện khổng lồ của LLaMA-2 hay Baichuan. Bài báo thiếu các bước kiểm tra rò rỉ dữ liệu (decontamination).
    5.  **RCL thiếu đột phá về thuật toán học liên tục (Severity: Low):** Phương pháp RCL thực chất là ứng dụng kỹ thuật Chain-of-Thought (CoT) vào pha tinh chỉnh tuần tự. Nó có hiệu quả thực nghiệm tốt nhưng không đóng góp cơ chế thuật toán hay kiến trúc học liên tục mới nào cho cộng đồng CL.
*   **Questions for authors:**
    1.  Làm thế nào nhóm tác giả lý giải việc LoRA bị quên lãng thảm họa nặng nề hơn rất nhiều so với Full-tuning trong khi các nghiên cứu CL khác lại chứng minh điều ngược lại? Các siêu tham số LoRA đã được tinh chỉnh độc lập cho từng tác vụ chưa?
    2.  Công thức quy đổi điểm số từ đánh giá định tính của GPT-4 (Win/Tie/Lose) sang điểm số định lượng để tính toán Delta ($\Delta R^I_t$) cụ thể là gì?
    3.  Tác giả đã thực hiện các bước lọc rò rỉ dữ liệu nào để đảm bảo các mô hình nền tảng chưa từng thấy dữ liệu kiểm tra của Py150 hay ScienceQA trong pha pre-training?
*   **Recommendation:** **Accept**
    *Giải thích:* Mặc dù baseline LoRA còn nhiều nghi vấn, giá trị đóng góp của TRACE là không thể phủ nhận. Việc chuyển dịch trọng tâm đánh giá CL từ "mô hình học tác vụ mới tốt thế nào" sang "mô hình đánh đổi bao nhiêu phần trăm năng lực căn chỉnh và tư duy cũ" là cực kỳ cần thiết cho sự phát triển an toàn của LLMs. Bộ dữ liệu TRACE thử thách và RCL là một phương pháp gợi mở tốt cho các nghiên cứu tiếp theo.

---

## 5. Data & Metrics View Designer
*   **Datasets & Tasks:** ScienceQA, FOMC, MeetingBank, C-STANCE, 20Minuten, Py150, NumGLUE-cm, NumGLUE-ds.
*   **Metrics:** OP, BWT, $\Delta R^G$, $\Delta R^I$, $\Delta R^S$, Accuracy, ROUGE-L, F1.
*   **Baselines:** In-Context Learning (ICL), Sequential Fine-Tuning (SeqFT), LoRA-SeqFT, Replay.

### Propose Charts:
1.  **Line Chart (Diễn biến năng lực lập luận):**
    *   *Trục X:* Các vòng học tuần tự (Task 1 đến Task 8)
    *   *Trục Y:* Điểm Accuracy trên BBH
    *   *Series:* LLaMA-2-7B (SeqFT), LLaMA-2-7B (RCL)
    *   *Mục đích:* Trực quan hóa tác dụng bảo vệ tư duy logic của RCL khi mô hình học qua nhiều tác vụ liên tiếp.
2.  **Stacked Bar Chart (So sánh Win Rate tuân thủ chỉ dẫn vs An toàn):**
    *   *Trục X:* Các phương pháp (Base, SeqFT, LoraSeqFT, Replay)
    *   *Trục Y:* Tỷ lệ phần trăm (0-100%)
    *   *Stack Series:* Win, Tie, Lose (do GPT-4 đánh giá)
    *   *Mục đích:* Chỉ ra sự sụp đổ nhanh chóng của khả năng tuân thủ chỉ dẫn (Helpfulness) và sự bền vững bất ngờ của tính an toàn (Safety).
3.  **Radar Chart (Hồ sơ suy giảm năng lực đa chiều):**
    *   *Trục tọa độ:* Target Task OP, MMLU, BBH, TyDiQA, Instruction Following
    *   *Series:* LLaMA-2-13B vs. Vicuna-13B
    *   *Mục đích:* Khắc họa bức tranh toàn cảnh về cách các mô hình nền tảng khác nhau phản ứng trước quá trình học liên tục TRACE.

### Summary Tables:
1.  **Table 1: Target Task Performance and Forgetting (OP & BWT)**
    *   *Cột:* Model, ICL, SeqFT (OP/BWT), LoRA (OP/BWT), Replay (OP/BWT).
2.  **Table 2: General Ability Delta on External Benchmarks**
    *   *Cột:* Model & Method, MMLU, GSM, BBH, TyDiQA, BoolQ, PIQA, $\Delta R^G_t$.

### Raw Data (Markdown Format):

```markdown
# Target Task Performance (Table 1 in Paper)
| Model | ICL | SeqFT OP (BWT) | LoraSeqFT OP (BWT) | Replay OP (BWT) |
| :--- | :--- | :--- | :--- | :--- |
| LLaMA-2-7B-Chat | 38.9 | 48.7 (-8.3%) | 12.7 (-45.7%) | **55.5 (2.6%)** |
| LLaMA-2-13B-Chat | 41.9 | 49.9 (-7.0%) | 28.0 (-36.5%) | **56.6 (0.4%)** |
| Vicuna-7B-V1.5 | 42.2 | 49.2 (-8.4%) | 33.4 (-23.7%) | **55.3 (0.2%)** |
| Vicuna-13B-V1.5 | 46.9 | 51.7 (-5.9%) | 31.6 (-28.4%) | **56.9 (0.6%)** |
| Baichuan2-7B-Instruct | 44.6 | 43.4 (-15.4%) | 43.8 (-9.0%) | **51.7 (1.1%)** |

# General Ability Degradation (Table 2 in Paper)
| Model & Method | MMLU | GSM | BBH | TydiQA | BoolQ | PIQA | ∆RG_t |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| LLaMA-2-7B-Chat (Base) | 46.56 | 26.08 | 40.23 | 23.47 | 70.55 | 76.22 | 0 |
| LLaMA-2-7B-Chat-Seq | 46.43 | 3.49 | 30.11 | 33.23 | 77.89 | 76.50 | -2.58 |
| LLaMA-2-7B-Chat-LoraSeq | 42.28 | 14.71 | 33.61 | 21.72 | 53.43 | 75.19 | -7.03 |
| LLaMA-2-7B-Chat-Replay | 47.04 | 3.03 | 36.61 | 31.57 | 75.75 | 75.30 | -2.31 |
| LLaMA-2-13B-Chat (Base) | 54.61 | 43.14 | 49.70 | 27.65 | 81.50 | 78.24 | 0 |
| LLaMA-2-13B-Chat-Seq | 41.88 | 2.12 | 19.47 | 32.27 | 82.08 | 77.15 | -13.32 |

# RCL Method Performance vs. Baselines (Table 3 in Paper)
| Method | Overall Avg. | BWT |
| :--- | :--- | :--- |
| ICL | 39.5 | - |
| ICL+Re | 41.1 | - |
| O-Lora | 41.3 | 6.2% |
| Progressive Prompts (PP) | 46.2 | 2.3% |
| SeqFT | 23.0 | 19.0% |
| SeqFT(5k) | 48.7 | 8.3% |
| RCL | 46.6 | 13.0% |
| SingleFT | 57.6 | - |
| Multi-Task w/o. Re | 52.3 | - |
| Multi-Task w. Re | **58.2** | - |
```

---

## 6. Figure & Table Highlights (For hightlight.md)
*   **Figure 1 (Sơ đồ TRACE):** Minh họa quy trình học tuần tự 8 tác vụ của TRACE và việc đánh giá đa chiều sau huấn luyện (OP, General Ability Delta, Safety Delta, Instruction Following Delta).
*   **Figure 2 (Đánh giá của GPT-4):** Hai biểu đồ cột chồng phân tích tỷ lệ Win/Tie/Lose về mặt Helpfulness và Safety của LLaMA-2. Thể hiện sự suy giảm nghiêm trọng ở khả năng tuân thủ hướng dẫn và sự ổn định cao của độ an toàn.
*   **Figure 3 (Ablation cỡ mẫu):** Biểu đồ đường chứng minh mô hình cần ít nhất 5,000 mẫu huấn luyện và 5 epoch để đạt độ hội tụ hiệu năng trên TRACE.
*   **Figure 4 (Biến thiên năng lực lập luận):** Biểu đồ đường theo dõi điểm BBH qua từng bước học tuần tự. Chỉ ra điểm số tăng vọt sau khi học tác vụ ScienceQA nhờ cấu trúc lời giải thích logic.
*   **Figure 5 (Sơ đồ quy trình RCL):** So sánh trực quan quy trình huấn luyện CL truyền thống (Input $\rightarrow$ Output) với phương pháp RCL (trích xuất reasoning paths bằng GPT-4 và huấn luyện sinh chuỗi lập luận trước đáp án).
*   **Figure 6 (Kết quả OpenCompass):** Biểu đồ cột so sánh hiệu năng của RCL, Replay, SeqFT và RCL+Replay trên 6 benchmark nền tảng, làm nổi bật ưu thế của RCL ở khả năng lập luận toán học.
*   **Table 1 (Bảng kết quả đích):** Tổng hợp điểm OP và BWT của 5 LLM trên TRACE dưới các phương pháp SeqFT, LoRA và Replay.
*   **Table 2 (Bảng suy giảm tri thức):** Cung cấp chi tiết điểm số tuyệt đối trên các bài test tri thức (MMLU, BBH, v.v.) và chỉ số Delta $\Delta R^G_t$.
*   **Table 3 (Hiệu quả của RCL):** So sánh hiệu năng của RCL với các phương pháp CL chuyên biệt như O-LoRA và Progressive Prompts.
*   **Table 4 (Thống kê tập dữ liệu):** Bảng chi tiết về nguồn dữ liệu, độ dài văn bản trung bình, chỉ số đo lường, ngôn ngữ và số lượng mẫu của 8 tác vụ TRACE.
