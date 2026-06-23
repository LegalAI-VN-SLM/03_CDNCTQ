---
updated:
  - 2026-06-23T21:42:48+07:00
  - 2026-06-23T17:32:11+07:00
  - 2026-06-23T16:55:36+07:00
---
# Báo cáo Tổng hợp Toàn cầu: Học liên tục và Quên thảm khốc trên các Mô hình Ngôn ngữ Lớn (LLMs)

Tài liệu này tổng hợp toàn bộ các nghiên cứu từ Group 1 đến Group 5 trong thư viện tài liệu tham khảo cốt lõi (`references_core.bib`), tập trung vào các chủ đề: Học liên tục (Continual Learning - CL), Quên thảm khốc (Catastrophic Forgetting - CF), Tinh chỉnh hiệu quả tham số (PEFT/LoRA), các phương pháp bán tham số (RAG/ReGrad), và Case Study chuyên ngành Pháp luật Việt Nam.

---

## 1. Global Method × Axes Table (Task 1)

Bảng dưới đây thống nhất toàn bộ 36 bài báo thuộc 5 nhóm nghiên cứu, phân loại theo các trục kỹ thuật cốt lõi:

| ID | Nhóm | Năm | Giai đoạn (Stage) | Cập nhật tham số | Bán tham số (Semi-parametric) | Cơ chế chống quên | Tập dữ liệu / Benchmark | Thước đo quên (Metrics) | Miền tri thức (Domain) | Phát hiện cốt lõi về sự quên (Key Findings) |
| :--- | :---: | :---: | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **Wu2024_CL_Survey** | 1 | 2024 | CPT, CFT, alignment | none/analysis_only | none | rehearsal/replay, regularization, architecture/PEFT, model_editing/merging, external_memory | Nhiều tập dữ liệu tổng hợp | average forgetting, BWT, FWT, safety metrics | general, multilingual | • Hệ thống hóa CL thành 3 pha: CPT (tri thức), CFT (tác vụ), Alignment (giá trị).<br>• Cảnh báo hiện tượng "Quên chéo giai đoạn" (Cross-Stage Forgetting).<br>• Phân biệt rõ CL với RAG và Model Editing. |
| **Shi2024_CL_Survey** | 1 | 2024 | CPT, CFT, alignment | none/analysis_only | none | rehearsal/replay, regularization, architecture/PEFT, model_editing/merging, external_memory | Nhiều tập dữ liệu tổng hợp | average forgetting, BWT, FWT, stability gap | general, multilingual | • Khảo sát các kỹ thuật CL trên LLM từ góc độ động lực học tối ưu hóa.<br>• Phân tích sự đánh đổi giữa Plasticity vs Stability.<br>• Nhấn mạnh vai trò của dữ liệu replay trong việc giữ vững năng lực nền tảng. |
| **Zheng2024_LL_Survey** | 1 | 2024 | CPT, CFT, alignment | none/analysis_only | none | rehearsal/replay, regularization, architecture/PEFT, model_editing/merging, external_memory | Nhiều tập dữ liệu tổng hợp | BWT, FWT, performance retention | general | • Làm rõ ranh giới giữa học trọn đời (Lifelong Learning) với RAG và Model Editing.<br>• Đề xuất khung đánh giá chuẩn cho các mô hình ngôn ngữ lớn học liên tục. |
| **Guo2025_CL_GenAI** | 1 | 2025 | CPT, CFT, alignment, agent | none/analysis_only | none | rehearsal/replay, regularization, architecture/PEFT, model_editing/merging, external_memory | Nhiều tập dữ liệu tổng hợp | BWT, FWT, agent-specific metrics | general, multilingual | • Mở rộng khảo sát CL từ LLM sang MLLM (đa phương thức) và các Agents tương tác.<br>• Phân loại cơ chế bộ nhớ ngoài và vai trò của nó trong việc chống quên ở cấp độ Agent. |
| **Zheng2025_Awesome** | 1 | 2025 | CPT, CFT, alignment, agent | none/analysis_only | none | rehearsal/replay, regularization, architecture/PEFT, model_editing/merging, external_memory | Nhiều tập dữ liệu tổng hợp | qualitative only | general | • Awesome list lưu trữ và phân loại các công trình nghiên cứu CL cho LLM.<br>• Chỉ ra sự dịch chuyển từ phương pháp regularization sang PEFT và semi-parametric. |
| **Luo2025_EmpCF** | 2 | 2025 | CFT | full_finetune | none | rehearsal/replay, analysis_only | Simp, Emdg, InqQG, Exp, HGen, MMLU, Hellaswag, BoolQ, RACE, CrowSPairs | Forgetting Gradient (FG %) | general | • CF xảy ra trên mọi kiến trúc SFT tuần tự; nặng nhất là đọc hiểu (RACE) và MMLU.<br>• Decoder-only kháng quên tốt hơn Encoder-Decoder.<br>• Trộn 10k Alpaca data (rehearsal) tạo tấm đệm tri thức giúp giảm tốc độ quên. |
| **Wang2023_TRACE** | 2 | 2023 | CFT | full_finetune, PEFT/LoRA | none | rehearsal/replay, analysis_only | TRACE (ScienceQA, FOMC, MeetingBank, C-STANCE, 20Minuten, Py150, NumGLUE-cm, NumGLUE-ds) | OP, BWT, $\Delta R^G$, $\Delta R^I$, $\Delta R^S$ | general, multilingual | • Đề xuất TRACE benchmark gồm 8 tác vụ và đo lường sự suy giảm tri thức nền thông qua các chỉ số Delta ($\Delta$).<br>• Khả năng lập luận logic dễ quên nhất, trong khi an sauan (safety) rất bền vững.<br>• Đề xuất RCL (Reasoning-augmented CL) để bảo vệ mạch tư duy của LLM. |
| **Scialom2022_FineTuned** | 2 | 2022 | CFT | full_finetune | none | rehearsal/replay | T0, SuperGLUE | average forgetting, BWT | general | • Chỉ ra LLM pre-trained là continual learner tốt hơn so với khởi tạo ngẫu nhiên.<br>• Thực nghiệm chứng minh chỉ cần 0.25% - 1% replay dữ liệu cũ là giữ được năng lực zero-shot. |
| **Yin2022_ConTinTin** | 2 | 2022 | CFT | full_finetune | none | rehearsal/replay | ConTinTin, SuperGLUE | average forgetting, FWT | general | • Xây dựng benchmark ConTinTin đánh giá CL từ các task instructions.<br>• Các tác vụ phân loại trùng lặp nhãn dễ gây quên nhất do xung đột không gian nhãn. |
| **Li2024_RevisitingCF** | 2 | 2024 | CFT | full_finetune, PEFT/LoRA | none | rehearsal/replay, regularization | AdvBench, Alpaca, ARC, GSM8K | Accuracy, logit-based evaluation | general | • CF liên quan đến hình học của loss landscape (LLS). Tinh chỉnh OOD tạo ra các hẻm núi nhọn (sharp LLS).<br>• Đề xuất dùng SAM để tìm vùng trọng số phẳng, giúp tăng đáng kể khả năng chống quên. |
| **Kalajdzievski2024_ScalingForgetting** | 2 | 2024 | CFT | PEFT/LoRA | none | architecture/PEFT, analysis_only | OpenOrca, WikiText-103, ARC, AdvBench | Forgetting Loss ($L_f$ - logit-based), Accuracy | general | • Mức độ quên ($L_f$) và fine-tuning loss ($L_{ft}$) tuân theo định luật tuyến tính nghịch đảo (Inverse Linear Law).<br>• Giảm rank LoRA hay dừng sớm không giúp chống quên, chỉ làm mô hình học tác vụ mới kém đi. |
| **Gupta2023_CPT_Warmup** | 3 | 2023 | CPT | full_finetune | none | rehearsal/replay | Pythia (410M-2.8B), Pile, SlimPajama | validation loss, downstream accuracy | general | • CPT từ checkpoint cũ bắt buộc phải Re-warming và Re-decay LR để optimizer hoạt động lại.<br>• Bỏ qua bước warm-up ($WU=0$) gây ra hiện tượng bùng nổ loss (loss spike) tạm thời (Stability Gap). |
| **Yildiz2025_InvestigatingCPT** | 3 | 2025 | CPT | full_finetune | none | rehearsal/replay | LLaMA-2-7B, WikiText, RedPajama, Dolma | validation loss, downstream accuracy | general | • Khảo sát chi tiết hành vi của AdamW trong CPT. MaxLR quyết định sự đánh đổi Plasticity vs Stability.<br>• Stability Gap là do động lực học optimizer chứ không phải do data shift. |
| **Ibrahim2024_StrategiesCPT** | 3 | 2024 | CPT | full_finetune | none | rehearsal/replay | LLaMA, SlimPajama, RedPajama | validation loss, downstream performance | general | • Đề xuất Compute-equivalent Replay để so sánh công bằng.<br>• Trong lệch pha nhẹ (Weak Shift), chỉ cần 5% replay để đạt hiệu năng ngang Union Baseline với 1/2 compute. |
| **Du2024_UnlockingCL** | 3 | 2024 | CPT | full_finetune | none | regularization | Pythia, Pile, LLaMA-2 | stability gap, validation loss | general | • Đề xuất thuật toán MIGU tối ưu hóa gradient để làm mịn stability gap.<br>• Chứng minh việc cân bằng động lực học gradient ở pha đầu của CPT giúp giảm quên tri thức chung. |
| **Peng2024_ScalableLM** | 3 | 2024 | CPT | full_finetune | none | regularization, architecture/PEFT | LLaMA, Pile | stability gap, validation loss | general | • Thiết lập mô hình Scalable LM sử dụng MoE thích ứng động để phân phối luồng tri thức cũ/mới.<br>• Giảm thiểu hiệu ứng Stability Gap thông qua cấu trúc tách biệt tham số. |
| **Que2024_DCPTLaw** | 3 | 2024 | CPT | full_finetune | none | rehearsal/replay | Qwen-1.5, 6 domains (Code, Math, Law, Chemistry, Music, Medical) | validation loss (general vs domain) | law, math, code, medical, music, chemistry | • Đề xuất **D-CPT Law** đưa tham số tỷ lệ trộn `r` vào công thức Chinchilla (R² > 0.97).<br>• Đề xuất DLC (Domain-specific Learnable Coefficient) giúp predict unseen domains với 1% cost.<br>• Cho phép tính toán tỷ lệ trộn tối ưu để đạt loss tốt nhất trên domain mà ít suy hao tri thức chung. |
| **Zheng2024_CrossLingualCPT** | 3 | 2024 | CPT | full_finetune | none | rehearsal/replay | LLaMA-2, Chinese & English corpora | validation loss, cross-lingual metrics | multilingual, Chinese | • Khảo sát CPT xuyên ngôn ngữ (Strong Shift).<br>• Cần tỷ lệ replay dữ liệu cũ lớn hơn (15% - 25%) để giữ vững cấu trúc ngôn ngữ gốc khi học ngôn ngữ mới. |
| **Li2025_TiCLM** | 3 | 2025 | CPT | full_finetune | none | rehearsal/replay | TiC-LM, TiC-Wiki, TiC-RedPajama | time-continual perplexity, forgetting rate | general | • Đề xuất benchmark TiC-LM để đánh giá pre-training liên tục theo dòng thời gian (Time-Continual).<br>• Phân tích tốc độ lỗi thời của tri thức và thiết lập các baseline replay theo thời gian thực. |
| **Bethune2025_ScalingLawsInjection** | 3 | 2025 | CFT, CPT | full_finetune | none | rehearsal/replay | LLaMA, Pile, Fine-tuning datasets | validation loss, forgetting scaling laws | general | • Xây dựng luật tỷ lệ cho sự quên khi tinh chỉnh với cơ chế tiêm dữ liệu pre-training (Pretraining Data Injection).<br>• Mô hình hóa lượng dữ liệu general tối thiểu cần inject để bảo toàn năng lực gốc. |
| **Guo2024_MitigateStabilityGap** | 3 | 2024 | CPT | full_finetune | none | regularization, rehearsal/replay | LLaMA, Pile, SlimPajama | stability gap, validation loss | general | • Phân tích nguyên nhân Stability Gap do sự mất cân bằng momentum trong AdamW.<br>• Đề xuất giải pháp khôi phục trạng thái optimizer và điều chỉnh đệm gradient. |
| **Hu2021_LoRA** | 4 | 2021 | CFT | PEFT/LoRA | none | architecture/PEFT | GPT-3, RoBERTa, DeBERTa, GLUE, WikiText-103 | Accuracy, generation quality | general | • Đề xuất LoRA: đóng băng ma trận gốc, tối ưu hóa qua hai ma trận hạng thấp đầu vào/đầu ra.<br>• Giảm tham số huấn luyện 10,000 lần nhưng giữ nguyên hoặc cải thiện chất lượng tác vụ đơn. |
| **Wang2023_OrthogonalSubspace** | 4 | 2023 | CFT | PEFT/LoRA | none | regularization, architecture/PEFT | TRACE, LLaMA, Alpaca | OP, BWT, forgetting rate | general | • Chiếu các cập nhật tham số LoRA vào không gian con trực giao của các tác vụ cũ.<br>• Loại bỏ hoàn toàn sự can thiệp chéo giữa các tác vụ tuần tự. |
| **Biderman2024_LoRALearnsLess** | 4 | 2024 | CFT | PEFT/LoRA | none | architecture/PEFT | LLaMA-2, OpenOrca, WikiText-103 | Accuracy, forgetting rate | general | • LoRA học ít hơn và quên ít hơn Full FT. Không phù hợp cho CPT quy mô lớn do rank nội tại của $\Delta W$ rất cao (SVD rank ~2000).<br>• LoRA bảo tồn tốt độ đa dạng sinh từ (generation diversity) và tránh overfit cấu trúc. |
| **Han2025_SLIM** | 4 | 2025 | CFT | PEFT/LoRA | none | architecture/PEFT | TRACE, LLaMA, Vicuna | OP, BWT, average forgetting | general | • Đề xuất SLIM kết hợp Soft LoRA với Identity Mixture (định tuyến mềm giữa các LoRA Experts).<br>• Phân chia tri thức tác vụ vào các chuyên gia khác nhau để triệt tiêu ghi đè tham số. |
| **Yang2024_ParameterCollision** | 4 | 2024 | CFT | PEFT/LoRA | none | architecture/PEFT | LLaMA-2, Alpaca, custom tasks | Accuracy, forgetting rate | general | • Định lượng hiện tượng Parameter Collision (Xung đột tham số) khi học tuần tự trên cùng một LoRA.<br>• Đề xuất N-LoRA sử dụng chuẩn hóa động để giảm thiểu sự chồng lấn trọng số. |
| **Gao2024_HigherLayersLoRA** | 4 | 2024 | CFT | PEFT/LoRA | none | architecture/PEFT | LLaMA-2, custom experts | Accuracy, forgetting rate | general | • Khám phá ra các tầng Transformer cao hơn (higher layers) cần nhiều LoRA Experts hơn (MoLA).<br>• Tầng cao xử lý thông tin ngữ nghĩa phức tạp nên dễ bị xung đột tham số nhất. |
| **Ren2024_PETForgetting** | 4 | 2024 | CFT | PEFT/LoRA | none | architecture/PEFT, regularization | LLaMA-2, custom tasks | Accuracy, forgetting rate | general | • Đề xuất I-LoRA điều phối tích hợp thông tin thông minh giữa các tầng mạng.<br>• Giảm thiểu catastrophic forgetting trong PEFT bằng cách phân bổ lại trọng tâm tối ưu hóa. |
| **Razdaibiedina2023_ProgressivePrompts** | 4 | 2023 | CFT | prompt-based | none | architecture/PEFT | T5, SuperGLUE, custom instruction sets | average forgetting, FWT | general | • Đề xuất Progressive Prompts: đóng băng hoàn toàn LLM, chỉ huấn luyện và nối thêm các soft prompt tokens mới.<br>• Loại bỏ hoàn toàn sự can thiệp tham số, bảo vệ tuyệt đối tri thức cũ. |
| **Zhang2026_OLoRA_Forgetting** | 5 | 2026 | CFT | PEFT/LoRA | none | architecture/PEFT | LLaMA-2-7B, Alpaca, TruthfulQA, BLiMP, MMLU, Arithmetic Subtraction, RACE | Forgetting Gradient ($FG\%$) | general | • Thực nghiệm O-LoRA cho thấy giảm chỉ số quên cực tốt trên các tác vụ đọc hiểu/tri thức.<br>• O-LoRA rất nhạy cảm siêu tham số và có thể gây sụp đổ hiệu năng về 0% trên tác vụ logic/toán học khi $\alpha$ lớn. |
| **Le2025_LegalSLM** | 5 | 2025 | CFT, CPT | full_finetune, PEFT/LoRA | none | rehearsal/replay, none/analysis_only | LegalSLM (vi-law-nli, vi-law-qa, vilaw-syllo) | Accuracy, rubric-based score (0-1) | Vietnamese-law | • Đánh giá năng lực suy luận pháp lý của các SLMs tiếng Việt.<br>• Continued pre-training trên dữ liệu luật Việt Nam cải thiện mô hình 4B nhưng làm giảm hiệu năng mô hình 1.7B.<br>• Các mô hình đạt điểm trắc nghiệm/NLI rất cao (~98%) nhưng sụt giảm nặng ở tự luận Syllogism (~50%). |
| **Sumida2025_LUFY** | 5 | 2025 | agent | none/analysis_only | RAG, external_memory_agent | external_memory | CANDOR, LUFY-Dataset | human ratings, GPT-4o UX, Sentiment, Precision | general | • Thiết lập chatbot RAG dài hạn LUFY tích hợp đường cong quên nhận thức của con người.<br>• Áp dụng cơ chế quên 90% các câu thoại ít quan trọng giúp tăng 17% độ chính xác truy xuất và giảm tải bộ nhớ. |
| **Zheng2026_AgentLifelongSurvey** | 5 | 2026 | agent | none/analysis_only | external_memory_agent | external_memory | WebArena, OSWorld, Crafter, AlfWorld | AA, BWT, FWT | general | • Khảo sát toàn diện về học trọn đời cho LLM-based Agents qua 3 mô-đun: Perception, Memory, Action.<br>• Hệ thống hóa các cấu trúc bộ nhớ ngoài (Working, Episodic, Semantic, Parametric). |
| **Nguyen2025_VLQA** | 5 | 2025 | CFT | none/analysis_only, full_finetune | RAG | none | VLQA (59k điều luật, 3.1k QA) | Recall@K, MRR@10, ROUGE, BLEU | Vietnamese-law | • Giới thiệu bộ dữ liệu VLQA đầu tiên được gán nhãn chuyên gia cho QA pháp luật tiếng Việt.<br>• Đánh giá hiệu năng truy xuất điều luật và sinh câu trả lời của các LLMs hiện đại (GPT-4o, Qwen 2.5). |
| **Duong2026_ViLegalNLI** | 5 | 2026 | CFT | full_finetune | none | none | ViLegalNLI (42k cặp câu) | Accuracy, PMI analysis | Vietnamese-law | • Xây dựng bộ dữ liệu NLI pháp luật tiếng Việt quy mô lớn nhất.<br>• Áp dụng kỹ thuật loại bỏ nhiễu từ vựng (PMI lexical shortcuts) giúp đánh giá chính xác năng lực suy luận logic của LLMs. |
| **Su2026_ReGrad** | 5 | 2026 | CPT, CFT | PEFT/LoRA | gradient_bank (ReGrad-style) | external_memory, rehearsal/replay | LLaMA (1B-8B), General/Law/Medical datasets | F1-score, Accuracy, cross-domain performance | law, general, medical | • Đề xuất REGRAD: lưu trữ gradient LoRA của tài liệu mới ngoại tuyến (Gradient Bank).<br>• Khi inference, truy xuất gradient liên quan và cập nhật tạm thời vào LLM.<br>• Tránh hoàn toàn hiện tượng tích lũy trôi trọng số (Weight Drift) và không gây quên tri thức cũ. |
| **Wang2026_RAGUnlearning** | 5 | 2026 | CFT | none/analysis_only | RAG, external_memory | external_memory, prompt_constraints | Tiny-nq, Wikipedia topics, Harmful topics | ROUGE-L, USR, TPR@1%FPR, HOP | general, safety | • Đề xuất khung học quên hành vi nhẹ nhàng dựa trên RAG.<br>• Sử dụng tri thức unlearn gồm thành phần truy xuất P và ràng buộc Q để mô phỏng sự quên mà không thay đổi tham số.<br>• Có hiệu quả cao đối với cả mô hình mã nguồn đóng như GPT-4o và Gemini. |

