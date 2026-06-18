<img src="https://r2cdn.perplexity.ai/pplx-full-logo-primary-dark%402x.png" style="height:64px;margin-right:32px"/>

# sửa lại tí nữa model phải từ nhỏ slm luôn ví dụ mình có 0.6 1.7, 4b trước rồi mới thử nghiệm 7b và các <15b.

ví lại

Mình viết lại gọn nhưng đầy đủ 3 mục, đã chỉnh để thể hiện rõ pipeline từ **SLM nhỏ (0.6B, 1.7B, 4B)** lên **7B và <15B**.

***

## Phạm vi nghiên cứu

### 1. Phạm vi nội dung

Nghiên cứu tập trung vào **catastrophic forgetting** và **continual learning (CL)** trong bối cảnh **Large Language Models (LLMs)**, với trọng tâm là mô hình học tuần tự trên dữ liệu pháp lý. Cụ thể, đề tài xem xét ba tiến trình chính:

- **Continual pre-training (CPT):** cập nhật mô hình nền trên các corpus mới (luật/án mới, tài liệu pháp lý bổ sung).[^1][^2]
- **Continual instruction tuning (CIT):** tinh chỉnh tuần tự trên các luồng tác vụ có hướng dẫn (legal QA, tóm tắt bản án, phân loại vụ việc…).[^2][^1]
- **Continual alignment (CA):** điều chỉnh căn chỉnh dựa trên phản hồi hoặc đánh giá từ chuyên gia pháp lý (ví dụ RLHF/DPO, đánh giá an toàn trong tư vấn pháp luật).[^1][^2]

Về giải pháp, phạm vi tập trung vào các nhóm phương pháp CL hiện đại dành cho LLMs:

- **PEFT/LoRA‑based continual learning**: O‑LoRA, LoRA Learns Less, SLIM, N‑LoRA, I‑LoRA, v.v.[^1]
- **Replay và synthetic rehearsal**: SSR, SEEKR, LAMOL, Magpie – đặc biệt hữu ích khi dữ liệu pháp lý thật bị giới hạn bởi bảo mật.[^1]
- **Model merging và knowledge editing**: task arithmetic, TIES‑Merging, DARE, model soups, ROME, MEMIT, AlphaEdit…[^1]

Các phương pháp CL “cổ điển” (EWC, SI, GEM, DER, v.v.) được sử dụng chủ yếu như **cơ sở lý thuyết** và điểm tham chiếu, không là trọng tâm triển khai thực nghiệm.[^2]

### 2. Phạm vi mô hình và dữ liệu

- **Phạm vi mô hình:**
    - Đề tài giới hạn trong các **mô hình ngôn ngữ từ cỡ nhỏ đến trung bình**, cụ thể:
        - **Nhóm SLM nhỏ:** các mô hình khoảng **0.6B, 1.7B, 4B tham số**, dùng để thử nghiệm ban đầu, kiểm tra nhanh xu hướng catastrophic forgetting và tính khả thi của từng phương pháp CL trong điều kiện rất hạn chế tài nguyên.
        - **Nhóm LLM vừa:** các mô hình cỡ **7B đến dưới 15B tham số** (ví dụ họ LLaMA/Mistral/Qwen open‑source, và các mô hình luật như SaulLM‑7B).[^3][^1]
    - Chiến lược nghiên cứu: **bắt đầu từ SLM nhỏ (0.6B–4B)** để khảo sát hiện tượng và so sánh phương pháp, sau đó **mở rộng lên 7B và <15B** cho các thí nghiệm representative hơn đối với ứng dụng thực tế.
    - Các mô hình rất lớn (≥ 34B/70B tham số) không được huấn luyện hay đánh giá trực tiếp; các kết quả liên quan chỉ được tham khảo từ tài liệu công bố.[^1]
- **Phạm vi dữ liệu:**
    - **Dữ liệu pháp lý:** bộ luật, án lệ, hợp đồng, văn bản quy phạm pháp luật và/hoặc các benchmark như LegalBench, LawBench, LexGLUE, MultiLegalPile (tùy khả năng truy cập).[^4][^5][^6]
    - **Dữ liệu tổng quát:** một số benchmark ngoài pháp luật (MMLU, GSM8K, v.v.) dùng làm chuẩn tham chiếu để đánh giá **mức độ mất năng lực chung** khi mô hình được chuyên môn hóa vào luật.[^2]


### 3. Phạm vi thời gian và bối cảnh ứng dụng

- **Thời gian:** tập trung vào các công trình, phương pháp, mô hình giai đoạn **2020–2025**, tương ứng với thế hệ LLM hiện đại và các nghiên cứu CL cho LLMs.[^1]
- **Bối cảnh ứng dụng:**
    - Hướng tới các tác vụ **hỗ trợ công việc pháp lý**: legal QA, tóm tắt bản án, phân loại vụ việc, trích xuất điều khoản, hỗ trợ soạn thảo – rà soát hợp đồng, trợ lý nghiên cứu cho luật sư/thẩm phán/nhà làm luật.[^3][^4]
    - Các ứng dụng phi pháp lý chỉ dùng làm ví dụ hoặc nguồn ý tưởng, không nằm trong phạm vi đánh giá chính.[^1]

***

## Đối tượng nghiên cứu

### 1. Đối tượng lý thuyết

