# 2_Empirical_Study.md: An Empirical Study of Catastrophic Forgetting in Large Language Models During Continual Fine-tuning

## 1. High-Level Summary
Nghiên cứu này thực hiện một khảo sát thực nghiệm chi tiết về hiện tượng quên lãng thảm họa (catastrophic forgetting - CF) xảy ra trong các mô hình ngôn ngữ lớn (LLMs) từ 1 tỷ đến 7 tỷ tham số khi trải qua tinh chỉnh chỉ dẫn tuần tự (continual instruction tuning). Lĩnh vực học liên tục trước đó chủ yếu tập trung vào các tác vụ phân loại hoặc mô hình chỉ mã hóa (encoder-only), bỏ qua cách mà tri thức nền tảng và khả năng lập luận của các mô hình sinh ngôn ngữ giải mã (decoder-only) bị xói mòn. Ý tưởng chính của nghiên cứu là cho các LLM học tuần tự 5 tác vụ sinh ngôn ngữ khác nhau, sau đó đo lường mức độ suy giảm năng lực của chúng trên các benchmark tri thức tổng quát, lập luận logic, đọc hiểu và định kiến xã hội. Kết quả thực nghiệm cho thấy quên lãng thảm họa diễn ra phổ biến trên mọi kiến trúc, đặc biệt ảnh hưởng nặng nhất đến khả năng đọc hiểu và tri thức chuyên ngành. Thú vị là, nghiên cứu phát hiện ra các mô hình lớn hơn có tốc độ quên lãng tương đối nhanh hơn do xuất phát điểm ban đầu rất cao, và việc sử dụng kiến trúc chỉ giải mã (decoder-only) cũng như việc trộn dữ liệu chỉ dẫn tổng hợp (như Alpaca) có thể xoa dịu đáng kể hiện tượng này.

---

## 2. Section Summaries
*   **Introduction:** Mặc dù LLM sở hữu năng lực đa dạng, chúng vẫn cần được tinh chỉnh trên dữ liệu chuyên biệt để ứng dụng trong các bài toán thực tế. Vì dữ liệu tiền huấn luyện khổng lồ ban đầu thường không thể tiếp cận được, quá trình cập nhật tuần tự này hoạt động như một tiến trình học liên tục, đe dọa trực tiếp đến tri thức nền tảng tích lũy của mô hình. Tác giả đặt ra 3 câu hỏi cốt lõi: liệu quên lãng thảm họa có tồn tại khi học tuần tự chỉ dẫn, các yếu tố quy mô và kiến trúc ảnh hưởng như thế nào, và làm sao để giảm thiểu tác động của nó.
*   **Method:** Nghiên cứu thiết lập một đường ống học liên tục, trong đó mô hình lần lượt học 5 tác vụ sinh ngôn ngữ tự nhiên (Đơn giản hóa, Hội thoại đồng cảm, Tạo câu hỏi tò mò, Tạo lời giải thích, và Tạo tiêu đề ràng buộc). Để đánh giá mức độ quên lãng tri thức cũ, nhóm tác giả sử dụng các bộ benchmark độc lập bao gồm Tri thức chuyên ngành (MMLU), Lập luận (Hellaswag, BoolQ, v.v.), Đọc hiểu (RACE) và Định kiến xã hội (CrowSPairs). Họ định nghĩa chỉ số $FG$ để đo lường tỷ lệ phần trăm sụt giảm hiệu năng tương đối so với mô hình gốc ban đầu.
*   **Experiments:** Kết quả chứng minh các mô hình học tốt các tác vụ chỉ dẫn mới nhưng đồng thời chịu sự sụt giảm nghiêm trọng ở tất cả các bài kiểm tra tri thức tổng quát, xác nhận sự hiện diện của quên lãng thảm họa. Đáng ngạc nhiên là việc tăng quy mô tham số mô hình (từ 1.1B lên 7.1B) lại làm trầm trọng thêm tỷ lệ quên lãng tương đối ($FG$), và các mô hình chỉ giải mã (BLOOMZ) giữ tri thức tốt hơn mô hình mã hóa-giải mã (mT0). Đồng thời, thực nghiệm hé lộ một hiệu ứng phụ thú vị: quá trình học liên tục giúp giảm thiểu đáng kể định kiến xã hội có sẵn trong LLMs.
*   **Discussion/Conclusion:** Nhóm tác giả kết luận rằng quên lãng thảm họa là một thách thức lớn trong thực tế triển khai LLM và đòi hỏi các nhà phát triển phải giám sát chặt chẽ. Họ chỉ ra rằng việc căn chỉnh chỉ dẫn đa dạng trước đó (như chuyển từ LLaMA sang Alpaca) cung cấp một tấm đệm bảo vệ tri thức rất mạnh cho các pha tinh chỉnh tiếp theo. Tuy vậy, nghiên cứu thừa nhận một số hạn chế do giới hạn tính toán nên chỉ thử nghiệm tới quy mô 7B và chạy trên một thứ tự tác vụ cố định.

