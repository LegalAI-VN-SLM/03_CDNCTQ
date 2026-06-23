# Towards Lifelong Learning of Large Language Models: A Survey

**ArXiv:** 2406.06391  
**Authors:** Junhao Zheng, Shengjie Qiu, Chengming Shi, Qianli Ma (South China University of Technology)  
**Venue:** Survey 2024  
**Source File:** `1_ 2406.06391 _Towards Lifelong Learning of Large Language Models A Survey.pdf`

---

## 1. High-Level Summary (5–7 câu)

Bài khảo sát này hệ thống hóa các phương pháp Học suốt đời (Lifelong Learning - còn gọi là Continual Learning) cho các Mô hình Ngôn ngữ Lớn (LLMs). Điểm đổi mới cốt lõi là việc phân chia các hướng tiếp cận thành hai nhóm chính: **Internal Knowledge** (hấp thụ tri thức thông qua cập nhật trọng số tham số của mô hình) và **External Knowledge** (tích hợp tri thức bên ngoài qua truy xuất RAG hoặc sử dụng công cụ/API mà không cần thay đổi trọng số). Nhóm tác giả xây dựng một taxonomy chi tiết bao trùm 12 kịch bản học liên tục từ hẹp đến rộng (như Text Classification, NER, MT, Instruction Tuning, Knowledge Editing, Alignment, và RAG). Nghiên cứu chỉ ra các xu hướng quan trọng trong kỷ nguyên LLM như việc chuyển dịch từ tinh chỉnh toàn bộ sang tinh chỉnh một phần (PEFT/LoRA) và từ tri thức tham số sang tri thức ngoài. Tóm lại, đây là công trình đầu tiên bao phủ toàn bộ 12 kịch bản này, mang lại một bản đồ hướng dẫn chi tiết cho các nhà nghiên cứu LLM.

---

## 2. Section Summaries

### 2.1 Introduction
Phần mở đầu phân tích tính chất tĩnh của dữ liệu pre-train khiến LLM nhanh chóng bị lỗi thời và gặp hiện tượng quên thảm khốc khi cập nhật thông tin mới. Để giải quyết, tác giả đề xuất phân loại lifelong learning thành nhóm tri thức nội tại (Internal Knowledge) và tri thức ngoại vi (External Knowledge). Bài báo định vị mình là khảo sát đầu tiên bao phủ 12 kịch bản chi tiết, bao gồm cả các tác vụ NLP truyền thống và các kỹ thuật mới nổi như chắp vá tri thức ngoại vi.

### 2.2 Methodology / Taxonomy (12 Scenarios & Internal/External)
Hệ thống phân loại 12 kịch bản được chia làm hai nhóm. Nhóm Internal Knowledge bao gồm Continual Pretraining (dọc, ngôn ngữ, thời gian) và Continual Finetuning chia thành tác vụ chuyên biệt (text classification, NER, relation extraction, machine translation) và tác vụ tổng quát (instruction tuning, knowledge editing, alignment). Nhóm External Knowledge gồm các phương thức Retrieval-Based (truy xuất nâng cao RAG) và Tool-Based (gọi API).

### 2.3 Experiments (Benchmarks & Evaluation)
Phần thực nghiệm tổng hợp các thiết lập nghiên cứu trong tài liệu cũ. Tác giả định nghĩa các chỉ số đo lường hiệu năng học suốt đời bao gồm Độ chính xác trung bình (AA), Độ chính xác tăng dần trung bình (AIA), Khả năng chuyển giao tiến (FWT) và Khả năng chuyển giao lùi (BWT). Các benchmark phổ biến như chuỗi 5 tác vụ phân loại văn bản tiêu chuẩn hay chuỗi 15 tác vụ dài (kết hợp GLUE và SuperGLUE) được tổng hợp cụ thể.

