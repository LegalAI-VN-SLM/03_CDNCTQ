# Thuật ngữ & Viết tắt: Học liên tục và NLP Pháp lý (5 Nhóm Nghiên cứu)

Tài liệu này giải nghĩa ngắn gọn các từ khóa, thuật ngữ viết tắt, tập dữ liệu, benchmarks và thước đo đánh giá được sử dụng xuyên suốt trong các nghiên cứu từ Nhóm 1 đến Nhóm 5 về chủ đề **Học liên tục (Continual Learning)** và **NLP Pháp lý Việt Nam**.

---

## 1. Khái niệm & Phương pháp Học liên tục (Continual Learning Concepts)

*   **CL (Continual Learning) / Lifelong Learning:** *Học liên tục / Học trọn đời*. Khả năng của mô hình tiếp tục tiếp thu các tri thức và tác vụ mới theo thời gian mà không cần phải huấn luyện lại từ đầu trên toàn bộ dữ liệu.
*   **CF (Catastrophic Forgetting):** *Quên thảm khốc*. Hiện tượng mô hình mạng nơ-ron bị sụt giảm hiệu năng nghiêm trọng trên các tác vụ đã học trước đó sau khi được tinh chỉnh hoặc huấn luyện trên một tác vụ mới.
*   **Stability-Plasticity Dilemma:** *Thế lưỡng nan giữa tính ổn định và tính mềm dẻo*. Sự đánh đổi cốt lõi trong học liên tục: mô hình cần đủ ổn định để giữ vững tri thức cũ (**Stability**) nhưng cũng phải đủ mềm dẻo để học tri thức mới (**Plasticity**).
*   **Stability Gap:** *Khoảng trống ổn định*. Hiện tượng validation loss trên các tác vụ cũ bị tăng vọt đột ngột và tạm thời ngay khi mô hình bắt đầu học trên một phân phối dữ liệu mới.
*   **Parameter Collision:** *Xung đột tham số*. Hiện tượng các gradient cập nhật của tác vụ mới ghi đè trực tiếp lên các trọng số quan trọng đang lưu trữ tri thức của tác vụ cũ trên cùng một không gian tham số.
*   **Weight Drift:** *Trôi dạt trọng số*. Sự sai lệch tích lũy của các tham số mô hình sau nhiều vòng tinh chỉnh tuần tự, khiến mô hình trôi xa khỏi phân phối biểu diễn tối ưu ban đầu.
*   **Experience Replay / Rehearsal:** *Ôn tập dữ liệu*. Phương pháp chống quên bằng cách lưu trữ một phần dữ liệu cũ (thường từ 1% đến 5%) và trộn đều vào tập dữ liệu mới trong các chu kỳ huấn luyện tiếp theo.
*   **Regularization-based Methods:** *Phương pháp dựa trên chính quy hóa*. Kỹ thuật hạn chế sự thay đổi của các tham số quan trọng đối với tác vụ cũ bằng cách thêm các thành phần phạt (penalty) vào hàm loss (ví dụ: Elastic Weight Consolidation - EWC).
*   **Architecture-based / Parameter-Isolation Methods:** *Phương pháp dựa trên kiến trúc / Cô lập tham số*. Cơ chế chống quên bằng cách cấp phát các vùng trọng số, các adapter hoặc chuyên gia (experts) độc lập cho từng tác vụ riêng biệt.
*   **Semi-parametric Continual Learning:** *Học liên tục bán tham số*. Hướng tiếp cận kết hợp mô hình tham số (trọng số mạng) với các thành phần bộ nhớ ngoài phi tham số (vector database, gradient bank) để lưu trữ thông tin mới mà không làm thay đổi trọng số vĩnh viễn.
*   **Machine Unlearning:** *Học máy quên*. Kỹ thuật loại bỏ ảnh hưởng của một tập dữ liệu huấn luyện cụ thể hoặc một khái niệm đã học khỏi mô hình mà không cần huấn luyện lại từ đầu.

---


## 2. Kiến trúc & Kỹ thuật Tinh chỉnh (Architecture & Fine-Tuning)

