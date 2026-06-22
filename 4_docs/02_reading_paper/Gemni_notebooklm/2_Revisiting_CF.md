# 2_Revisiting_CF.md: Revisiting Catastrophic Forgetting in Large Language Model Tuning

## 1. High-Level Summary
Nghiên cứu này điều tra nguyên nhân sâu xa của hiện tượng quên lãng thảm họa (catastrophic forgetting - CF) trong pha tinh chỉnh chỉ dẫn (instruction fine-tuning) của các mô hình ngôn ngữ lớn (LLMs). Các tác giả lần đầu tiên chứng minh bằng thực nghiệm mối tương quan trực tiếp giữa mức độ quên lãng và độ dốc/độ nhọn hình học của không gian hàm mất mát (loss landscape - LLS). Ý tưởng chính của nghiên cứu là chuyển bài toán kháng quên sang góc độ tối ưu hóa hình học, sử dụng thuật toán **Sharpness-Aware Minimization (SAM)** để chủ động làm phẳng không gian LLS trong quá trình huấn luyện. Trải qua thực nghiệm trên nhiều quy mô mô hình từ 1.1 tỷ đến 13 tỷ tham số, nghiên cứu chứng minh SAM không chỉ giảm thiểu quên lãng thảm họa một cách độc lập mà còn có tính tương thích trực giao (orthogonal complementarity), cho phép kết hợp hoàn hảo với các phương pháp cũ như Rehearsal (phát lại dữ liệu) hay Wise-FT (trung bình trọng số) để đạt hiệu năng tối ưu.

---

## 2. Section Summaries
*   **Introduction:** Tinh chỉnh chỉ dẫn là bước quan trọng để nâng cao khả năng kiểm soát của LLM, nhưng việc này thường gây ra quên lãng thảm họa các khả năng tổng quát gốc. Các kỹ thuật hiện tại như phát lại dữ liệu cũ hoặc trung bình hóa trọng số (Wise-FT) thường tốn kém chi phí thu thập dữ liệu, gặp rào cản do dữ liệu pre-training bị bảo mật, và có chi phí huấn luyện bất ổn định. Nhóm tác giả đề xuất giải pháp tiếp cận bài toán quên lãng từ lăng kính tối ưu hóa, chỉ ra rằng việc làm phẳng không gian loss landscape là chìa khóa kháng quên giá rẻ và ổn định.
*   **Methodology:** Phương pháp được xây dựng dựa trên phát hiện: huấn luyện trên các tập dữ liệu có khoảng cách phân phối (domain gap) lớn sẽ tạo ra không gian loss landscape sắc nhọn (sharp LLS) và hỗn loạn hơn, tương quan trực tiếp với mức độ quên tri thức nghiêm trọng. Để khắc phục, thuật toán **Sharpness-Aware Minimization (SAM)** được áp dụng nhằm tối ưu hóa mô hình theo hướng tìm kiếm các vùng trọng số có loss landscape phẳng. Thuật toán hoạt động qua 2 bước lan truyền: đầu tiên tìm kiếm vector nhiễu $\epsilon$ trong một vùng lân cận bán kính $\rho$ sao cho loss đạt cực đại (worst-case scenario), sau đó thực hiện cập nhật trọng số chính thức dựa trên gradient tại điểm nhiễu này để đảm bảo loss xung quanh không bị tăng vọt.
*   **Experiments:** Nghiên cứu thiết lập thí nghiệm tinh chỉnh tuần tự trên 3 quy mô mô hình: TinyLlama-1.1B, LLaMA-2-7B, và LLaMA-2-13B. Các mô hình xuất phát từ phiên bản đã học chỉ dẫn trên tập Alpaca, sau đó tiếp tục tinh chỉnh trên các tập dữ liệu ShareGPT52K, Open-Platypus, hoặc MetaMathQA. Năng lực tổng quát được đánh giá trên 4 nhóm tác vụ bao gồm Tri thức chuyên ngành (MMLU), Đọc hiểu (RACE, v.v.), Lập luận (BoolQ, Hellaswag, v.v.), và các kỳ thi (ARC-c), đối chứng trực tiếp giữa AdamW tiêu chuẩn, Rehearsal, Wise-FT và thuật toán đề xuất SAM.
*   **Discussion/Conclusion:** Kết quả chứng minh SAM giúp phục hồi đáng kể điểm số trên các benchmark tổng quát (lên tới +9.78% cho mô hình 13B), vượt trội hơn Wise-FT và Rehearsal khi hoạt động độc lập. Do SAM tác động trực tiếp vào cơ chế tối ưu hóa trọng số của optimizer, nó dễ dàng kết hợp trực giao với Wise-FT (+0.97% hiệu năng cộng dồn) và Rehearsal (+3.02% cộng dồn). Nhóm tác giả thừa nhận giới hạn của nghiên cứu là chỉ tập trung vào pha tinh chỉnh có giám sát (SFT) và chưa thử nghiệm trên pha học tăng cường phản hồi con người (RLHF).

