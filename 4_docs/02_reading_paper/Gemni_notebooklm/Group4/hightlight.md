# Tổng hợp Số liệu & Hình vẽ: Group 4 (PEFT, LoRA & Prompt-Based Continual Learning)

Tài liệu này hệ thống hóa toàn bộ các bảng số liệu thực nghiệm và sơ đồ cấu trúc của 8 bài báo thuộc **Group 4** để hỗ trợ việc phân tích định lượng, vẽ biểu đồ và tích hợp vào các báo cáo kỹ thuật của dự án **VN-Legal-AI / CDNCTQ**.

---

## 1. Paper 1: Orthogonal Subspace Learning for LM Continual Learning (O-LoRA)

### Hình vẽ (Figures)
*   **Hình 1: Sơ đồ hình chiếu trực giao (Orthogonal Projection Concept)**
    *   *Mô tả:* Biểu diễn không gian vector 3D chỉ ra hướng cập nhật gradient của tác vụ mới bị ràng buộc vuông góc ($90^\circ$) với không gian con LoRA của các tác vụ cũ để triệt tiêu ảnh hưởng xấu lên loss landscape của tác vụ cũ.
    *   *Chi tiết:* [Gradient mới] $\rightarrow$ [Chiếu trực giao] $\rightarrow$ [Hướng cập nhật trực giao] $\perp$ [Không gian con LoRA cũ].
*   **Hình 2: Khung kiến trúc O-LoRA (O-LoRA Framework)**
    *   *Mô tả:* Sơ đồ khối Transformer tiêu chuẩn có Attention ($K, Q, V$) bị đóng băng. Song song với đó là các cặp adapter LoRA của từng tác vụ cũ $A_1 B_1, A_2 B_2$ (đã đóng băng) và adapter tác vụ mới $A_t B_t$ (đang train). Khối *Orthogonal regularization* tính toán tích ma trận $A_i^T A_t = 0$ để phạt sự chồng lấn.
*   **Hình 3: Biểu đồ Histogram biến động Loss (Loss Change Histogram)**
    *   *Mô tả:* Trục X là mức độ thay đổi hàm loss trên dữ liệu cũ; Trục Y là số lượng batches.
    *   *Dữ liệu:* Khi không dùng O-LoRA ($\lambda_1 = 0$), loss change dao động lớn từ $1.5$ đến $2.5$. Khi dùng O-LoRA ($\lambda_1 = 0.5$), loss change tập trung sát về $0.0 - 0.2$.
*   **Hình 4: Biến động trạng thái ẩn theo tầng (Hidden State Changes)**
    *   *Mô tả:* Biểu đồ nhiệt cho thấy các tầng thấp (1-12) ít bị biến động trạng thái ẩn nhất khi dùng O-LoRA, chứng minh tri thức ngữ nghĩa dùng chung được bảo toàn ở các lớp dưới.

### Bảng số liệu (Tables)

#### Bảng 1: So sánh đặc tính hệ thống các phương pháp CL
*(RF: Rehearsal-Free, PE: Parameter-Efficient, TIF: Task-ID in Inference, UT: Unseen Tasks)*

| Method | RF | PE | TIF | UT |
| :--- | :---: | :---: | :---: | :---: |
| EWC / LwF / OGD | ✓ | ✓ | | |
| A-GEM / MBPA++ / IDBR | | | | |
| L2P | ✓ | ✓ | ✓ | ✓ |
| LFPT5 | | ✓ | | |
| ProgPrompt | ✓ | ✓ | ✓ | |
| **O-LoRA (Ours)** | **✓** | **✓** | **✓** | **✓** |

#### Bảng 2 & 8: Hiệu năng trên Standard và Long CL Benchmark (T5-Large)

| Method | Standard CL (5-task) Avg Acc (%) | Long CL (15-task) Avg Acc (%) |
| :--- | :---: | :---: |
| Fine-Tuning (FT) | 28.5 | 7.4 |
| Vanilla LoRA | 43.7 | 1.6 |
| MoELoRA | 54.1 | 27.6 |
| LFPT5 | 72.7 | 69.2 |
| ProgPrompt | 75.1 | **77.9** |
| **O-LoRA (Ours)** | **76.6** | 69.5 |
| Multi-Task Learning (Upper Bound) | 80.0 | 76.5 |

