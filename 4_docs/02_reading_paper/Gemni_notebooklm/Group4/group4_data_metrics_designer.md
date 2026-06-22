# Tổng hợp "Data & Metrics View Designer" cho Group 4

Tài liệu này tổng hợp toàn bộ phần thiết kế trực quan hóa dữ liệu, thước đo đánh giá (metrics), baselines và các bảng số liệu thô (raw data) của 8 bài báo thuộc **Group 4 (PEFT / LoRA & Prompt-based Continual Learning)**. 

---

## 1. Paper 1: Orthogonal Subspace Learning for LM Continual Learning (O-LoRA)

### 1. Tóm tắt thiết lập thực nghiệm
*   **Datasets & Tasks:** Standard CL Benchmark (AG News, Amazon reviews, Yelp reviews, DBpedia, Yahoo Answers) cho phân loại văn bản và phân tích cảm xúc. Long CL Benchmark (15 tác vụ gồm GLUE, SuperGLUE, IMDB). Đánh giá zero-shot trên MMLU.
*   **Baselines:** EWC, A-GEM, MBPA++, IDBR, L2P, LwF, OGD, LFPT5, EIP, ProgPrompt, Vanilla LoRA, MoELoRA, MTL (Multi-Task Learning).
*   **Metrics:** Average Accuracy (AA) sau khi hoàn tất chuỗi tác vụ.

### 2. Đề xuất trực quan hóa (Charts)
1.  **Bar Chart (So sánh hiệu năng CL):** 
    *   *Trục X:* Các phương pháp CL (O-LoRA, ProgPrompt, LFPT5, Vanilla LoRA, EWC)
    *   *Trục Y:* Average Accuracy (%)
    *   *Nhóm (Series):* Standard CL Benchmark vs Long CL Benchmark
    *   *Mục đích:* So sánh trực quan khả năng chống quên của O-LoRA trên chuỗi ngắn vs chuỗi dài.
2.  **Line Chart (Tác động của kích thước mô hình):** 
    *   *Trục X:* Kích thước mô hình (T5-Base, T5-Large, T5-XL)
    *   *Trục Y:* Average Accuracy (%)
    *   *Nhóm (Series):* O-LoRA vs Multitask Learning
    *   *Mục đích:* Đo lường mức độ ảnh hưởng của quy mô mô hình đến khả năng chống quên.
3.  **Heatmap (Biến động layer):** 
    *   *Trục X:* Tác vụ trong chuỗi (Task Sequence)
    *   *Trục Y:* Tầng Transformer (Layer 1 đến 12)
    *   *Mục đích:* Thể hiện sự biến động biểu diễn ở các tầng khác nhau (tương tự Hình 4 của paper).

### 3. Đề xuất cấu trúc bảng
*   **Bảng tổng hợp:** `[Method | Standard CL Avg Acc (5 tasks) | Long CL Avg Acc (15 tasks) | MMLU Zero-shot Acc | Rehearsal-Free? (Y/N)]`
*   *Highlight:* Bold cho kết quả tốt nhất, thêm cột $\Delta$ chênh lệch so với baseline không replay mạnh nhất (LFPT5).

### 4. Số liệu thô (Raw Data)
```markdown
| Method | Standard CL Avg Acc (5 tasks) | Long CL Avg Acc (15 tasks) |
| :--- | :---: | :---: |
| Fine-Tuning (FT) | 28.5 | 7.4 |
| Vanilla LoRA | 43.7 | 1.6 |
| MoELoRA | 54.1 | 27.6 |
| LFPT5 | 72.7 | 69.2 |
| ProgPrompt | 75.1 | 77.9 |
| **O-LoRA (Ours)** | **76.6** | **69.5** |
| Multi-Task Learning (Upper Bound) | 80.0 | 76.5 |
```

---

## 2. Paper 2: SLIM: Let LLM Learn More and Forget Less with Soft LoRA and Identity Mixture

