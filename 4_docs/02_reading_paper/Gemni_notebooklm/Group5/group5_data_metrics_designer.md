# Tổng hợp "Data & Metrics View Designer" cho Group 5

Tài liệu này tổng hợp toàn bộ phần thiết kế trực quan hóa dữ liệu, thước đo đánh giá (metrics), baselines và các bảng số liệu thô (raw data) của 7 bài báo thuộc **Group 5 (Continual Learning, Legal NLP & Memory RAG Benchmarks)**.

---

## 1. Paper 1: Mitigating Catastrophic Forgetting in Fine-Tuned LLMs: An Experimental Study of LoRA and O-LoRA

### 1. Tóm tắt thiết lập thực nghiệm
*   **Datasets & Tasks:**
    *   *Alpaca (52K instructions):* Tinh chỉnh chỉ dẫn đơn tác vụ.
    *   *Simp (Text simplification) $\rightarrow$ Exp (Explanation generation) $\rightarrow$ InqQG (Inquisitive question generation):* Chuỗi tác vụ học liên tục.
    *   *Tập kiểm thử:* TruthfulQA (MC1, MC2), BLiMP Causative, MMLU GlobalFacts, Arithmetic Subtraction (2-digit, 4-digit), MMLU (STEM, Social, Human, Other), Reasoning (BoolQ, PIQA, Winogrande, HellaSwag, MathQA, Mutual), RACE (high/middle).
*   **Baselines:** Fine-Tuning (FT), LoRA (với các cấu hình $r$ và $\alpha$), Prefix-Tuning, P-Tuning, Prompt-Tuning, NEFTune+LoRA, O-LoRA.
*   **Metrics:** Accuracy (%) cho các tác vụ đơn; Forgetting Gradient (FG %) cho học liên tục.

### 2. Đề xuất trực quan hóa (Charts)
1.  **Bar Chart (So sánh hiệu năng chống quên):**
    *   *Trục X:* Các phương pháp CL (Full FT, LoRA, Prefix-Tuning, NEFTune, O-LoRA).
    *   *Trục Y:* Forgetting Gradient (FG %) - thấp hơn là tốt hơn.
    *   *Nhóm (Series):* Domain Knowledge, Reasoning, Reading Comprehension.
    *   *Mục đích:* So sánh trực quan khả năng bảo toàn tri thức của O-LoRA so với các PEFT khác.
2.  **Line Chart (Độ nhạy của tỷ lệ $\alpha/r$ trên toán học):**
    *   *Trục X:* Các giá trị hệ số tỷ lệ $\alpha$ (1, 16, 32, 64, 128).
    *   *Trục Y:* Accuracy (%) trên tác vụ `arithmetic_2ds`.
    *   *Nhóm (Series):* LoRA (r=64) vs. O-LoRA (r=64).
    *   *Mục đích:* Minh họa điểm sụp đổ hiệu năng khi $\alpha$ vượt ngưỡng chịu đựng.

### 3. Đề xuất cấu trúc bảng
*   **Bảng tổng hợp:** `[Method | Domain Knowledge FG (%) | Reasoning FG (%) | Reading Comprehension FG (%) | Peak Memory (GB)]`
*   *Highlight:* Bold cho FG thấp nhất, in nghiêng cho các cấu hình PEFT bị sụp đổ hiệu năng số học.

### 4. Số liệu thô (Raw Data)
```markdown
| Method / Setting | Domain Knowledge FG | Reasoning FG | Reading Comprehension FG |
| :--- | :---: | :---: | :---: |
| Full Fine-Tuning | 42.45 | 37.90 | 42.29 |
| LoRA ($r=64, \alpha=1$) | 30.79 | 20.03 | 19.69 |
| LoRA ($r=128, \alpha=1$) | 38.87 | 40.46 | 43.90 |
| LoRA ($r=64, \alpha=16$) | 40.43 | 40.73 | 43.66 |
| LoRA ($r=64, \alpha=128$) | 42.71 | 40.90 | 44.95 |
| Prefix-Tuning | 37.15 | 38.54 | 42.05 |
| P-Tuning | 36.51 | 38.54 | 42.05 |
| Prompt-Tuning | 38.39 | 37.39 | 41.81 |
| NEFTune + LoRA (noise=1) | 40.55 | 40.84 | 43.90 |
| NEFTune + LoRA (noise=5) | 42.40 | 40.94 | 42.69 |
| O-LoRA ($r=64, \lambda=0.5$) | 1.11 | 0.43 | -1.20 |
| O-LoRA ($r=128, \lambda=0.5$) | 0.77 | 0.25 | -0.24 |
| O-LoRA ($r=64, \alpha=16, \lambda=0.5$) | 3.49 | -0.16 | -0.88 |
| O-LoRA ($r=64, \alpha=128, \lambda=0.5$) | 1.70 | 0.51 | 1.20 |
```

