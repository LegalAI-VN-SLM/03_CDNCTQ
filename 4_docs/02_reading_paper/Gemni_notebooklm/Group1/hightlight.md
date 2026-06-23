# Group 1 — Highlights: Figures, Tables, and Key Taxonomies

**Group:** Surveys & Overviews (4 papers)  
**Saved:** E:\DoCode\1 VN-Legal-AI\03_CDNCTQ\4_docs\02_reading_paper\Suvery\hightlight.md

> ⚠️ NOTE: Các figures và tables dưới đây được **reconstruct từ text** vì không thể extract trực tiếp từ PDF. Các cấu trúc và nội dung bảng là chính xác từ paper; layout hình vẽ là approximation.

---

## Paper 1 — Continual Learning for LLMs: A Survey (2402.01364)

### Figure 1: Pipeline So Sánh Linear vs. Multi-Stage
```
Traditional PLMs Adaptation (Linear):
[Pre-training] ──→ [Fine-tuning] ──→ [Deployment]

LLM Continual Learning (Multi-Stage Hybrid):
               ┌──→ Continual Pre-training (CPT) ──┐
[Pre-trained] ─┼──→ Continual Instruction Tuning (CIT) ──┼──→ [Dynamic Deployment]
               └──→ Continual Alignment (CA) ──────┘
```

**Message:** Huấn luyện LLM học liên tục không phải là đường thẳng đơn giản, mà là sự cập nhật đồng thời/xen kẽ nhiều giai đoạn phức tạp có sự phụ thuộc lẫn nhau.

### Figure 2: Khung Đọc Framework của Tác Giả
```
                   LLM Continual Learning Framework
                                 │
         ┌───────────────────────┼───────────────────────┐
         ▼                       ▼                       ▼
Continual Pre-training (CPT)  Continual Instruction   Continual Alignment (CA)
   [Self-Supervised]              Tuning (CIT)          [Human/AI Feedback]
         │                    [Supervised Pairs]                 │
 ┌───────┴───────┐               ┌───────┴───────┐        ┌──────┴──────┐
 ▼               ▼               ▼               ▼        ▼             ▼
Updating     Adapting to      Task-           Domain-   Adhering      Adhering to
Facts        New Domains      Incremental     Adapting  to Safety     Preferences
```

### Table 2: Loại Thông Tin Cập Nhật Theo Giai Đoạn (Reconstructed)
*(○: Có áp dụng tốt nhất, ×: Không phù hợp/Không áp dụng)*

| Loại tri thức / Thông tin | Pre-training (CPT) | Instruction-tuning (CIT) | Alignment (CA) |
| :--- | :---: | :---: | :---: |
| **Fact (Sự kiện)** | ○ | × | × |
| **Domain (Miền tri thức)** | ○ | ○ | × |
| **Language (Ngôn ngữ mới)**| ○ | × | × |
| **Task (Tác vụ mới)** | × | ○ | × |
| **Skill (Kỹ năng/Công cụ)**| × | ○ | × |
| **Value (Chuẩn mực đạo đức)**| × | × | ○ |
| **Preference (Sở thích)** | × | × | ○ |

---

## Paper 2 — Continual Learning of LLMs: A Comprehensive Survey (2404.16789)

### Figure 1: Trục Tọa Độ Hai Chiều (Vertical vs. Horizontal Continuity)
```
Vertical Continuity (Chiều Dọc - Vòng đời thích ứng):
   [Upstream Supplier: General CPT]
                 │
                 ▼
   [Downstream Consumer: Domain-Adaptive Pre-training (DAP)]
                 │
                 ▼
   [Downstream Consumer: Continual Fine-Tuning (CFT)] (CIT/CMR/CMA)

Horizontal Continuity (Chiều Ngang - Dịch chuyển phân phối sau triển khai):
   Model updates sequentially over time:
   Task 1 ──→ Task 2 ──→ ... ──→ Task T (Temporal / Content / Language Shifts)
```