### 1. Tóm tắt thiết lập thực nghiệm
*   **Datasets & Tasks:** Downstream (CSQA, HellaSwag, Winogrande, ARC-c, ARC-e, OBQA, SIQA, BoolQ) cho Commonsense & Reading Comprehension. Generalization (MMLU, GSM8K, PIQA) để đo độ quên.
*   **Baselines:** LoRA, DoRA, LoRAMoE, MixLoRA, MixLoRA-Dy, MoLA, SLIM.
*   **Metrics:** Accuracy (%) trên từng tập dữ liệu.

### 2. Đề xuất trực quan hóa (Charts)
1.  **Grouped Bar Chart (Độ quên toán/logic):** 
    *   *Trục X:* Các tác vụ tổng quát (MMLU, GSM8K, PIQA)
    *   *Trục Y:* Accuracy (%)
    *   *Nhóm (Series):* LoRA, LoRAMoE, MixLoRA, SLIM
    *   *Mục đích:* Nổi bật việc LoRA/DoRA bị sụp đổ khả năng giải toán (GSM8K về 0%) so với SLIM (76.11%).
2.  **Line Chart (Độ ổn định khi train):** 
    *   *Trục X:* Số lượng steps huấn luyện (2k, 4k, 6k, 8k, 12k)
    *   *Trục Y:* MMLU Accuracy (%)
    *   *Nhóm (Series):* LoRA, MixLoRA, SLIM
    *   *Mục đích:* Chứng minh tính ổn định của SLIM qua các bước tinh chỉnh.
3.  **Scatter Plot (Downstream vs Generalization):** 
    *   *Trục X:* Downstream AVG (SDS & MDS)
    *   *Trục Y:* Generalization AVG
    *   *Mục đích:* Tìm kiếm điểm tối ưu Pareto (nằm ở góc trên bên phải).

### 3. Đề xuất cấu trúc bảng
*   **Bảng tổng hợp:** `[Model | Downstream AVG | Generalization AVG | MMLU | GSM8K | PIQA]`
*   *Highlight:* Bold cho best score trong nhóm PEFT, in nghiêng cho base model.

### 4. Số liệu thô (Raw Data)
```markdown
| Model | Downstream AVG (SDS & MDS) | Generalization AVG | MMLU | GSM8K | PIQA |
| :--- | :---: | :---: | :---: | :---: | :---: |
| LoRA | 77.39 | 31.24 | 32.73 | 0.00 | 60.99 |
| DoRA | 77.32 | 29.41 | 31.07 | 0.00 | 57.18 |
| LoRAMoE | 85.12 | 66.39 | 60.20 | 59.43 | 79.54 |
| MixLoRA | 83.06 | 51.08 | 55.41 | 20.47 | 77.36 |
| MixLoRA-Dy | 83.16 | 51.98 | 56.14 | 21.38 | 78.43 |
| MoLA | 83.11 | 47.79 | 53.15 | 15.54 | 74.70 |
| **SLIM (Ours)** | **86.63** | **75.65** | **65.83** | **76.11** | **84.65** |
```

---

## 3. Paper 3: LoRA: Low-Rank Adaptation of Large Language Models

### 1. Tóm tắt thiết lập thực nghiệm
*   **Datasets & Tasks:** GLUE (NLU), WikiSQL (NL2SQL), SAMSum (Summarization), DART, WebNLG, E2E (NLG).
*   **Baselines:** Full Fine-Tuning (FT), BitFit, PrefixEmbed, PrefixLayer, AdapterH, AdapterL, LoRA.
*   **Metrics:** Accuracy (GLUE, WikiSQL), ROUGE (SAMSum), BLEU/NIST/METEOR/CIDEr (NLG).

### 2. Đề xuất trực quan hóa (Charts)
1.  **Line Chart (Độ co giãn tham số):** 
    *   *Trục X:* $\log_{10}$ của số lượng tham số huấn luyện (Trainable Parameters)
    *   *Trục Y:* Validation Accuracy (WikiSQL hoặc MNLI)
    *   *Nhóm (Series):* LoRA, Prefix-tuning, Adapter, Full FT
    *   *Mục đích:* Tái hiện khả năng scale ổn định của LoRA so với prefix-tuning khi tăng số token ảo.