### 2.4 Discussion
Phần thảo luận chỉ ra các thách thức lớn như thế tiến thoái lưỡng nan giữa tính dẻo và tính ổn định (Plasticity-Stability Dilemma), "thuế liên kết" (alignment tax) và chi phí tính toán đắt đỏ. Các tác giả nhận diện 3 xu hướng chuyển dịch chính: từ tác vụ chuyên biệt sang tác vụ tổng quát, từ full fine-tuning sang PEFT, và từ tri thức nội tại sang tri thức ngoại vi. Cuối cùng, các hướng đi tương lai như học suốt đời đa phương thức và khai thác cửa sổ ngữ cảnh siêu lớn (in-context lifelong learning) được đề xuất.

---

## 3. Methodology Deep-Dive

### Framework
**Khung phân loại 12 kịch bản (12-Scenario Taxonomy):**
- **Internal Knowledge:** Gradient-based updates. CPT (Vertical, Language, Temporal) + CFT [Task-Specific (Classification, NER, RE, MT) & Task-Agnostic (Instruction-Tuning, Knowledge Editing, Alignment)].
- **External Knowledge:** Non-parametric methods. Retrieval-based (RAG) + Tool-based (API calling).

### Core Ideas
1. **Phân rã Tri thức Nội tại vs. Ngoại vi:** Mở rộng khái niệm học suốt đời bằng cách công nhận RAG và API-calling là các phương pháp học liên tục "ngoại vi" (bộ nhớ ngoài), giúp mô hình cập nhật facts thời gian thực mà không làm hỏng không gian biểu diễn tham số.
2. **Generative Replay nâng cao (LAMOL, LFPT5):** Tách biệt việc lưu dữ liệu cũ (gây vi phạm quyền riêng tư GDPR) bằng cách bắt LLM tự sinh ra các mẫu dữ liệu giả (pseudo-samples) dựa trên tiền tố tác vụ (task prefixes) để làm tài liệu ôn tập.
3. **MoE động trong CL (ModuleFormer, Lifelong-MoE):** Tận dụng kiến trúc hỗn hợp chuyên gia (MoE) để kích hoạt/thêm các chuyên gia mới chuyên biệt cho tác vụ mới, giúp cô lập vùng trọng số và loại bỏ xung đột thông tin.

### Data / Setup / Metrics
- **CL Benchmarks:** AG News, Amazon, Yelp, DBpedia, Yahoo (classification).
- **Long-Sequence Benchmark:** 15 tasks kết hợp GLUE và SuperGLUE.
- **Metrics:** Average Accuracy (AA), Average Incremental Accuracy (AIA), Forward Transfer (FWT), Backward Transfer (BWT).

### 3 Key Assumptions
1. **Parametric-Non-Parametric Orthogonality:** Giả định rằng tri thức sự kiện (facts) có thể tách rời và xử lý độc lập bởi RAG/Tools, trong khi năng lực ngôn ngữ và logic bắt buộc phải nằm trong trọng số mô hình.
2. **Representation Space Isolation via Adapters:** Giả định rằng việc thêm các adapter/PEFT mới sẽ cô lập hoàn toàn biểu diễn của từng tác vụ mới, giải quyết triệt để sự quên thảm khốc mà không làm giảm dung lượng học tập của mô hình.
3. **Data Contamination Uniformity:** Giả định rằng các bộ dữ liệu benchmark lịch sử (như GLUE) được dùng để đánh giá CL không bị rò rỉ (contamination) từ pha pre-training ban đầu của các backbone mạnh (LLaMA/GPT).

---

## 4. Brutally Honest Review (NeurIPS Style)

### Summary
This paper presents a comprehensive survey of LLM Lifelong Learning, establishing a 12-scenario taxonomy categorized into Internal Knowledge (gradient-based parameter updates) and External Knowledge (non-parametric retrieval/tool-use). It maps traditional NLP classification/extraction tasks alongside modern instruction-tuning and model alignment, summarizing metrics (AA, AIA, BWT, FWT) and PEFT integrations.