---

## 2. Paper 2: Overview of the LegalSLM Shared Task: Evaluating Legal Reasoning of Vietnamese Small Language Models

### 1. Tóm tắt thiết lập thực nghiệm
*   **Datasets & Tasks:**
    *   *Multiple-Choice Legal Knowledge (MC):* 800 mẫu, trắc nghiệm 4 đáp án.
    *   *Legal Natural Language Inference (NLI):* 850 mẫu, nhãn nhị phân {Có, Không}.
    *   *Legal Syllogism Reasoning (Syllogism):* 300 mẫu, sinh tự do, đánh giá theo 4 tiêu chí.
*   **Baselines:** Qwen-3-1.7B/4B Base, Qwen-3-1.7B/4B ours (BTC tiếp tục pretrain), và 10 đội tham gia leaderboard.
*   **Metrics:** Accuracy cho MC và NLI; Rubric-based score (0 - 1.0) cho Syllogism.

### 2. Đề xuất trực quan hóa (Charts)
1.  **Grouped Bar Chart (So sánh hiệu năng Top 5 đội thi):**
    *   *Trục X:* Tên đội thi (Bosch@AI, MinLegal, URAx, Innovation-LLM, LICTU).
    *   *Trục Y:* Score (%).
    *   *Nhóm (Series):* vi-law-nli, vi-law-qa, vilaw-syllo.
    *   *Mục đích:* Làm nổi bật hiện trạng các đội đạt điểm rất cao ở trắc nghiệm/NLI nhưng tụt sâu ở tự luận Syllogism.
2.  **Bar Chart (Tác động của Continued Pretraining):**
    *   *Trục X:* Các cấu hình mô hình (Qwen-3-1.7B vs. Qwen-3-4B).
    *   *Trục Y:* Average Score hoặc Syllogism Score.
    *   *Nhóm (Series):* Base (Original) vs. Continued Pretrained (Ours).
    *   *Mục đích:* Thể hiện trực quan tác động tích cực của pretraining lên mô hình 4B nhưng gây suy giảm điểm ở mô hình 1.7B.

### 3. Đề xuất cấu trúc bảng
*   **Bảng tổng hợp:** `[Hạng | Tên đội / Mô hình | vi-law-nli | vi-law-qa | vilaw-syllo | Avg | Phân loại (Đội thi/Baseline)]`
*   *Highlight:* Bold cho điểm cao nhất ở mỗi tác vụ, in nghiêng các baseline của BTC.

### 4. Số liệu thô (Raw Data)
```markdown
| Team / Model | vi-law-nli | vi-law-qa | vilaw-syllo | Avg | Type |
| :--- | :---: | :---: | :---: | :---: | :--- |
| Bosch@AI Team | 0.9700 | 0.9267 | 0.5358 | 0.8108 | Team |
| MinLegal | 0.9800 | 0.8733 | 0.5308 | 0.7947 | Team |
| URAx | 0.9450 | 0.8333 | 0.5767 | 0.7850 | Team |
| Innovation-LLM | 0.9567 | 0.8367 | 0.5417 | 0.7784 | Team |
| LICTU | 0.8467 | 0.8067 | 0.5375 | 0.7303 | Team |
| NHK | 0.9333 | 0.8683 | 0.3275 | 0.7097 | Team |
| PSLV-Warrior | 0.8517 | 0.7483 | 0.5250 | 0.7083 | Team |
| Abe | 0.8200 | 0.8400 | 0.2875 | 0.6492 | Team |
| NLPhi | 0.6517 | 0.8150 | 0.4792 | 0.6486 | Team |
| Nguyen Quang Thao | 0.5816 | 0.8217 | 0.3800 | 0.5944 | Team |
| Qwen 3 - 4B (ours) | 0.9600 | 0.8500 | 0.5950 | 0.8010 | Baseline |
| Qwen 3 - 4B - Base (original) | 0.9700 | 0.8210 | 0.5160 | 0.7690 | Baseline |
| Qwen 3 - 1.7B - Base (original) | 0.5617 | 0.7933 | 0.4680 | 0.6076 | Baseline |
| Qwen 3 - 1.7B (ours) | 0.6450 | 0.7867 | 0.3840 | 0.6050 | Baseline |
```