2.  **Bar Chart (Hiệu quả bộ nhớ):** 
    *   *Trục X:* Thiết lập huấn luyện (Full FT vs LoRA trên GPT-3 175B)
    *   *Trục Y:* VRAM yêu cầu (GB) hoặc Dung lượng checkpoint (GB)
    *   *Mục đích:* Minh họa trực quan việc tiết kiệm 3x dung lượng VRAM và 10,000x tham số lưu trữ.
3.  **Line Chart (Tối ưu hóa hạng $r$):** 
    *   *Trục X:* Rank $r$ (1, 2, 4, 8, 64)
    *   *Trục Y:* Accuracy (%)
    *   *Nhóm (Series):* Chỉnh $W_q$ vs Chỉnh $W_q$ và $W_v$
    *   *Mục đích:* Thể hiện năng lực hội tụ của rank thấp và tầm quan trọng của việc chọn module đích.

### 3. Đề xuất cấu trúc bảng
*   **Bảng tổng hợp:** `[Model | Method | Trainable Params | WikiSQL Acc | MNLI-m Acc | ROUGE-L]`
*   *Highlight:* Bold cho kết quả tốt nhất ở mỗi cột.

### 4. Số liệu thô (Raw Data)
```markdown
| Model & Method | Trainable Parameters | WikiSQL Acc. (%) | MNLI-m Acc. (%) | SAMSum (R1/R2/RL) |
| :--- | :---: | :---: | :---: | :---: |
| GPT-3 (FT) | 175,255.8M | 73.8 | 89.5 | 52.0 / 28.0 / 44.5 |
| GPT-3 (BitFit) | 14.2M | 71.3 | 91.0 | 51.3 / 27.4 / 43.5 |
| GPT-3 (PreEmbed) | 3.2M | 63.1 | 88.6 | 48.3 / 24.2 / 40.5 |
| GPT-3 (PreLayer) | 20.2M | 70.1 | 89.5 | 50.8 / 27.3 / 43.5 |
| GPT-3 (AdapterH) | 7.1M | 71.9 | 89.8 | 53.0 / 28.9 / 44.8 |
| GPT-3 (AdapterH) | 40.1M | 73.2 | 91.5 | 53.2 / 29.0 / 45.1 |
| **GPT-3 (LoRA)** | **4.7M** | **73.4** | **91.7** | **53.8 / 29.8 / 45.9** |
| **GPT-3 (LoRA)** | **37.7M** | **74.0** | **91.6** | **53.4 / 29.2 / 45.1** |
```

---

## 4. Paper 4: Progressive Prompts: Continual Learning for Language Models

### 1. Tóm tắt thiết lập thực nghiệm
*   **Datasets & Tasks:** 15 tác vụ phân loại văn bản (AG News, Amazon, Yelp, DBpedia, Yahoo, MNLI, QQP, RTE, SST2, WiC, CB, COPA, MultiRC, BoolQ, IMDB).
*   **Baselines:** Finetune, Replay, Prompt Tuning, Per-task Prompts, IDBR, LFPT5, ProgPrompt, MTL.
*   **Metrics:** Average Accuracy, Forward Transfer (FWT), Backward Transfer (BWT).

### 2. Đề xuất trực quan hóa (Charts)
1.  **Line Chart (Tải dữ liệu vs Hiệu năng):** 
    *   *Trục X:* Số lượng dữ liệu huấn luyện mỗi lớp (2, 5, 20, All)
    *   *Trục Y:* Average Score (%)
    *   *Nhóm (Series):* Prompt Tuning vs Progressive Prompts
    *   *Mục đích:* Chứng minh khả năng học chuyển giao vượt trội của Progressive Prompts trong điều kiện ít dữ liệu (few-shot).
2.  **Heatmap (Attention giữa các task):** 
    *   *Trục X:* Các tác vụ cũ trong chuỗi (Task 1 đến 14)
    *   *Trục Y:* Tác vụ hiện tại đang suy luận (Task 2 đến 15)
    *   *Mục đích:* Biểu diễn trực quan việc mô hình tự động liên kết các task có tính tương đồng ngữ nghĩa.