#### Bảng 3: Khả năng giữ năng lực Zero-shot trên LLaMA-7B (MMLU)

| Model Configuration | MMLU (Zero-shot) Acc (%) | CL Benchmark Acc (%) |
| :--- | :---: | :---: |
| LLaMA-7B (Base) | 34.4 | - |
| Alpaca-LoRA (Tuned) | 37.5 | - |
| Alpaca-LoRA-CL (No O-LoRA) | 23.3 (Sụp đổ) | 46.7 |
| Alpaca-inc-LoRA-CL | 28.6 | 33.1 |
| **Alpaca-OLoRA-CL** | **33.6** (Bảo toàn tốt) | **76.8** |

#### Bảng 5: Ảnh hưởng của hạng LoRA $r$ (T5-Base)

| Rank ($r$) | Order 1 Acc (%) | Order 2 Acc (%) | Order 3 Acc (%) | Avg (%) |
| :--- | :---: | :---: | :---: | :---: |
| $r=2$ | 74.2 | 71.1 | 73.6 | 73.0 |
| $r=4$ | 73.0 | 72.7 | 74.1 | 73.3 |
| $r=8$ | 75.6 | 71.7 | 71.9 | 73.1 |
| $r=16$ | 74.5 | 73.4 | 74.8 | 74.2 |

---

## 2. Paper 2: SLIM: Let LLM Learn More and Forget Less with Soft LoRA and Identity Mixture

### Hình vẽ (Figures)
*   **Hình 1: Radar Chart kết quả thực nghiệm**
    *   *Mô tả:* Biểu đồ mạng nhện biểu diễn 11 benchmarks. Đường bao của SLIM (màu lục) bao phủ rộng nhất, đặc biệt nhô xa ở trục GSM8K và MMLU so với các baseline PEFT bị sụp đổ trục này.
*   **Hình 3: Kiến trúc Dynamic Merging (FAST Architecture)**
    *   *Mô tả:* Sơ đồ chỉ ra luồng xử lý của ma trận LoRA $A$ và $B$. Trong quá trình train, hai ma trận này được ngẫu nhiên dropout/mask một tỷ lệ hàng/cột nhất định trước khi nhân với vector ẩn, hoạt động tương tự cơ chế DARE.
*   **Hình 4: Tác động của tỷ lệ Masking (Masking Ratio vs Acc)**
    *   *Mô tả:* Trục X là tỷ lệ mask (0%, 25%, 50%, 75%). Trục Y là độ chính xác (%). Khi tăng tỷ lệ mask lên 50%, độ chính xác MMLU tăng từ 61% lên 66% trong khi downstream task (MDS) vẫn giữ ổn định ở 78-80%.
*   **Hình 5: Đồ thị suy giảm năng lực MMLU theo thời gian tinh chỉnh**
    *   *Mô tả:* Trục X là số step huấn luyện downstream (2k đến 12k). Trục Y là MMLU Acc. SLIM giữ nguyên đường thẳng nằm ngang ở ~66%; MixLoRA giảm dần xuống 55%; LoRA giảm sâu về 32%.

### Bảng số liệu (Tables)

#### Bảng 1 & 2: So sánh hiệu năng Downstream và Generalization (OpenChat-8B)

| Model | Downstream Avg (SDS & MDS) % | Generalization Avg % | MMLU % | GSM8K % | PIQA % |
| :--- | :---: | :---: | :---: | :---: | :---: |
| LoRA | 77.39 | 31.24 | 32.73 | 0.00 | 60.99 |
| DoRA | 77.32 | 29.41 | 31.07 | 0.00 | 57.18 |
| LoRAMoE | 85.12 | 66.39 | 60.20 | 59.43 | 79.54 |
| MixLoRA | 83.06 | 51.08 | 55.41 | 20.47 | 77.36 |
| MixLoRA-Dy | 83.16 | 51.98 | 56.14 | 21.38 | 78.43 |
| MoLA | 83.11 | 47.79 | 53.15 | 15.54 | 74.70 |
| **SLIM (Ours)** | **86.63** | **75.65** | **65.83** | **76.11** | **84.65** |

