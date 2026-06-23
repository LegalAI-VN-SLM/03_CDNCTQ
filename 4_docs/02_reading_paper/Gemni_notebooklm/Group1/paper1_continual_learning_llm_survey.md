# Continual Learning for Large Language Models: A Survey

**ArXiv:** 2402.01364  
**Authors:** Tongtong Wu, Linhao Luo, Yuan-Fang Li, Shirui Pan, Thuy-Trang Vu, Gholamreza Haffari (Monash University + Griffith University)  
**Venue:** Survey 2024  
**Source File:** `1_ 2402.01364 _Continual Learning for Large Language Models A Survey.pdf`

---

## 1. High-Level Summary (5–7 câu)

Bài báo khảo sát này hệ thống hóa các nghiên cứu về Học liên tục (Continual Learning - CL) dành riêng cho các Mô hình Ngôn ngữ Lớn (LLMs). Để đối phó với chi phí tính toán khổng lồ khi phải train lại LLM từ đầu khi có dữ liệu mới, nhóm tác giả đề xuất một khung phân loại đa giai đoạn đồng bộ với quy trình huấn luyện LLM thực tế bao gồm: Continual Pre-training (CPT), Continual Instruction Tuning (CIT), và Continual Alignment (CA). Nghiên cứu chỉ ra sự khác biệt cơ bản giữa học liên tục trên LLM với các mô hình nhỏ hơn, đồng thời phân biệt rõ CL với các phương pháp chỉnh sửa dữ liệu cục bộ như RAG hay Model Editing. Hơn thế nữa, bài khảo sát cũng tổng hợp các tập dữ liệu, phương thức đánh giá và chỉ ra các thách thức lớn như "quên chéo giai đoạn" (cross-stage forgetting) và sự thiếu hụt nền tảng lý thuyết cho CL trên LLM. Nhìn chung, đây là cẩm nang hữu ích định hướng cho các nghiên cứu tiếp theo về việc xây dựng các hệ thống AI có khả năng tự thích ứng liên tục.

---

## 2. Section Summaries

### 2.1 Introduction
Phần mở đầu nhấn mạnh nhu cầu cập nhật tri thức và giá trị đạo đức liên tục cho LLM mà không phải chịu chi phí tính toán cực lớn khi huấn luyện lại từ đầu. Các tác giả phân định rõ ranh giới giữa học liên tục (nhằm nâng cao năng lực ngôn ngữ và suy luận tổng thể của mô hình) với các phương pháp vá lỗi kiến thức như RAG hay Model Editing. Họ khẳng định việc áp dụng CL lên LLM cần một quy trình đa tầng tương thích với vòng đời của mô hình thay vì các phương pháp thích ứng tuyến tính của các mô hình nhỏ hơn.

### 2.2 Methodology / Taxonomy
Phần này xây dựng hệ thống phân loại cốt lõi chia CL trên LLM thành ba giai đoạn chính. Huấn luyện tiền đề liên tục (CPT) giúp cập nhật các sự kiện thế giới, ngôn ngữ hoặc miền tri thức mới thông qua học tự giám sát. Tinh chỉnh hướng dẫn liên tục (CIT) giúp mô hình học cách giải quyết các tác vụ mới hoặc thích ứng với định dạng hướng dẫn mới mà không làm mất kỹ năng cũ. Căn chỉnh liên tục (CA) đảm bảo các mô hình luôn tuân thủ các chuẩn mực an toàn và giá trị của con người thông qua phản hồi từ người dùng.

### 2.3 Experiments (Benchmarks & Evaluation)
Nhóm tác giả tổng hợp các bộ dữ liệu đánh giá tiêu chuẩn như TemporalWiki, Firehose và TRACE cho giai đoạn CPT, cùng với CITB và ConTinTin cho CIT. Hiệu năng học liên tục được đo lường qua ba chỉ số chính: Hiệu năng trung bình, Khả năng chuyển giao tiến (FWT) và Khả năng chuyển giao lùi (BWT). Điểm nhấn thực nghiệm là việc đề xuất đánh giá "quên chéo giai đoạn", tức là đo lường sự suy giảm của tri thức nền tảng hoặc các rào cản an toàn do quá trình tinh chỉnh hướng dẫn gây ra.