**Message:** CL trong kỷ nguyên LLM phân mảnh thành hai bài toán: chiều dọc (supplier-consumer transfer) và chiều ngang (sequential tasks over time).

### Table 1: Excerpt of Upstream Continual Pre-training
*(✗ = Không nghiên cứu, ✓ = Có triển khai, ♣ = Được so sánh làm baseline)*

| Phương pháp | Dịch chuyển | Rehearsal | Param. Reg. | Arch. Exp. | CPT Eval | Downstream Eval |
| :--- | :---: | :---: | :---: | :---: | :---: | :---: |
| **TimeLMs** | Temporal | ✗ | ✗ | ✗ | ✓ | ✓ |
| **CKL** | Temporal | Mix-Review ♣ | P-Freeze ♣ | K-Adapter ♣ | ✗ | ✓ |
| **RHO-1** | Other (Loss) | ✗ | ✗ | ✗ | ✓ | ✓ |

### Table 3 Excerpt: Continual Fine-Tuning Paradigms
- **TIL (Task-Incremental):** Model được cung cấp Task ID lúc inference.
- **DIL (Domain-Incremental):** Định dạng chung, không cung cấp Task ID lúc inference.
- **CIL (Class-Incremental):** Model phải tự suy luận ra Task ID khi chạy. (Đã lỗi thời trong LLM sinh tạo).

---

## Paper 3 — Towards Lifelong Learning of LLMs: A Survey (2406.06391)

### Figure 2: Phân Tách Tri Thức Nội Tại và Ngoại Vi
```
                       Lifelong Learning of LLMs
                                   │
         ┌─────────────────────────┴─────────────────────────┐
         ▼                                                   ▼
  Internal Knowledge                                  External Knowledge
(gradient-based weight updates)                      (non-parametric bypass)
  ┌──────┴───────────────────────┐                    ┌──────┴──────┐
  ▼                              ▼                    ▼             ▼
Continual Pretraining    Continual Finetuning   Retrieval-Based  Tool-Based
- Vertical Domain       - Task-Specific         (RAG pipelines)  (API calls)
- Language Domain       - Task-Agnostic (Editing)
- Temporal Domain
```

**Message:** Có thể học suốt đời mà không cần cập nhật trọng số bằng cách sử dụng các hệ thống lưu trữ/truy xuất bên ngoài (RAG) hoặc sử dụng Tools.

### Figure 3: Bốn Nhánh Kỹ Thuật Phổ Biến
```
(a) Replay-Based:         (b) Regularization-Based:  (c) Architecture-Based:  (d) Distillation-Based:
[Memory Buffer]            [Gradient Penalty]         [Adapters / PEFT]       [Teacher Logits]
   Data A ──┐                 Loss_total =               Freeze base            Loss_KD =
            ├─→ train            Loss_new +           [Base Model]            KL(P_teacher ||
   Data B ──┘                 λ·||θ_t - θ_old||²        └─→ [LoRA experts]         P_student)
```

### Figure 4: Sáu Dạng Thiết Kế Kiến Trúc
- **(a) Prompt Tuning:** Thêm token ảo vào đầu chuỗi đầu vào.
- **(b) Prefix Tuning:** Chèn thêm các key-value pairs vào attention layers.
- **(c) LoRA:** Thêm các ma trận tích hợp low-rank ($W + A \cdot B$) song song.
- **(d) Adapters:** Chèn thêm các lớp FFN nhỏ sau attention/FFN layer.
- **(e) Mixture of Experts (MoE):** Nhân bản và phân bổ các FFN experts chuyên biệt.
- **(f) Model Expansion:** Thêm trực tiếp các block transformer mới lên trên.