3.  **Grouped Bar Chart (BERT vs T5):** 
    *   *Trục X:* Số lượng samples/class (20, 200, 1000)
    *   *Trục Y:* Average Accuracy (%)
    *   *Nhóm (Series):* Replay, LFPT5, ProgPrompt, MTL
    *   *Mục đích:* Thể hiện chênh lệch hiệu năng giữa Progressive Prompts và các baseline trên cả 2 kiến trúc T5 và BERT.

### 3. Đề xuất cấu trúc bảng
*   **Bảng tổng hợp:** `[Method | 20 Samples/Class (Avg Acc) | 200 Samples/Class (Avg Acc) | 1000 Samples/Class (Avg Acc)]`
*   *Highlight:* Bold cho best score trong nhóm CL, in nghiêng cho MTL (Upper Bound).

### 4. Số liệu thô (Raw Data - T5-Large trên Long-Sequence Order 8)
```markdown
| Method | 20 Samples/Class | 200 Samples/Class | 1000 Samples/Class |
| :--- | :---: | :---: | :---: |
| Finetune | 9.3 | 8.9 | 7.4 |
| Replay | 46.0 | 45.0 | 55.2 |
| Prompt Tuning | 9.7 | 8.4 | 8.2 |
| Per-task Prompts | 69.9 | 75.2 | 77.0 |
| LFPT5 | 54.7 | 61.6 | 70.4 |
| **ProgPrompt (Ours)** | **75.4** | **79.1** | **79.5** |
| MTL (Upper Bound) | 70.7 | 72.5 | 76.3 |
```

---

## 5. Paper 5: Higher Layers Need More LoRA Experts (MoLA)

### 1. Tóm tắt thiết lập thực nghiệm
*   **Datasets & Tasks:** Tinh chỉnh chỉ thị (OpenOrca), NLU (MRPC, COLA, RTE), Commonsense QA (ScienceQA, CSQA, OpenbookQA). Continual domain adaptation (5 lĩnh vực khoa học).
*   **Baselines:** Full-Parameter FT, Prompt Tuning, LLaMA-Adapter, LoRA, MoLA variants (Rectangle, Triangle, Hourglass, Inverted-Triangle).
*   **Metrics:** Accuracy (%), Overall Performance (OP), Performance Drop (PD).

### 2. Đề xuất trực quan hóa (Charts)
1.  **Line Chart (Chuẩn Frobenius chứng minh dư thừa):** 
    *   *Trục X:* Transformer Layer (1 đến 32)
    *   *Trục Y:* Frobenius Norm trung bình giữa các expert
    *   *Nhóm (Series):* MoLA-▽ (Inverted-Triangle), MoLA-△ (Triangle)
    *   *Mục đích:* Tái hiện trực quan việc các expert ở tầng cao có sự khác biệt lớn (norm cao), chứng minh tầng dưới dư thừa hơn tầng trên.
2.  **Grouped Bar Chart (So sánh cấu hình):** 
    *   *Trục X:* Các tập dữ liệu đánh giá (MRPC, COLA, RTE, ScienceQA, CSQA, OBQA)
    *   *Trục Y:* Accuracy (%)
    *   *Nhóm (Series):* LoRA, MoLA-□ (Rectangle uniform), MoLA-▽ (Inverted-Triangle)
    *   *Mục đích:* Nổi bật việc MoLA-▽ vượt trội hơn LoRA và Rectangle uniform dù dùng ít tham số hơn.
3.  **Scatter Plot (Hiệu quả tham số):** 
    *   *Trục X:* Số lượng tham số huấn luyện (Trainable Parameters)
    *   *Trục Y:* Average Accuracy (%)
    *   *Mục đích:* Minh họa hiệu quả sử dụng tham số của cấu hình Tam giác ngược (nằm ở góc trên bên trái tối ưu nhất).