---

## 2. Tổng hợp theo nhóm nghiên cứu (Task 2)

### Nhóm 1: Khảo sát & Tổng quan

#### (1) Giới thiệu nhóm
Chủ đề chung của nhóm này là sự phân loại có hệ thống và nền tảng lý thuyết của Học liên tục (Continual Learning - CL) được điều chỉnh đặc biệt cho các Mô hình Ngôn ngữ Lớn (LLMs). Trong bối cảnh CL cho LLMs, các khảo sát này giải quyết vấn đề phụ về việc khái niệm hóa cách thức xảy ra sự trôi dạt tri thức (knowledge drift) qua các giai đoạn huấn luyện khác nhau, định nghĩa thuật ngữ thống nhất và vạch ra hệ thống phân loại của các chiến lược giảm thiểu hiện có. Chúng liên quan đến quên thảm khốc bằng cách cung cấp một góc nhìn vĩ mô về cách việc tối ưu hóa cho các mục tiêu mới (ví dụ: tác vụ hoặc sở thích) làm hỏng các biểu diễn được thiết lập trong quá trình tiền huấn luyện ban đầu, giới thiệu khái niệm "Quên chéo giai đoạn" (Cross-Stage Forgetting) như một rủi ro lớn đối với sự căn chỉnh (alignment) hệ thống. Đặc biệt, xu hướng mới nhất đến năm 2026 đã mở rộng biên giới nghiên cứu từ việc đánh giá các mô hình tĩnh sang các Agent tự trị có khả năng tương tác và thích ứng liên tục với môi trường thực tế (Zheng2026_AgentLifelongSurvey).

