# 2_Fine_tuned_LMs.md: Fine-tuned Language Models are Continual Learners (Continual-T0)

## 1. High-Level Summary
Nghiên cứu này giải quyết vấn đề quên lãng thảm họa (catastrophic forgetting) trong các mô hình ngôn ngữ lớn (LLMs) khi chúng được tinh chỉnh (fine-tune) tuần tự trên các tác vụ mới nằm ngoài phân phối huấn luyện ban đầu. Nhóm tác giả đề xuất **Continual-T0 (CT0)**, chứng minh rằng các LLM đã qua căn chỉnh chỉ dẫn (instruction-tuned LLMs) có thể trở thành những mô hình học trọn đời cực kỳ hiệu quả mà chỉ cần một cơ chế ôn tập dữ liệu (rehearsal) vô cùng đơn giản. Bằng việc phát lại (replay) chỉ 1% dữ liệu huấn luyện của các tác vụ cũ (tối đa 1,000 mẫu cho mỗi tác vụ), mô hình học thành công 8 tác vụ sinh ngôn ngữ mới trong khi vẫn bảo toàn gần như 100% hiệu năng zero-shot trên 70 tập dữ liệu cũ của T0. Trong bối cảnh nghiên cứu hiện tại, kết quả này thách thức quan niệm phổ biến cho rằng cần phải có các thuật toán học liên tục phức tạp và tốn kém cho NLP. Thay vào đó, nghiên cứu chỉ ra rằng khả năng học trọn đời mạnh mẽ thực chất tự xuất hiện từ quá trình tiền huấn luyện tự giám sát (self-supervised pre-training) quy mô lớn của các mô hình nền tảng.

---

## 2. Section Summaries
*   **Introduction:** Mặc dù các mô hình ngôn ngữ lớn sau khi tinh chỉnh chỉ dẫn đạt khả năng zero-shot ấn tượng, chúng vẫn gặp nhiều khó khăn trước các tác vụ chưa từng thấy trong tập đánh giá gốc. Việc tinh chỉnh chúng tuần tự trên các kỹ năng mới thường dẫn đến hiện tượng quên lãng thảm họa các tri thức cũ. Nhóm tác giả đề xuất CT0 bằng cách áp dụng cơ chế học liên tục dựa trên phát lại dữ liệu (experience rehearsal) lên mô hình T0, giúp mô hình tiếp thu tuần tự 8 tác vụ sinh mới mà không làm suy giảm năng lực vốn có.
*   **Method:** Phương pháp cốt lõi là Học liên tục qua Phát lại (Continual Learning via Rehearsal - CLR), sử dụng một bộ đệm bộ nhớ ngoài chứa một phần nhỏ dữ liệu huấn luyện cũ (tham số tỷ lệ $r$). Tác giả khởi tạo từ trọng số của mô hình T0 (đã học trên 50 tập dữ liệu) và thiết lập $r$ ở mức cực kỳ tiết kiệm là 1% (tương đương tối đa 1,000 ví dụ cho mỗi tác vụ cũ trong bộ đệm). Họ giới thiệu 8 tác vụ sinh ngôn ngữ tự nhiên mới (ví dụ: viết thơ Haiku, hội thoại đồng cảm, đơn giản hóa văn bản) được định dạng dưới dạng prompt chỉ dẫn để huấn luyện tuần tự.
*   **Experiments:** Các thí nghiệm huấn luyện độc lập chỉ ra rằng việc không ôn tập (0% rehearsal) sẽ hủy hoại hoàn toàn khả năng zero-shot của T0, trong khi ôn tập 1% đem lại sự ổn định hoàn hảo. Trong quá trình học tuần tự cả 8 tác vụ, CT0 duy trì từ 98% đến 99.8% hiệu năng so với cận trên lý thuyết (Upper Bound - huấn luyện riêng biệt), vượt xa baseline LAMOL. Mô hình cũng thể hiện khả năng kết hợp chỉ dẫn zero-shot ấn tượng (zero-shot instruction compositionality) khi có thể phối hợp các ràng buộc định dạng và tông giọng cảm xúc chưa từng thấy cùng nhau khi suy luận.
*   **Discussion/Conclusion:** Nhóm tác giả đi sâu tìm hiểu nguyên nhân gốc rễ của năng lực học liên tục xuất sắc này bằng cách thử nghiệm triệt tiêu trên các mô hình T5-small, T5-3B và một transformer khởi tạo ngẫu nhiên. Họ kết luận rằng khả năng học trọn đời không đơn thuần xuất hiện từ kích thước mô hình hay quá trình huấn luyện đa tác vụ, mà bắt nguồn trực tiếp từ pha tiền huấn luyện tự giám sát cường độ cao (pre-training stage). Từ đó, họ đề xuất coi việc tích hợp kỹ năng mới vào LLM tương tự như việc cập nhật tính năng mới trong phát triển phần mềm nguồn mở, loại bỏ nhu cầu huấn luyện lại từ đầu.