---

## 3. Methodology
*   **Framework Description:** Tiến trình huấn luyện xuất phát từ một mô hình LLM ban đầu ($M_0$) và lần lượt tinh chỉnh toàn bộ tham số (full-parameter fine-tuning) trên một chuỗi gồm $N=5$ tác vụ sinh ($T_1 \dots T_5$). Tại mỗi bước học tác vụ thứ $m$, mô hình chỉ được huấn luyện trên dữ liệu $D_m$ của tác vụ đó để tạo ra checkpoint $M_m$, sau đó checkpoint này được đem đi đánh giá trên các benchmark tri thức tĩnh bên ngoài.
*   **Key Technical/Architecture Ideas:**
    *   **Architecture vs. Scale comparison:** So sánh trực tiếp giữa kiến trúc Decoder-only (BLOOMZ, LLaMA, Alpaca) và Encoder-Decoder (mT0) trên cùng quy mô tham số. Đồng thời đánh giá tác động của quy mô bằng cách thử nghiệm BLOOMZ trên 4 kích cỡ (1.1B, 1.7B, 3B, và 7.1B).
    *   **Mitigation via general instruction injection:** Thử nghiệm giải pháp giảm thiểu quên lãng bằng cách trộn thêm 10,000 mẫu dữ liệu chỉ dẫn tổng quát từ tập Alpaca vào luồng huấn luyện của các tác vụ chuyên biệt.
*   **Data & Setup:**
    *   *5 Tác vụ học liên tục:* Simp (Đơn giản hóa), Emdg (Hội thoại đồng cảm), InqQG (Tạo câu hỏi), Exp (Giải thích), HGen (Tạo tiêu đề). Mỗi tác vụ dùng 100,000 mẫu huấn luyện. Thứ tự học cố định: Simp $\rightarrow$ Emdg $\rightarrow$ InqQG $\rightarrow$ Exp $\rightarrow$ HGen.
    *   *Tác vụ đánh giá tri thức:* MMLU ( STEM, Human, Social, Other); Hellaswag, BoolQ, Winogrande, PIQA, MathQA, Mutual (Lập luận); RACE (Đọc hiểu); CrowSPairs (Định kiến xã hội).
    *   *Setup phần cứng:* Huấn luyện trên 8 card Tesla A100 (40GB), batch size = 4 mỗi thiết bị, learning rate = 2e-5. BLOOMZ/mT0 dùng hằng số learning rate, LLaMA/Alpaca dùng cosine scheduler. Huấn luyện 3 epochs cho mỗi tác vụ.
*   **Evaluation Metrics:**
    *   Hiệu năng tác vụ học: SARI (cho Simp), ROUGE-1 (cho HGen), BLEU (cho các tác vụ còn lại).
    *   Năng lực tổng quát: 5-shot Accuracy cho MMLU, 0-shot Accuracy cho các tác vụ lập luận/đọc hiểu, Perplexity (độ hỗn loạn) cho CrowSPairs.
    *   Chỉ số quên lãng ($FG_i$): Trung bình cộng của phần trăm thay đổi hiệu năng tương đối:
        $$FG_i = \frac{1}{|E_i|} \sum_{e \in E_i} \frac{1}{N} \sum_{m=1}^N \frac{Re_o - Re_m}{Re_o} \times 100\%$$
