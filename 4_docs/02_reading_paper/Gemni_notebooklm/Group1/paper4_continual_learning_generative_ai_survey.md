# Continual Learning for Generative AI: From LLMs to MLLMs and Beyond

**ArXiv:** 2506.13045  
**Authors:** Haiyang Guo, Fanhu Zeng, Fei Zhu, Jiayi Wang, Xukai Wang, Jingang Zhou, Hongbo Zhao, Wenzhuo Liu, Shijie Ma, Da-Han Wang, Xu-Yao Zhang, Cheng-Lin Liu (Institute of Automation, Chinese Academy of Sciences)  
**Venue:** Survey 2025  
**Source File:** `1_ 2506.13045 _Continual Learning for Generative AI From LLMs to MLLMs and Beyond.pdf`

---

## 1. High-Level Summary (5–7 câu)

Bài khảo sát này hệ thống hóa các nghiên cứu về Học liên tục (Continual Learning - CL) cho các mô hình AI tạo sinh (Generative AI), đánh dấu sự dịch chuyển từ các mô hình phân biệt (discriminative) truyền thống sang các mô hình sinh tạo phức tạp. Nhóm tác giả đề xuất một hệ thống phân loại lấy cảm hứng từ cấu trúc trí nhớ của não bộ con người, chia các kỹ thuật thành ba nhóm chính: dựa trên kiến trúc (Architecture-based), điều chuẩn (Regularization-based) và phát lại (Replay-based). Điểm đặc biệt của khảo sát này là phạm vi bao phủ rộng khắp 4 miền generative AI chủ lưu bao gồm: Mô hình Ngôn ngữ Lớn (LLMs), Mô hình Đa phương thức Lớn (MLLMs), Mô hình Thị giác-Ngôn ngữ-Hành động (VLA cho robot) và Mô hình Khuếch tán (Diffusion Models). Nghiên cứu phân tích sâu sắc các hàm mục tiêu huấn luyện, backbone kiến trúc và bộ benchmark tương ứng cho từng miền. Cuối cùng, bài viết thảo luận về các xu hướng tương lai như tối ưu hóa dựa trên Học tăng cường (Reinforcement Learning) và chưng cất tự phát lại (self-generative replay).

---

## 2. Section Summaries

### 2.1 Introduction
Phần giới thiệu nêu bật sự chuyển dịch từ CL phân biệt (chỉ tập trung vào tác vụ phân loại/nhận diện) sang CL tạo sinh (đòi hỏi mô hình phải sáng tạo nội dung mới). Các tác giả nhấn mạnh hiện tượng quên thảm khốc xảy ra khi mô hình cập nhật kiến thức, phương thức hoặc sở thích mới của người dùng. Họ khẳng định việc đào tạo lại từ đầu các mô hình khổng lồ này là bất khả thi về mặt compute, tạo tiền đề cho sự phát triển của các phương pháp CL hiệu quả.

### 2.2 Methodology / Taxonomy
Nhóm tác giả xây dựng khung taxonomy 3 nhánh lấy cảm hứng từ sự cộng tác giữa hồi hải mã (học nhanh) và vỏ não (tích hợp dài hạn). Phương pháp dựa trên Kiến trúc cô lập tri thức mới bằng các module PEFT động (như LoRA hoặc dynamic routers). Phương pháp dựa trên Điều chuẩn hạn chế sự thay đổi của các trọng số quan trọng thông qua các hàm phạt gradient hoặc chưng cất tri thức. Phương pháp dựa trên Phát lại trộn dữ liệu cũ hoặc pseudo-data do mô hình tự sinh để củng cố trí nhớ.

### 2.3 Large Language Models (LLMs)
Phần này tập trung vào CL trên các mô hình văn bản tự hồi quy, nơi mọi tác vụ được quy về bài toán dự đoán token tiếp theo. Các kỹ thuật PEFT động như O-LoRA hay LLaMA PRO được phân tích cùng với các chiến lược replay và điều chuẩn đặc trưng. Các tác giả cũng thống kê các benchmark lớn cho LLM như TRACE hay chuỗi classification dài.