*   **PEFT (Parameter-Efficient Fine-Tuning):** *Tinh chỉnh hiệu quả tham số*. Nhóm các phương pháp chỉ huấn luyện một lượng nhỏ tham số bổ sung (adapters, prompts) trong khi đóng băng hoàn toàn mô hình gốc nhằm tiết kiệm tài nguyên.
*   **LoRA (Low-Rank Adaptation):** *Thích ứng hạng thấp*. Kỹ thuật PEFT phổ biến nhất, hoạt động bằng cách đóng băng ma trận trọng số gốc $W$ và biểu diễn cập nhật $\Delta W$ thông qua tích của hai ma trận hạng thấp hạng $r$ ($A$ và $B$).
*   **O-LoRA (Orthogonal LoRA):** *LoRA trực giao*. Kỹ thuật chiếu các cập nhật trọng số LoRA mới vào không gian con trực giao với không gian của các tác vụ cũ nhằm loại bỏ hoàn toàn sự can thiệp chéo giữa các tác vụ.
*   **DoRA (Weight-Decomposed Low-Rank Adaptation):** *LoRA phân rã trọng số*. Phương pháp phân tách cập nhật trọng số thành hai thành phần độc lập: độ lớn (magnitude) và hướng (direction) để tối ưu hóa hiệu năng hội tụ của LoRA hạng thấp.
*   **MoE (Mixture of Experts):** *Hỗn hợp chuyên gia*. Kiến trúc mạng chia nhỏ mạng nơ-ron thành nhiều nhánh nhỏ (experts) và sử dụng một bộ định tuyến (router) để chuyển tiếp các token đến experts phù hợp nhất.
*   **MoLA / SLIM (Soft LoRA with Identity Mixture):** Các kiến trúc kết hợp nhiều adapter LoRA đóng vai trò như các experts dưới sự điều phối của cơ chế định tuyến động nhằm tránh xung đột tham số giữa các tác vụ.
*   **Progressive Prompts:** *Prompt lũy tiến*. Phương pháp học liên tục đóng băng toàn bộ LLM, chỉ huấn luyện và nối thêm (concatenate) các token soft prompt mới vào đầu vào cho từng tác vụ mới.
*   **REGRAD (Retrievable Gradients):** *Gradient có thể truy xuất*. Cơ chế lưu trữ gradient cập nhật của tài liệu mới vào một "Ngân hàng Gradient" (Gradient Bank) ngoại tuyến và chỉ tải động tạm thời vào trọng số mô hình khi có truy vấn suy luận.
*   **RAG (Retrieval-Augmented Generation):** *Tạo văn bản tăng cường truy xuất*. Kỹ thuật kết hợp LLM với một bộ truy xuất (retriever) để tìm thông tin từ nguồn tài liệu bên ngoài làm ngữ cảnh đầu vào, giúp trả lời chính xác mà không cần đổi trọng số.
*   **LUFY (Long-term User Chatbot with Forgetting):** Cơ chế thiết lập bộ nhớ dài hạn cho chatbot RAG mô phỏng theo đường cong quên tự nhiên của con người, tự động quên đi 90% thông tin hội thoại ít quan trọng để giảm tải ngữ cảnh.
*   **SAM (Sharpness-Aware Minimization):** *Tối thiểu hóa nhạy cảm độ sắc nét*. Thuật toán tối ưu hóa tìm kiếm các vùng cực tiểu phẳng (flat minima) trên loss landscape, giúp trọng số mô hình ít nhạy cảm với các biến động và tăng khả năng kháng quên.
*   **SVD (Singular Value Decomposition):** *Phân tích suy hao giá trị suy biến*. Công cụ toán học phân tích ma trận để đo lường hạng nội tại (intrinsic rank) của các ma trận cập nhật trọng số trong LLM.

---

## 3. Thước đo đánh giá (Evaluation Metrics)