---

## 3. Paper 3: Enhancing Long-term RAG Chatbots with Psychological Models of Memory Importance and Forgetting

### 1. Tóm tắt thiết lập thực nghiệm
*   **Datasets & Tasks:** 
    *   *CANDOR corpus:* 300 cặp câu thoại để fit trọng số ban đầu.
    *   *LUFY-Dataset:* 17 người tham gia, 10 sessions hội thoại (trung bình 253.8 turns/session, tổng ~2 giờ).
*   **Baselines:** Naive RAG (không quên, cosine similarity), MemoryBank (quên dựa trên tần suất truy xuất, cosine similarity), LUFY (quên 90% dựa trên 5 chỉ số, truy xuất bằng cosine + importance).
*   **Metrics:** Subjective Human Ratings (Personalization, Flow of Conv, Overall), GPT-4o UX rating, Sentiment Score, Retrieval Precision (%).

### 2. Đề xuất trực quan hóa (Charts)
1.  **Line Chart (Độ ổn định của Personalization qua các Sessions):**
    *   *Trục X:* Sessions (S1, S2, S3, S4).
    *   *Trục Y:* Personalization Score (1-5).
    *   *Nhóm (Series):* Naive RAG, MemoryBank, LUFY.
    *   *Mục đích:* Minh họa sự trồi sụt của Naive RAG/MemoryBank ở session cuối do quá tải bộ nhớ, đối lập với sự ổn định của LUFY.
2.  **Bar Chart (Độ chính xác truy xuất bộ nhớ):**
    *   *Trục X:* Các hệ thống (Naive RAG, MemoryBank, LUFY).
    *   *Trục Y:* Retrieval Precision (%).
    *   *Mục đích:* Nổi bật việc quên 90% thông tin giúp LUFY tăng 17% độ chính xác truy xuất.

### 3. Đề xuất cấu trúc bảng
*   **Bảng tổng hợp:** `[Hệ thống | Personalization (Avg) | Flow (Avg) | Overall (Avg) | Sentiment (Avg) | Retrieval Precision (%)]`
*   *Highlight:* Bold cho giá trị cao nhất ở mỗi cột.

### 4. Số liệu thô (Raw Data)
```markdown
| Metric / System | Session 1 | Session 2 | Session 3 | Session 4 |
| :--- | :---: | :---: | :---: | :---: |
| **Personalization** | | | | |
| - Naive RAG | 3.94 | 3.85 | 3.89 | 3.76 |
| - MemoryBank | 3.87 | 3.95 | 3.87 | 3.56 |
| - LUFY (Ours) | 3.94 | 4.13 | 3.98 | 4.04 |
| **Flow of Conversation** | | | | |
| - Naive RAG | 3.86 | 3.46 | 3.57 | 3.20 |
| - MemoryBank | 3.69 | 3.58 | 3.64 | 3.31 |
| - LUFY (Ours) | 3.78 | 3.63 | 3.57 | 3.72 |
| **Overall Experience** | | | | |
| - Naive RAG | 3.82 | 3.65 | 3.74 | 3.50 |
| - MemoryBank | 3.85 | 3.75 | 3.64 | 3.36 |
| - LUFY (Ours) | 3.80 | 3.81 | 3.74 | 3.80 |
| **GPT-4o UX Rating** | | | | |
| - Naive RAG | 3.71 | 3.53 | 3.38 | 3.18 |
| - MemoryBank | 3.71 | 3.53 | 3.44 | 3.21 |
| - LUFY (Ours) | 3.71 | 3.53 | 3.32 | 3.50 |
| **Sentiment Score** | | | | |
| - Naive RAG | 0.58 | 0.51 | 0.38 | 0.32 |
| - MemoryBank | 0.58 | 0.47 | 0.34 | 0.30 |
| - LUFY (Ours) | 0.58 | 0.43 | 0.45 | 0.62 |
```