#### (2) Tóm tắt nhóm
Các bài báo trong Nhóm 1 tổng hợp sự phát triển của học liên tục từ học sâu truyền thống (tập trung vào các mô hình chỉ có encoder và các tác vụ phân loại) sang các LLM tạo sinh hiện đại. Họ thiết lập một khung đồng thuận "Căn chỉnh ba giai đoạn" (Three-Stage Alignment): Tiền huấn luyện liên tục (Continual Pre-training - CPT) cho tri thức thực tế/miền, Tinh chỉnh hướng dẫn liên tục (Continual Instruction Tuning - CIT) cho các hướng dẫn cấp tác vụ, và Căn chỉnh liên tục (Continual Alignment - CA) cho các giá trị và sở thích của con người. Các cơ chế chính để giảm thiểu sự quên được phân loại thành bốn trường phái (paradigms): Dựa trên ôn tập (Rehearsal-based/Experience Replay), Dựa trên chính quy hóa (Regularization-based - hạn chế cập nhật tham số), Dựa trên kiến trúc (Architecture-based - mở rộng mô-đun/PEFT), và Bộ nhớ ngoài (External Memory - RAG phi tham số).

Khảo sát lộ trình mới nhất năm 2026 (Zheng2026_AgentLifelongSurvey) đã hệ thống hóa và nâng cấp khung học liên tục này lên cấp độ Agent thông qua mô hình POMDP có điều kiện mục tiêu, chia cấu trúc Agent thành 3 mô-đun cốt lõi: Nhận thức (Perception), Bộ nhớ (Memory - bao gồm Working, Episodic, Semantic, và Parametric Memory) và Hành động (Action). Trong đó, các nghiên cứu mới (như Sumida2025_LUFY) áp dụng thêm các mô hình tâm lý học về bộ nhớ và cơ chế quên để tối ưu hóa hiệu quả hoạt động của bộ nhớ ngoài phi tham số (Semantic & Episodic Memory), giúp cắt giảm ngữ cảnh dư thừa và nâng cao độ chính xác truy xuất.

