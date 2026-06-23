# Tổng hợp Số liệu & Hình vẽ: Group 5 (Continual Learning, Legal NLP & RAG Benchmarks)

Tài liệu này hệ thống hóa toàn bộ các hình vẽ (Figures) và bảng biểu (Tables) của 7 bài báo thuộc **Group 5** nhằm hỗ trợ việc phân tích định lượng, vẽ biểu đồ và tích hợp vào các báo cáo kỹ thuật của dự án **VN-Legal-AI / CDNCTQ**.

---

## 1. Paper 1: Mitigating Catastrophic Forgetting in Fine-Tuned Large Language Models: An Experimental Study of LoRA and O-LoRA

### Bảng số liệu (Tables)
*   **Table 1: Hiệu năng của LoRA và các phương pháp PEFT dưới các thiết lập siêu tham số khác nhau**
    *   *Nội dung:* Thống kê độ chính xác trên TruthfulQA (MC1, MC2), Arithmetic 2-digit & 4-digit Subtraction (2ds, 4ds), BLiMP Causative, và GlobalFacts.
    *   *Chi tiết:* Cho thấy hiện tượng sụp đổ (độ chính xác về 0.0000) trên các tác vụ toán học (arithmetic_2ds, arithmetic_4ds) khi tỷ lệ $\alpha/r$ của LoRA tăng cao ($\alpha \ge 64$ với $r=64$, hoặc $\alpha \ge 64$ với $r=128$).
*   **Table 2: Các tập dữ liệu đánh giá và các thành phần cấu thành**
    *   *Nội dung:* Chia làm 3 nhóm năng lực: Domain Knowledge (MMLU: STEM, Social, Human, Other), Reasoning (BoolQ, PIQA, Winogrande, HellaSwag, MathQA, Mutual), và Reading Comprehension (RACE-high, RACE-middle).
*   **Table 3: Chỉ số quên FG (%) của LoRA và các phương pháp PEFT dưới tinh chỉnh liên tục**
    *   *Nội dung:* Đo lường mức độ quên trên Domain Knowledge, Reasoning và Reading Comprehension.
*   **Table 4: Chỉ số quên FG (%) khi kết hợp NEFTune với LoRA**
    *   *Nội dung:* Đo lường xem việc thêm nhiễu embedding (noise=1, noise=5) có giúp giảm quên hay không. Kết quả cho thấy FG vẫn ở mức cao (>40%).
*   **Table 5: Chỉ số quên FG (%) của O-LoRA dưới tinh chỉnh liên tục**
    *   *Nội dung:* O-LoRA giúp giảm chỉ số quên rõ rệt (Domain Knowledge FG giảm xuống 1.11% từ mức ~40%; Reasoning FG giảm xuống 0.43%; Reading Comprehension FG đạt mức âm -1.2%, biểu thị hiệu năng tăng).
*   **Table 6: Hiệu năng của O-LoRA dưới các thiết lập siêu tham số khác nhau trên các tác vụ đơn**
    *   *Nội dung:* Thể hiện sự nhạy cảm của O-LoRA với rank $r$ và $\alpha$, vẫn bị hiện tượng sụp đổ hiệu năng về 0% trên tác vụ toán học khi $\alpha$ lớn.

---

## 2. Paper 2: Overview of the LegalSLM Shared Task: Evaluating Legal Reasoning of Vietnamese Small Language Models

### Hình vẽ (Figures)
*   **Figure 1: Thống kê phân bổ dữ liệu của LegalSLM**
    *   *Mô tả:* Biểu đồ cột biểu diễn số lượng mẫu trên 3 tác vụ chính: 800 mẫu trắc nghiệm (Multiple Choice), 850 mẫu suy luận tự nhiên (NLI), và 300 mẫu tam đoạn luận (Syllogism) - Tổng cộng 1,950 mẫu.
*   **Figure 2, 3 & 4: Ví dụ minh họa định dạng dữ liệu các tác vụ**
    *   *Hình 2:* Ví dụ về câu hỏi trắc nghiệm tìm mã số thuế trên hóa đơn.
    *   *Hình 3:* Ví dụ về cặp Premise - Hypothesis của tác vụ NLI liên quan đến quy định miễn thị thực cho công dân Ba Lan, Séc, Thụy Sỹ.
    *   *Hình 4:* Ví dụ về một câu hỏi suy luận tam đoạn luận liên quan đến xác thực sinh trắc học khi mở tài khoản ngân hàng online tại BIDV.

### Bảng số liệu (Tables)
*   **Table 1: Các siêu tham số huấn luyện mô hình Baseline**
    *   *Chi tiết:* Batch size = 256, Context length = 4096, Learning Rate = $10^{-5}$, Epoch = 1.