---

## 4. Paper 4: Lifelong Learning of Large Language Model based Agents: A Roadmap

*Lưu ý: Vì đây là bài báo khảo sát (survey), không có dữ liệu thực nghiệm chạy đơn lẻ trên cùng một môi trường. Thiết kế dưới đây phục vụ cho việc thống kê nghiên cứu.*

### 1. Tóm tắt thiết lập thực nghiệm
*   **Benchmarks được khảo sát:** WebArena, OSWorld, Crafter, AlfWorld, và các tập pre-training liên tục.
*   **Baselines:** Các nhóm phương pháp Continual Learning: Rehearsal-based, Regularization-based, Architecture-based (LoRA/Prompt-based), Memory-based (RAG-based).
*   **Metrics:** Average Accuracy (AA), Backward Transfer (BWT), Forward Transfer (FWT).

### 2. Đề xuất trực quan hóa (Charts)
1.  **Radar Chart (So sánh đặc tính các nhóm CL):**
    *   *Trục góc:* Stability, Plasticity, Memory Efficiency, Compute Efficiency, Generalization.
    *   *Nhóm (Series):* Rehearsal, Regularization, Architecture, External Memory (RAG).
    *   *Mục đích:* Đưa ra cái nhìn trực quan về ưu nhược điểm của từng nhóm công nghệ để thiết kế hệ thống.
2.  **Scatter Plot (Mô phỏng Stability vs. Plasticity):**
    *   *Trục X:* Plasticity (khả năng học tác vụ mới nhanh).
    *   *Trục Y:* Stability (khả năng chống quên tác vụ cũ).
    *   *Mục đích:* Xác định vị trí các nhóm phương pháp trên thế lưỡng nan stability-plasticity.

### 3. Đề xuất cấu trúc bảng
*   **Bảng tổng hợp:** `[Nhóm phương pháp | Đại diện | Lưu dữ liệu cũ? (Y/N) | Thay đổi tham số? (Y/N) | Compute Cost | Memory Cost]`

### 4. Số liệu thô (Raw Data)
*(Thống kê số lượng các công trình nghiên cứu theo phân loại bộ nhớ của Agent được khảo sát)*
```markdown
| Memory Type | Tech Subcategory | Representative Systems |
| :--- | :--- | :--- |
| Working Memory | Prompt Compression | LLMLingua, Selective-Context |
| | Long Context Comprehension | MemGPT, Landmark Attention |
| | Self-Correction | Reflexion, Self-Refine |
| Episodic Memory | Data & Feature Replay | Experience Replay, Generative Replay |
| | Continual RL | CLEAR, Co-play |
| Semantic Memory | Continual KG Learning | CogKG, K-Adapter |
| | Continual Doc Learning | Retrieval-augmented agents |
| Parametric Memory| Continual SFT | LoRA-CL, O-LoRA |
| | Knowledge Editing | ROME, MEMIT |
| | Continual Alignment | Continual DPO/RLHF |
```

---

## 5. Paper 5: VLQA: The First Comprehensive, Large, and High-Quality Vietnamese Dataset for Legal Question Answering

### 1. Tóm tắt thiết lập thực nghiệm
*   **Datasets & Tasks:**
    *   *Kho luật Việt Nam:* 59,636 điều luật thuộc 27 chủ đề pháp lý (từ 2,162 văn bản luật).
    *   *Câu hỏi:* 3,129 câu hỏi thực tế của người dân (Train/Val/Test = 7:1:2).
    *   *Tác vụ:* Truy xuất điều luật (LIR) và Trả lời câu hỏi pháp luật (LQA).
*   **Baselines:**
    *   *Retrieval:* BM25, fastText (sparse); Sentence-BERT, BGE (dense zero-shot); Fine-tuned Cross-Encoder.
    *   *QA:* Extractive (BERT, RoBERTa); Generative (BARTPho, ViT5); LLMs (Qwen 2.5, LLaMA 3.1, DeepSeek-V3, GPT-4o, GPT-4o-mini).
*   **Metrics:** Recall@K, MRR@10 (Retrieval); ROUGE (1, 2, L), BLEU-4 (QA); Human evaluation tỷ lệ lỗi.