### Strengths
- **Modernized Taxonomy:** Incorporating RAG and Tool-use under "External Knowledge" is a highly relevant shift matching how modern industries bypass parametric forgetting.
- **Granular Scenario Coverage:** Bridging historical NLP tasks (NER, RE, MT) with contemporary generative paradigms (alignment, editing, CIT) is highly thorough.
- **Methodological Table Mapping:** Tables 1 and 2 map specific baselines (LAMOL, O-LoRA, SSR, SLM) directly to their code bases, PEFT choices, and CL strategies.

### Weaknesses
1. **Lack of Original Empirical Comparisons (Entire Paper):** The survey catalogs methods extensively but fails to perform any unified baseline runs. It does not provide empirical validation indicating whether Mixture of Experts (MoE) systematically outperforms orthogonal subspaces in large models.
2. **Definitional Stretch of "Lifelong Learning" (Section 2 & 5):** Classifying RAG and Tool-use as "External Knowledge Lifelong Learning" stretches the mathematical definitions of CL (sequential parameter optimization). These are retrieval and in-context techniques, not learning, as they do not solve weight representation stability.
3. **Superficial Treatment of Knowledge Editing (Section 4.2):** While listed as a CFT scenario, the survey fails to analyze the mathematical constraints of direct weight editing (e.g., ROME, MEMIT) and how editing collisions behave under continuous operations.
4. **Omission of Replay Safety / Privacy (Section 2.3.1):** Experience Replay is praised as the most common baseline, but the paper ignores GDPR, privacy risks, and data storage policies associated with storing user interactions in memory buffers over time.
5. **Backbone Scale Inconsistency (Section 6):** The survey treats RoBERTa/T5-based studies interchangeably with LLaMA/GPT-3 scale studies, ignoring whether emergent properties in large models qualitatively alter forgetting dynamics.

### Questions for Authors
1. Under sequential updates, how does direct parameter editing (e.g., ROME) scale over 100+ continuous facts compared to simple RAG?
2. Since RAG and Tool-use do not update model weights, is it mathematically precise to characterize them as "learning" or simply "in-context prompting"?
3. What specific privacy-preserving algorithms do you propose for Experience Replay to comply with data deletion requests (GDPR) in production systems?

### Recommendation
**Accept**  
This survey is exceptionally thorough, mapping 12 scenarios that link NLP history with the LLM era. The decoupling of Internal vs. External knowledge reflects a deep understanding of current engineering constraints.

---

## 5. Data & Metrics View Designer

### Datasets, Tasks, and Metrics
- **Datasets:** GLUE, SuperGLUE, AG News, Amazon, Yelp, DBpedia, Yahoo, SQuAD, S2ORC, ScienceQA, MedMCQA, MMLU.
- **Tasks:** Text classification, named entity recognition, relation extraction, machine translation, instruction tuning, knowledge editing, alignment, RAG.
- **Metrics:** Average Accuracy (AA), Average Incremental Accuracy (AIA), Forward Transfer (FWT), Backward Transfer (BWT).
- **Key Baselines:** LAMOL, O-LoRA, ProgPrompt, SAPT, I-LoRA, SSR, SLM.

### 3 Chart Proposals
1. **Scenario Research Density Radar Chart:**
   - **Axes (radii):** 12 scenarios.
   - **Value:** Number of SOTA papers.
   - **Purpose:** Highlights where research is saturated (e.g., Instruction Tuning) vs. open areas (e.g., Continual NER/RE).
2. **Sequence Length vs. Cumulative BWT:**
   - **X-axis:** Sequential tasks (up to 15 tasks).
   - **Y-axis:** Cumulative BWT (forgetting).
   - **Group/Series:** PEFT (LoRA) vs. Full FT vs. Replay-augmented PEFT.
   - **Purpose:** Illustrates the degradation rate over long task sequences.