---

## 3. Methodology
*   **Framework Description:** Hệ thống tiến hành huấn luyện tuần tự một mô hình ngôn ngữ trên chuỗi tác vụ $T = (T_1, T_2, ..., T_N)$. Khi chuyển sang huấn luyện tác vụ mới $T_i$, tập dữ liệu huấn luyện $D_i$ sẽ được trộn với một bộ đệm chứa $r\%$ mẫu dữ liệu được chọn ngẫu nhiên từ toàn bộ các tác vụ trước đó ($T_1, \dots, T_{i-1}$).
*   **Key Technical Ideas:**
    *   **Experience Rehearsal:** Sử dụng kiến trúc T5 Encoder-Decoder khởi tạo bằng trọng số của T0 (bản 3B và 11B tham số). Trộn dữ liệu tác vụ hiện tại với đúng 1% dữ liệu của các tác vụ trong quá khứ. Đặc biệt, mô hình bảo toàn được khả năng zero-shot trên các tập dữ liệu đánh giá chưa từng thấy của T0 dù dữ liệu zero-shot này chưa bao giờ được đưa vào bộ đệm ôn tập.
    *   **Zero-shot Instruction Compositionality:** Mô hình sau khi học liên tục có khả năng tự động kết hợp các loại chỉ dẫn khác nhau (như ràng buộc từ khóa của headline và phong cách viết của stylometry) một cách tự nhiên mà không cần huấn luyện chung.
*   **Data & Setup:**
    *   Mô hình nền tảng T0 được huấn luyện trước trên 50 tác vụ.
    *   8 tác vụ sinh mới được đưa vào học liên tục bao gồm: Đơn giản hóa văn bản (WikiAuto/ASSET), Tạo tiêu đề có ràng buộc từ khóa (Gigaword), Tạo thơ Haiku (Reddit), Trả lời câu hỏi COVID-QA không ngữ cảnh (Covid QA), Tạo câu hỏi tò mò (ELI5), Tạo hội thoại đồng cảm (Empathetic Dialogues), Tạo lời giải thích lập luận (eSNLI), và Twitter Stylometry (Top 20 người dùng Twitter nổi tiếng).
    *   Tỷ lệ bộ đệm $r = 1\%$ (tối đa 1,000 ví dụ mỗi tác vụ).
*   **Evaluation Metrics:**
    *   Các chỉ số tự động tiêu chuẩn: Accuracy (cho phân loại), BLEU, SARI, ROUGE, BERTScore (cho sinh ngôn ngữ).
    *   Các chỉ số thiết kế riêng: 1Tok (đo khoảng cách phân phối từ đầu tiên), Clf (độ chính xác của bộ phân loại RidgeClassifier nhận diện tác giả tweet), và Hcust (đánh giá cấu trúc âm tiết, số dòng và nội dung thơ Haiku).
    *   Relative Gain: Hiệu năng hiện tại chia cho hiệu năng cận trên (Upper Bound) khi mô hình chỉ học duy nhất tác vụ đó.