*   **3 Key Assumptions:**
    1.  Công thức tính tỷ lệ sụt giảm tương đối ($FG$) phản ánh chính xác mức độ nghiêm trọng của sự quên lãng bất kể điểm nền ban đầu (giả định việc mất 10% từ mốc 80% tương đương với mất 10% từ mốc 40%).
    2.  Một chuỗi tác vụ cố định (Simp $\rightarrow$ Emdg $\rightarrow$ InqQG $\rightarrow$ Exp $\rightarrow$ HGen) đại diện đủ tốt cho quá trình học liên tục, giả định rằng việc thay đổi thứ tự các tác vụ này không làm thay đổi các kết luận chính về kiến trúc và quy mô.
    3.  Việc huấn luyện tinh chỉnh toàn bộ tham số (full fine-tuning) là mô hình chuẩn để khảo sát hành vi quên lãng tri thức của LLM, giả định các phương pháp PEFT cũng sẽ tuân theo các xu hướng tương tự.

---

## 4. Brutally Honest Review
*   **Summary:** Nghiên cứu này đánh giá thực nghiệm hiện tượng quên lãng thảm họa trên các LLM quy mô 1B-7B khi học tuần tự 5 tác vụ chỉ dẫn. Đánh giá trên các benchmark tri thức, lập luận, đọc hiểu và bias cho thấy sự suy giảm mạnh mẽ ở tất cả các khía cạnh. Nhóm tác giả tuyên bố các mô hình lớn hơn có xu hướng quên lãng nhiều hơn về mặt tỷ lệ tương đối, kiến trúc decoder-only bền vững hơn encoder-decoder, và việc trộn dữ liệu chỉ dẫn đa dạng giúp làm phẳng đường cong quên lãng.
*   **Strengths:**
    1.  **Thiết lập thực tế:** Việc mô phỏng quá trình tinh chỉnh tuần tự các tác vụ sinh ngôn ngữ phản ánh rất đúng cách mà cộng đồng công nghệ đang cập nhật và sử dụng các LLM mã nguồn mở hiện nay.
    2.  **Đánh giá sự tiến triển của Định kiến (Bias):** Việc đưa thước đo định kiến xã hội vào nghiên cứu học liên tục là rất mới mẻ, đặc biệt là phát hiện bất ngờ rằng việc quên lãng tri thức cũ đi kèm với việc làm giảm các định kiến độc hại sẵn có.
    3.  **Giải pháp thực tiễn dễ dùng:** Đưa ra bằng chứng thực nghiệm rõ ràng cho thấy việc trộn dữ liệu Alpaca (10k mẫu) giúp giữ vững tri thức nền tảng, cung cấp giải pháp ăn liền hiệu quả cho các kỹ sư hệ thống.