### 2.4 Multimodal LLMs (MLLMs)
Phần này nghiên cứu CL trên các mô hình xử lý đồng thời văn bản và hình ảnh, đối mặt với thách thức lớn về sự can thiệp chéo giữa các phương thức (cross-modal interference). Các tác giả giới thiệu các framework mở rộng backbone (như Eproj) và mở rộng LoRA (như MoELoRA) kèm các chỉ số đánh giá khả năng suy luận và căn chỉnh thực tế trên benchmark CoIN hay CLiMB.

### 2.5 Vision-Language-Action Models (VLA)
Khảo sát chuyển sang lĩnh vực Embodied AI nơi mô hình robot phải học liên tục các chuỗi thao tác vật lý mới trong môi trường giả lập hoặc thực tế. Các phương pháp học liên tục ở đây sử dụng học củng cố nghịch đảo suốt đời (Lifelong IRL) để tránh việc robot học kỹ năng mới (ví dụ: gạt đồ) mà quên mất kỹ năng cũ (ví dụ: gắp đồ) trên các benchmark LIBERO hay ALFRED.

### 2.6 Diffusion Models
Phần này xem xét sự cá nhân hóa liên tục của các mô hình khuếch tán Latent Diffusion Models (LDMs) để sinh ảnh theo khái niệm hoặc phong cách nghệ thuật mới. Các tác giả thảo luận về các giải pháp tinh chỉnh U-Net và bảo toàn bộ nhớ để tránh hiện tượng quên các phong cách cũ trên benchmark CLoG.

### 2.7 Discussion (Future Directions)
Phần thảo luận chỉ ra các hướng đi tương lai hứa hẹn như việc sử dụng các thuật toán học củng cố (ví dụ GRPO) cho CL vì chúng có khả năng tổng quát hóa tốt hơn SFT. Tác giả cũng kêu gọi xây dựng một khung tối ưu hóa hợp nhất xuyên suốt mọi phương thức (modalities) và kiến trúc, đồng thời phát triển cơ chế tự phát lại (self-generative replay) để giảm sự phụ thuộc vào bộ đệm dữ liệu thật.

---

## 3. Methodology Deep-Dive

### Framework
**Khung phân loại CL tạo sinh (Generative CL Framework):**
Hệ thống hóa 3 nhánh giải thuật (Kiến trúc, Điều chuẩn, Phát lại) xuyên suốt 4 miền Mô hình Tạo sinh (LLM, MLLM, VLA, Diffusion).

### Core Ideas
1. **LoRA Expansion & MoE Routing:** Sử dụng các mạng con low-rank (LoRA) làm chuyên gia và huấn luyện bộ định tuyến (router) để phân phối token đầu vào vào đúng chuyên gia LoRA, hạn chế ghi đè trọng số (ví dụ: MoELoRA).
2. **Lifelong IRL cho Embodied AI:** Thiết kế thuật toán học củng cố nghịch đảo để mô hình robot tự phục hồi hàm thưởng (reward function) cũ khi tiếp nhận môi trường hoạt động mới.
3. **Self-Generative Replay:** LLM hoặc Diffusion Model tự tạo ra ảnh/văn bản giả lập của tác vụ cũ (hallucinated data) để trộn vào dữ liệu mới khi train, triệt tiêu sự cần thiết của việc lưu trữ bộ đệm dữ liệu thật.

### Data / Setup / Metrics
- **Benchmarks:** AG News, Amazon, Yelp (LLM), CLiMB, CoIN (MLLM), LIBERO, ALFRED (VLA), CLoG (Diffusion).
- **Setup:** Huấn luyện trên dòng dữ liệu tuần tự (task stream) không có nhãn tác vụ ở pha inference.
- **Metrics:** Zero-shot Transfer (ZT - đánh giá forward), Backward Transfer (BWT - đo độ quên), và Overall Performance (OP).