*   **3 Key Assumptions:**
    1.  Tỷ lệ ôn tập cố định 1% là đủ để đại diện cho phân phối của một tác vụ cũ và ngăn chặn hiệu quả hiện tượng quên lãng thảm họa mà không gây quá khớp (overfitting) trên dữ liệu bộ đệm.
    2.  Việc ôn tập tập huấn luyện của các tác vụ T0 đóng vai trò như một điểm neo (anchor) giữ vững không gian biểu diễn biểu tượng của mô hình, từ đó gián tiếp bảo vệ cả hiệu năng trên các tập kiểm tra zero-shot của T0.
    3.  Các bộ lọc heuristic tự động (như chỉ số Hcust) và bộ phân loại ngoài (Ridge Classifier) đủ chính xác để phản ánh chất lượng sinh văn bản mở, cho phép bỏ qua đánh giá từ con người (human evaluation).

---

## 4. Brutally Honest Review
*   **Summary:** Bài báo khảo sát khả năng học liên tục của mô hình T0 được tinh chỉnh chỉ dẫn trên chuỗi 8 tác vụ sinh mới. Bằng cách sử dụng phương pháp ôn tập 1% dữ liệu huấn luyện cũ, mô hình duy trì tốt năng lực trên 70 tập dữ liệu ban đầu. Phân tích loại bỏ chỉ ra rằng khả năng chống quên lãng thảm họa này chủ yếu thừa hưởng từ pha tiền huấn luyện tự giám sát (C4 pre-training) chứ không phải do quy mô tham số hay học đa tác vụ.
*   **Strengths:**
    1.  **Tính đơn giản và hiệu quả cao:** Chứng minh được việc giải quyết quên lãng thảm họa trên mô hình 11 tỷ tham số bằng một bộ đệm ôn tập 1% cực kỳ đơn giản là một phát hiện có giá trị ứng dụng thực tiễn rất lớn, giảm chi phí tính toán trong công nghiệp.
    2.  **Bảo toàn năng lực Zero-shot:** Khám phá ra rằng việc phát lại dữ liệu huấn luyện cũ giúp duy trì cả hiệu năng zero-shot trên các tác vụ chưa từng thấy là một phát hiện mới lạ trong văn liệu Continual Learning.
    3.  **Khả năng kết hợp chỉ dẫn linh hoạt:** Các thực nghiệm về Instruction Compositionality chứng minh mô hình thực sự hiểu các khái niệm (như ràng buộc từ khóa kết hợp với phong cách viết tweet) thay vì chỉ ghi nhớ vẹt dữ liệu.