*   **Table 2: Kết quả xếp hạng của 10 đội tham gia thử thách LegalSLM**
    *   *Nội dung:* Bảng điểm vi-law-nli, vi-law-qa, vilaw-syllo và điểm trung bình. Đội Bosch@AI dẫn đầu với điểm trung bình 0.8108.
*   **Table 3: Điểm số của các mô hình Baseline**
    *   *Nội dung:* So sánh Qwen-3-1.7B và Qwen-3-4B bản gốc (base) và bản của BTC (ours) sau khi pretrain trên 96k tài liệu luật Việt Nam.

---

## 3. Paper 3: Enhancing Long-term RAG Chatbots with Psychological Models of Memory Importance and Forgetting

### Hình vẽ (Figures)
*   **Figure 1: Câu lệnh (Prompt) dùng để LLM ước tính độ quan trọng (L)**
    *   *Mô tả:* Prompt yêu cầu đánh giá độ quan trọng của đoạn hội thoại trên thang điểm từ 1 đến 10 dựa trên độ hữu dụng trong tương lai.
*   **Figure 2: Quy trình tính toán độ quan trọng bộ nhớ của LUFY**
    *   *Mô tả:* Sơ đồ khối biểu diễn luồng tích hợp: Arousal (A), Perplexity (P), LLM Importance (L), R1, R2 qua các trọng số $w$ để tính sức mạnh $S$, sau đó đưa vào đường cong quên để tính Importance.
*   **Figure 3: Kiến trúc sinh câu trả lời RAG của LUFY**
    *   *Mô tả:* Luồng xử lý: User input $\rightarrow$ So khớp tương đồng ngữ nghĩa kết hợp điểm Importance $\rightarrow$ Truy xuất ký ức tốt nhất $\rightarrow$ Sinh câu trả lời qua LLM.
*   **Figure 4: Prompt sinh câu thoại của Chatbot**
    *   *Mô tả:* Khung prompt truyền dữ liệu: Key Summary, Recent Utterances, và Relevant Memory.
*   **Figure 5: Sơ đồ cơ chế quên (Forgetting Process)**
    *   *Mô tả:* Cuối phiên hội thoại, toàn bộ các ký ức được sắp xếp theo điểm Importance và hệ thống chỉ giữ lại top 10%, xóa bỏ 90% còn lại.

### Bảng số liệu (Tables)
*   **Table 1: So sánh chiều dài hội thoại của bộ dữ liệu LUFY-Dataset với các bộ dữ liệu khác**
    *   *Nội dung:* Số turns và số từ trung bình của cuộc trò chuyện. Bộ dữ liệu LUFY đạt trung bình 253.8 turns và 5,538.5 từ (dài gấp 4.5 lần dataset lớn nhất trước đó là MSC).
*   **Table 2: Bộ trọng số tối ưu hóa cho các chỉ số tâm lý học**
    *   *Chi tiết:* $w_A = 2.76$, $w_P = -0.28$, $w_L = 0.44$, $w_{R1} = 1.02$, $w_{R2} = -0.012$.
*   **Table 3: So sánh số lượng ký ức được giữ lại qua 4 phiên trò chuyện**
    *   *Chi tiết:* Naive RAG giữ lại 131.2 ký ức (100%), trong khi MemoryBank giữ 13.5 (9.99%) và LUFY giữ 12.8 (10.07%).
*   **Table 4: Phân biệt các hệ thống RAG theo cơ chế quên và phương thức truy xuất**
*   **Table 5 & 6: Ví dụ về hội thoại và các cặp câu hỏi - câu trả lời tương ứng trong LUFY-Dataset**
*   **Table 7 & 8: Kết quả đánh giá chất lượng hội thoại bằng Con người và GPT-4o**
    *   *Chi tiết:* Điểm Personalization, Flow of Conv, và Overall qua 4 sessions. LUFY đạt điểm vượt trội ở Session 4.
*   **Table 10: Kết quả phân tích cảm xúc (Sentiment Analysis)**
    *   *Chi tiết:* LUFY đạt điểm cảm xúc trung bình 0.50 (Session 4 đạt 0.62), cao hơn hẳn Naive RAG (0.40) và MemoryBank (0.38).

---

## 4. Paper 4: Lifelong Learning of Large Language Model based Agents: A Roadmap

### Hình vẽ (Figures)
*   **Fig. 1: Đồ thị tăng trưởng số lượng ấn phẩm về học trọn đời và LLM Agents**
    *   *Mô tả:* Thể hiện sự bùng nổ số lượng bài báo trên Google Scholar trong giai đoạn 2021-2024.