#### Bảng 3: Thử nghiệm Ablation các module của SLIM (MDS vs MMLU)

| Attn w/o LoRA | Identity Experts | Weight Yielding | Dynamic Merging | MDS Acc (%) | MMLU Acc (%) |
| :---: | :---: | :---: | :---: | :---: | :---: |
| | | | | 78.00 | 55.41 |
| ✓ | | | | 77.80 | 57.70 |
| | ✓ | | | 76.87 | 56.37 |
| ✓ | ✓ | ✓ | | 79.53 | 60.29 |
| **✓** | **✓** | **✓** | **✓** | **80.23** | **65.83** |

---

## 3. Paper 3: LoRA: Low-Rank Adaptation of Large Language Models

### Hình vẽ (Figures)
*   **Hình 1: Sơ đồ cấu trúc LoRA (LoRA Reparametrization)**
    *   *Mô tả:* Sơ đồ nhánh kép. Nhánh chính bên trái là trọng số mô hình gốc $W_0 \in \mathbb{R}^{d \times k}$ bị đóng băng (ký hiệu bông tuyết). Nhánh song song bên phải là 2 ma trận tích tuyến tính $A$ (khởi tạo Gaussian) và $B$ (khởi tạo bằng 0) có kích thước hạng $r$. Đầu ra được cộng chéo: $h = W_0 x + B A x$.
*   **Hình 2: Accuracy vs Số lượng tham số huấn luyện (GPT-3 175B)**
    *   *Mô tả:* Hai đồ thị biểu diễn WikiSQL (trái) và MultiNLI (phải). Trục X là $\log_{10}$ số lượng tham số huấn luyện. Trục Y là độ chính xác. LoRA giữ phong độ đi lên và đi ngang ổn định ở mức cao nhất (~0.74 trên WikiSQL, ~0.917 trên MNLI) trong khi Prefix-tuning bị tụt dốc khi tăng tham số.

### Bảng số liệu (Tables)

#### Bảng 4: Hiệu năng thích ứng trên siêu mô hình GPT-3 175B

| Model & Method | Trainable Params (M) | WikiSQL Acc (%) | MNLI-m Acc (%) | SAMSum (ROUGE-1/2/L) |
| :--- | :---: | :---: | :---: | :---: |
| GPT-3 (Full FT) | 175,255.8 | 73.8 | 89.5 | 52.0 / 28.0 / 44.5 |
| GPT-3 (BitFit) | 14.2 | 71.3 | 91.0 | 51.3 / 27.4 / 43.5 |
| GPT-3 (PrefixEmbed) | 3.2 | 63.1 | 88.6 | 48.3 / 24.2 / 40.5 |
| GPT-3 (PrefixLayer) | 20.2 | 70.1 | 89.5 | 50.8 / 27.3 / 43.5 |
| GPT-3 (AdapterH) | 7.1 | 71.9 | 89.8 | 53.0 / 28.9 / 44.8 |
| GPT-3 (AdapterH) | 40.1 | 73.2 | 91.5 | 53.2 / 29.0 / 45.1 |
| **GPT-3 (LoRA)** | **4.7** | **73.4** | **91.7** | **53.8 / 29.8 / 45.9** |
| **GPT-3 (LoRA)** | **37.7** | **74.0** | **91.6** | **53.4 / 29.2 / 45.1** |

#### Bảng 13: Hiệu năng sinh văn bản DART Benchmark (GPT-2 Medium 354M)

| Method | Trainable Params (M) | BLEU | METEOR | TER |
| :--- | :---: | :---: | :---: | :---: |
| Fine-Tune | 354.0 | 46.2 | 0.39 | 0.46 |
| AdapterL | 0.37 | 42.4 | 0.36 | 0.48 |
| AdapterL | 11.0 | 45.2 | 0.38 | 0.46 |
| PrefixLayer | 0.35 | 46.4 | 0.38 | **0.46** |
| **LoRA** | **0.35** | **47.1** | **0.39** | **0.46** |