*   **Weaknesses:**
    1.  **Thiếu tính mới về mặt thuật toán (Severity: High):** Kỹ thuật cốt lõi là Experience Replay (Rehearsal) đã được nghiên cứu và sử dụng hàng thập kỷ trong học liên tục. Bài báo không đề xuất bất kỳ cải tiến hay thuật toán CL mới nào.
    2.  **Baseline đối chứng quá yếu (Severity: Medium):** Baseline duy nhất được đem ra so sánh là LAMOL (vốn lỗi thời và thiết kế cho GPT). Nhóm tác giả hoàn toàn bỏ qua các phương pháp Parameter-Efficient Fine-Tuning (PEFT) hiện đại cho học liên tục như LoRA, Adapters hay Prompt-tuning, vốn có thể đạt mức quên lãng bằng 0 mà không cần lưu trữ bất kỳ mẫu dữ liệu cũ nào.
    3.  **Chuỗi tác vụ quá ngắn (Severity: Medium):** Việc chỉ thử nghiệm trên chuỗi tuần tự gồm 8 tác vụ mới là quá ít để khẳng định độ ổn định học trọn đời của LLM. Khi chuỗi tác vụ kéo dài lên hàng trăm hay hàng nghìn, bộ đệm 1% tích lũy sẽ phình to và gặp các vấn đề về phân phối dữ liệu nghiêm trọng.
    4.  **Nguy cơ rò rỉ dữ liệu từ tiền huấn luyện (Severity: Low):** Do mô hình T0 được xây dựng trên T5 (huấn luyện trên tập C4 khổng lồ thu thập từ web), rất có thể mô hình đã được tiếp xúc với dữ liệu của các tác vụ "mới" (như Reddit hay Wikipedia) từ trước, làm sai lệch đánh giá về độ khó của quá trình học liên tục thực tế.
    5.  **Quá phụ thuộc vào các chỉ số tự động cho bài toán sinh (Severity: Medium):** Đánh giá các tác vụ mang tính chủ quan cao như thơ Haiku hay hội thoại đồng cảm hoàn toàn bằng BERTScore và các bộ lọc tự chế (Hcust) là thiếu độ tin cậy. Cần thiết phải có đánh giá từ con người (human evaluation) để xác thực chất lượng văn bản sinh ra.
*   **Questions for authors:**
    1.  Tại sao nhóm tác giả không so sánh CT0 với các phương pháp dựa trên phân tách tham số (PEFT như Adapters/LoRA chuyên biệt cho từng tác vụ), vốn lưu giữ tri thức cũ hoàn hảo mà không tốn bộ nhớ lưu trữ dữ liệu?
    2.  Nếu chuỗi tác vụ kéo dài tới 1,000 tác vụ, bộ đệm 1% sẽ phình to vượt quá kích thước dữ liệu của tác vụ mới tại mỗi bước. Phương pháp này sẽ đối phó với giới hạn lưu trữ của chuỗi tác vụ dài vô hạn như thế nào?
    3.  Với việc T5 đã được pre-train trên C4 chứa Wikipedia và Reddit, làm thế nào để chứng minh mô hình thực sự đang "học liên tục" kỹ năng mới thay vì chỉ đang kích hoạt lại các tri thức đã ghi nhớ từ trước thông qua căn chỉnh chỉ dẫn?
*   **Recommendation:** **Accept**
    *Giải thích:* Mặc dù thiếu hụt sự đột phá về mặt thuật toán khi chỉ sử dụng rehearsal truyền thống, giá trị thực nghiệm của bài báo là rất lớn. Việc chứng minh các mô hình lớn được instruction-tuned có thể dễ dàng tiếp thu các tác vụ sinh ngôn ngữ mới mà vẫn giữ vững năng lực zero-shot bằng một bộ đệm siêu nhỏ đã bác bỏ các lo ngại trước đây về sự mong manh của LLM khi tinh chỉnh. Phát hiện về mối liên hệ giữa tiền huấn luyện và khả năng học liên tục mang lại giá trị khoa học sâu sắc.

---

## 5. Data & Metrics View Designer
*   **Datasets & Tasks:** WikiAuto/ASSET (Đơn giản hóa), Gigaword (Headline), Reddit (Haiku), Covid QA (Hỏi đáp), ELI5 (Tạo câu hỏi), Empathetic Dialogues (Hội thoại), eSNLI (Giải thích), Top 20 Twitter Users (Stylometry).
*   **Metrics:** BLEU, SARI, ROUGE, BERTScore, Accuracy, 1Tok, Clf, Hcust.
*   **Baselines:** LAMOL, Seq-finetune, Multi-task (Upper Bound).

### Propose Charts:
1.  **Line Chart (Độ ổn định của Rehearsal):**
    *   *Trục X:* Các bước huấn luyện (0 đến 134)
    *   *Trục Y:* Điểm Relative Gain
    *   *Series:* Tác vụ mục tiêu (0%, 0.25%, 1% rehearsal), T0zs (0%, 0.25%, 1% rehearsal)
    *   *Mục đích:* Chỉ ra sự sụp đổ của hiệu năng zero-shot ở mức 0% và tính ổn định tuyệt vời khi nâng lên 1% rehearsal.