*   **Fig. 2: So sánh sơ đồ học trọn đời của LLMs tĩnh vs. LLM Agents tương tác**
    *   *Mô tả:* LLM tĩnh nhận dữ liệu và cập nhật nội bộ; LLM Agent nhận quan sát từ môi trường, thực hiện hành động và nhận phản hồi (feedback loop).
*   **Fig. 3 & 4: Minh họa sự thích nghi và tiến hóa hành vi của Agent trong thế giới thực**
    *   *Mô tả:* Khả năng di chuyển linh hoạt giữa các môi trường khác nhau (game, mua sắm, việc nhà) mà không cần đổi kiến trúc.
*   **Fig. 5: Lịch sử phát triển học trọn đời cho hệ thống AI qua 4 giai đoạn**
    *   *Mô tả:* Từ nền tảng lý thuyết (thập niên 1980s), deep learning (2010s), LLMs (2020s), đến LLM Agents (2023-nay).
*   **Fig. 6: Kiến trúc tổng thể của Lifelong Learning LLM Agent**
    *   *Mô tả:* Sơ đồ liên kết 3 mô-đun: Perception (Single/Multi-modal) $\rightarrow$ Memory (Working, Episodic, Semantic, Parametric) $\rightarrow$ Action (Grounding, Retrieval, Reasoning).
*   **Fig. 7: Sơ đồ tổ chức phân cấp các mô-đun trong bài báo khảo sát**

### Bảng số liệu (Tables)
*   **Table 1: So sánh phạm vi đóng góp của bài báo với các nghiên cứu khảo sát trước đây**
    *   *Nội dung:* Liệt kê 14 surveys khác để chứng minh đây là survey đầu tiên tập trung hoàn toàn vào học liên tục của LLM-based Agents.

---

## 5. Paper 5: VLQA: The First Comprehensive, Large, and High-Quality Vietnamese Dataset for Legal Question Answering

### Hình vẽ (Figures)
*   **Fig. 1: Sơ đồ quy trình 4 giai đoạn xây dựng bộ dữ liệu VLQA**
    *   *Mô tả:* (1) Thu thập kho luật; (2) Lọc câu hỏi và trích xuất tham chiếu; (3) Con người gán nhãn, kiểm duyệt; (4) Chia dữ liệu Train/Val/Test.
*   **Fig. 2: Giao diện trang Streamlit dành cho đội ngũ annotator chuyên nghiệp**
    *   *Mô tả:* Hiển thị câu hỏi, câu trả lời thô và danh sách các điều luật đề xuất để người kiểm duyệt đánh giá Đúng/Sai, sửa nội dung và ghi nhận phản hồi.
*   **Fig. 3: Biểu đồ phễu lọc số lượng mẫu qua từng bước xử lý**
    *   *Dữ liệu:* 430,000 câu hỏi thô $\rightarrow$ 27,310 câu hỏi sạch $\rightarrow$ 3,180 câu hỏi gán nhãn $\rightarrow$ 3,129 câu hỏi được kiểm duyệt cuối bởi chuyên gia pháp lý.
*   **Fig. 4: Các biểu đồ phân bố thống kê của VLQA**
    *   *Nội dung:* (a) Phân bố độ dài điều luật; (b) Phân bố độ dài câu hỏi; (c) Phân bố độ dài câu trả lời; (d) Số lượng điều luật liên quan trên mỗi câu hỏi (94% chỉ cần 1-2 điều luật).
*   **Fig. 5 & 6: Biểu đồ phân bố loại câu hỏi và chủ đề pháp lý trong VLQA**
    *   *Hình 5:* Loại câu hỏi "How" chiếm nhiều nhất (36%), tiếp theo là "What" (27.1%) và "Binary" (21.6%).
    *   *Hình 6:* Lĩnh vực Hành chính (Administrative System) chiếm tỷ trọng lớn nhất (14.67%), tiếp theo là Kinh doanh (Business) và Thương mại (Commerce).
*   **Fig. 7: Sơ đồ quy trình huấn luyện giám sát cho tác vụ truy xuất điều luật**
    *   *Mô tả:* Kết hợp pre-ranking bằng lexical retriever và phân loại nhị phân tinh chỉnh bằng Cross-Encoder.

### Bảng số liệu (Tables)
*   **Table 1: So sánh thống kê giữa VLQA và các bộ dữ liệu LQA trên thế giới**
    *   *Nội dung:* Chi tiết số từ, số câu hỏi, loại câu trả lời của COLIEE, JEC-QA, LLeQA, ALQAC, v.v. VLQA nổi bật là tập dữ liệu tiếng Việt lớn nhất gán nhãn chuyên gia cho câu trả lời tự luận.