### 2.4 Discussion (Challenges & Future Directions)
Phần thảo luận vạch ra các hướng đi tương lai như xây dựng hệ thống Học liên tục tự động (Automatic Continual Learning) có khả năng tự lập kế hoạch và điều chỉnh chiến lược huấn luyện. Các tác giả cũng đề xuất phát triển kỹ thuật "quên có kiểm soát" (controllable forgetting) để mô hình tự động xóa bỏ thông tin lỗi thời hoặc sai lệch mà không ảnh hưởng tới năng lực cốt lõi. Cuối cùng, sự thiếu vắng của các lý thuyết phân tích sâu về cơ chế quên và tác động của huấn luyện đa tầng được nhấn mạnh như một khoảng trống nghiên cứu lớn cần lấp đầy.

---

## 3. Methodology Deep-Dive

### Framework
**Stage-aligned taxonomy** nhằm phân tầng các phương pháp Học liên tục trên LLM đồng hành cùng vòng đời phát triển thực tiễn: Continual Pre-training (CPT) $\rightarrow$ Continual Instruction Tuning (CIT) $\rightarrow$ Continual Alignment (CA).

### Core Ideas
1. **Hợp nhất loại thông tin cập nhật (Information Mapping):** Ánh xạ trực tiếp 7 loại thông tin cần cập nhật (Sự kiện, Miền tri thức, Ngôn ngữ, Tác vụ, Kỹ năng/Công cụ, Giá trị đạo đức, Sở thích) vào đúng giai đoạn tối ưu nhất (ví dụ: Fact/Domain vào CPT; Task/Skill vào CIT; Value/Preference vào CA).
2. **Hệ thống hóa giải pháp kỹ thuật:** Chia các kỹ thuật chống quên thành Data Replay (Continual-T0, DynaInst), Parameter-Efficient Tuning (Progressive Prompts, O-LoRA, ELM) và Architectural Expansion (LLaMA PRO).
3. **Định hình khái niệm "Quên chéo giai đoạn" (Cross-Stage Forgetting):** Nghiên cứu sự suy giảm tri thức của giai đoạn trước do hoạt động tinh chỉnh ở giai đoạn sau (như Instruction Tuning làm xói mòn an toàn đạo đức hay tri thức nền).

### Data / Setup / Metrics
- **Dữ liệu đánh giá CPT:** TemporalWiki (Wikipedia snapshots), Firehose (100M tweets), CKL (web & news datasets), TRACE (8 domains).
- **Dữ liệu đánh giá CIT:** CITB (1,600+ NLP tasks từ SuperNI), ConTinTin (61 tasks).
- **Metrics:** Average Performance, Forward Transfer Rate (FWT), Backward Transfer Rate (BWT), và Cross-stage evaluation scores.

### 3 Key Assumptions
1. **Stage-Specific Information Separation:** Giả định rằng các loại tri thức khác nhau (ví dụ: tri thức sự kiện thế giới vs. sở thích hành vi) được phân tách tốt nhất và chỉ nên cập nhật ở các giai đoạn riêng biệt (CPT vs. CA).
2. **Holistic Capability Improvement:** Giả định rằng CL thực sự cải thiện năng lực suy luận và ngôn ngữ nằm trong các trọng số (parametric reasoning), khác biệt hoàn toàn với cơ chế truy xuất bên ngoài như RAG.
3. **Cross-Stage Vulnerability:** Giả định rằng việc huấn luyện ở các giai đoạn sau (như CIT) vốn dĩ luôn đặt các tri thức hoặc bộ lọc an toàn đã học ở giai đoạn trước (như CA, CPT) vào trạng thái dễ bị tổn thương và bị "quên vô ý thức".

---