*   **BWT (Backward Transfer):** *Khả năng truyền ngược*. Thước đo hiệu năng trên các tác vụ cũ sau khi mô hình học tác vụ mới. Giá trị BWT âm thể hiện mức độ quên (CF) của mô hình (BWT càng gần 0 càng tốt).
*   **FWT (Forward Transfer):** *Khả năng truyền xuôi*. Đo lường mức độ ảnh hưởng của việc học các tác vụ cũ lên khả năng tiếp thu và giải quyết tác vụ mới của mô hình (FWT dương thể hiện học chuyển giao tốt).
*   **OP (Overall Performance):** *Hiệu năng tổng thể*. Điểm trung bình của mô hình trên toàn bộ chuỗi tác vụ sau khi quá trình huấn luyện liên tục kết thúc.
*   **FG / FG% (Forgetting Gradient / Rate):** *Hệ số quên / Tốc độ quên*. Thước đo định lượng phần trăm suy giảm hiệu năng của mô hình trên các tác vụ cũ qua các bước tinh chỉnh.
*   **PPL (Perplexity):** *Độ hỗn loạn*. Chỉ số đánh giá chất lượng mô hình ngôn ngữ dựa trên mức độ bất định khi dự đoán token tiếp theo. PPL càng thấp nghĩa là mô hình dự đoán càng chính xác và tự tin.
*   **Recall@K:** *Tỷ lệ truy xuất trúng tại K*. Đo lường tỷ lệ các tài liệu thực sự liên quan được xếp hạng trong top K kết quả trả về bởi hệ thống truy xuất (như RAG).
*   **MRR (Mean Reciprocal Rank):** *Thứ hạng nghịch đảo trung bình*. Thước đo chất lượng của hệ thống truy xuất dựa trên vị trí của kết quả chính xác đầu tiên trong danh sách phản hồi.
*   **ROUGE / BLEU:** Các thước đo tự động đánh giá độ tương đồng giữa văn bản do mô hình sinh ra với văn bản tham chiếu (thường dùng cho các tác vụ dịch máy, tóm tắt hoặc sinh câu trả lời tự luận).
*   **HOP (Harmful Output Probability):** *Xác suất đầu ra độc hại*. Tỷ lệ phần trăm mô hình sinh ra câu trả lời độc hại hoặc vi phạm rào chắn an toàn trước các prompt nhạy cảm.
*   **USR (Unlearning Success Rate):** *Tỷ lệ học quên thành công*. Tỷ lệ phần trăm mô hình từ chối trả lời thành công hoặc phản hồi an toàn trước các câu hỏi chạm vào dữ liệu/khái niệm cần quên.

---


## 4. Tập dữ liệu & Bộ đánh giá (Datasets & Benchmarks)

*   **MMLU (Massive Multitask Language Understanding):** Bộ câu hỏi trắc nghiệm đa ngành chuẩn hóa dùng để đánh giá năng lực hiểu ngôn ngữ tự nhiên và tri thức nền tảng của LLM trên 57 lĩnh vực khác nhau.
*   **TRACE:** Benchmark đánh giá học liên tục toàn diện trên LLM bao gồm 8 tác vụ thuộc nhiều lĩnh vực (Khoa học, Toán, Lập luận, An toàn...).
*   **ConTinTin:** Benchmark đánh giá khả năng học liên tục từ các chỉ dẫn (instructions) đa dạng thay vì các tác vụ phân loại nhãn cố định.
*   **TiC-LM (Time-Continual Language Model):** Benchmark đánh giá khả năng cập nhật tri thức của mô hình ngôn ngữ theo dòng thời gian thực tế để đo lường tốc độ lỗi thời của tri thức.
*   **VLQA (Vietnamese Legal Question Answering):** Tập dữ liệu hỏi đáp pháp lý tiếng Việt đầu tiên được gán nhãn bởi các chuyên gia luật, bao gồm 59k điều luật và 3.1k câu hỏi đáp phức tạp.
*   **ViLegalNLI:** Tập dữ liệu suy luận ngôn ngữ tự nhiên (NLI) trong lĩnh vực luật Việt Nam gồm 42k cặp câu, được lọc bỏ các lối tắt từ vựng (lexical shortcuts) dựa trên phân tích PMI.
*   **LegalSLM:** Bộ đánh giá năng lực lập luận pháp lý tiếng Việt của các SLMs (Small Language Models), bao gồm 3 tác vụ chính: QA trắc nghiệm, NLI pháp lý và Tam đoạn luận (Syllogism - suy luận tự luận).
*   **AdvBench:** Benchmark chuẩn dùng để đánh giá độ an toàn (safety guardrails) của LLM trước các prompt tấn công độc hại (jailbreak).
*   **WikiText-103 / SlimPajama / RedPajama:** Các tập dữ liệu văn bản thô quy mô lớn phục vụ quá trình tiền huấn luyện liên tục (CPT) và đo lường độ hỗn loạn ngôn ngữ tổng quát (Perplexity).