### 2. Đề xuất trực quan hóa (Charts)
1.  **Horizontal Bar Chart (Phân bổ số lượng điều luật theo 27 chủ đề):**
    *   *Trục Y:* 27 chủ đề pháp luật (Hành chính, Thương mại, Sở hữu trí tuệ, v.v.).
    *   *Trục X:* Số lượng điều luật.
    *   *Mục đích:* Minh họa độ phủ và tính không đồng đều về regulatory density của kho luật Việt Nam.
2.  **Pie Chart (Phân phối loại câu hỏi):**
    *   *Cơ cấu:* How (36%), What (27.1%), Binary (21.6%), Other (6.3%), Which (3.3%), When (2.8%), Who (2.2%), Where (0.6%).
    *   *Mục đích:* Chỉ ra các dạng câu hỏi thực tế của người dân tập trung nhiều nhất vào phương thức giải quyết vụ việc (How) và định nghĩa (What).

### 3. Đề xuất cấu trúc bảng
*   **Bảng tổng hợp:** `[Lĩnh vực pháp lý | Số chương | Số mục | Số điều luật | Tỷ lệ phần trăm câu hỏi liên quan (%)]`

### 4. Số liệu thô (Raw Data)
```markdown
| Topic | Chapters | Sections | Articles | % Questions |
| :--- | :---: | :---: | :---: | :---: |
| Administrative System | 1381 | 1784 | 8566 | 14.67% |
| Commerce | 651 | 937 | 4748 | 6.80% |
| Business | 487 | 656 | 3816 | 7.30% |
| Transportation and Logistics | 417 | 555 | 3017 | 4.80% |
| Natural Resources & Environment | 404 | 581 | 2922 | 4.50% |
| Sports and Healthcare | 406 | 568 | 2494 | 3.50% |
| Currency and Banking | 366 | 494 | 2475 | 3.80% |
| Labor and Wages | 407 | 557 | 2440 | 5.20% |
| Administrative Violations | 227 | 372 | 2372 | 4.80% |
| Civil Rights | 218 | 364 | 2218 | 5.00% |
| Litigation Procedure | 230 | 300 | 2213 | 3.50% |
| Criminal Liability | 207 | 244 | 2197 | 4.50% |
| Investment | 260 | 383 | 2017 | 4.80% |
| Information Technology | 318 | 418 | 2012 | 4.00% |
| Education | 356 | 419 | 1896 | 3.50% |
| Real Estate | 213 | 326 | 1701 | 3.50% |
| Insurance | 193 | 268 | 1432 | 3.80% |
| Culture and Society | 255 | 331 | 1530 | 3.50% |
| Taxes, Fees, and Charges | 218 | 260 | 1278 | 1.80% |
| Urban Development | 137 | 220 | 1122 | 3.00% |
| Import and Export | 124 | 181 | 870 | 2.10% |
| Securities | 67 | 135 | 799 | 1.50% |
| Intellectual Property | 73 | 102 | 737 | 2.50% |
| Accounting and Auditing | 68 | 120 | 658 | 2.00% |
| Legal Services | 102 | 117 | 642 | 1.00% |
```

---

## 6. Paper 6: ViLegalNLI: Natural Language Inference for Vietnamese Legal Texts

### 1. Tóm tắt thiết lập thực nghiệm
*   **Datasets & Tasks:** ViLegalNLI (42,012 cặp premise-hypothesis tiếng Việt chuyên ngành luật, chia 8:1:1). Phân loại nhị phân Entailment vs. Non-entailment.
*   **Baselines:** XLM-R, mBERT, InfoXLM (Multilingual); PhoBERT, viBERT, CafeBERT (Vietnamese); DeBERTa V3 (Architectural); Gemma-3, Qwen2.5 (LLMs).
*   **Metrics:** Accuracy (%), Macro F1-score (%).

### 2. Đề xuất trực quan hóa (Charts)
1.  **Grouped Bar Chart (So sánh hiệu năng các nhóm mô hình):**
    *   *Trục X:* Các mô hình tiêu biểu (mBERT, XLM-R-large, PhoBERT-large, CafeBERT, Gemma-3 few-shot, Qwen2.5 few-shot).
    *   *Trục Y:* Score (%).
    *   *Nhóm (Series):* Accuracy vs. F1-score.
    *   *Mục đích:* Làm nổi bật hiệu năng dẫn đầu của Qwen2.5 few-shot và khả năng cạnh tranh tốt của CafeBERT (encoder nhỏ).