#### Bảng 16: So sánh tính hiệu quả dữ liệu trên các tập con MNLI (GPT-3 175B)

| Method | MNLI-100 (samples) | MNLI-1k | MNLI-10k | MNLI-392k (Full) |
| :--- | :---: | :---: | :---: | :---: |
| GPT-3 (Fine-Tune) | 60.2 | 85.8 | 88.9 | 89.5 |
| GPT-3 (PrefixEmbed) | 37.6 | 75.2 | 79.5 | 88.6 |
| GPT-3 (PrefixLayer) | 48.3 | 82.5 | 85.9 | 89.6 |
| **GPT-3 (LoRA)** | **63.8** | **85.6** | **89.2** | **91.7** |

---

## 4. Paper 4: Progressive Prompts: Continual Learning for Language Models

### Hình vẽ (Figures)
*   **Hình 1: So sánh cấu trúc thích ứng (Concatenation Scheme)**
    *   *Mô tả:* So sánh việc nhân bản dữ liệu đầu vào của Progressive Networks dạng chuẩn với Progressive Prompts. Với Progressive Prompts, văn bản đầu vào $x$ được giữ cố định, các prompt ảo $P_1, P_2, \dots$ được chèn nối tiếp ở phía trước theo sự tăng dần của số lượng tác vụ.
*   **Hình 2: Biểu đồ nhiệt Attention giữa các Prompts (Inter-prompt Attention Map)**
    *   *Mô tả:* Biểu đồ ma trận 15x15. Trục X và Y đại diện cho 15 tác vụ. Các điểm sáng màu nằm ngoài đường chéo chỉ ra mức độ liên kết chú ý (attention) rất cao giữa các tác vụ tương đồng ngữ nghĩa (ví dụ: `sst2` chú ý mạnh đến `imdb`, `amazon` chú ý đến `yelp`).
*   **Hình 4: Hiệu quả của học chuyển giao tiến (Forward Transfer by Data Size)**
    *   *Mô tả:* Trục X là số lượng mẫu (2, 5, 20, all). Trục Y là Average Score. Progressive Prompts (đường màu cam) bắt đầu rất cao ở kịch bản 2-shot (~61%) so với Prompt Tuning chuẩn (~55%), chứng minh tri thức tích lũy cũ hỗ trợ đắc lực khi dữ liệu cực ít.

### Bảng số liệu (Tables)

#### Bảng 9: Hiệu năng trung bình trên chuỗi dài 15 tác vụ (Orders 8, 9, 10)

| Model & Method | 20 Samples/Class Avg Acc (%) | 200 Samples/Class Avg Acc (%) | 1000 Samples/Class Avg Acc (%) |
| :--- | :---: | :---: | :---: |
| **T5-Large results** | | | |
| Fine-Tuning | 9.7 | 8.3 | 7.4 |
| Experience Replay | 43.6 | 44.2 | 54.4 |
| Prompt Tuning | 17.4 | 13.9 | 10.9 |
| Per-task Prompts | 69.8 | 75.2 | 77.0 |
| LFPT5 (Previous SOTA) | 54.3 | 58.2 | 69.2 |
| **ProgPrompt (Ours)** | **76.2** | **78.7** | **79.5** |
| MTL (Upper Bound) | 70.7 | 72.5 | 76.3 |
| **BERT-base results** | | | |
| Fine-Tuning | 31.3 | 42.4 | 41.7 |
| Experience Replay | 36.4 | 47.0 | 49.6 |
| IDBR (Previous SOTA) | 36.8 | 47.9 | 52.2 |
| Per-task Prompts | 50.6 | 62.4 | 67.2 |
| **ProgPrompt (Ours)** | **53.5** | **66.9** | **69.3** |
| MTL (Upper Bound) | 56.9 | 67.7 | 69.9 |

#### Bảng 10: Tác động của cấu trúc Residual MLP reparameterization (BERT-base)
*(Đo trên IMDB và QQP với chiều dài prompt 5 và 30)*