---

## 3. Methodology
*   **Framework Description:** Hệ thống bắt đầu bằng một LLM nền tảng đã tinh chỉnh trên tập Alpaca ($M_{base}$), sau đó tiến hành học tuần tự trên các tập dữ liệu mới ($D_{new}$). Quá trình tối ưu hóa sử dụng SAM thay vì AdamW truyền thống. Sau khi học xong, checkpoint của mô hình được đem đi đánh giá trên một loạt các benchmark tri thức tĩnh để đo lường độ trôi tri thức.
*   **Key Technical/Architecture Ideas:**
    *   **Loss Landscape Flatness Connection:** Chứng minh bằng toán học mối liên hệ giữa độ phẳng loss landscape (đo bằng Surface Curvature - SC, Average Gradient - AG, và Mean Absolute Gradient - MAG) với năng lực kháng quên.
    *   **Sharpness-Aware Minimization (SAM):** Công thức tối ưu hóa:
        $$\min_w \max_{\|\epsilon\|_2 \le \rho} f(w + \epsilon)$$
        Giải quyết qua 2 bước gradient:
        1. Tính toán vector nhiễu: $\epsilon \approx \rho \frac{\nabla_w f(w)}{\|\nabla_w f(w)\|_2}$
        2. Cập nhật trọng số chính thức: $w \leftarrow w - \eta \nabla_w f(w + \epsilon)$
*   **Data & Setup:**
    *   *Dữ liệu tinh chỉnh:* Alpaca (khởi tạo baseline), ShareGPT52K (hội thoại tổng quát), MetaMathQA (toán học), Open-Platypus (lập luận chuyên sâu).
    *   *Setup phần cứng:* Chạy trên 16 card NVIDIA A800. Baseline AdamW chạy 2 epochs, mô hình dùng SAM chạy 1 epoch để đảm bảo công bằng về chi phí tính toán FLOPs (do SAM tốn gấp đôi số lần lan truyền tiến/lùi).
*   **Evaluation Metrics:**
    *   MMLU (Tri thức chuyên ngành); SuperGLUE, Hellaswag, BoolQ, SIQA (Lập luận); RACE, OpenbookQA, CSL (Đọc hiểu); ARC-c (Thi cử).
    *   Chỉ số đo độ phẳng LLS: Surface Curvature (SC - độ cong bề mặt), Average Gradient (AG - gradient trung bình), Mean Absolute Gradient (MAG - gradient trị tuyệt đối trung bình).
*   **3 Key Assumptions:**
    1.  Độ nhọn/dốc của không gian loss landscape cục bộ tại điểm tối ưu là nguyên nhân cốt lõi gây ra sự mất ổn định trọng số dẫn đến quên lãng thảm họa khi phân phối dữ liệu thay đổi.
    2.  Việc so sánh 1 epoch huấn luyện của SAM với 2 epochs của AdamW là tương đương về mặt chi phí tính toán thực tế và là một thiết lập đối chứng công bằng.
    3.  Độ phẳng loss landscape đo đạc trên 2 chiều tham số ngẫu nhiên phản ánh chính xác hành vi hình học của không gian hàng tỷ chiều của LLM.

---