2.  **Horizontal Bar Chart (Trigger Tokens PMI):**
    *   *Trục Y:* Các từ khóa (không cần, Dù, bất kể, chỉ cần, tự do, Khi, Theo quy định, đồng thời).
    *   *Trục X:* PMI score.
    *   *Nhóm (Series):* Nhãn tương ứng (Entailment / Non-entailment).
    *   *Mục đích:* Minh họa các trigger tokens có PMI cao nhất gây nhiễu và shortcut learning trước khi bị loại bỏ.

### 3. Đề xuất cấu trúc bảng
*   **Bảng tổng hợp:** `[Kiến trúc | Tên mô hình | Dev Acc (%) | Dev F1 (%) | Test Acc (%) | Test F1 (%) | Params]`

### 4. Số liệu thô (Raw Data)
```markdown
| Architecture | Model | Dev Acc | Dev F1 | Test Acc | Test F1 |
| :--- | :--- | :---: | :---: | :---: | :---: |
| Multilingual Encoder | mBERT | 80.41 | 79.83 | 79.36 | 78.75 |
| | XLM-R (base) | 84.34 | 84.18 | 83.65 | 83.43 |
| | XLM-R (large) | 86.98 | 86.81 | 86.37 | 86.19 |
| | InfoXLM (base) | 83.96 | 83.65 | 83.25 | 82.85 |
| | InfoXLM (large) | 89.24 | 89.15 | 87.98 | 87.85 |
| Vietnamese Monolingual| viBERT | 75.00 | 74.24 | 74.53 | 73.54 |
| | PhoBERT (base) | 85.14 | 84.84 | 84.46 | 84.13 |
| | PhoBERT (large) | 86.13 | 85.97 | 84.98 | 84.78 |
| | CafeBERT | 87.56 | 87.44 | 87.49 | 87.36 |
| Improved Transformer | DeBERTa V3 (base) | 76.15 | 75.56 | 76.01 | 75.52 |
| | DeBERTa V3 (large) | 86.16 | 85.98 | 85.35 | 85.18 |
| LLMs (Zero-shot) | Gemma-3 | 81.56 | 81.52 | 80.76 | 80.72 |
| | Qwen2.5 | 80.65 | 79.15 | 79.62 | 77.83 |
| LLMs (Few-shot) | Gemma-3 | 89.49 | 89.49 | 88.92 | 88.86 |
| | Qwen2.5 | 90.89 | 90.78 | 90.72 | 90.64 |
| LLMs (Finetuned) | Gemma-2 | 84.11 | 82.46 | 83.74 | 81.80 |
```

---

## 7. Paper 7: Retrievable Gradients: Continual Post-Training Without Cumulative Weight Drift

### 1. Tóm tắt thiết lập thực nghiệm
*   **Datasets & Corpora:** DPR Wikipedia (General), PubMed Abstracts (Medicine), Pile-of-Law (Law).
*   **Baselines:** Direct, Standard CPT, Instruction CPT, RAG, PE-RAG, Parametric RAG (PRAG), cùng các phiên bản fine-tuned tương ứng; REGRAD và REGRAD + ICL.
*   **Metrics:** Accuracy (closed-ended) và token-level F1 (open-ended).

### 2. Đề xuất trực quan hóa (Charts)
1.  **Line Chart (Độ ổn định khi nạp tri thức liên tục quy mô lớn):**
    *   *Trục X:* Số lượng tài liệu nạp vào (2.5K, 5K, 7.5K, 10K).
    *   *Trục Y:* F1 Score (%) trung bình trên 3 Wikipedia benchmarks.
    *   *Nhóm (Series):* Continual Post-Training (CPT) vs. REGRAD (Ours).
    *   *Mục đích:* Chứng minh CPT bị sụp đổ hiệu năng do weight drift, trong khi REGRAD tiếp tục cải thiện hiệu năng khi Gradient Bank lớn lên.
2.  **Grouped Bar Chart (So sánh hiệu năng LLaMA-8B trên các lĩnh vực):**
    *   *Trục X:* Ba lĩnh vực (General, Medicine, Law).
    *   *Trục Y:* Average Performance (%).
    *   *Nhóm (Series):* Direct, PE-RAG, Fine-tuned-PRAG, REGRAD, REGRAD + ICL.
    *   *Mục đích:* Làm nổi bật hiệu năng đứng đầu của REGRAD + ICL trên mọi miền dữ liệu.

