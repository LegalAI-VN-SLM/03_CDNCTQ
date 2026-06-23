# Continual Learning of Large Language Models: A Comprehensive Survey

**ArXiv:** 2404.16789  
**Authors:** Haizhou Shi, Zihao Xu, Hengyi Wang, Weiyi Qin, Wenyuan Wang, Yibin Wang, Zifeng Wang, Sayna Ebrahimi, Hao Wang (Rutgers University + Google Cloud AI Research)  
**Venue:** Survey 2024  
**Source File:** `1_ 2404.16789 _Continual Learning of Large Language Models A Comprehensive Survey.pdf`

---

## 1. High-Level Summary (5–7 câu)

Bài khảo sát này cung cấp một cái nhìn toàn diện và hệ thống hóa về các tiến bộ nghiên cứu trong việc áp dụng Học liên tục (Continual Learning - CL) lên các Mô hình Ngôn ngữ Lớn (LLMs). Điểm sáng cốt lõi của paper là việc giới thiệu khung phân loại hai chiều mới: **Vertical Continuity** (Học liên tục theo chiều dọc - quá trình chuyển đổi và thích ứng từ giai đoạn pre-training chung sang domain chuyên ngành rồi đến task cụ thể) và **Horizontal Continuity** (Học liên tục theo chiều ngang - quá trình thích ứng trực tiếp trên các phân phối dữ liệu biến đổi theo thời gian, ngôn ngữ và nội dung). Bằng việc bóc tách chu trình sống của LLM thành Continual Pre-Training (CPT), Domain-Adaptive Pre-training (DAP), và Continual Fine-Tuning (CFT), tác giả làm rõ sự khác biệt giữa các kỹ thuật chống quên ở mỗi giai đoạn. Paper cũng chỉ ra sự chuyển dịch thực tế trong ngành công nghiệp khi cấu trúc "nhà cung cấp - người tiêu dùng" (supplier-consumer) định hình cách thiết kế thuật toán CL. Tóm lại, nghiên cứu này thu hẹp khoảng cách giữa lý thuyết CL truyền thống và thực tiễn phát triển mô hình nền tảng ở quy mô lớn.

---

## 2. Section Summaries

### 2.1 Introduction
Phần giới thiệu nhấn mạnh thách thức của hiện tượng quên thảm khốc (catastrophic forgetting) khi cập nhật dữ liệu hoặc thích ứng LLM vào các domain mới. Nhóm tác giả khẳng định việc train lại mô hình từ đầu là hoàn toàn không khả thi về mặt chi phí và tài nguyên phần cứng, đặt ra yêu cầu cấp bách phải sử dụng học liên tục. Từ đó, họ định hình một sự dịch chuyển ngữ nghĩa của CL trong kỷ nguyên LLM và đề xuất khung phân loại hợp nhất dựa trên tính liên tục dọc và ngang.

### 2.2 Methodology / Taxonomy
Hệ thống phân loại hai chiều được xây dựng chi tiết, trong đó Vertical CL thể hiện quá trình chuyển dịch tri thức từ thượng nguồn xuống hạ nguồn qua ba tầng CPT $\rightarrow$ DAP $\rightarrow$ CFT. Ngược lại, Horizontal CL mô tả các kịch bản cập nhật động khi mô hình phải thích nghi với sự thay đổi theo thời gian (Temporal Shift), thay đổi nội dung (Content Shift) hoặc dịch chuyển ngôn ngữ (Language Shift) mà không có ranh giới tác vụ cố định. Giai đoạn CFT tiếp tục được chia nhỏ thành tinh chỉnh hướng dẫn (CIT), tinh chỉnh mô hình (CMR) và căn chỉnh mô hình (CMA).

### 2.3 Experiments (Evaluation & Datasets)
Phần thực nghiệm tổng hợp các giao thức và chỉ số đánh giá cốt lõi, bao gồm Hiệu năng tổng thể (OP), Khả năng quên (Forgetting/Backward Transfer) và Khả năng chuyển giao tiến (Forward Transfer). Các công cụ kiểm định kiến thức như LAMA và chỉ số đo lường tỷ lệ tri thức bị quên trên tri thức mới tiếp thu (FUAR) được hệ thống hóa cùng với các benchmark đa dạng như TimeLMs, TRACE, CITB và FEVER.