### Table 1: So Sánh Các Phương Pháp CIT / CFT Nổi Bật (2023-2024)
| Phương pháp | Backbone | Dataset | PEFT | Replay | Distillation | Regularization | Architecture |
| :--- | :--- | :--- | :---: | :---: | :---: | :---: | :---: |
| **LAMOL** | GPT-2 | SuperNI | No | Pseudo | No | No | No |
| **O-LoRA** | LLaMA, T5 | GLUE / SuperGLUE | LoRA | No | No | Yes | Yes (Orthogonal) |
| **ProgPrompt** | T5, BERT | GLUE / SuperGLUE | Prompt | No | No | No | Yes (Dynamic) |
| **SAPT** | LLaMA, T5 | SuperNI | LoRA | Pseudo | No | No | Yes (Dynamic) |
| **SLM** | LLaMA, T5 | AGNews, Yelp | LoRA | No | No | No | Yes (Retrieval-LM) |

---

## Paper 4 — Continual Learning for Generative AI (2506.13045)

### Figure 3: Khung Cực Cấu Trúc Generative CL
```
                             Generative CL
                                   │
         ┌───────────────┬─────────┴─────┬───────────────┐
         ▼               ▼               ▼               ▼
        LLM             MLLM            VLA          Diffusion
    [Text Only]     [Text+Image]     [Robotics]     [Generation]
  - CIT / CPT     - VQA / Ground    - Action outputs - Custom concepts
```

### Figure 4: Reconstruct Cơ Chế Phát Lại Sinh Tạo (Generative Replay)
```
[Previous Model (t-1)] ──→ auto-generate ──→ [Pseudo Data / Tokens] ──┐
                                                                       ├─→ [Joint Train] ─→ [Active Model]
[Current Task Data (t)] ───────────────────────────────────────────────┘
```

**Message:** Generative Replay bảo vệ quyền riêng tư bằng cách biến mô hình cũ thành bộ tạo dữ liệu giả lập để làm dữ liệu ôn tập cùng dữ liệu tác vụ mới.

### Table Summary: Modality & Tasks Across Domains (2025 Survey)
| Domain | Input Modality | Target Output | Primary Challenge | SOTA Baseline |
| :--- | :---: | :---: | :--- | :--- |
| **LLM** | Text | Text tokens | Factual/Temporal decay | O-LoRA, LLaMA PRO |
| **MLLM** | Image + Text | Text tokens | Cross-modal interference | Eproj, MoELoRA |
| **VLA** | Image + Text + Sensor | Continuous Robot Actions | Environmental action drift | Lifelong IRL |
| **Diffusion**| Text prompt | Pixel latents | Style/Concept deletion | Concept Regularization |

---

## 🔑 Cross-Paper Survey Insights & Rules of Thumb

1. **Replay vs. Parameter Freezing:** Hầu hết các bài survey đều đồng ý rằng Replay (đặc biệt là Generative Replay) và Parameter-Efficient Tuning (PEFT) là 2 cột trụ vững chắc nhất để triển khai CL thực tiễn trong ngành công nghiệp.
2. **Alignment Tax vs. Plasticity:** Trong Continual Alignment (CA), việc liên tục điều chỉnh mô hình theo sở thích hoặc độ an toàn mới sẽ sinh ra một khoản "thuế căn chỉnh" (alignment tax), vô tình làm giảm năng lực suy luận Logic tổng thể của mô hình.
3. **Flat-Loss Basin Advantage:** Cả Paper 2 và 4 đều đề cập đến thuộc tính nổi trội (emergent properties) của LLM: khi mô hình lớn hơn, landscape của loss phẳng hơn, giúp mô hình ít bị trôi trọng số và giữ bộ nhớ tốt hơn hẳn so với các mô hình CNN/RNN nhỏ trước đây.
4. **Shift to External Knowledge:** Để hạn chế catastrophic forgetting hoàn toàn trên các facts thời gian thực, xu hướng công nghiệp đang chuyển dịch mạnh mẽ sang tích hợp RAG và APIs gọi ngoài thay vì tinh chỉnh trực tiếp các tham số.