### 3. Đề xuất cấu trúc bảng
*   **Bảng tổng hợp:** `[Kích thước LLM | Phương pháp | General Avg (%) | Medical Avg (%) | Law Avg (%) | Overall Average (%) | Trôi trọng số? (Y/N)]`

### 4. Số liệu thô (Raw Data)
```markdown
| Model | Method | 2WQA | CWQ | HQA | CaseHOLD | LH | Housing | PubMed | MedQA | BioASQ | Avg |
| :--- | :--- | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| LLaMA-8B | Direct | 33.42 | 42.73 | 26.34 | 61.30 | 54.10 | 46.00 | 91.20 | 16.46 | 81.20 | 50.31 |
| | Standard CPT | 25.67 | 40.24 | 23.51 | 58.10 | 53.80 | 46.20 | 90.30 | 15.59 | 80.20 | 48.18 |
| | Instruction CPT| 36.31 | 46.58 | 27.44 | 63.80 | 54.00 | 49.50 | 38.70 | 19.24 | 60.60 | 44.02 |
| | RAG (ICL) | 26.98 | 31.47 | 35.23 | 66.30 | 39.90 | 53.50 | 80.10 | 17.88 | 80.50 | 47.98 |
| | PE-RAG | 29.61 | 36.58 | 38.70 | 69.70 | 51.40 | 60.20 | 80.60 | 14.15 | 81.00 | 51.33 |
| | PRAG | 31.63 | 40.96 | 27.18 | 60.30 | 52.00 | 46.20 | 92.40 | 15.93 | 82.10 | 49.86 |
| | Fine-tuned-Dir | 32.18 | 49.67 | 28.84 | 56.90 | 84.20 | 80.20 | 92.80 | 22.89 | 81.40 | 58.79 |
| | Fine-tuned-RAG | 37.17 | 54.21 | 39.16 | 59.60 | 91.90 | 69.50 | 92.70 | 23.22 | 84.60 | 61.34 |
| | Fine-tuned-PRAG| 35.71 | 51.26 | 32.97 | 66.20 | 78.60 | 83.00 | 91.80 | 21.37 | 82.60 | 60.39 |
| | **REGRAD** | 38.43 | 56.41 | 36.31 | 70.30 | 96.20 | 84.80 | 93.20 | 21.65 | 81.90 | **64.36** |
| | **REGRAD+ICL** | **45.06** | **57.52** | **45.27** | **74.10** | 94.80 | **85.40** | **98.10** | **19.83** | **85.30** | **67.26** |
```

---

## 8. Paper 8: When Machine Unlearning Meets Retrieval-Augmented Generation (RAG) - Keep Secret or Forget Knowledge

### 1. Tóm tắt thiết lập thực nghiệm
*   **Datasets & Tasks:**
    *   *Tiny-nq:* 2,000 cặp câu hỏi đáp (Natural Questions) phục vụ tác vụ quên mẫu dữ liệu (*Sample Unlearning*).
    *   *Wikipedia Topics:* 100 chủ đề ngẫu nhiên, mỗi chủ đề có 5 câu hỏi liên quan, phục vụ tác vụ quên khái niệm (*Concept Unlearning*).
    *   *Harmful Topics:* 25 chủ đề độc hại (như giết người, cướp của), mỗi chủ đề gồm 10 cặp QA huấn luyện sinh độc hại và 5 cặp QA kiểm thử phục vụ phòng vệ đầu ra (*Harmful Output Defense*).
    *   *MMLU & ARC:* Đánh giá giữ nguyên năng lực tổng quát (*Model Utility*).
*   **Baseline Models:** Gradient Ascent, In-context Unlearning, $\mu$-Unlearning.
*   **Evaluation Metrics:** ROUGE-L (↓), Unlearning Success Rate - USR (↑), TPR@1%FPR - MIA (↓), Harmful Output Probability - HOP (↓).