#### (3) Chiều thiết kế & Khoảng trống nghiên cứu
*   **Các chiều thiết kế (Design Dimensions):**
    1.  *Căn chỉnh theo giai đoạn Alignment (Alignment Stage Alignment):* Phân biệt các phương pháp nhắm vào CPT (văn bản thô) so với CIT (mẫu hướng dẫn) so với CA (tối ưu hóa sở thích) hoặc mở rộng sang học trọn đời dạng Agent (Perception-Memory-Action).
    2.  *Tham số so với Phi tham số (Parametric vs. Non-parametric):* Liệu phương pháp có thay đổi vĩnh viễn trọng số mô hình (cập nhật tham số qua Continual SFT/Knowledge Editing) hay sử dụng các cơ chế lưu trữ bên ngoài (bộ nhớ ngoài, RAG, mô hình quên).
*   **Các khoảng trống nghiên cứu (Open Gaps):**
    1.  *Thiếu sự tập trung vào đa ngôn ngữ và tài nguyên thấp:* Hầu hết các phương pháp được khảo sát đều giả định cài đặt tiếng Anh có tài nguyên cao, khiến học liên tục xuyên ngôn ngữ vẫn chưa được nghiên cứu đầy đủ.
    2.  *Tính dễ bị tổn thương của các rào chắn bảo vệ Alignment (Alignment Guardrails):* Các khảo sát lưu ý rằng CIT và CPT thường xuyên phá vỡ căn chỉnh an toàn (CA), nhưng không đưa ra khung thống nhất tiêu chuẩn nào để thực hiện đánh giá đa giai đoạn đồng thời.
    3.  *Khoảng trống đánh giá định lượng cho Agent học trọn đời:* Các nghiên cứu về Agent (như Zheng2026_AgentLifelongSurvey) phần lớn chỉ dừng lại ở mô tả định tính (qualitative taxonomy) mà thiếu các so sánh hiệu năng định lượng chuẩn hóa trên cùng các môi trường động phức tạp.


---

### Nhóm 2: Quên thảm khốc & Benchmarks

#### (1) Giới thiệu nhóm
Chủ đề chung của Nhóm 2 là mô tả thực nghiệm và xây dựng benchmark định lượng để đánh giá sự quên thảm khốc trong quá trình tinh chỉnh hướng dẫn (instruction tuning). Nhóm này giải quyết vấn đề phụ về việc đo lường mức độ tinh chỉnh hướng dẫn trên các tác vụ hạ nguồn làm suy giảm khả năng lập luận chung, khả năng tuân theo hướng dẫn và các rào chắn an toàn của mô hình. Nó liên quan đến quên thảm khốc bằng cách phơi bày các nguyên nhân vật lý và động lực của sự quên (chẳng hạn như độ dốc của cảnh quan loss - loss landscape sharpness) và phát triển các benchmark chuẩn hóa (như TRACE và ConTinTin) để theo dõi sự trôi dạt tham số.

#### (2) Tóm tắt nhóm
Các bài báo trong Nhóm 2 cung cấp bằng chứng thực nghiệm chặt chẽ về sự quên thảm khốc trên nhiều kích thước mô hình và kiến trúc khác nhau. Một phát hiện chính là kiến trúc chỉ có bộ giải mã (Decoder-only) (ví dụ: LLaMA, BLOOMZ) vốn có khả năng chống quên tốt hơn so với các mô hình Encoder-Decoder (ví dụ: mT0). Từ góc nhìn động lực học, các bài báo như Li và các cộng sự (2024) giải thích sự quên thông qua hình học của cảnh quan loss: tinh chỉnh trên các tác vụ ngoài phân phối (out-of-distribution - OOD) làm biến dạng bề mặt loss, tạo ra các "hẻm núi" sắc nhọn nơi những thay đổi nhỏ về tham số cũng gây ra các đỉnh loss tăng vọt thảm khốc. Để giảm thiểu điều này, họ giới thiệu phương pháp Tối thiểu hóa nhạy cảm độ sắc nét (Sharpness-Aware Minimization - SAM) để tìm các cực tiểu phẳng, hoạt động hiệp đồng với ôn tập tỷ lệ thấp (low-rate rehearsal). Ở cấp độ dữ liệu, các bài báo chứng minh rằng tỷ lệ ôn tập cực thấp (0,25% đến 1,0% dữ liệu tiền huấn luyện) rất hiệu quả trong việc bảo tồn năng lực zero-shot. Về mặt định lượng, Kalajdzievski (2024) công thức hóa "Định luật tuyến tính nghịch đảo" (Inverse Linear Law) giữa tiến trình tinh chỉnh và sự quên, chứng minh rằng các quy tắc kinh nghiệm phổ biến như giảm rank LoRA hoặc dừng sớm là không hiệu quả vì sự quên bị khóa trực tiếp vào mức độ học tốt tác vụ mới.

#### (3) Chiều thiết kế & Khoảng trống nghiên cứu
*   **Các chiều thiết kế (Design Dimensions):**
    1.  *Loại thước đo (Metric Type):* Đánh giá dựa trên logit (so sánh phân phối xác suất với mô hình nền tảng) so với Hiệu năng thực tế (độ chính xác trên các benchmark hạ nguồn).
    2.  *Phạm vi đánh giá (Evaluation Scope):* Các chỉ số đặc thù của tác vụ (OP, BWT) so với Các chỉ số sức khỏe hệ thống toàn diện (Độ lệch tuân thủ hướng dẫn, Độ lệch an toàn).