### 3 Key Assumptions
1. **Cognitive Analogy Translatability:** Giả định rằng cơ chế phân tầng bộ nhớ sinh học (hippocampus vs. neocortex) có thể mô hình hóa chính xác bằng toán học thông qua các hàm loss và định tuyến tham số.
2. **Autoregressive Modality Unification:** Giả định rằng mọi dạng đầu ra (từ token văn bản, tọa độ ảnh, hành động robot đến latent pixel) đều có thể học liên tục hiệu quả dưới cùng một hàm loss dự đoán tuần tự (next-token prediction).
3. **PEFT Non-Saturation:** Giả định rằng việc chỉ cập nhật các tham số low-rank (PEFT) cung cấp đủ dung lượng học tập để thích ứng mô hình suốt đời mà không bị bão hòa dung lượng trọng số.

---

## 4. Brutally Honest Review (NeurIPS Style)

### Summary
This survey provides a comprehensive systematization of continual learning techniques applied to modern generative AI. By establishing a brain-inspired taxonomy (Architecture, Regularization, Replay), the authors systematically map CL strategies across LLMs, MLLMs, VLAs, and Diffusion Models, cataloging recent benchmarks, metrics, and PEFT implementations.

### Strengths
- **Exceptional Modality Breadth:** Uniquely bridges the gap between text-only LLMs and complex multimodal/embodied domains (robotics and image customization) which are usually highly siloed.
- **Elegant Taxonomy:** Decoupling methods into Architecture, Regularization, and Replay (linked to cognitive memory mechanisms) creates a clean, unified framework.
- **Phenomenal Cataloging:** Cross-references backbones, specific datasets, modalities, and open-source codebases (Tables 1, 2, 3) thoroughly.

### Weaknesses
1. **Lack of Original Empirical Benchmarking (Entire Paper):** The survey summarizes literature without running experiments. It fails to compare methods (e.g. O-LoRA vs. MoELoRA) under a standardized compute/data environment.
2. **Superficial Treatment of Safety Alignment (Section 3 & 7):** Despite identifying RL-based training (GRPO) as a future trend, the paper under-explores how CL prevents alignment collapse and reward hacking during sequential preference tuning (Continual RLHF/DPO).
3. **VLA Section Brevity (Section 5):** The robotics section is noticeably shorter and lacks mathematical depth on how continuous physical action space shifts differ from discrete token prediction.
4. **Shoehorned Biological Analogy (Section 2.3):** Drawing parallels to the hippocampus and neocortex adds narrative flavor but lacks technical precision, as standard weight regularization is mathematically detached from biological synaptic plasticity.
5. **No Compute/VRAM Scaling Comparisons (Section 3 & 4):** The survey fails to provide a systematic comparison of training FLOPs, memory footprint growth, and inference latency among the proposed PEFT and replay approaches.

### Questions for Authors
1. Given a fixed VRAM budget on a single edge GPU, which taxonomic branch (Architecture, Regularization, Replay) provides the best accuracy-latency trade-off for a 7B LLM?
2. Mathematically, how does the forgetting mechanism in Latent Diffusion Models differ from that in autoregressive text decoders?
3. For MLLM benchmarks (CLiMB/CoIN), what is the current quantitative SOTA model, and does it rely on rehearsal or architecture isolation?

### Recommendation
**Accept**  
The survey covers a massive range of very recent (2024/2025) literature across text, image, and action modalities. The unified perspective is a valuable roadmap for the community.

---

## 5. Data & Metrics View Designer

### Datasets, Tasks, and Metrics
- **Datasets:** AG News, Amazon, Yelp, DBpedia, Yahoo, TRACE (LLM), CLiMB, CoIN, MLLM-CL (MLLM), LIBERO, ALFRED, MetaWorld (VLA), CLoG (Diffusion).
- **Tasks:** Text classification, visual question answering, robotic manipulation, concept image customization.
- **Metrics:** Zero-shot transfer (ZT), Backward transfer (BWT), Overall Performance (OP).
- **Key Baselines:** O-LoRA, I-LoRA, ProgPrompt, LLaVA-C, MoELoRA, SLIM, Eproj, Model Tailor.

### 3 Chart Proposals
1. **Modalities vs. Forgetting Heatmap:**
   - **X-axis:** Generative Domains (LLM, MLLM, VLA, Diffusion).
   - **Y-axis:** BWT (forgetting %).
   - **Group/Series:** CL Method (Replay vs. PEFT vs. Regularization).
   - **Purpose:** Identifies which modalities are most vulnerable to forgetting and which methods are most robust.