| Dataset & Length | Vanilla PT Acc (%) | PT + standard MLP Acc (%) | PT + Residual MLP Acc (%) | Full model FT Acc (%) |
| :--- | :---: | :---: | :---: | :---: |
| IMDB (len 5) | 85.8 | 83.4 | **91.4** | 92.9 |
| IMDB (len 30) | 89.1 | 90.2 | **91.2** | 92.9 |
| QQP (len 5) | 72.9 | 73.1 | **76.6** | 78.6 |
| QQP (len 30) | 72.2 | 73.9 | **77.3** | 78.6 |

---

## 5. Paper 5: Higher Layers Need More LoRA Experts (MoLA)

### Hình vẽ (Figures)
*   **Hình 1: Cấu trúc tầng MoLA (MoLA Block Architecture)**
    *   *Mô tả:* Minh họa lớp Transformer gồm Attention và FFN bị đóng băng (snowflake). Đi kèm là router $W_r$ và một tập hợp $N$ các experts LoRA ($A_1 B_1$ đến $A_N B_N$). Router tính toán phân bổ Top-K và cộng trọng số đầu ra.
*   **Hình 2: 4 dạng phân bổ expert theo tầng**
    *   *Mô tả:* Sơ đồ khối biểu diễn 4 cấu hình: (a) Triangle (rút gọn expert ở các tầng trên), (b) Inverted-Triangle (tăng expert ở các tầng trên), (c) Hourglass (nhiều expert ở hai đầu), (d) Rectangle (uniform lượng expert).
*   **Hình 3 & 4: Frobenius Norm giữa các expert theo tầng Transformer (1-32)**
    *   *Mô tả:* Trục X là Layer (0-32). Trục Y là chuẩn Frobenius trung bình giữa các ma trận expert.
    *   *Đặc trưng:* Các đường đồ thị của tất cả các cấu hình đều dốc thẳng đứng đi lên từ tầng thấp (chuẩn Frobenius sát 1.0) lên tầng cao (chuẩn Frobenius lớn), chứng minh expert ở tầng dưới rất giống nhau (dư thừa), còn tầng trên phân hóa rõ nét.

### Bảng số liệu (Tables)

#### Bảng 1: Hiệu năng tinh chỉnh trực tiếp trên LLaMA-2-7B (Downstream Accuracy)

| Models (Expert configuration) | MRPC | COLA | RTE | ScienceQA | CSQA | OBQA |
| :--- | :---: | :---: | :---: | :---: | :---: | :---: |
| Full-Parameter FT | 87.13% | 86.29% | 87.73% | 93.12% | 77.48% | 80.4% |
| LoRA (Rank 64) | 84.41% | 84.95% | 84.48% | 91.01% | 74.61% | 76.6% |
| MoLA-$\square$ (8888) | 84.23% | 85.72% | 87.36% | 92.13% | 77.15% | 78.4% |
| MoLA-$\square$ (5555) | 84.93% | 84.56% | 88.81% | 91.73% | 75.92% | 77.6% |
| MoLA-$\triangle$ (8642) | 84.46% | 85.23% | 89.17% | 91.41% | 76.33% | 78.8% |
| MoLA-$\bowtie$ (8228) | 84.35% | 84.85% | 87.72% | 91.41% | 75.02% | 77.4% |
| **MoLA-$\nabla$ (2468)** | **85.45%** | **86.19%** | **89.17%** | **92.36%** | **77.15%** | **78.4%** |

#### Bảng 3: Kết quả Continual Domain Adaptation (Biology $\rightarrow$ Physics $\rightarrow$ Chemistry $\rightarrow$ Econ $\rightarrow$ Earth)

| Configuration | Overall Performance (OP) ↑ | Performance Drop (PD) ↑ |
| :--- | :---: | :---: |
| LoRA | 78.67% | -2.17% |
| MoLA-$\square$ (5555) | 88.80% | -0.60% |
| MoLA-$\bowtie$ (8228) | 83.82% | -3.92% |
| MoLA-$\triangle$ (8642) | 88.84% | -2.10% |
| **MoLA-$\nabla$ (2468)** | **89.82%** | **-0.47%** (Chống quên tốt nhất) |

---