*   **Các khoảng trống nghiên cứu (Open Gaps):**
    1.  *Giới hạn của Benchmark tĩnh:* Các benchmark hiện tại (TRACE, ConTinTin) sử dụng các tập dữ liệu cũ có khả năng đã rò rỉ vào kho ngữ liệu tiền huấn luyện của các LLM mới hơn, đòi hỏi phải lọc bỏ rò rỉ động (dynamic decontamination).
    2.  *Sự đánh đổi giữa An toàn và Năng lực (Safety-Capabilities Trade-off):* Các nghiên cứu thực nghiệm cho thấy các mô hình tinh chỉnh dễ dàng mất đi rào chắn an toàn (AdvBench), nhưng lại thiếu các phương pháp duy trì an toàn mà không làm giảm khả năng thích ứng miền (domain adaptation).

---

### Nhóm 3: Tiền huấn luyện liên tục & Định luật tỷ lệ

#### (1) Giới thiệu nhóm
Chủ đề chung của Nhóm 3 là tối ưu hóa lịch trình Tiền huấn luyện liên tục (Continual Pre-training - CPT) và rút ra các định luật tỷ lệ (scaling laws) để điều chỉnh các cập nhật đặc thù của miền. Nhóm này giải quyết vấn đề phụ về cách tiền huấn luyện liên tục các LLM trên văn bản thô (ví dụ: mã nguồn, luật pháp, y tế) mà không gây quên thảm khốc tri thức chung và tránh các bất thường về tối ưu hóa. Nó liên quan trực tiếp đến quên thảm khốc bằng cách mô hình hóa sự suy giảm của tri thức đã có từ trước như một hàm số của tỷ lệ trộn dữ liệu, lịch trình tốc độ học (learning rate schedules) và động lực học tối ưu hóa.

#### (2) Tóm tắt nhóm
Các bài báo trong Nhóm 3 giải quyết cơ chế của học liên tục trên ngân sách token khổng lồ. Thách thức chính được xác định là "Khoảng trống ổn định" (Stability Gap) — một sự gia tăng đột ngột, tạm thời trong validation loss khi chuyển sang một ngữ liệu mới. Yildiz (2025) và Gupta (2023) chỉ ra rằng đây là một hiện tượng nhân tạo tối ưu hóa do trạng thái động lượng (momentum) và tốc độ học của AdamW, điều này phải được giải quyết bằng cách "hâm nóng lại" (re-warming) và "giảm dần lại" (re-decaying) tốc độ học một cách cẩn thận. Đối với lịch trình dữ liệu, Ibrahim (2024) giới thiệu "Ôn tập tương đương tính toán" (Compute-equivalent Replay), cho thấy rằng đối với sự dịch chuyển phân phối nhẹ (Weak Shift), ôn tập 5% dữ liệu giúp đạt hiệu năng ngang bằng với việc huấn luyện lại từ đầu trong khi giảm một nửa chi phí tính toán. Tuy nhiên, đối với sự dịch chuyển ngôn ngữ (Strong Shift), yêu cầu ôn tập tăng lên đến 15-25%. Ở cấp độ lý thuyết, Que và các cộng sự (2024) giới thiệu **Định luật D-CPT** (D-CPT Law), mở rộng định luật tỷ lệ Chinchilla bằng cách tích hợp tỷ lệ pha trộn `r` để dự đoán validation loss đặc thù của miền. Họ cũng trình bày "Hệ số học được đặc thù miền" (Domain-specific Learnable Coefficient - DLC) để mô tả độ khó của miền, cho phép các nhà phát triển dự đoán tỷ lệ trộn tối ưu chỉ bằng 1% ngân sách huấn luyện thông thường.

#### (3) Chiều thiết kế & Khoảng trống nghiên cứu
*   **Các chiều thiết kế (Design Dimensions):**
    1.  *Lịch trình tốc độ học (Learning Rate Schedule):* Re-warming/re-decay cosine so với Lịch trình vô hạn/ủ nhiệt (infinite/annealing).
    2.  *Mức độ nghiêm trọng của dịch chuyển phân phối (Distribution Shift Severity):* Dịch chuyển nhẹ (văn bản đặc thù miền trong cùng một ngôn ngữ) so với Dịch chuyển mạnh (chuyển đổi xuyên ngôn ngữ).
*   **Các khoảng trống nghiên cứu (Open Gaps):**
    1.  *Chi phí tính toán của việc khớp định luật tỷ lệ (Fitting Scaling Laws):* Việc khớp các tham số cho các định luật tỷ lệ phức tạp vẫn đòi hỏi phải chạy nhiều mô hình quy mô nhỏ, đây vẫn là một rào cản cho các nhóm bị hạn chế về tài nguyên.
    2.  *Ràng buộc của bộ mã hóa từ vựng (Tokenizer) trong CPT xuyên ngôn ngữ:* Việc chuyển từ tiếng Anh sang các ngôn ngữ ít tài nguyên (ví dụ: tiếng Việt) yêu cầu mở rộng vốn từ vựng, điều này làm gián đoạn các biểu diễn token đã có từ trước và tăng nhanh tốc độ quên.

---

### Nhóm 4: PEFT/LoRA & Học liên tục dựa trên Prompt

#### (1) Giới thiệu nhóm
Chủ đề chung của Nhóm 4 là việc sử dụng Tinh chỉnh hiệu quả tham số (Parameter-Efficient Fine-Tuning - PEFT), chủ yếu là LoRA và soft prompts, làm cơ chế cấu trúc để ngăn chặn sự quên thảm khốc. Nhóm này giải quyết vấn đề phụ về "Xung đột tham số" (Parameter Collision) — nơi các cập nhật tuần tự vào cùng một tập hợp tham số ghi đè lên tri thức đã học trước đó. Nó liên quan đến quên thảm khốc bằng cách chứng minh rằng việc cô lập các cập nhật tham số vào các không gian hạng thấp (low-rank spaces) hoặc các chuyên gia đặc thù của tác vụ (task-specific experts) có thể ngăn chặn vật lý các gradient của tác vụ mới can thiệp vào các trọng số lịch sử.

#### (2) Tóm tắt nhóm
Các bài báo trong Nhóm 4 phân tích hành vi của LoRA và soft prompts trong các kịch bản đa tác vụ. Biderman và các cộng sự (2024) xác lập rằng "LoRA học ít hơn và quên ít hơn" so với Tinh chỉnh toàn bộ (Full Fine-Tuning). Thông qua Phân tích suy hao giá trị suy biến (SVD), họ chỉ ra rằng các cập nhật CPT có hạng nội tại cao (yêu cầu hạng $r \approx 2000$), khiến LoRA hạng thấp ($r \le 256$) không đủ khả năng hấp thụ tri thức khổng lồ trong quá trình tiền huấn luyện. Tuy nhiên, đối với Tinh chỉnh hướng dẫn (Instruction Fine-Tuning - IFT), LoRA hạng cao có tính cạnh tranh rất lớn và ngăn ngừa "sụp đổ tính đa dạng" (overfitting vào các cấu trúc đầu ra cố định). Để giải quyết xung đột tham số trong quá trình học tác vụ tuần tự, Wang và các cộng sự (2023) giới thiệu phương pháp học Không gian con trực giao (Orthogonal Subspace), buộc các cập nhật tác vụ mới chiếu lên phần bù trực giao của không gian tác vụ trước đó. Các mở rộng kiến trúc khác bao gồm việc bổ sung lũy tiến các token soft prompt (Razdaibiedina, 2023) và Soft LoRA dựa trên MoE (Han, 2025), giúp định tuyến các token đến các chuyên gia LoRA đặc thù của tác vụ để cô lập các thay đổi tham số và loại bỏ can thiệp chéo giữa các tác vụ.