### 3. Đề xuất cấu trúc bảng
*   **Bảng tổng hợp:** `[Model / Configuration | Trainable Params Ratio | Continual OP | Continual PD | Downstream Tasks (MRPC, COLA, RTE, ScienceQA, CSQA, OBQA)]`
*   *Highlight:* Bold cho best score ở mỗi cột.

### 4. Số liệu thô (Raw Data)
```markdown
| Model / Configuration | Trainable Params Ratio | Continual OP | Continual PD | MRPC | COLA | RTE | ScienceQA | CSQA | OBQA |
| :--- | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Full-Parameter FT | 100% | - | - | 87.13% | 86.29% | 87.73% | 93.12% | 77.48% | 80.4% |
| LoRA (Rank 64) | 1.5% | 78.67% | -2.17% | 84.41% | 84.95% | 84.48% | 91.01% | 74.61% | 76.6% |
| MoLA-□ (Uniform 8888) | 2.4% | - | - | 84.23% | 85.72% | 87.36% | 92.13% | 77.15% | 78.4% |
| MoLA-□ (Uniform 5555) | 1.5% | 88.80% | -0.60% | 84.93% | 84.56% | 88.81% | 91.73% | 75.92% | 77.6% |
| MoLA-△ (Triangle 8642) | 1.5% | 88.84% | -2.10% | 84.46% | 85.23% | 89.17% | 91.41% | 76.33% | 78.8% |
| MoLA-▷◁ (Hourglass 8228) | 1.5% | 83.82% | -3.92% | 84.35% | 84.85% | 87.72% | 91.41% | 75.02% | 77.4% |
| **MoLA-▽ (Inverted 2468)** | **1.5%** | **89.82%** | **-0.47%** | **85.45%** | **86.19%** | **89.17%** | **92.36%** | **77.15%** | **78.4%** |
```

---

## 6. Paper 6: Analyzing and Reducing Catastrophic Forgetting in Parameter Efficient Tuning (I-LoRA)

### 1. Tóm tắt thiết lập thực nghiệm
*   **Datasets & Tasks:** 8 tập chuyên biệt (C-STANCE, FOMC, MeetingBank, ScienceQA, NumGLUE-cm, 20Minuten, MedMCQA, JEC-QA) cho các tác vụ lý luận và ngôn ngữ khác nhau.
*   **Baselines:** Zero-shot inference (ZSI), Sequence Fine-tuning (Seq-Train), Experience Replay (ER), EWC, GEM, A-GEM, MBPA++, L2P, LFPT5, O-LoRA.
*   **Metrics:** Average Accuracy ($Acc_T$), Backward Transfer ($BWT_T$), previous/current/all task accuracies.

### 2. Đề xuất trực quan hóa (Charts)
1.  **Line Chart (Độ cong Mode Connectivity):** 
    *   *Trục X:* Hệ số nội suy $\lambda$ (chạy từ 0.0 đến 1.0)
    *   *Trục Y:* Accuracy (%)
    *   *Nhóm (Series):* Tác vụ cũ ($A_p$), Tác vụ mới ($A_n$), Trung bình ($A_{all}$)
    *   *Mục đích:* Tái hiện Hình 1 của paper để chứng minh các điểm nằm ở giữa đạt hiệu suất tốt hơn hai đầu mút $\theta_t$ và $\theta_{t+1}$.
2.  **Bar Chart (So sánh điểm quên):** 
    *   *Trục X:* Các phương pháp CL (Seq-Train, EWC, ER, O-LoRA, I-LoRA)
    *   *Trục Y:* Forgetting score (%) trên NumGLUE-cm hoặc 20Minuten
    *   *Mục đích:* Nổi bật khả năng kiểm soát quên thảm khốc của I-LoRA.
3.  **Line Chart (Độ ổn định theo mốc thời gian):** 
    *   *Trục X:* Các mốc thời gian học (Sau tác vụ 1, Sau tác vụ 2, ..., Sau tác vụ 8)
    *   *Trục Y:* Accuracy (%) trên Tác vụ 1
    *   *Nhóm (Series):* Seq-Train vs ER vs I-LoRA
    *   *Mục đích:* Xem tốc độ suy giảm hiệu năng của tác vụ đầu tiên qua các bước học liên tục.