*   **Table 2: Ví dụ về một mẫu dữ liệu trong VLQA**
    *   *Chi tiết:* Gồm các trường Question, Answer và Relevant Articles (mã luật và số điều).
*   **Table 3: Thống kê kết quả kiểm duyệt của chuyên gia đối với dữ liệu gán nhãn**
    *   *Dữ liệu:* 74.18% giữ nguyên hoàn toàn; 14.83% sửa luật, giữ câu trả lời; 3.8% giữ luật, sửa câu trả lời; 7.19% sửa cả hai (thường do luật cũ hết hiệu lực).
*   **Table 4: Ví dụ cụ thể về việc cập nhật luật hết hiệu lực trong quá trình kiểm duyệt**
    *   *Chi tiết:* Lương cơ bản thay đổi từ 1,490,000 VND (Nghị định 38/2019) thành 1,800,000 VND (Nghị định 24/2023).
*   **Table 5: Thống kê số lượng điều luật phân bổ theo 27 chủ đề pháp lý Việt Nam**
    *   *Dữ liệu:* Lĩnh vực Hành chính có nhiều điều luật nhất (8,566 điều), ít nhất là Sở hữu trí tuệ (737 điều). Tổng cộng cả kho luật là 59,636 điều.
*   **Table 6 & 7: Mô tả chi tiết và ví dụ minh họa của các loại câu hỏi và loại suy luận pháp lý**

---

## 6. Paper 6: ViLegalNLI: Natural Language Inference for Vietnamese Legal Texts

### Hình vẽ (Figures)
*   **Fig. 1: Minh họa tác vụ NLI pháp lý tiếng Việt**
    *   *Mô tả:* Sơ đồ khối chỉ ra đầu vào gồm Premise (luật) và Hypothesis (phát biểu của người dùng) đưa qua mô hình phân loại nhị phân ra Entailment/Non-entailment.
*   **Fig. 2: Quy trình 7 bước xây dựng bộ dữ liệu ViLegalNLI**
    *   *Mô tả:* Thu thập $\rightarrow$ Tiền xử lý $\rightarrow$ Trích xuất tiền đề $\rightarrow$ Tối ưu prompt $\rightarrow$ Sinh giả thuyết $\rightarrow$ Kiểm định chéo $\rightarrow$ Đánh giá độ khó $\rightarrow$ ViLegalNLI.
*   **Fig. 3: Sơ đồ chi tiết ví dụ trích xuất tiền đề từ Điều luật**
    *   *Mô tả:* Chỉ ra cách phân tách các Điểm, Khoản của Luật Mineral thành các câu tiền đề độc lập.
*   **Fig. 4: Đồ thị tăng trưởng độ đồng thuận Fleiss' Kappa qua các vòng tinh chỉnh prompt**
    *   *Dữ liệu:* Tăng từ 0.67 (vòng 1) lên 0.87 (vòng 6).
*   **Fig. 5: Biểu đồ phân phối độ dài câu của Premise và Hypothesis**
    *   *Nội dung:* Premise có phân phối trải rộng (đuôi dài tới 200 tokens), Hypothesis tập trung cao ở ngưỡng 25-60 tokens.
*   **Fig. 6: Biểu đồ cột phân bố tỷ lệ nhãn Entailment / Non-entailment theo 10 quy tắc sinh giả thuyết**
    *   *Nội dung:* Quy tắc 1 (Active-passive) và Quy tắc 7 (Logical consequence) sinh nhiều Entailment; Quy tắc 6 (Invalid reasoning) và Quy tắc 10 (Independent statement) sinh nhiều Non-entailment.
*   **Fig. 7: Phân bố Cosine Similarity giữa câu gốc và câu đã sửa đổi để loại bỏ nhiễu từ vựng**
    *   *Nội dung:* 55.5% số câu đạt độ tương đồng ngữ nghĩa trên 0.9, chứng minh nghĩa gốc được bảo toàn tốt sau khi xóa trigger words.

### Bảng số liệu (Tables)
*   **Table 1: So sánh ViLegalNLI với các bộ dữ liệu NLI trên thế giới và Việt Nam**
    *   *Nội dung:* ViLegalNLI là bộ dữ liệu NLI tiếng Việt lớn nhất (42,012 mẫu), vượt qua ViNLI (30k) và VnNewsNLI (32k), và là bộ duy nhất chuyên ngành luật.