2.  **Line Chart (Độ ổn định trên chuỗi tuần tự):**
    *   *Trục X:* Thứ tự các tác vụ được học tuần tự (T1 đến T8)
    *   *Trục Y:* Relative Gain
    *   *Series:* 8 tác vụ sinh mới + T0zs (zero-shot)
    *   *Mục đích:* Minh chứng rằng hiệu năng của tất cả các tác vụ cũ và mới đều được duy trì tiệm cận mức 1.0 trong suốt tiến trình học liên tục.
3.  **Bar Chart (Độ tổng quát hóa ràng buộc zero-shot):**
    *   *Trục X:* Số lượng ràng buộc đồng thời (# Constraints = 1, 2, 3)
    *   *Trục Y:* Phần trăm số ràng buộc được đáp ứng đúng
    *   *Series:* CT0 vs CT0-No-Constraint
    *   *Mục đích:* Chứng minh mô hình CT0 có khả năng kết hợp các chỉ thị phức tạp chưa từng gặp trong huấn luyện một cách tự nhiên.

### Summary Tables:
1.  **Table 1: Main Continual Learning Results (CT0 vs Baselines)**
    *   *Cột:* Model, T0tr (Acc), T0zs (Acc), ASSET (SARI), Gigaword (ROUGE), Haiku (Hcust), CovidQA (BERTScore), v.v.
    *   *Dòng:* T0_3B (base), UB_3B (Upper Bound), LAMOL, CT0_3B, CT0_11B.
2.  **Table 2: Ablation on Model Initialization**
    *   *Cột:* Mô hình (Random, T5-small, T5-3B, T0-3B), Điểm Upper Bound trung bình, Điểm Continual Performance trung bình.

### Raw Data (Markdown Format):

```markdown
# Main Evaluation Results (Table 3 in Paper)
| Model | T0tr (Acc) | T0zs (Acc) | ASSET (BLEU/SARI) | Simp (BLEU/SARI) | HGen (R1/Cons) | Haiku (Hcust) | CQA (BS) | InqQG (1Tok/BS) | EmDg (BS) | Exp (BS) | TwSt (Clf/BS) |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| T0_3B | 49.8 | 48.2 | 70.1/41.0 | 12.8/41.1 | 33.6/32.2 | 34.2 | 47.6 | 2.1/58.7 | 48.6 | 32.7 | 54.4/38.0 |
| T0pp | 54.2 | 65.6 | 56.5/37.7 | 11.7/40.1 | 34.9/35.9 | 31.6 | 46.0 | 2.4/59.8 | 49.7 | 37.2 | 66.4/45.1 |
| UB_3B | 49.8 | 48.2 | 79.9/45.2 | 13.8/44.6 | 39.7/81.0 | 62.6 | 90.0 | 5.3/63.3 | 55.7 | 71.8 | 74.8/56.5 |
| UB_pp | 54.2 | 65.6 | 85.3/46.1 | 15.0/44.8 | 41.9/86.9 | 63.9 | 90.0 | 4.9/65.7 | 56.6 | 73.5 | 74.4/57.9 |
| LAMOL | 32.6 | 33.6 | 37.3/12.6 | 8.4/21.4 | 22.9/33.5 | 25.8 | 46.6 | 1.8/47.9 | 45.1 | 27.6 | 50.1/35.2 |
| CT0_3B | 47.9 | 46.6 | 78.0/44.5 | 14.6/43.7 | 37.3/77.5 | 60.4 | 86.8 | 5.2/61.9 | 55.3 | 72.4 | 74.8/56.5 |
| CT0_pp | **53.7** | **64.4** | **85.9/46.6** | **14.6/44.7** | **40.7/85.5** | **65.8** | **89.8** | **4.8/65.2** | **56.2** | **73.0** | **74.4/57.9** |
| revfinal| 48.1 | 48.8 | 83.3/45.4 | 14.6/43.9 | 39.0/81.6 | 61.2 | 88.6 | 4.4/61.9 | 55.0 | 72.4 | 73.2/57.3 |

# Constraint Compositionality Generalization (Table 4 in Paper)
| Model | HGen (# Cons = 1) | HGen (# Cons = 2) | HGen (# Cons = 3) | TwSt (# Cons = 1) |
| :--- | :--- | :--- | :--- | :--- |
| CT0 | 77.0% | 56.4% | 39.5% | 46.4% |
| CT0NoCons | 33.6% | 15.4% | 8.1% | 10.7% |

# Initialization Ablation Results (Table 5 in Paper excerpt)
| Model/Metric | UB_rand | UB_T5small | UB_T53B | UB_T0 | CT_rand | CT_T5small | CT_T53B | CT0_3B |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| T0zs Acc | N/A | N/A | N/A | 48.2 | N/A | N/A | N/A | 46.6 |
| ASSET SARI | 24.3 | 45.9 | 45.6 | 45.2 | 22.9 | 45.8 | 45.8 | 44.5 |
| Simp SARI | 29.6 | 43.2 | 43.7 | 44.6 | 28.5 | 42.8 | 44.0 | 43.7 |
| HGen Cons | 0.1 | 67.8 | 89.4 | 81.0 | 0.0 | 64.8 | 88.3 | 77.5 |
```

---

## 6. Figure & Table Highlights (For hightlight.md)
*   **Table 1:** Minh họa quá trình định dạng một tác vụ NLU thông thường thành prompt đầu vào/đầu ra tương thích với instruction tuning của T5/T0.
*   **Table 2:** Danh sách 8 tác vụ sinh mới được định nghĩa đi kèm ví dụ cụ thể về Prompt chỉ dẫn và câu trả lời mẫu tương ứng.
*   **Figure 1 (Rehearsal Rates):** Ba biểu đồ đường biểu diễn sự thay đổi hiệu năng của tác vụ học mới và các tác vụ zero-shot cũ khi thay đổi tỷ lệ rehearsal ($0\\%, 0.25\\%, 1\\%$) trên 3 tập dữ liệu. Thể hiện rõ 1% là điểm cân bằng lý tưởng.
*   **Figure 2 (Sequential Learning Curve):** Biểu đồ đường chứng minh sự ổn định của Relative Gain (tiệm cận 1.0) trên cả 8 tác vụ khi chúng được tiếp thu tuần tự.
*   **Table 3 (Bảng kết quả chính):** So sánh chi tiết CT0 (3B và 11B) với T0 gốc, Upper Bound và LAMOL. Chỉ ra CT0 giữ vững năng lực cho cả tác vụ huấn luyện và zero-shot.
*   **Table 4 (Ràng buộc zero-shot):** Bảng số liệu đo lường mức độ tuân thủ ràng buộc của CT0 khi tăng số lượng từ khóa yêu cầu từ 1 lên 3.
*   **Figure 3 (Emotional Haiku):** Biểu đồ cột thể hiện tỷ lệ sinh thơ Haiku đáp ứng đúng cảm xúc yêu cầu của người dùng trong điều kiện zero-shot.
*   **Table 5 (Ablation Khởi tạo):** So sánh hiệu năng học liên tục khi thay thế T0 bằng T5-small, T5-3B và transformer ngẫu nhiên, chứng minh pha tiền huấn luyện tự giám sát là nguồn gốc của khả năng học trọn đời.
*   **Table 6 (Ví dụ định tính):** So sánh các mẫu câu trả lời do CT0 và T0pp sinh ra, cho thấy CT0 tuân thủ tốt các ràng buộc cú pháp và ngữ cảnh (như luật thơ Haiku).