## 4. Brutally Honest Review
*   **Summary:** Nghiên cứu này đề xuất giải pháp kháng quên lãng thảm họa cho LLM từ góc độ hình học tối ưu hóa. Bằng cách chứng minh mối tương quan chặt chẽ giữa độ nhọn loss landscape và sự sụt giảm MMLU, nhóm tác giả đưa thuật toán SAM vào tinh chỉnh LLM (1.1B đến 13B). Kết quả cho thấy SAM độc lập vượt qua các baseline Wise-FT hay Rehearsal và có khả năng kết hợp cộng dồn trực giao với các phương pháp này.
*   **Strengths:**
    1.  **Góc nhìn độc đáo và nguyên bản:** Nhìn nhận quên lãng thảm họa dưới lăng kính tối ưu hóa hình học loss landscape thay vì các phương pháp xử lý dữ liệu truyền thống là một đóng góp rất mới mẻ và có chiều sâu học thuật.
    2.  **Tính trực giao cao (Orthogonality):** Vì SAM hoạt động ở tầng optimizer, nó hoàn toàn độc lập và có thể kết hợp song song với bất kỳ phương pháp CL nào khác ở tầng dữ liệu (Rehearsal) hay mô hình (Wise-FT) để mang lại hiệu năng cộng dồn.
    3.  **Toán học hóa trực quan:** Việc sử dụng các chỉ số Surface Curvature (SC) và Mean Absolute Gradient (MAG) để định lượng độ phẳng và vẽ bản đồ loss landscape 3D mang lại tính thuyết phục cao cho giả thuyết ban đầu.
*   **Weaknesses:**
    1.  **Chi phí tính toán gấp đôi (Severity: Medium):** Thuật toán SAM yêu cầu 2 lượt lan truyền tiến và lùi (forward-backward passes) cho mỗi bước cập nhật trọng số. Dù tác giả cố gắng bào chữa bằng cách so sánh 1 epoch SAM với 2 epochs AdamW [18, 20], trong huấn luyện LLM quy mô lớn, việc tăng gấp đôi độ trễ bước (step latency) và lượng bộ nhớ kích hoạt (activation memory) là một trở ngại thực tế rất lớn.
    2.  **Bỏ qua baseline LoRA/PEFT (Severity: Medium):** Bài báo thực hiện thí nghiệm trên tinh chỉnh toàn bộ tham số (full fine-tuning). Trong thực tế tinh chỉnh LLM, các kỹ sư thường dùng LoRA. Việc thiếu đánh giá xem liệu SAM có tương thích tốt và giúp ích cho các adapter LoRA hay không làm giảm bớt tính ứng dụng thực tiễn của công trình.
    3.  **Giới hạn ở pha tinh chỉnh có giám sát (SFT) (Severity: Low):** Nghiên cứu chỉ đánh giá hiện tượng quên lãng trong pha SFT thông thường. Các pha huấn luyện nhạy cảm dễ quên hơn như DPO hay RLHF chưa được khảo sát, giới hạn phạm vi ứng dụng trong toàn bộ vòng đời phát triển LLM.
*   **Questions for authors:**
    1.  Làm thế nào SAM hoạt động khi kết hợp với tinh chỉnh tham số hiệu quả (PEFT) như LoRA? Độ phẳng loss landscape của các tham số adapter có tuân theo quy luật tương tự không?
    2.  Việc so sánh 1 epoch SAM vs 2 epochs AdamW có thực sự tương đương về mặt FLOPs và thời gian huấn luyện thực tế (wall-clock time) trên hệ thống 16 GPU A800 không?
    3.  Độ cong bề mặt (SC) của không gian loss landscape ở các mô hình lớn hơn 13B (như LLaMA-70B) có tự động phẳng hơn mô hình nhỏ không, và hiệu quả của SAM trên các mô hình siêu lớn này ra sao?
*   **Recommendation:** **Accept**
    *Giải thích:* Mặc dù SAM làm tăng chi phí tính toán của mỗi bước huấn luyện, ý tưởng liên kết độ phẳng hình học của loss landscape với khả năng kháng quên lãng thảm họa của LLM là một đóng góp học thuật xuất sắc. Thực nghiệm thiết kế tốt, chứng minh được tính trực giao bổ trợ cho các phương pháp rehearsal truyền thống và Wise-FT. Kết quả phục hồi điểm số ấn tượng (+9.78% cho mô hình 13B) đủ thuyết phục để chấp nhận bài báo.