#### (3) Chiều thiết kế & Khoảng trống nghiên cứu
*   **Các chiều thiết kế (Design Dimensions):**
    1.  *Loại cô lập tham số (Parameter Isolation Type):* Cô lập cứng (đóng băng trọng số + các token prompt mới/adapters mới) so với Cô lập mềm (chiếu trực giao/chính quy hóa các trọng số dùng chung).
    2.  *Kiến trúc Adapter (Adapter Architecture):* Một adapter dùng chung duy nhất so với Các adapter định tuyến đa chuyên gia (MoE-LoRA).
*   **Các khoảng trống nghiên cứu (Open Gaps):**
    1.  *Chi phí bộ nhớ GPU của hệ thống đa Adapter:* Việc lưu trữ và chạy nhiều adapter trực giao hoặc các lớp định tuyến MoE làm tăng bộ nhớ GPU đỉnh, làm giảm lợi ích chính của PEFT.
    2.  *Độ nhạy siêu tham số trong các ràng buộc trực giao:* Bộ chính quy hóa trực giao cực kỳ nhạy cảm với tỷ lệ alpha và rank, thường gây sụp đổ hiệu năng đột ngột trên các tác vụ logic/toán học.

---

### Nhóm 5: Bán tham số / RAG / Lifelong Agents & Miền Pháp luật Việt Nam

#### (1) Giới thiệu nhóm
Chủ đề chung của Nhóm 5 là sự tích hợp của các hệ thống bán tham số (kết hợp cập nhật tham số với cơ sở dữ liệu bên ngoài, RAG, hoặc các ngân hàng gradient) và xây dựng các benchmark pháp lý chất lượng cao cho các ngôn ngữ ít tài nguyên (tiếng Việt). Nhóm này giải quyết vấn đề phụ về cách cập nhật các LLM với tri thức chuyên ngành thay đổi nhanh chóng (chẳng hạn như luật pháp) trong khi tránh biến dạng trọng số vĩnh viễn (Trôi dạt trọng số - Weight Drift) và tối ưu hóa bộ nhớ dài hạn. Nó liên quan đến quên thảm khốc bằng cách chỉ ra rằng việc lưu trữ thông tin hoặc gradient bên ngoài giúp tránh được tình trạng lưỡng nan giữa tính ổn định và tính mềm dẻo (stability-plasticity dilemma).

#### (2) Tóm tắt nhóm
Nhóm 5 đại diện cho những cải tiến mới nhất trong việc kết hợp các mô hình tham số với các mô-đun bộ nhớ ngoài. Su và các cộng sự (2026) giới thiệu **REGRAD (Gradient có thể truy xuất - Retrievable Gradients)**, giúp tránh trôi dạt trọng số tích lũy trong CPT bằng cách lưu trữ các gradient LoRA của tài liệu mới trong một "Ngân hàng Gradient" (Gradient Bank) ngoại tuyến. Trong quá trình suy luận, hệ thống truy xuất các gradient liên quan và áp dụng tạm thời vào trọng số mô hình, đạt được chất lượng cập nhật cấp độ tham số mà không gây trôi dạt trọng số vĩnh viễn. Đối với học trọn đời cấp Agent, Sumida (2025) mô hình hóa đường cong trí nhớ của con người (LUFY), chỉ ra rằng việc quên đi 90% lượt hội thoại lịch sử có mức độ quan trọng thấp giúp tăng độ chính xác truy xuất RAG thêm 17% và ngăn ngừa lộn xộn cửa sổ ngữ cảnh. Trong miền pháp luật Việt Nam, Lê và các cộng sự (2025) giới thiệu LegalSLM (VLSP 2025), đánh giá các SLM tiếng Việt trên các tác vụ QA, NLI và Tam đoạn luận (Syllogism). Họ tiết lộ rằng mặc dù các mô hình đạt 98% trên phân loại/NLI, nhưng chúng giảm xuống 50% trên lập luận tam đoạn luận tự luận. Việc tạo ra VLQA (Nguyễn, 2025) và ViLegalNLI (Dương, 2026) cung cấp các tập dữ liệu QA pháp lý (59k điều khoản) và NLI (42k cặp câu đã được lọc bỏ các lối tắt từ vựng bằng PMI) được gán nhãn chuyên gia nhằm hỗ trợ thích ứng miền pháp luật chặt chẽ.

#### (3) Chiều thiết kế & Khoảng trống nghiên cứu
*   **Các chiều thiết kế (Design Dimensions):**
    1.  *Tính bền vững của bộ nhớ (Memory Persistence):* Thay đổi tham số vĩnh viễn (CPT/CFT) so với Thay đổi động tạm thời (truy xuất gradient ReGrad) so với Tiêm ngữ cảnh phi tham số (RAG).
    2.  *Loại tác vụ pháp lý (Legal Task Type):* QA thực tế (nặng về truy xuất) so với Logic/Lập luận (tam đoạn luận, áp dụng quy tắc pháp lý).
*   **Các khoảng trống nghiên cứu (Open Gaps):**
    1.  *Thiếu hụt lập luận tam đoạn luận ở các SLM:* Các SLM tiếng Việt hiện tại thất bại trước các lập luận tam đoạn luận pháp lý phức tạp ngay cả khi được huấn luyện CPT, cho thấy khoảng cách giữa truy xuất bộ nhớ và lập luận chủ động.
    2.  *Độ trễ suy luận cao của việc chèn gradient động:* Việc áp dụng gradient động tại thời điểm suy luận (ReGrad) gây ra độ trễ tính toán, khiến việc triển khai thời gian thực gặp nhiều thách thức.

---

## 3. Đề cương liên nhóm cho Khảo sát / Đề xuất nghiên cứu (Task 3)

Dưới đây là đề xuất đề cương gồm 7 phần cho một đề xuất nghiên cứu/khảo sát về **"Học liên tục cho các Mô hình Ngôn ngữ Lớn trong điều kiện Quên thảm khốc: Góc nhìn từ Bán tham số và Miền Pháp luật Tài nguyên thấp"**:

### Phần 1: Giới thiệu và Nền tảng của Học liên tục trong LLMs
*   **Mục tiêu:** Thiết lập định nghĩa cốt lõi về thế lưỡng nan giữa Tính ổn định - Tính mềm dẻo (Stability-Plasticity) trong AI tạo sinh, phân định vòng đời căn chỉnh ba giai đoạn (CPT, CFT, CA), và phân biệt học liên tục với chỉnh sửa mô hình cục bộ.
*   **Tài liệu hỗ trợ:** Các khảo sát Nhóm 1 (Wu2024_CL_Survey, Shi2024_CL_Survey, Zheng2024_LL_Survey, Guo2025_CL_GenAI) và khảo sát lộ trình Agent 2026 (Zheng2026_AgentLifelongSurvey).