*   **Table 2: Ví dụ cụ thể kèm nhãn gán của ViLegalNLI**
*   **Table 3: Các từ khóa ngữ pháp quy tắc dùng để nhận dạng cấu trúc điều luật**
*   **Table 4: Các thuộc tính dữ liệu lưu trữ kèm mỗi mẫu ViLegalNLI**
*   **Table 5: Thống kê Fleiss' Kappa qua 6 vòng tinh chỉnh prompt**
*   **Table 6: Mô tả chi tiết 10 quy tắc sinh giả thuyết cho hai nhãn**
*   **Table 7: Thống kê tỷ lệ đồng thuận của 3 mô hình kiểm định nhãn**
    *   *Dữ liệu:* 79.34% đồng thuận cả 3 mô hình; 92.45% đồng thuận $\ge 2$ mô hình.
*   **Table 8: Hiệu năng CafeBERT trước khi thực hiện loại bỏ nhiễu từ vựng**
*   **Table 9: Bảng trigger expressions và các cụm từ đồng nghĩa tương ứng dùng để thay thế**
    *   *Chi tiết:* Ví dụ: Thay trigger "không cần" bằng "không yêu cầu/không nhất thiết phải".
*   **Table 10: 27 lĩnh vực pháp lý được bao phủ**
*   **Table 11: Số lượng mẫu chi tiết phân bổ theo domain và split (Train/Dev/Test)**
*   **Table 12: Các chỉ số chồng lấn từ vựng (Jaccard, LCS) và POS của premise-hypothesis**
*   **Table 13: Top các từ có điểm PMI cao nhất với từng nhãn**
    *   *Chi tiết:* "không cần" có PMI 0.95 với Non-entailment; "khi" có PMI 0.99 với Entailment.
*   **Table 14: Kết quả thực nghiệm chi tiết của các mô hình trên Dev và Test set**

---

## 7. Paper 7: Retrievable Gradients: Continual Post-Training Without Cumulative Weight Drift

### Hình vẽ (Figures)
*   **Figure 1: Tổng quan khung kiến trúc REGRAD qua 3 giai đoạn**
    *   *Mô tả:* Sơ đồ khối mô tả: (1) Meta-learning tối ưu hóa weights khởi đầu LoRA và step size; (2) Pre-compute gradient của tài liệu ngoại tuyến để xây dựng Gradient Bank; (3) Truy xuất gradient trực tuyến, tạm thời cộng dồn và cập nhật vào LLM khi inference.
*   **Figure 2: Phân tích Ablation kiểm chứng vai trò của giai đoạn Meta-learning**
    *   *Dữ liệu:* So sánh Direct Generation, Raw Gradient (không meta-learning - làm giảm hiệu năng sâu dưới mức Direct), và Meta-Learned Gradient (REGRAD - cải thiện hiệu năng vượt bậc).
*   **Figure 3: Phân tích Ablation về tính liên quan của Gradient**
    *   *Dữ liệu:* So sánh việc áp dụng Random Gradients (chỉ tăng nhẹ hiệu năng do kích hoạt QA mode) vs. Relevant Gradients (tăng mạnh hiệu năng nhờ thông tin ngữ nghĩa thực tế).
*   **Figure 4: Tác động của số lượng gradient nạp vào (K từ 1 đến 10)**
    *   *Nội dung:* Cả REGRAD và RAG đều đạt đỉnh hiệu năng ở tầm K=3, sau đó đi xuống do nhiễu thông tin (attention dilution ở RAG và gradient interference ở REGRAD).

### Bảng số liệu (Tables)
*   **Table 1: Bảng kết quả thực nghiệm chính của REGRAD**
    *   *Nội dung:* So sánh điểm F1/Acc trên 9 benchmarks thuộc 3 lĩnh vực (General, Law, Medical) trên các mô hình LLaMA-1B, LLaMA-3B, và LLaMA-8B. REGRAD vượt trội các baseline parametric (CPT, PRAG) và đạt điểm cao nhất khi kết hợp với in-context RAG (REGRAD + ICL).
*   **Table 2: Kiểm chứng khả năng bảo toàn tri thức khi nạp tri thức quy mô lớn liên tục**
    *   *Nội dung:* Khi nạp từ 2.5K đến 10K tài liệu, hiệu năng của CPT giảm dần (từ 30.73% về 27.80%), trong khi REGRAD tăng liên tục (từ 37.71% lên 44.05%).
*   **Table 3: Khả năng chuyển giao liên miền (Cross-Domain Transfer)**
    *   *Nội dung:* So sánh các chiến lược huấn luyện cấu hình meta-initialization: General-init, Continued ReGrad (tiếp tục thích ứng trên tập đích), và Mixed-domain ReGrad (huấn luyện hỗn hợp).