---

## 5. Data & Metrics View Designer
*   **Datasets & Tasks:** Alpaca (khởi tạo), ShareGPT52K, MetaMathQA, Open-Platypus (SFT tasks).
*   **Metrics:** MMLU (Accuracy), SuperGLUE/Hellaswag/BoolQ (Accuracy), RACE (F1/Accuracy), ARC-c (Accuracy), Surface Curvature (SC), Average Gradient (AG), Mean Absolute Gradient (MAG).
*   **Baselines:** AdamW (standard SeqFT), Wise-FT, Rehearsal.

### Propose Charts:
1.  **3D Surface Plot / Contour Plot (Loss Landscape Visual):**
    *   *Trục X & Y:* Hướng dịch chuyển của 2 vector tham số ngẫu nhiên.
    *   *Trục Z:* Trị số Loss function.
    *   *Mục đích:* Tái hiện trực quan độ phẳng của vùng tối ưu khi dùng SAM (vùng đáy rộng và phẳng) so với AdamW (vòng contours nhọn và hẹp).
2.  **Scatter Plot (Flatness vs. Forgetting):**
    *   *Trục X:* Surface Curvature (SC%, càng thấp càng phẳng)
    *   *Trục Y:* Điểm MMLU trung bình sau huấn luyện
    *   *Series:* Alpaca (base), Open-Platypus (AdamW), Open-Platypus (SAM)
    *   *Mục đích:* Khẳng định mối tương quan tuyến tính nghịch đảo: không gian càng phẳng (SC thấp) thì tri thức giữ lại càng cao (MMLU cao).
3.  **Bar Chart (Hiệu năng cộng dồn trực giao):**
    *   *Trục X:* Phương pháp (SeqFT, Wise-FT, Rehearsal)
    *   *Trục Y:* Điểm Accuracy trung bình trên các tập đánh giá
    *   *Series:* w/o SAM (AdamW) vs. w/ SAM (SAM)
    *   *Mục đích:* Minh chứng rõ rệt lợi ích cộng dồn: SAM nâng cao hiệu năng của cả Wise-FT và Rehearsal khi kết hợp chung.

### Summary Tables:
1.  **Table 1: Main Evaluation Results on SAM vs Baselines**
    *   *Cột:* Model & Dataset, DK (MMLU), Understanding (RACE), Reasoning, Exams (ARC-c), AVG score, $\Delta$ improvement.
2.  **Table 2: Flatness Degree Metrics**
    *   *Cột:* Model state, Surface Curvature (SC), Average Gradient (AG), Mean Absolute Gradient (MAG), MMLU Accuracy.

### Raw Data (Markdown Format):