*   **Weaknesses:**
    1.  **Kết luận sai lệch về quy mô do công thức toán học (Severity: High):** Bài báo khẳng định mô hình 7B quên nhiều hơn mô hình 1B dựa hoàn toàn vào chỉ số sụt giảm tương đối $FG$. Trên thực tế, do mô hình 7B bắt đầu ở mức điểm ban đầu rất cao (ví dụ: 60%) và bị kéo tụt xuống mức đoán ngẫu nhiên (sàn 25% cho câu hỏi trắc nghiệm), tỷ lệ giảm tương đối của nó lớn hơn nhiều so với mô hình 1B (bắt đầu ở mức 30% và tụt xuống 25%). Tuy nhiên, ở checkpoint cuối cùng, mô hình 7B vẫn hoạt động tốt hơn hoặc bằng mô hình 1B về mặt điểm số tuyệt đối. Kết luận "mô hình lớn quên nhiều hơn" là một sự diễn giải sai lệch do phụ thuộc quá mức vào công thức $FG$.
    2.  **Thiếu vắng hoàn toàn baseline PEFT (Severity: High):** Tác giả chỉ thực hiện tinh chỉnh toàn bộ tham số (full-parameter fine-tuning). Trong kỷ nguyên LLM, việc tinh chỉnh toàn bộ tham số cho học liên tục là cực kỳ hiếm gặp và kém hiệu quả; thay vào đó, cộng đồng sử dụng LoRA hoặc Adapters. Việc bỏ qua PEFT khiến giá trị tham khảo của các thí nghiệm đối với thực tế triển khai bị giảm sút nghiêm trọng.
    3.  **Thứ tự tác vụ bị cố định cứng nhắc (Severity: Medium):** Hiện tượng quên lãng thảm họa chịu ảnh hưởng rất lớn từ thứ tự học của các tác vụ (interference/transfer). Chỉ sử dụng duy nhất một chuỗi cố định khiến các kết luận về tốc độ quên lãng của các danh mục tri thức (như RACE bị tụt sâu nhất) thiếu đi tính tổng quát hóa khoa học.
    4.  **Giới hạn quy mô mô hình (Severity: Medium):** Việc dừng lại ở quy mô 7.1B tham số hạn chế tầm vóc của một nghiên cứu về "Large" Language Models. Hành vi của các tham số ở quy mô 7B thường rất khác so với các mô hình thực sự lớn như 70B+, nơi khả năng kháng quên lãng có thể tự xuất hiện mạnh mẽ hơn.
    5.  **Giải pháp giảm thiểu thiếu tính sáng tạo (Severity: Low):** Phương pháp trộn dữ liệu chỉ dẫn tổng quát thực chất chỉ là một dạng kinh điển của Experience Replay (phát lại dữ liệu). Nó hữu ích nhưng không mang lại bất kỳ đóng góp mới nào về mặt thuật toán cho lĩnh vực Continual Learning.
*   **Questions for authors:**
    1.  Nếu đánh giá dựa trên điểm số tuyệt đối (absolute scores) thay vì tỷ lệ phần trăm sụt giảm tương đối ($FG$), liệu mô hình 7.1B có thực sự hoạt động kém hơn mô hình 1.1B sau khi hoàn thành chuỗi huấn luyện không?
    2.  Liệu xu hướng quên lãng và các kết luận về kiến trúc có giữ nguyên nếu chúng ta sử dụng tinh chỉnh tham số hiệu quả (PEFT) như LoRA thay vì full fine-tuning?
    3.  Nhóm tác giả có thử nghiệm đảo ngược hoặc xáo trộn thứ tự 5 tác vụ sinh ngôn ngữ để kiểm tra xem liệu tốc độ quên lãng của RACE hay MMLU có bị đảo lộn theo thứ tự tác vụ không?
*   **Recommendation:** **Borderline**
    *Giải thích:* Nghiên cứu cung cấp một tập hợp kết quả thực nghiệm phong phú và có giá trị tham khảo tốt cho việc tinh chỉnh LLM trong thực tế, đặc biệt là phần phân tích bias độc đáo. Tuy nhiên, việc thiếu các baseline PEFT (như LoRA) làm giảm tính thời sự của bài báo. Hơn nữa, việc diễn giải kết quả quy mô mô hình dựa trên một chỉ số tương đối dễ gây hiểu lầm là một điểm trừ lớn cần được tác giả giải trình và hiệu chỉnh.

---

## 5. Data & Metrics View Designer
*   **Datasets & Tasks:** Simp (Đơn giản hóa), Emdg (Hội thoại), InqQG (Tạo câu hỏi), Exp (Giải thích), HGen (Headline).
*   **Metrics:** SARI, BLEU, ROUGE-1, Accuracy (0-shot, 5-shot), Perplexity ($FG$ calculation).
*   **Baselines:** Initial Model (trạng thái pre-trained ban đầu).