## 6. Paper 6: Analyzing and Reducing Catastrophic Forgetting in Parameter Efficient Tuning (I-LoRA)

### Hình vẽ (Figures)
*   **Hình 1: Đồ thị thung lũng liên kết chế độ (Mode Connectivity Curves)**
    *   *Mô tả:* Một lưới gồm các đồ thị parabol. Trục X đại diện cho hệ số nội suy $\lambda$ chạy từ 0.0 (cực tiểu tác vụ cũ $\theta_t$) đến 1.0 (cực tiểu tác vụ mới $\theta_{t+1}$). Trục Y là kiểm tra độ chính xác.
    *   *Kết quả:* Accuracy trên toàn bộ tác vụ đã học ($A_{all}$) đạt đỉnh lồi rõ nét ở các tọa độ $\lambda \in [0.4, 0.6]$ thay vì nằm ở hai đầu mút, chứng minh có thung lũng loss thấp kết nối hai tác vụ và điểm nội suy tối ưu hơn điểm cực tiểu đơn lẻ.

### Bảng số liệu (Tables)

#### Bảng 14 & 15: Hiệu năng so sánh giữa Single Task FT và kết hợp tuyến tính Fast/Slow

| Dataset / Task | Single Task FT Acc (Max Plasticity) | Linear Combination (Fast/Slow Interpolation) |
| :--- | :---: | :---: |
| C-STANCE | 0.388 | 0.276 |
| FOMC | 0.342 | 0.488 |
| MeetingBank | 0.164 | 0.032 |
| ScienceQA | 0.324 | 0.252 |
| NumGLUE-cm | 0.308 | 0.004 |
| 20Minuten | 0.014 | 0.014 |
| MedMCQA | 0.328 | 0.236 |
| JEC-QA | 0.324 | 0.272 |

#### Số liệu tóm tắt quá trình học liên tục qua 8 tác vụ (LLaMA-2-7B)
*(Độ chính xác sau khi kết thúc chuỗi học 8 tác vụ)*

*   **Sequence Train (No Replay):** Forgetting score rất cao. Accuracy của tác vụ đầu (C-STANCE) giảm từ 40.8% xuống còn 21.0% sau khi học xong tác vụ 8.
*   **Experience Replay (ER):** Giúp duy trì C-STANCE ở mức 16.4% ở cuối chuỗi.
*   **I-LoRA (Ours):** Khống chế điểm quên trên tập toán NumGLUE-cm xuống còn 9.1% so với mức quên 20.6% của EWC.

---

## 7. Paper 7: LoRA Learns Less and Forgets Less

### Hình vẽ (Figures)
*   **Hình 1 & 2: Đường cong học tập miền đích và quên miền nguồn (Learning vs Forgetting)**
    *   *Mô tả:* Đồ thị lưới so sánh Code và Math. Cột trái đo Target Acc; cột phải đo Forgetting Avg. 
    *   *Kết quả:* Trong kịch bản CPT ( स्टारCoder-Python), Full FT (đường đen) bỏ xa các rank của LoRA (đường tím) về tốc độ học. Tuy nhiên, ở đồ thị bên phải, Full FT tụt giảm độ chính xác tri thức chung cực nhanh, còn LoRA duy trì đường dốc thoai thoải hơn nhiều.
*   **Hình 3: Đường biên Pareto đánh đổi (Pareto Frontiers)**
    *   *Mô tả:* Trục X là Forgetting (càng cao càng tốt - ít quên). Trục Y là Target Accuracy. LoRA phân bố ở góc dưới bên phải (học ít hơn, quên ít hơn); Full FT phân bố ở góc trên bên trái (học nhiều hơn, quên nhiều hơn).
*   **Hình 4: So sánh với các Regularizer chuẩn**
    *   *Mô tả:* So sánh LoRA (r=256) với Attention Dropout (0.05, 0.1) và Weight Decay (5e-5, 1e-4) trên Magicoder. LoRA (r=256) nằm ở góc trên bên phải tối ưu hơn hẳn tất cả các bộ điều chuẩn thông thường.