## 4. Brutally Honest Review (NeurIPS Style)

### Summary
This survey paper organizes the fragmented research on Large Language Model (LLM) Continual Learning into a structured, stage-aligned taxonomy (CPT, CIT, CA). By mapping the types of knowledge updates to specific standard training phases, the authors successfully define clear boundaries between actual Continual Learning and superficial patching techniques like RAG or Model Editing. It acts as a comprehensive reference cataloging benchmarks and evaluation metrics for future work.

### Strengths
- **Logical Alignment:** Structuring CL by LLM lifecycle stages (Pre-training $\rightarrow$ Instruction Tuning $\rightarrow$ Alignment) is highly practical and maps directly to real-world development pipelines.
- **Conceptual Clarity:** Effectively decouples CL (overall parametric capability improvement) from RAG (external memory retrieval) and Model Editing (local factual patching).
- **Security Alert:** Correctly identifies "cross-stage forgetting" (loss of safety guardrails or foundational knowledge during task adaptation) as a major bottleneck.

### Weaknesses
1. **Lack of Empirical Benchmarking (Entire Paper):** The survey is purely qualitative. It fails to implement and compare SOTA methods (e.g., O-LoRA vs. LLaMA PRO vs. Replay) under a unified hardware and dataset benchmark, making it difficult for practitioners to evaluate their actual utility.
2. **Underdeveloped Continual Alignment (Section 2.3 & 5):** Although CA is proposed as one of the three core pillars, the algorithmic mechanics of continually updating preference alignment (e.g., continual DPO, PPO under distribution shift) are briefly mentioned without deep analysis.
3. **Omission of Compute/Memory Costs (Section 5):** The authors advocate for parameter-efficient methods and architecture expansion, but omit a rigorous comparison of VRAM scaling, training latency, and storage overhead, which are key constraints in industrial CL.
4. **Conflating standard PEFT with CL (Section 5.2):** The paper classifies generic PEFT (e.g., LoRA, prompt tuning) as CL methods, whereas standard PEFT without specific modifications (like orthogonal projection or regularized replay) still suffers from severe catastrophic forgetting.
5. **No Theoretical Synthesis (Section 6):** While the paper notes the lack of theoretical explanations for LLM forgetting, it does not attempt to synthesize existing traditional CL theory (e.g., NTK, loss landscape geometry) to propose a theoretical direction for LLMs.

### Questions for Authors
1. Given the lack of a standardized baseline, which architectural expansion technique (e.g., LLaMA PRO vs. O-LoRA) do you hypothesize would scale best to 50+ sequential tasks without parameter explosion?
2. How does the FLOP cost of dynamic generative replay (ConPET) compare to standard periodic model re-warming on a mixed corpus?
3. How can we prevent reward model collapse or policy divergence during Continual Alignment (CA) when human preferences shift dynamically?

### Recommendation
**Accept (Borderline to Weak Accept)**  
This survey provides a valuable and intuitive taxonomical framework for the community. Although it lacks original empirical validation and leaves some algorithmic details of Continual Alignment thin, its structured lifecycle perspective and focus on cross-stage forgetting make it a strong reference paper.

---

## 5. Data & Metrics View Designer

### Datasets, Tasks, and Metrics
- **Datasets:** TemporalWiki, Firehose (100M tweets), CKL, TRACE (8 domains for CPT). CITB (1,600+ NLP tasks), ConTinTin (61 tasks for CIT).
- **Tasks:** Domain-incremental pretraining, text classification, question generation, mathematical reasoning, code generation, and alignment tuning.
- **Metrics:** Average Performance, Forward Transfer Rate (FWT), Backward Transfer Rate (BWT), and Cross-stage forgetting metrics (general knowledge accuracy and safety scores).
- **Key Baselines:** ERNIE 2.0, TAPT, Continual-T0, ConTinTin, RationaleCL, DynaInst, SLM, Progressive Prompts, ELM, O-LoRA, DAPT, LLaMA PRO, ConPET, AdaptLLM, PlugLM.