### Phần 2: Quên thảm khốc: Động lực học, Cảnh quan Loss và Benchmarks
*   **Mục tiêu:** Phân tích hình học của sự quên thảm khốc (cực tiểu sắc nhọn so với cực tiểu phẳng), khám phá mối quan hệ tuyến tính giữa các cập nhật tinh chỉnh và sự quên (rsLoRA, Định luật tuyến tính nghịch đảo), và vạch ra các giao thức đánh giá (TRACE, ConTinTin, thước đo dựa trên Logit).
*   **Tài liệu hỗ trợ:** Nhóm 2 (Luo2025_EmpCF, Wang2023_TRACE, Scialom2022_FineTuned, Yin2022_ConTinTin, Li2024_RevisitingCF, Kalajdzievski2024_ScalingForgetting).

### Phần 3: Động lực học Tối ưu hóa Tiền huấn luyện liên tục và Định luật tỷ lệ
*   **Mục tiêu:** Giải quyết cơ chế tiền huấn luyện liên tục trên dữ liệu thô, nêu chi tiết các nguyên nhân gây ra "Stability Gap" (mất cân bằng động lượng của optimizer), các yêu cầu đối với lịch trình LR re-warming/vô hạn, và toán học đằng sau Định luật D-CPT để dự đoán validation loss của hỗn hợp miền.
*   **Tài liệu hỗ trợ:** Nhóm 3 (Gupta2023_CPT_Warmup, Yildiz2025_InvestigatingCPT, Ibrahim2024_StrategiesCPT, Du2024_UnlockingCL, Que2024_DCPTLaw, Bethune2025_ScalingLawsInjection, Guo2024_MitigateStabilityGap).

### Phần 4: Học liên tục Hiệu quả tham số và Dạng mô-đun
*   **Mục tiêu:** Xem xét các giới hạn dung lượng của các cập nhật hạng thấp (ràng buộc hạng SVD của LoRA trong CPT), giảm thiểu xung đột tham số thông qua chiếu không gian con trực giao (O-LoRA), và mở rộng kiến trúc thông qua progressive prompting và Multi-Expert Soft LoRA (SLIM).
*   **Tài liệu hỗ trợ:** Nhóm 4 (Hu2021_LoRA, Wang2023_OrthogonalSubspace, Biderman2024_LoRALearnsLess, Han2025_SLIM, Yang2024_ParameterCollision, Gao2024_HigherLayersLoRA, Razdaibiedina2023_ProgressivePrompts) và Nhóm 5 (Zhang2026_OLoRA_Forgetting).

### Phần 5: Học liên tục Bán tham số, Tăng cường bộ nhớ và Dạng Agent
*   **Mục tiêu:** Đề xuất sự dịch chuyển sang các hệ thống bán tham số, nêu chi tiết Retrievable Gradients (REGRAD) như một phương pháp thực hiện cập nhật tham số cấp tài liệu tại thời điểm chạy (runtime), và các mô hình trí nhớ tâm lý học (LUFY) để tối ưu hóa khả năng duy trì bộ nhớ dài hạn của agent.
*   **Tài liệu hỗ trợ:** Nhóm 5 (Sumida2025_LUFY, Zheng2026_AgentLifelongSurvey, Su2026_ReGrad, Wang2026_RAGUnlearning).


### Phần 6: Nghiên cứu điển hình: Thích ứng và Đánh giá miền Pháp luật Việt Nam
*   **Mục tiêu:** Tập trung vào miền pháp lý tài nguyên thấp (tiếng Việt), phân tích hành vi của các SLM tiếng Việt trên các tác vụ chuyên biệt (Bảng xếp hạng LegalSLM), việc tạo ra các kho ngữ liệu QA được gán nhãn chuyên gia (VLQA), và giảm thiểu các lối tắt từ vựng trong NLI (ViLegalNLI).
*   **Tài liệu hỗ trợ:** Nhóm 3 (Que2024_DCPTLaw - miền CPT Luật) và Nhóm 5 (Le2025_LegalSLM, Nguyen2025_VLQA, Duong2026_ViLegalNLI).

### Phần 7: Thách thức mở và Hướng đi tương lai
*   **Mục tiêu:** Vạch ra các hướng nghiên cứu quan trọng trong tương lai, bao gồm mở rộng vốn từ vựng trong CPT xuyên ngôn ngữ, giải quyết sự đánh đổi giữa an toàn và độ hữu dụng (safety-utility) trong các cập nhật hướng dẫn, và nâng cao hiệu quả suy luận của việc truy xuất gradient động.
*   **Tài liệu hỗ trợ:** Tổng hợp khoảng trống từ tất cả các nhóm, nêu bật nhu cầu học liên tục mạnh mẽ, độ trễ thấp và bảo toàn an toàn trong các miền chuyên biệt tài nguyên thấp.

---

### Các kết nối quan trọng liên nhóm

```mermaid
graph TD
    A[Định luật tỷ lệ cho sự quên] -- "Định luật tuyến tính nghịch đảo (G2/G3)<br>Khóa FT Loss vào sự quên" --> B[Không gian ràng buộc PEFT / LoRA]
    B -- "Chiếu trực giao (G4/G5)<br>Ngăn ghi đè tham số" --> C[Bán tham số / Bộ nhớ Gradient]
    C -- "Gradient động (G5 REGRAD)<br>Tránh hoàn toàn trôi dạt trọng số" --> D[LLM Pháp luật đặc thù miền]
    E[Định luật D-CPT (G3)] -- "Dự đoán tỷ lệ trộn tối ưu<br>cho tiền huấn luyện Luật" --> D
    F[Sự quên tâm lý học (G5)] -- "Cắt tỉa lộn xộn ngữ cảnh<br>trong RAG Luật dài hạn" --> D
```

1.  **Kết nối Tính ổn định - Tính mềm dẻo (Định luật tỷ lệ $\leftrightarrow$ LoRA):** Định luật tuyến tính nghịch đảo của Kalajdzievski (Nhóm 2) chỉ ra sự quên là một hàm số của fine-tuning loss. Vì LoRA (Nhóm 4) có dung lượng thấp hơn (hạng SVD nội tại thấp), nó học ít hơn nhưng cũng tự nhiên quên ít hơn. Tuy nhiên, trong CPT, dung lượng thấp này ngăn cản mô hình hấp thụ các sự kiện miền mới, chứng minh rằng LoRA tiêu chuẩn không thể thay thế cho tiền huấn luyện toàn bộ nhưng hoạt động như một bộ điều tiết mạnh mẽ trong SFT.
2.  **Giảm thiểu trôi dạt trọng số (LoRA $\leftrightarrow$ ReGrad):** Việc tinh chỉnh tác vụ tuần tự trên một LoRA duy nhất gây ra xung đột tham số (Yang, 2024). ReGrad (Su, 2026) giải quyết vấn đề này bằng cách chuyển độ cập nhật tham số thành một tác vụ truy xuất bán tham số — lưu trữ các cập nhật trọng số LoRA dưới dạng các "gradient" và tải chúng động, từ đó bỏ qua hoàn toàn sự trôi dạt trọng số được mô tả trong Nhóm 2.
3.  **Ứng dụng Pháp lý Tài nguyên thấp (D-CPT $\leftrightarrow$ Benchmark Pháp luật Việt Nam):** Để xây dựng LLM Pháp luật Việt Nam, Định luật D-CPT (Nhóm 3) cung cấp khung toán học để tính toán tỷ lệ pha trộn tối ưu giữa văn bản tiếng Việt chung và các bộ luật. Trong quá trình triển khai, cơ chế cắt tỉa bộ nhớ RAG của LUFY (Nhóm 5) và việc lọc các lối tắt từ vựng của ViLegalNLI (Nhóm 5) đảm bảo rằng suy luận pháp lý của SLM có cơ sở toán học và không bị thiên lệch bởi từ vựng.