### 2.4 Discussion
Phần thảo luận khai thác các đặc tính nổi bật của LLMs, nhấn mạnh rằng các mô hình pre-trained quy mô lớn vốn dĩ nằm trong các vùng tối ưu phẳng (flat-loss basins) giúp chúng có khả năng chống quên tự nhiên tốt hơn các mô hình nhỏ. Nhóm tác giả đề xuất nới lỏng các ràng buộc khắt khe của CL truyền thống để tập trung tối ưu hóa hiệu suất tính toán thông qua cơ chế replay hiệu quả. Cuối cùng, họ chỉ ra khoảng trống lớn về mặt lý thuyết đảm bảo cho CL trên LLM và hướng đi cá nhân hóa mô hình theo sở thích khách hàng (CMA).

---

## 3. Methodology Deep-Dive

### Framework
**Khung phân loại hai chiều (Dual-axis Taxonomy):**
- **Trục dọc (Vertical Continuity):** CPT (thượng nguồn - Supplier) $\rightarrow$ DAP (trung nguồn - Consumer) $\rightarrow$ CFT (hạ nguồn - Consumer). Mục tiêu chính là bảo toàn tri thức tổng quát khi thu hẹp miền dữ liệu.
- **Trục ngang (Horizontal Continuity):** Cập nhật mô hình theo thời gian thực trước các phân phối trôi (distributional shifts) gồm Language, Content, và Temporal shifts mà không có ranh giới nhiệm vụ rõ ràng.

### Core Ideas
1. **Mô hình hóa chuỗi giá trị Supplier-Consumer:** Xác định sự bất đối xứng tài nguyên giữa nhà phát triển mô hình gốc (Supplier) và người dùng đầu cuối (Consumer) để phân bổ thuật toán phù hợp (ví dụ: Consumer ưu tiên PEFT và Replay nhỏ).
2. **Emergent Flatness Basin:** Lý giải khả năng chống quên nội tại của LLM nhờ việc các trọng số pre-trained lớn nằm trong các thung lũng loss phẳng (flat loss valleys), làm giảm tác hại của gradient cập nhật.
3. **Chỉ số đánh giá FUAR (Forgotten / Updated + Acquired Ratio):** Đo lường tỷ lệ thông tin bị quên so với lượng kiến thức được cập nhật và học mới, giúp đánh giá hành vi chi tiết hơn chỉ số OP truyền thống.

### Data / Setup / Metrics
- **Datasets:** TimeLMs (Twitter data), CC-RecentNews (CKL news), TRACE, CITB (CIT), FEVER, VitaminC (CMR), Reddit TL;DR.
- **Setups:** Đánh giá Zero-shot (ZS), Few-shot (FS), tinh chỉnh (FT) và đánh giá bằng con người/LLM-as-a-judge cho các tác vụ sinh tạo.
- **Metrics:** Overall Performance (OP), Backward Transfer (BWT / Forgetting), Forward Transfer (FWT), và FUAR.

### 3 Key Assumptions
1. **Pre-trained Flat-Loss Basin Advantage:** Giả định rằng không gian tham số của LLM đã pre-train có các vùng phẳng tự nhiên, cho phép tinh chỉnh mà không dịch chuyển quá xa khỏi vùng tối ưu cũ.
2. **Rehearsal Necessity:** Giả định rằng việc cấm hoàn toàn replay dữ liệu cũ (zero-memory CL) là phi thực tế đối với LLMs; duy trì một bộ nhớ đệm replay hiệu quả là điều bắt buộc và được chấp nhận.
3. **Lifecycle Asymmetric Resource Allocation:** Giả định rằng thiết kế thuật toán CL phải phân tách rõ ràng theo tài nguyên phần cứng (upstream CPT yêu cầu quy mô tính toán cực lớn, downstream DAP/CFT tối ưu hóa hiệu năng tính toán trên GPU đơn lẻ).

---