### Propose Charts:
1.  **Line Chart (So sánh Điểm tuyệt đối vs. Tỷ lệ tương đối):**
    *   *Trục X:* Các giai đoạn học liên tục ($M_0$ đến $M_5$)
    *   *Trục Y:* Điểm Accuracy tuyệt đối trên MMLU
    *   *Series:* BLOOMZ-1.1B, BLOOMZ-7.1B
    *   *Mục đích:* Minh chứng trực quan rằng dù BLOOMZ-7.1B có tỷ lệ sụt giảm ($FG$) lớn hơn, nó vẫn giữ mức điểm số tuyệt đối cao hơn BLOOMZ-1.1B trong suốt quá trình.
2.  **Bar Chart (So sánh Kiến trúc Hệ thống):**
    *   *Trục X:* Các danh mục đánh giá (Domain Knowledge, Reasoning, Reading Comprehension)
    *   *Trục Y:* Điểm Forgetting Measure ($FG\%$, càng thấp càng tốt)
    *   *Series:* BLOOMZ-3B (Decoder-only) vs. mT0-3.7B (Encoder-Decoder)
    *   *Mục đích:* Chỉ ra ưu thế vượt trội của kiến trúc chỉ giải mã (decoder-only) trong việc chống quên lãng tri thức nền tảng.
3.  **Line Chart (Hiệu quả của việc Trộn dữ liệu):**
    *   *Trục X:* Tiến trình học tuần tự ($M_0$ đến $M_5$)
    *   *Trục Y:* Điểm MMLU-Human Accuracy
    *   *Series:* LLAMA-7B (Dữ liệu tác vụ thuần), LLAMA-7B (Trộn dữ liệu Alpaca)
    *   *Mục đích:* Làm nổi bật hiệu quả của việc chèn dữ liệu chỉ dẫn tổng quát giúp giữ vững đồ thị hiệu năng không bị dốc đứng.

### Summary Tables:
1.  **Table 1: Overall Forgetting Rates across Scales and Architectures**
    *   *Cột:* Model, Params, Architecture, Domain Knowledge (FG%), Reasoning (FG%), Reading Comp (FG%), Bias (FG%).
2.  **Table 2: Ablation of Instruction-Tuning History (Base vs. Tuned)**
    *   *Cột:* Model Family, Base Model (FG% across 3 categories), Instruction-Tuned Variant (FG% across 3 categories).

### Raw Data (Markdown Format):

```markdown
# Main Forgetting Metric (FG) Results (Table 4 in Paper)
| Model | DK Re_o | DK Re_-1 | DK FG | Reason Re_o | Reason Re_-1 | Reason FG | RC Re_o | RC Re_-1 | RC FG |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| mT0-1.2b | 26.82 | 22.47 | 9.18% | 45.43 | 40.22 | 7.75% | 35.06 | 29.54 | 17.45% |
| mT0-3.7b | 30.99 | 20.14 | 20.15% | 48.61 | 38.39 | 16.73% | 41.10 | 30.45 | 28.42% |
| BLOOMZ-1.1b | 27.19 | 23.84 | 9.54% | 47.37 | 41.97 | 6.73% | 36.77 | 27.28 | 18.04% |
| BLOOMZ-1.7b | 28.72 | 24.52 | 10.72% | 48.30 | 44.96 | 6.48% | 42.65 | 30.09 | 24.29% |
| BLOOMZ-3b | 30.04 | 24.29 | 14.63% | 56.17 | 47.03 | 11.09% | 48.29 | 31.38 | 27.56% |
| BLOOMZ-7.1b | 33.08 | 25.61 | 18.37% | 59.15 | 49.24 | 13.62% | 48.79 | 33.05 | 26.75% |

# Bias Mitigation Results (Table 5 in Paper)
| Model | Bias Rs_0 | Bias Rs_-1 | Bias FG |
| :--- | :--- | :--- | :--- |
| mT0-1.2b | 56.31 | 53.46 | 5.62% |
| mT0-3.7b | 57.16 | 50.59 | 13.10% |
| BLOOMZ-1.1b | 61.07 | 58.65 | 6.27% |
| BLOOMZ-1.7b | 65.18 | 56.48 | 7.78% |
| BLOOMZ-3b | 63.90 | 62.14 | 2.97% |
| BLOOMZ-7.1b | 65.82 | 60.61 | 7.15% |

# Effect of Base vs. Instruction Tuned (Table 6 in Paper)
| Model | DK Re_o | DK Re_-1 | DK FG | Reason Re_o | Reason Re_-1 | Reason FG | RC Re_o | RC Re_-1 | RC FG |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| BLOOM-7.1b | 29.42 | 24.83 | 13.54% | 52.79 | 47.76 | 6.67% | 38.25 | 31.55 | 12.00% |
| BLOOMZ-7.1b | 33.08 | 25.61 | 18.37% | 59.15 | 49.24 | 13.62% | 48.79 | 33.05 | 26.75% |
| LLAMA-7b | 37.27 | 24.05 | 34.57% | 58.73 | 40.38 | 31.33% | 41.36 | 27.62 | 31.72% |
| ALPACA-7b | 39.29 | 29.88 | 18.14% | 60.11 | 53.68 | 7.56% | 44.47 | 37.61 | 10.31% |
```