---

## 4. Gợi ý về Bảng và Hình ảnh (Gợi ý thiết kế) (Task 4)

### Các bảng toàn cầu

#### Bảng 1: Sự đánh đổi giữa Học liên tục tham số và Bán tham số
*   **Loại:** Bảng so sánh liên nhóm (Nhóm 3, 4, và 5).
*   **Các cột:** `[Chiều đánh giá | Tinh chỉnh toàn bộ (Full FT) | LoRA/PEFT | Adapters trực giao (O-LoRA) | Ngân hàng Gradient (ReGrad) | RAG (LUFY)]`
*   **Các dòng:**
    *   *Chi phí lưu trữ:* O(1) so với O(Adapters) so với O(Adapters tác vụ) so với O(Cơ sở dữ liệu Gradient) so với O(Cơ sở dữ liệu vector).
    *   *Chi phí tính toán khi huấn luyện:* Cao so với Thấp so với Trung bình (do chính quy hóa trực giao) so với Thấp (tạo gradient ngoại tuyến) so với Không có.
    *   *Tính ổn định (Ngăn chặn quên):* Không/Thấp so với Trung bình so với Cao (cô lập tác vụ 100%) so với Tuyệt đối (không trôi dạt trọng số) so với Tuyệt đối.
    *   *Tính mềm dẻo (Hấp thụ miền mới):* Cao so với Thấp (CPT) / Cao (IFT) so với Trung bình (bị giới hạn bởi không gian trực giao) so với Cao so với Không có (không thay đổi trọng số).
    *   *Độ trễ suy luận:* Độ trễ cơ sở so với Cơ sở + epsilon so với Cơ sở + lựa chọn tác vụ so với Cơ sở + áp dụng gradient động (Cao) so với Cơ sở + tra cứu truy xuất.

#### Bảng 2: Ma trận đánh giá LLM Pháp luật Việt Nam
*   **Loại:** Bảng đánh giá mục tiêu theo nhóm (Nhóm 5).
*   **Các cột:** `[Tập dữ liệu | Loại tác vụ | Định dạng đầu vào | Định dạng đầu ra | Thước đo đánh giá | Hiệu năng mô hình cơ sở (Qwen-3-4B)]`
*   **Các dòng:**
    *   *VLQA:* Hỏi đáp pháp lý, Ngữ cảnh (Điều luật) + Câu hỏi $\rightarrow$ Câu trả lời tự do, đánh giá qua ROUGE-L / BLEU-4 / Độ chính xác theo chuyên gia. Cơ sở: ~59%.
    *   *ViLegalNLI:* Suy luận ngôn ngữ tự nhiên, Giả định (Luật) + Giả thuyết $\rightarrow$ Phân loại {Kéo theo (Entailment), Không kéo theo (Non-entailment)}, đánh giá qua Độ chính xác (Accuracy). Cơ sở: ~96%.
    *   *LegalSLM (Syllogism):* Lập luận tam đoạn luận, Tình huống pháp lý $\rightarrow$ Lập luận + Kết luận, đánh giá qua Điểm số của con người dựa trên tiêu chí (rubric) (0-1). Cơ sở: ~59.5%.

---

### Các sơ đồ khái niệm

#### Sơ đồ 1: Luồng xử lý học liên tục hợp nhất giữa tham số và bán tham số
*   **Mô tả:**
    *   **Giai đoạn đầu vào:** Một dòng tài liệu đầu vào ($D_t$) đại diện cho tri thức mới (ví dụ: các cập nhật luật pháp Việt Nam).
    *   **Tách luồng:**
        *   *Luồng tham số (Trên cùng):* Tài liệu đi vào một **Khối tiền huấn luyện liên tục (CPT)**. Khối này làm nổi bật **Stability Gap** (vấn đề thiết lập lại động lượng). Trọng số của **LLM ($W_{base}$)** được cập nhật trực tiếp $\rightarrow$ khả năng gây ra **Trôi dạt trọng số (Weight Drift) & Quên thảm khốc (Catastrophic Forgetting)**.
        *   *Luồng mô-đun/PEFT (Giữa):* Gradient được tính toán và ràng buộc. Một **Khối chiếu trực giao** chiếu các cập nhật lên một không gian con trực giao $\rightarrow$ Cập nhật các **LoRA Adapters đặc thù tác vụ ($A_t, B_t$)** $\rightarrow$ LLM chạy với các adapters được kích hoạt.
        *   *Luồng bán tham số (Dưới cùng):* Tài liệu được xử lý ngoại tuyến để tính toán gradient. Các gradient này được lưu trong một **Ngân hàng Gradient (Cơ sở dữ liệu)**. Tại thời điểm suy luận, truy vấn của người dùng sẽ truy xuất các gradient liên quan, các gradient này được tạm thời cộng vào trọng số LLM bị đóng băng ($\Delta W_t = W_{base} + \text{retrieve}(\text{Grad Bank})$) $\rightarrow$ không xảy ra trôi dạt trọng số vĩnh viễn.
    *   **Đầu ra đánh giá:** Tất cả các luồng đều cho ra các dự đoán được đánh giá dựa trên cả thước đo chung (WikiText, MMLU) và độ chính xác của tác vụ.

#### Sơ đồ 2: Nghiên cứu điển hình: Kiến trúc LLM Pháp luật Việt Nam
*   **Mô tả:**
    *   **Tiêm dữ liệu (D-CPT):** Văn bản web tiếng Việt chung (tỷ lệ trộn ôn tập 25% để ngăn chặn thiên lệch tiếng Anh/trôi dạt ngôn ngữ) và Ngữ liệu Luật Việt Nam (75% đặc thù miền) được trộn với nhau bằng cách sử dụng **tỷ lệ pha trộn `r` của Định luật D-CPT**.
    *   **Cốt lõi mô hình:** Dữ liệu pha trộn huấn luyện một **Mô hình Ngôn ngữ Nhỏ (SLM) tiếng Việt** (ví dụ: Qwen-3-4B) sử dụng **Lịch trình Tốc độ học Re-warming** để tránh khoảng trống ổn định (stability gap).
    *   **Vòng lặp suy luận (Bộ nhớ lai):**
        *   *Truy vấn:* Người dùng hỏi: *"Tôi có được miễn thuế thu nhập cá nhân khi bán nhà cho bố mẹ đẻ không?"*
        *   *Truy xuất kép (Dual Retrieval):*
            *   *Truy xuất RAG:* Truy vấn **cơ sở dữ liệu VLQA** (59k điều khoản) $\rightarrow$ truy xuất Điều 4 Luật Thuế thu nhập cá nhân $\rightarrow$ đưa vào làm ngữ cảnh.
            *   *Truy xuất Gradient:* Truy vấn **Ngân hàng Gradient ReGrad** $\rightarrow$ truy xuất các gradient tối ưu hóa tương ứng cho các cập nhật thuế thu nhập cá nhân $\rightarrow$ cập nhật tạm thời vào trọng số SLM.
        *   *Lập luận Logic:* SLM được cập nhật động sẽ chạy prompt dưới sự hướng dẫn của **Đường dẫn tăng cường lập luận (Reasoning-augmented Paths - RCL)** để tạo ra một **Tam đoạn luận pháp lý** chặt chẽ (Đại tiền đề, Tiểu tiền đề, Kết luận).
        *   *Đầu ra:* Lời khuyên pháp lý được định dạng chính xác và đúng thực tế.