### 3 Chart Proposals
1. **Cross-Stage Forgetting Heatmap:**
   - **X-axis:** Sequence of stages (CPT $\rightarrow$ CIT $\rightarrow$ CA).
   - **Y-axis:** Performance on downstream evaluations (General QA, Safety, Factual accuracy).
   - **Group/Series:** CL methods vs. naive Fine-Tuning.
   - **Purpose:** Answers if CIT/CA updates degrade previous CPT capabilities (unconscious forgetting).
2. **Compute-Stability Frontier Plot:**
   - **X-axis:** Compute cost (FLOPs or VRAM overhead).
   - **Y-axis:** Stability metric (1 - BWT).
   - **Group/Series:** Architectural expansion (e.g. LLaMA PRO) vs. Regularization vs. Replay.
   - **Purpose:** Helps researchers select the optimal tradeoff between memory overhead and performance retention.
3. **Knowledge Update Coverage Radar Chart:**
   - **Axes (radii):** Different types of information (Fact, Domain, Task, Value, Preference, Skill, Language).
   - **Group/Series:** CPT, CIT, CA.
   - **Purpose:** Illustrates which stage of the LLM pipeline is most effective at updating which type of knowledge.

### Table Structure — Proposed Summary
| Method | Target Stage (CPT/CIT/CA) | VRAM Overhead (MB) | FWT (%) | BWT (%) | Avg. Downstream Acc (%) | Delta vs. Naive FT |
|---|---|---|---|---|---|---|
| *Baseline: Naive FT* | CPT / CIT / CA | 0 (base) | Ref | Ref | Ref | - |
| **O-LoRA** | CIT | Low | *Score* | *Score* | *Score* | *+X%* |
| **LLaMA PRO** | CPT | High | *Score* | *Score* | *Score* | *+Y%* |
| **Generative Replay** | CIT | Medium | *Score* | *Score* | *Score* | *+Z%* |

### Raw Data Extraction
**Table 2: Information updated during different stages of continual learning for LLMs** (○: Applicable, ×: Not Applicable)

| Information | Pretraining | Instruction-tuning | Alignment |
| :--- | :---: | :---: | :---: |
| **Fact** | ○ | × | × |
| **Domain** | ○ | ○ | × |
| **Language** | ○ | × | × |
| **Task** | × | ○ | × |
| **Skill (Tool use)**| × | ○ | × |
| **Value** | × | × | ○ |
| **Preference** | × | × | ○ |

---

## 6. Key Takeaways & Relevance to VN-Legal-AI / CDNCTQ

- **Phân tầng huấn luyện Pháp luật:** Đối với AI Pháp luật Việt Nam, quá trình cập nhật phải được phân tầng rõ: CPT để nạp văn bản luật mới ban hành (thêm Facts/Domain kiến thức luật), CIT để dạy các tác vụ nghiệp vụ (soạn thảo, tóm tắt bản án, trích xuất thực thể pháp lý), và CA để căn chỉnh mô hình tuân thủ quy tắc tư duy tư pháp và chuẩn mực đạo đức ngành Luật.
- **Đo lường quên chéo giai đoạn:** Khi tinh chỉnh mô hình SLM pháp lý trên các tác vụ hẹp (như soạn hợp đồng), cần chạy đánh giá hồi quy để kiểm tra xem mô hình có bị xói mòn khả năng suy luận ngôn ngữ tổng quát hoặc làm mất các bộ lọc an toàn hành vi hay không.
- **Lựa chọn cơ chế cập nhật tối ưu:** Vì luật pháp liên tục thay đổi, chỉ dùng LoRA thông thường sẽ không đủ vì nó quên rất nhanh. Cần áp dụng O-LoRA (học trên không gian trực giao để bảo toàn luật cũ) hoặc kết hợp cơ chế Replay các văn bản luật cốt lõi (Hiến pháp, Bộ luật Dân sự) trong lúc huấn luyện luật chuyên ngành mới.