2. **Cumulative Task Accuracy Decay Curve:**
   - **X-axis:** Task sequence index (task 1 to task 10).
   - **Y-axis:** Downstream Task Accuracy (%).
   - **Group/Series:** Standard LoRA fine-tuning, MoELoRA, and Generative Replay.
   - **Purpose:** Illustrates the speed of performance decay as tasks accumulate.
3. **RL vs. SFT Generalization Scatter Plot:**
   - **X-axis:** Zero-shot Transfer (ZT).
   - **Y-axis:** Backward Transfer (BWT).
   - **Group/Series:** Supervised Fine-Tuning (SFT) vs. Reinforcement Learning (GRPO).
   - **Purpose:** Verifies the claim that RL-based training yields better out-of-distribution generalization in continual setups.

### Table Structure — Proposed Summary
| Method | Target Domain (LLM/MLLM/VLA/Diffusion) | PEFT Type | Replay (Yes/No) | VRAM Overhead (MB) | ZT (%) | BWT (%) | OP (%) | Code Link |
|---|---|---|---|---|---|---|---|---|
| **O-LoRA** | LLM / MLLM | LoRA | No | Low | *Score* | *Score* | *Score* | [Link](https://github.com) |
| **MoELoRA**| MLLM | LoRA-Experts | No | Medium | *Score* | *Score* | *Score* | [Link](https://github.com) |
| **Eproj** | MLLM | Q-Former | Yes | High | *Score* | *Score* | *Score* | [Link](https://github.com) |
| **LIBERO** | VLA | ViT/ResNet | Yes | High | *Score* | *Score* | *Score* | [Link](https://github.com) |

### Raw Data Extraction
**Table 1 Excerpt: Summary of continual learning methods for LLMs** (✔: Deployed)

| Methods | Setting | Backbone / Basic models | Arch. | Reg. | Rep. |
| :--- | :---: | :--- | :---: | :---: | :---: |
| Zheng et al. | CIT | LLaMA-2-7B-Chat, LLaMA-3-8B-Instruct | ✔ | | |
| SAPT | CIT | T5-large, LLaMA-2-7B/13B | ✔ | ✔ | |
| I-LoRA | CIT | LLaMA-2-7B | ✔ | ✔ | |
| P-Prompts | CIT | BERT-base, T5-large | ✔ | | |
| O-LoRA | CIT | T5-large, LLaMA-7B | ✔ | ✔ | |
| DAS | CPT | RoBERTa-base | ✔ | | |
| PCLL | CToD | GPT-2 small | ✔ | ✔ | ✔ |

---

## 6. Key Takeaways & Relevance to VN-Legal-AI / CDNCTQ

- **Ứng dụng GRPO trong Căn chỉnh Liên tục Pháp lý:** Vì dự án VN-Legal-AI sử dụng thuật toán GRPO (Group Relative Policy Optimization) ở Chương 3 để tối ưu hóa sở thích và căn chỉnh tư duy lập luận của mô hình, phát hiện của survey rằng các phương pháp học củng cố (RL) như GRPO giúp tăng khả năng tổng quát hóa (ZT) và giảm quên (BWT) tốt hơn SFT thông thường là một sự ủng hộ lý thuyết đắt giá cho thiết kế của chúng ta.
- **Mở rộng mô hình đa phương thức cho văn bản pháp lý scan:** Sau khi hoàn thành bản text, mô hình pháp lý có thể được nâng cấp lên MLLM để xử lý các tài liệu pháp lý dạng PDF quét hoặc ảnh chụp văn bản gốc. Khung kiến trúc PEFT động (như MoELoRA hoặc Eproj) sẽ giúp chúng ta bổ sung năng lực hiểu ảnh tài liệu mà không làm hỏng khả năng suy luận ngôn ngữ pháp lý đã tích hợp trước đó.
- **Generative Replay cho tư vấn pháp luật:** Để bảo mật thông tin khách hàng, chúng ta có thể sử dụng generator để tự sinh các đoạn hội thoại tư vấn pháp lý giả lập (Generative Replay) giúp mô hình ôn tập lại các kỹ năng hội thoại pháp lý cũ trong quá trình cập nhật các văn bản luật mới của năm 2026.