- **Hiện tượng catastrophic forgetting trong LLMs:**
    - Cách thức và mức độ mô hình (từ 0.6B đến <15B) đánh mất tri thức đã học khi được huấn luyện tuần tự trên các nhiệm vụ pháp lý mới.[^2][^1]
    - Các dạng forgetting trong LLM: mất điểm trên benchmark pháp lý, suy giảm năng lực suy luận chung, factual drift, và drift trong không gian biểu diễn.[^2]
- **Quy luật scaling liên quan đến forgetting:**
    - Mối quan hệ giữa kích thước mô hình (0.6B → 1.7B → 4B → 7B → <15B), số token CPT, tỷ lệ replay, độ lệch phân phối và mức độ quên lãng.[^1]


### 2. Đối tượng phương pháp/kỹ thuật

- **Continual pre‑training:** các chiến lược CPT và lịch trình học (Ibrahim 2024, Gupta 2023, TiC‑LM, D‑CPT Law, Reuse/Don’t Retrain, Lifelong‑MoE, LLaMA‑MoE…).[^1]
- **PEFT/LoRA‑based CL:** O‑LoRA, LoRA Learns Less, SLIM, N‑LoRA, I‑LoRA, MoLA, SAPT… áp dụng cho cả SLM nhỏ và LLM 7–15B để so sánh hiệu quả theo scale.[^2][^1]
- **Replay/synthetic rehearsal:** SSR, SEEKR, SERS, LAMOL, Magpie – đặc biệt quan trọng khi dữ liệu pháp lý thật khó lưu trữ hoặc chia sẻ.[^1]
- **Model merging/knowledge editing:** task arithmetic, TIES, DARE, model soups, ROME, MEMIT, AlphaEdit… phục vụ cập nhật/sửa tri thức pháp luật.[^1]


### 3. Đối tượng ứng dụng – hệ thống pháp lý

- **Các mô hình pháp lý và general nhỏ–vừa:**
    - SLM 0.6B–4B và LLM 7B–<15B được fine‑tune cho các tác vụ pháp lý, tiêu biểu như SaulLM‑7B và các model họ LLaMA/Mistral/Qwen tương ứng.[^3][^1]
- **Các benchmark/tập dữ liệu pháp lý:** LegalBench, LawBench, LexGLUE, MultiLegalPile (hoặc tương đương) dùng để đánh giá continual learning trong bối cảnh luật.[^5][^6][^4]

***

## Câu hỏi nghiên cứu (Research Questions)

**RQ1 – Đặc trưng catastrophic forgetting trên dải mô hình từ nhỏ đến vừa**

> **RQ1:** Catastrophic forgetting biểu hiện như thế nào trong các mô hình ngôn ngữ từ **0.6B, 1.7B, 4B** đến **7B và <15B tham số** khi được huấn luyện liên tục trên các tác vụ pháp lý khác nhau (legal QA, tóm tắt án, phân loại vụ việc, trích xuất điều khoản…)?
>
> Mục tiêu: mô tả định lượng và định tính các dạng forgetting trên dải kích thước mô hình, xem scale ảnh hưởng ra sao tới độ quên trong domain luật.[^2][^1]

**RQ2 – Ảnh hưởng của kích thước mô hình và cấu hình huấn luyện**

> **RQ2:** Những yếu tố nào – bao gồm **kích thước mô hình (0.6B → 1.7B → 4B → 7B → <15B)**, độ lệch phân phối dữ liệu, chiến lược learning rate và lịch trình CPT, tỷ lệ replay, cũng như lựa chọn nhóm phương pháp CL – ảnh hưởng mạnh nhất đến mức độ catastrophic forgetting trong LLM pháp lý?
>
> Câu hỏi này nhằm làm rõ mối quan hệ giữa cấu hình huấn luyện (scale + hyperparameters) và forgetting, dựa trên các kết quả và scaling laws gần đây.[^1]

**RQ3 – Lựa chọn phương pháp continual learning trong điều kiện compute hạn chế**

> **RQ3:** Trong bối cảnh domain law **và điều kiện tính toán hạn chế (bắt đầu từ SLM 0.6B–4B, sau đó mở rộng lên 7B–<15B trên 1–2 GPU)**, các nhóm phương pháp continual learning nào – gồm (i) CPT + replay, (ii) PEFT/LoRA‑based CL, (iii) instruction‑based CL như InsCL, (iv) synthetic rehearsal như SSR/SEEKR/Magpie, và (v) model merging/knowledge editing – mang lại trade‑off tốt nhất giữa:
> > (a) Giảm catastrophic forgetting;
> > (b) Cải thiện hiệu năng trên các tác vụ pháp lý;
> > (c) Bảo toàn hoặc ít suy giảm năng lực tổng quát của mô hình?
>
> Câu hỏi này hướng tới đề xuất “best practice” khả thi cho phòng lab cá nhân khi triển khai continual learning cho LLM pháp lý trên phần cứng hạn chế.[^3][^2][^1]

<div align="center">⁂</div>

[^1]: references.bib

[^2]: Catastrophic-Forgetting-in-LLMs_-A-Comprehensive-Survey-of-Methods-Scaling-Laws-and-Industrial-Best-Practices-2.md

[^3]: https://arxiv.org/abs/2403.03883

[^4]: https://arxiv.org/abs/2308.11462

[^5]: https://arxiv.org/abs/2110.00976

[^6]: https://arxiv.org/html/2306.02069v3