## 4. Brutally Honest Review (NeurIPS Style)

### Summary
This survey deconstructs Continual Learning for LLMs by introducing a dual-axis taxonomy of Vertical (hierarchical adaptation CPT $\rightarrow$ DAP $\rightarrow$ CFT) and Horizontal (temporal/content/language shifts) continuity. It maps existing PEFT, replay, and regularization approaches along these axes and compiles current evaluation metrics (OP, BWT, FWT, FUAR) and datasets.

### Strengths
- **Industrial Realism:** The Vertical vs. Horizontal taxonomy successfully models the real-world supplier-consumer dynamics of modern foundation model deployment.
- **CFT Taxonomy Granularity:** Classifying CFT into instruction tuning (CIT), model refinement (CMR), and model alignment (CMA) provides excellent structural clarity.
- **Curation Breadth:** Provides an exhaustive catalog of datasets and metrics tailored specifically for generative LLMs.

### Weaknesses
1. **Lack of Unified Empirical Benchmarking (Entire Paper):** The survey summarizes literature without running experiments. It fails to show which methods (e.g., EWC, LoRA, Mix-Review) perform best under standardized hardware and dataset conditions.
2. **Ambiguous CPT vs. DAP boundaries (Section 3 & 4):** The dividing line between Continual Pre-Training on a content shift and Domain-Adaptive Pre-training is conceptually weak. Both stages rely on self-supervised next-token prediction over unlabeled corpora, meaning the distinction is purely narrative (supplier vs. consumer) rather than algorithmic.
3. **High Theoretical Gap (Section 6.2):** The survey offers no mathematical synthesis explaining *why* model scaling affects forgetting dynamics, leaving the flat-loss basin explanation as a purely empirical observation without new generalization bounds.
4. **Superficial Treatment of Continual Alignment (Section 4.3):** Although CMA is marked as a critical stage, the survey dedicates minimal space to analyzing how CL interacts with RLHF/DPO reward model stability, focusing heavily on standard pre-training.
5. **Oversimplified Technique Categorization (Table 1 & 2):** Utilizing binary checkmarks (Rehearsal, Param. Reg., Arch. Exp.) fails to capture the subtle variations of how these techniques are modified specifically for LLM attention maps or low-rank matrices.

### Questions for Authors
1. Algorithmically, what distinct hyperparameter adjustments or objective functions separate CPT (content shift) from DAP in practice?
2. Under a restricted computational budget, does the surveyed literature suggest architecture expansion (e.g. O-LoRA) outperforms Mixture of Experts (MoE) in vertical forgetting mitigation?
3. How can we mathematically formulate the loss objective for CMA (Continual Model Alignment) to balance old and new human preference models without policy collapse?

### Recommendation
**Accept**  
The Vertical vs. Horizontal framework is highly intuitive and necessary for organizing the rapidly exploding field of LLM continual learning. While it lacks original empirical benchmarking, its dataset curation and structural clarity make it an invaluable resource for the community.

---

## 5. Data & Metrics View Designer

### Datasets, Tasks, and Metrics
- **Datasets:** TimeLMs, CC-RecentNews, CKL, TRACE, CITB, CoIN, FEVER, VitaminC, Reddit TL;DR.
- **Tasks:** Domain-adaptive pre-training, text classification, continual instruction tuning, fact-checking, question answering, summarization.
- **Metrics:** Overall Performance (OP), Backward Transfer (BWT / Forgetting), Forward Transfer (FWT), Loss, Perplexity, FUAR, Zero-Shot/Few-Shot Accuracy, Human/LLM Evaluation.
- **Key Baselines:** Parameter Freezing (P-Freeze), LoRA, K-Adapter, Mix-Review, EWC, Experience Replay (ER).

### 3 Chart Proposals
1. **Horizontal vs. Vertical Continuity Grid:**
   - **X-axis:** Horizontal Forgetting Rate (BWT).
   - **Y-axis:** Vertical Forgetting Rate (General Knowledge Degradation).
   - **Group/Series:** Individual CL methods (LoRA, Replay, EWC, O-LoRA).
   - **Purpose:** Identifies which methods balance horizontal adaptation with vertical general capability retention (ideal: top-right).