### 3. Đề xuất cấu trúc bảng
*   **Bảng tổng hợp:** `[Method | C-STANCE | FOMC | MeetingBank | ScienceQA | NumGLUE-cm | 20Minuten | MedMCQA | JEC-QA]`
*   *Highlight:* Bold cho best score.

### 4. Số liệu thô (Raw Data)
```markdown
| Task / Dataset | C-STANCE | FOMC | MeetingBank | ScienceQA | NumGLUE-cm | 20Minuten | MedMCQA | JEC-QA |
| :--- | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| **Single Task FT (Plasticity Upper)** | 0.388 | 0.342 | 0.164 | 0.324 | 0.308 | 0.014 | 0.328 | 0.324 |
| **Linear Comb. (Fast/Slow)** | 0.276 | 0.488 | 0.032 | 0.252 | 0.004 | 0.014 | 0.236 | 0.272 |
| **Seq-Train (After T8)** | 21.0 | 29.2 | 12.3 | 23.8 | 17.3 | 38.9 | 26.2 | 26.8 |
| **ER (After T8)** | 16.4 | 49.0 | 7.9 | 26.2 | 27.2 | - | - | - |
```

---

## 7. Paper 7: LoRA Learns Less and Forgets Less

### 1. Tóm tắt thiết lập thực nghiệm
*   **Datasets & Tasks:** StarCoder-Python, OpenWebMath (CPT), Magicoder-Evol-Instruct, MetaMathQA, Tülu-v2-mix (IFT). Các tác vụ lập trình, toán học và hội thoại.
*   **Baselines:** Full FT, Attention Dropout, Weight Decay.
*   **Metrics:** HumanEval pass@1, GSM8K strict match, MT-Bench, Average of HS/WG/ARC (Forgetting avg).

### 2. Đề xuất trực quan hóa (Charts)
1.  **Line Chart (Độ cong học miền đích):** 
    *   *Trục X:* Số lượng token huấn luyện (từ 0.25B đến 20B tokens) hoặc epochs (1-16)
    *   *Trục Y:* Target Accuracy (HumanEval / GSM8K)
    *   *Nhóm (Series):* Full FT, LoRA các ranks
    *   *Mục đích:* Tái hiện việc LoRA bị hụt hơi trong kịch bản CPT dài hạn bất kể tăng rank.
2.  **Scatter Plot (Biên Pareto học vs quên):** 
    *   *Trục X:* Forgetting Score (Average HellaSwag, ARC, WinoGrande - Càng cao càng tốt)
    *   *Trục Y:* Target Accuracy (HumanEval / GSM8K)
    *   *Nhóm (Series):* Full FT, LoRA các ranks
    *   *Mục đích:* Tái hiện Hình 3 để thể hiện rõ ràng: LoRA nằm ở vùng "Học ít hơn nhưng quên ít hơn".
3.  **Histogram (Độ đa dạng sinh từ):** 
    *   *Trục X:* Số lượng đáp án duy nhất sinh ra trong 50 lần thử
    *   *Trục Y:* Tần suất xuất hiện (Count)
    *   *Nhóm (Series):* Base LLaMA-2, Full FT, LoRA
    *   *Mục đích:* Minh họa hiện tượng sụp đổ độ đa dạng (collapse of diversity) của Full FT so với LoRA.

### 3. Đề xuất cấu trúc bảng
*   **Bảng tổng hợp:** `[Regime & Domain | Method Configuration | Training Length | Target Domain Acc | Source Domain Acc (No Forgetting)]`
*   *Highlight:* Bold cho kết quả tốt nhất.