*   **Hình 5: Phân tích độ đa dạng đầu ra (Output Diversity)**
    *   *Mô tả:* Biểu đồ histogram số lượng câu trả lời duy nhất (unique answers) khi sinh mẫu 50 lần. Full FT tập trung cột cao chót vót ở khoảng 0-5 (mất tính đa dạng); LoRA trải rộng tương đương mô hình gốc.

### Bảng số liệu (Tables)

#### Bảng S1, S2, S5, S6: So sánh chi tiết hiệu năng CPT và IFT (LLaMA-2-7B)

| Regime & Task | Method Configuration | Training Length | Target Domain Acc | Source Domain Acc (No Forgetting) |
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

---

## 8. Paper 8: Is Parameter Collision Hindering Continual Learning in LLMs (N-LoRA)

### Hình vẽ (Figures)
*   **Hình 1: Trực quan hóa khái niệm Đụng độ tham số (Parameter Collision Concept)**
    *   *Mô tả:* So sánh O-LoRA và N-LoRA. (a) O-LoRA ép trực giao vector cập nhật nhưng các điểm phần tử vẫn giao nhau (vùng đỏ đụng độ). (b) N-LoRA áp dụng phạt độ thưa, các cập nhật chỉ kích hoạt một vài phần tử thưa thớt không giao nhau (non-collision), tự động sinh trực giao hoàn hảo.
*   **Hình 3: Chiều của không gian con theo tầng (Nuclear Norms)**
    *   *Mô tả:* So sánh nuclear norm của các tầng Transformer. O-LoRA (màu xanh) có nuclear norm dao động cao; N-LoRA (màu đỏ) có nuclear norm rất thấp và phẳng, chứng minh không gian con của từng tác vụ được nén rất thưa.
*   **Hình 4: Radar Chart đánh giá độ chính xác qua 6 Task Orders**
    *   *Mô tả:* Biểu đồ radar 8 hướng. N-LoRA (màu xanh lam) bao ngoài O-LoRA (màu cam) trên tất cả các trục thứ tự tác vụ, thể hiện tính ổn định hiệu năng vượt trội.

### Bảng số liệu (Tables)

#### Bảng 2: Hiệu năng trung bình trên Standard CL Benchmark (LLaMA-7B)

| Model & Method | Order-1 Acc (%) | Order-2 Acc (%) | Order-3 Acc (%) | Average Acc (%) |
| :--- | :---: | :---: | :---: | :---: |
| O-LoRA | 76.8 | 75.7 | 75.7 | 76.1 |
| **N-LoRA (Ours)** | **77.2** | **77.3** | **78.4** | **77.6** (+1.5% vs O-LoRA) |

#### Kết quả đo đạc đụng độ và trực giao (T5-Large)
*   **Tỉ lệ đụng độ vô hướng (Average Collision Rate - ACR):** N-LoRA giảm **58.1 lần** tỉ lệ đụng độ so với O-LoRA trên chuỗi 15 tác vụ.
*   **Độ trực giao tác vụ (Task Orthogonality):** N-LoRA đạt độ trực giao tốt hơn **4.1 lần** so với O-LoRA.

---

## 9. Ghi chú về việc trích xuất hình vẽ (Visual Extraction Notes)

> [!NOTE]
> Do giới hạn kỹ thuật của các mô hình đọc hiểu văn bản thô, các hình ảnh bitmap nguyên bản trong PDF không thể hiển thị trực tiếp. Toàn bộ các mô tả hình vẽ trên được tái dựng lại thông qua dữ liệu mô tả phân tích tọa độ, bảng chú giải và case studies tương quan có trong text bài viết.
> 
> Các hình ảnh không thể khôi phục định dạng điểm ảnh bao gồm:
> 1.  Hình ảnh chụp heatmap chi tiết của attention trong từng head của Progressive Prompts (Chỉ tái dựng được heatmap tổng hợp 15x15).
> 2.  Hình ảnh phân bổ trọng số dạng đồ thị phân tán cụ thể của O-LoRA vs N-LoRA trong Tensor (Chỉ có mô hình hóa khái niệm dạng sơ đồ khối phẳng).
> 3.  Chi tiết ảnh chụp màn hình console sinh kết quả của Magicoder.