2. **Loss Landscape Flatness vs. Forgetting Rate:**
   - **X-axis:** Parameter perturbation magnitude (flatness indicator).
   - **Y-axis:** Forgetting Rate (BWT).
   - **Group/Series:** Model scales (1B, 7B, 13B).
   - **Purpose:** Verifies the assumption that larger models in flat basins forget less.
3. **FUAR Evolution Timeline:**
   - **X-axis:** Sequential training steps (timesteps).
   - **Y-axis:** FUAR (Forgotten / (Updated + Acquired) Ratio).
   - **Group/Series:** Replay ratios (0%, 10%, 25%, 50%).
   - **Purpose:** Determines when the model reaches a tipping point of forgetting under temporal shifts.

### Table Structure — Proposed Summary
| Method | Downstream Domain | Shift Type (Temporal/Content/Lang) | VRAM Overhead (MB) | Downstream Accuracy (%) | BWT (%) | FWT (%) | FUAR |
|---|---|---|---|---|---|---|---|
| *Base Model* | General | None | Reference | Reference | - | - | - |
| **TimeLMs** | Temporal News | Temporal | Low | *Score* | *Score* | *Score* | *Score* |
| **CKL (Mix-Review)**| Wikipedia | Temporal | Medium | *Score* | *Score* | *Score* | *Score* |
| **ConPET** | Multi-task CIT | Content | Low | *Score* | *Score* | *Score* | *Score* |

### Raw Data Extraction
**Table 1 Excerpt: Continual Pre-training of LLMs** (✓: Deployed, ✗: Not studied, ♣: Studied as baseline)

| Method | Dist. Shift | Rehearsal | Param. Reg. | Arch. Exp. | Pre-Training Eval | Downstream Eval |
| :--- | :---: | :---: | :---: | :---: | :---: | :---: |
| TimeLMs | Temporal | ✗ | ✗ | ✗ | ✓ | ✓ |
| LLM-Content | Content | ✗ | ✗ | ✗ | ✓ | ✗ |
| RHO-1 | Other | ✗ | ✗ | ✗ | ✓ | ✓ |
| LLM-Lang | Language | ♣ | P-Freeze ♣ | LoRA ♣ | ✓ | ✓ |
| CKL | Temporal | Mix-Review ♣ | P-Freeze ♣ | K-Adapter ♣ | ✗ | ✓ |

---

## 6. Key Takeaways & Relevance to VN-Legal-AI / CDNCTQ

- **Phân tách vai trò CPT vs. DAP trong Legal AI:** Trong dự án VN-Legal-AI, mô hình gốc (Supplier) có thể là một mô hình tiếng Việt chung hoặc mô hình đa ngôn ngữ lớn. Chúng ta thực hiện giai đoạn trung nguồn (DAP - Domain Adaptive Pre-training) bằng cách tiếp tục pre-train mô hình trên toàn bộ hệ thống văn bản pháp luật Việt Nam (unlabeled corpus). Sau đó, thực hiện CFT (Continual Fine-Tuning) bằng cách tinh chỉnh hướng dẫn (CIT) cho các tác vụ pháp lý cụ thể.
- **Khai thác đặc tính Loss Flatness của SLMs lớn:** Theo lý thuyết flat-loss basin, việc sử dụng các mô hình SLM có kích thước lớn hơn một chút (ví dụ Qwen-7B thay vì Qwen-1.5B) sẽ mang lại khả năng chống quên tri thức tổng quát tốt hơn khi chúng ta tinh chỉnh sâu vào miền dữ liệu luật pháp chuyên ngành.
- **Áp dụng chỉ số FUAR để kiểm tra độ quên pháp luật:** Trong quá trình cập nhật các văn bản luật mới ban hành theo thời gian (Horizontal Temporal Shift), chúng ta nên áp dụng chỉ số FUAR để theo dõi chính xác tỷ lệ các điều luật cũ bị quên so với lượng luật mới được mô hình tiếp thu, thay vì chỉ đo hiệu năng trung bình (OP) trên tập test.