---

## 6. Figure & Table Highlights (For hightlight.md)
*   **Table 1:** Liệt kê các ví dụ cụ thể về cách định dạng prompt chỉ dẫn và đầu ra tương ứng cho các tác vụ học liên tục (Tạo tiêu đề, Giải thích, Tạo câu hỏi, Đơn giản hóa).
*   **Figure 1 (Sơ đồ Khung đánh giá):** Minh họa tiến trình học tuần tự 5 tác vụ của mô hình $M_0 \rightarrow M_5$ và việc mang các checkpoint này đi đánh giá độc lập trên 4 bộ kiểm tra năng lực tĩnh.
*   **Table 2:** Bảng phân loại chi tiết các benchmark đánh giá tri thức nền tảng (STEM, Nhân văn, Lập luận logic, Đọc hiểu RACE cấp hai/cấp ba, và các loại bias).
*   **Table 3:** So sánh hiệu năng trước và sau tinh chỉnh của các mô hình trên chính các tác vụ học mới (đo bằng SARI, BLEU, ROUGE-1) để xác minh việc học tác vụ mới thành công.
*   **Figure 3 (So sánh Quy mô BLOOMZ):** Các đường đồ thị biểu diễn sự suy giảm năng lực của BLOOMZ-1.1B và BLOOMZ-7.1B. Thể hiện độ dốc đi xuống lớn hơn của mô hình 7.1B.
*   **Figure 4 (So sánh Kiến trúc):** Đồ thị so sánh tốc độ tụt điểm của BLOOMZ-3B và mT0-3.7B, chỉ ra kiến trúc Encoder-Decoder (mT0) sụt giảm dốc hơn rất nhiều.
*   **Table 4 (Bảng kết quả trung tâm):** Cung cấp toàn bộ dữ liệu số của $Re_0$, $Re_{-1}$ và chỉ số quên lãng $FG\%$ cho các phiên bản BLOOMZ và mT0.
*   **Table 5 (Sự thay đổi của Định kiến):** Bảng số liệu đo lường mức độ giảm Perplexity thiên vị xã hội của các mô hình sau chuỗi học liên tục.
*   **Table 6 (Tác động của Căn chỉnh chỉ dẫn):** So sánh trực diện khả năng kháng quên của LLaMA vs. Alpaca và BLOOM vs. BLOOMZ.
*   **Figure 5 (LLaMA vs. Alpaca):** Biểu đồ đường vẽ hành trình tụt điểm của LLaMA và Alpaca. Đường của Alpaca phẳng hơn rất nhiều, chứng tỏ hiệu quả của tiền tinh chỉnh chỉ dẫn đa dạng.
*   **Figure 6 (Hiệu quả Trộn dữ liệu):** Biểu đồ đường chứng minh việc trộn dữ liệu Alpaca (đường nét liền) giúp giữ đồ thị hiệu năng ổn định cao hơn so với chỉ học tác vụ chuyên biệt (đường nét đứt).