### 4. Số liệu thô (Raw Data)
```markdown
| Regime & Domain | Method Configuration | Tokens / Epochs | Target Domain Acc | Source Domain Acc (No Forgetting) |
| :--- | :--- | :--- | :---: | :---: |
| **Code CPT** | LoRA (r=16) | 20 Billion Tokens | 16.2% | 63.5% |
| **Code CPT** | LoRA (r=64) | 20 Billion Tokens | 19.6% | 62.6% |
| **Code CPT** | LoRA (r=256) | 20 Billion Tokens | 22.4% | 61.7% |
| **Code CPT** | Full Finetuning | 20 Billion Tokens | **26.3%** | 54.5% |
| **Code IFT** | LoRA (r=16) | 16 Epochs | 32.4% | 60.9% |
| **Code IFT** | LoRA (r=64) | 16 Epochs | 40.5% | 51.0% |
| **Code IFT** | LoRA (r=256) | 16 Epochs | **46.6%** | 51.7% |
| **Code IFT** | Full Finetuning | 16 Epochs | 41.6% | 41.4% |
| **Math IFT** | LoRA (r=16) | 16 Epochs | 56.9% | 59.6% |
| **Math IFT** | LoRA (r=256) | 16 Epochs | 59.4% | 56.7% |
| **Math IFT** | Full Finetuning | 16 Epochs | **59.9%** | 55.9% |
```

---

## 8. Paper 8: Is Parameter Collision Hindering Continual Learning in LLMs (N-LoRA)

### 1. Tóm tắt thiết lập thực nghiệm
*   **Datasets & Tasks:** Standard CL Benchmark (5 tác vụ), Large CL Benchmark (15 tác vụ). Các tác vụ phân loại, NLI, cảm xúc.
*   **Baselines:** ProgPrompt, PerTaskFT, Replay, EWC, LwF, O-LoRA.
*   **Metrics:** Average Accuracy, Average Collision Rate (ACR), Orthogonality, Nuclear Norms.

### 2. Đề xuất trực quan hóa (Charts)
1.  **Line Chart (Collision Rate tích lũy):** 
    *   *Trục X:* Số lượng tác vụ đã học tuần tự (1 đến 15)
    *   *Trục Y:* Average Collision Rate (ACR)
    *   *Nhóm (Series):* O-LoRA, N-LoRA
    *   *Mục đích:* Chứng minh trực quan khả năng kiểm soát đụng độ tham số bền bỉ của N-LoRA khi kéo dài chuỗi tác vụ.
2.  **Dual-Axis Bar Chart (Trực giao vs Accuracy):** 
    *   *Trục X:* Các phương pháp (O-LoRA, N-LoRA)
    *   *Trục Y trái:* Average Accuracy (%)
    *   *Trục Y phải:* Task Orthogonality Score
    *   *Mục đích:* Minh họa phát hiện: N-LoRA đạt độ trực giao tốt hơn cả O-LoRA (phương pháp vốn trực tiếp tối ưu hóa trực giao).
3.  **Radar Chart (Độ ổn định theo thứ tự tác vụ):** 
    *   *Các trục:* Order 1, Order 2, Order 3, Order 4, Order 5, Order 6
    *   *Nhóm (Series):* O-LoRA, N-LoRA
    *   *Mục đích:* Tái hiện cấu trúc Hình 4 để chứng minh N-LoRA luôn vượt trội hơn O-LoRA bất kể thứ tự sắp xếp tác vụ.

### 3. Đề xuất cấu trúc bảng
*   **Bảng tổng hợp:** `[Method | Standard CL Avg (Orders 1-3) | Long CL Avg (Orders 4-6) | Avg Collision Rate (ACR) | Orthogonality Score]`
*   *Highlight:* Bold cho best score.

### 4. Số liệu thô (Raw Data)
```markdown
| Method | Standard CL (Avg 1-3) | Long CL (Avg 4-6) | LLaMA-7B Standard CL Avg |
| :--- | :---: | :---: | :---: |
| **ProgPrompt** | 75.1% | 77.9% | - |
| **PerTaskFT** | 70.0% | 78.1% | - |
| **O-LoRA** | - | - | 76.1% |
| **N-LoRA (Ours)** | - | - | **77.6%** |
```