```markdown
# General Performance across Different Datasets (Table 2a in Paper)
| Model & Dataset | DK (MMLU) | Understanding | Reasoning | Exams | AVG | ∆ (vs w/o) |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| LLaMA-2-7B Alpaca (Base) | 40.53 | 58.74 | 63.33 | 45.08 | 51.92 | — |
| -> ShareGPT (w/o SAM) | 26.08 | 52.84 | 58.68 | 45.76 | 45.84 | -6.08 |
| -> ShareGPT (w/ SAM) | 40.08 | 57.91 | 63.78 | 44.41 | 51.55 | **+5.71** |
| -> Open-Platypus (w/o SAM) | 31.13 | 50.70 | 61.09 | 37.97 | 45.22 | -6.70 |
| -> Open-Platypus (w/ SAM) | 41.07 | 58.28 | 64.50 | 45.08 | 52.23 | **+7.01** |
| -> Meta-Math (w/o SAM) | 33.13 | 52.61 | 58.46 | 36.27 | 45.12 | -6.80 |
| -> Meta-Math (w/ SAM) | 34.79 | 55.77 | 62.38 | 42.71 | 48.91 | **+3.79** |

# Performance across Model Sizes (Table 2b in Paper)
| Model Size & Setting | DK (MMLU) | Understanding | Reasoning | Exams | AVG | ∆ (vs w/o) |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **TinyLlama-1.1B** | | | | | | |
| Alpaca Baseline | 23.16 | 30.62 | 46.16 | 26.78 | 31.68 | — |
| -> Open-Platypus (w/o SAM) | 23.04 | 31.06 | 46.23 | 27.12 | 31.86 | +0.18 |
| -> Open-Platypus (w/ SAM) | 23.14 | 30.39 | 46.91 | 26.10 | 31.64 | -0.22 |
| **LLaMA-2-7B** | | | | | | |
| -> Open-Platypus (w/o SAM) | 31.13 | 50.70 | 61.09 | 37.97 | 45.22 | -6.70 |
| -> Open-Platypus (w/ SAM) | 41.07 | 58.28 | 64.50 | 45.08 | 52.23 | **+7.01** |
| **LLaMA-2-13B** | | | | | | |
| Alpaca Baseline | 48.15 | 69.90 | 64.37 | 63.73 | 61.54 | — |
| -> Open-Platypus (w/o SAM) | 28.23 | 61.92 | 64.12 | 54.58 | 52.21 | -9.33 |
| -> Open-Platypus (w/ SAM) | 49.38 | 69.80 | 65.72 | 63.05 | 61.99 | **+9.78** |

# Comparison and Combinations with Other Methods (Table 2c in Paper)
| Method | DK (MMLU) | Understanding | Reasoning | Exams | AVG | ∆ (vs w/o) |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| Open-Platypus (w/o SAM) | 31.13 | 50.70 | 61.09 | 37.97 | 45.22 | -6.70 |
| Open-Platypus (w/ SAM) | 41.07 | 58.28 | 64.50 | 45.08 | 52.23 | +7.01 |
| Wise-FT (w/o SAM) | 37.75 | 56.64 | 62.65 | 47.12 | 51.04 | -0.88 |
| Wise-FT (w/ SAM) | 40.59 | 58.14 | 64.56 | 44.75 | 52.01 | **+0.97** |
| Rehearsal (w/o SAM) | 33.38 | 54.69 | 61.25 | 43.05 | 48.09 | -3.83 |
| Rehearsal (w/ SAM) | 40.35 | 57.09 | 63.27 | 43.73 | 51.11 | **+3.02** |
```

---

## 6. Figure & Table Highlights (For hightlight.md)
*   **Figure 1 (Bản đồ Loss Landscape 3D):** Trực quan hóa hình học không gian loss landscape với các đường contour cho 3 mô hình (Alpaca, Open-Platypus, Auto-Wiki). Cho thấy rõ các mô hình tinh chỉnh trên miền dữ liệu lệch pha nhiều sẽ có địa hình dốc, gồ ghề và nhọn hơn.
*   **Table 1 (Flatness vs Performance):** Bảng số liệu liên kết định lượng các chỉ số hình học (Surface Curvature - SC, Average Gradient - AG, Mean Absolute Gradient - MAG) với điểm số MMLU. Chỉ ra không gian càng nhọn thì điểm số càng sụt giảm sâu.
*   **Table 2 (Bảng kết quả trung tâm):** Chia làm 3 phần: (a) so sánh SAM vs AdamW trên 3 bộ dữ liệu; (b) so sánh SAM vs AdamW trên 3 quy mô tham số; (c) so sánh và kết hợp SAM với Wise-FT và Rehearsal.
*   **Table 3 (So sánh Epochs):** Bảng phụ lục đối chứng điểm số của SAM ở epoch=1 so với AdamW ở epoch=1 và epoch=2, chứng minh SAM chỉ cần 1 epoch đã vượt trội hơn AdamW chạy 2 epochs.
*   **Table 4 (Bảo tồn năng lực chuyên biệt):** Kiểm định trên TruthfulQA và Hellaswag chỉ ra rằng việc làm phẳng loss landscape của SAM không làm giảm năng lực học của mô hình trên chính tập dữ liệu tinh chỉnh chuyên biệt.
*   **Tables 5-8 (Bảng kết quả phân rã chi tiết):** Cung cấp toàn bộ dữ liệu số chi tiết của tất cả các bài test nhỏ cấu thành nên các nhóm MMLU, SuperGLUE, RACE, BoolQ, v.v. cho mọi thí nghiệm trong bài báo.