### 2. Đề xuất trực quan hóa (Charts)
1.  **Grouped Bar Chart (Hiệu năng chống quên tổng thể):**
    *   *Trục X:* Các mô hình (Llama-2-7b-chat, GPT-4o, Gemini).
    *   *Trục Y:* Unlearning Success Rate (USR) (%).
    *   *Nhóm (Series):* Gradient Ascent, $\mu$-Unlearning, In-context Unlearning, RAG-based Unlearning (Ours).
    *   *Mục đích:* So sánh trực quan hiệu quả unlearning khái niệm vượt trội của RAG-based so với các baseline can thiệp tham số.
2.  **Line Chart (Độ nhạy trước kẻ tấn công thích ứng - Adaptive Leakage Curve):**
    *   *Trục X:* Tỷ lệ rò rỉ tri thức unlearn $UK_{leakage}$ (0%, 20%, 40%, 60%, 80%, 100%).
    *   *Trục Y:* USR (%).
    *   *Nhóm (Series):* Sample Unlearning vs. Concept Unlearning trên Llama-2.
    *   *Mục đích:* Minh họa điểm sụp đổ hiệu năng của hệ thống unlearning khi thông tin constraints bị lộ dần cho kẻ tấn công.
3.  **Bar Chart (Bảo toàn năng lực tổng quát - Utility Preservation):**
    *   *Trục X:* Các cấu hình unlearning (Trước unlearning, Gradient Ascent, $\mu$-Unlearning, RAG-based).
    *   *Trục Y:* Accuracy (%).
    *   *Nhóm (Series):* MMLU vs. ARC.
    *   *Mục đích:* Làm nổi bật hiện tượng catastrophic forgetting làm giảm điểm benchmark ở các baseline và tính harmlessness của RAG-based.

### 3. Đề xuất cấu trúc bảng tổng hợp
*   **Bảng 1: So sánh hiệu năng học quên đa khía cạnh:**
    *   *Cột:* `[Method | Task Type | USR (%) | ROUGE-L (↓) | TPR@1%FPR (↓) | MMLU (Utility) | ARC (Utility) | Access Params (Y/N)]`
    *   *Dòng:* Các phương pháp unlearning trên Llama-2-7b-chat.
    *   *Highlight:* Bold cho kết quả tốt nhất ở mỗi metric; in nghiêng các baseline bị sụt giảm điểm utility nặng.

### 4. Số liệu thô (Raw Data)

#### Bảng 1: Hiệu năng Unlearning (ROUGE-L, USR, MIA) dưới các điều kiện chuẩn
```markdown
| Type | LLM | Scheme | ROUGE-L | USR | TPR@1%FPR |
| :--- | :--- | :--- | :---: | :---: | :---: |
| Concept | GPT-4o | In-context | 52.6 | 5.8% | 3.6% |
| Concept | GPT-4o | RAG-based (Ours) | 0.03 | 99.2% | 1.5% |
| Concept | Gemini | In-context | 70.8 | 4.6% | 2.9% |
| Concept | Gemini | RAG-based (Ours) | 0.05 | 99.5% | 1.3% |
| Concept | Llama-2-7b-chat | Gradient Ascent | 61.3 | 35.9% | 2.2% |
| Concept | Llama-2-7b-chat | In-context | 75.6 | 13.8% | 2.4% |
| Concept | Llama-2-7b-chat | μ-Unlearning | 80.2 | 43.4% | 2.0% |
| Concept | Llama-2-7b-chat | RAG-based (Ours) | 0.10 | 99.8% | 1.3% |
| Sample | Llama-2-7b-chat | Gradient Ascent | 32.7 | 75.8% | 2.0% |
| Sample | Llama-2-7b-chat | In-context | 72.4 | 20.7% | 2.2% |
| Sample | Llama-2-7b-chat | μ-Unlearning | 37.8 | 66.3% | 1.8% |
| Sample | Llama-2-7b-chat | RAG-based (Ours) | 0.00 | 100.0% | 1.2% |
```

#### Bảng 2: Hiệu năng USR (%) trước Kẻ tấn công thích ứng (Adaptive Adversary) ở các mức độ rò rỉ constraints
```markdown
| Objective / Leakage Rate | 0% | 20% | 40% | 60% | 80% | 100% |
| :--- | :---: | :---: | :---: | :---: | :---: | :---: |
| Sample Unlearning USR | 80.8% | 72.6% | 67.2% | 52.7% | 36.3% | 21.5% |
| Concept Unlearning USR | 83.5% | 74.8% | 65.9% | 48.3% | 35.1% | 20.2% |
```