3. **Parametric vs. Non-Parametric Cost-Accuracy Frontier:**
   - **X-axis:** Latency/Inference cost.
   - **Y-axis:** Temporal QA accuracy.
   - **Group/Series:** Internal (CPT/DAP updates) vs. External (RAG with different retriever scales).
   - **Purpose:** Guides developers in deciding whether to update model weights or use retrieval at inference.

### Table Structure — Proposed Summary
| Method | Scenario | Parameter Updates (Yes/No) | VRAM/Inference Cost | AA (%) | AIA (%) | BWT (%) | FWT (%) | Code Link |
|---|---|---|---|---|---|---|---|---|
| **LAMOL** | CIT | Yes (Full) | High | *Score* | *Score* | *Score* | *Score* | [Link](https://github.com) |
| **O-LoRA** | CIT | Yes (PEFT) | Low | *Score* | *Score* | *Score* | *Score* | [Link](https://github) |
| **SLM** | CIT | Yes (PEFT) | Medium | *Score* | *Score* | *Score* | *Score* | [Link](https://github) |
| **DPR / RAG** | RAG | No | High Latency | *Score* | *Score* | *Score* | *Score* | [Link](https://github) |

### Raw Data Extraction
**Table 1 Excerpt: CIT & CFT Representative Methods**

| Method | Year | Backbone | Dataset | PEFT | Replay | Distillation | Regularization | Architecture |
| :--- | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| HMI-LAMOL | 2023 | GPT-2, BERT | SST, AGNews, Yelp | No | Pseudo | No | No | No |
| DMEA | 2023 | GPT-2, BERT | CNN/DailyMail | Adapters | No | No | No | No |
| O-LoRA | 2023 | LLaMA, T5 | GLUE, SuperGLUE | LoRA | No | No | Yes | Yes (Orthogonal) |
| ProgPrompt | 2023 | T5, BERT | GLUE, SuperGLUE | Prompt | No | No | No | Yes |
| SAPT | 2024 | LLaMA, T5 | SuperNI, GLUE | LoRA | Pseudo | No | No | Yes |
| SSR | 2024 | LLaMA | SuperNI | LoRA | Pseudo | No | No | No |
| SLM | 2024 | LLaMA, T5 | AGNews, Yelp | LoRA | No | No | No | Yes (Retrieval-LM) |

---

## 6. Key Takeaways & Relevance to VN-Legal-AI / CDNCTQ

- **Phối hợp Tri thức Nội tại và Ngoại vi:** Đối với mô hình pháp luật Việt Nam, chúng ta không thể chỉ dựa vào việc cập nhật tham số (Internal Knowledge) vì luật sửa đổi liên tục và việc học gradient có thể gây quên luật cũ. Chiến lược tối ưu là kết hợp: CPT/DAP (Internal) để dạy mô hình ngữ cảnh tiếng Việt pháp lý, thuật ngữ hành chính và cấu trúc văn bản luật, kết hợp với RAG (External) để truy xuất chính xác nội dung các điều luật cụ thể tại thời điểm suy luận nhằm triệt tiêu ảo giác.
- **Continual Named Entity Recognition (NER) & Relation Extraction (RE) cho văn bản pháp lý:** Một phần quan trọng trong dự án là trích xuất thực thể pháp lý (điều khoản, chủ thể, mức phạt). Áp dụng các phương pháp kiến trúc trong Table 1 (như Adapters hoặc O-LoRA) sẽ giúp mô hình liên tục học các kiểu thực thể mới (ví dụ loại tội danh mới xuất hiện) mà không làm suy giảm hiệu năng trích xuất các thực thể cơ bản đã học trước đó.
- **Sử dụng Generative Replay bảo mật:** Khi thu thập các truy vấn pháp lý của người dùng để cải thiện mô hình, chúng ta không được phép lưu trữ trực tiếp các câu hỏi thật (do quy định bảo mật thông tin khách hàng). Việc áp dụng Generative Replay (bắt SLM tự sinh câu hỏi giả dựa trên phân phối cũ) là một giải pháp thiết thực để huấn luyện liên tục.
