---
updated:
  - 2026-06-23T16:51:58+07:00
  - 2026-06-20T15:38:18+07:00
---
Dựa trên 4 bài survey toàn diện nhất về Continual Learning (CL - Học liên tục) cho Large Language Models (LLMs) và Generative AI (AI Tạo sinh) mà chúng ta đã phân tích, tôi sẽ giúp bạn tổng hợp lại toàn bộ bức tranh nghiên cứu dưới dạng **5 câu hỏi cốt lõi để phát hiện vấn đề nghiên cứu**, kèm theo góc nhìn "người đi trước đã làm gì".

Đây là một dàn ý cực kỳ chặt chẽ để bạn đưa vào phần Introduction hoặc Related Work cho bài báo của mình:

---

### Bước 1: Phát hiện vấn đề nghiên cứu thông qua "Quan sát" và "Đặt câu hỏi"

#### 1. Hiện trạng và mức độ của vấn đề là gì?

- **Hiện trạng:** Các mô hình AI Tạo sinh (LLMs, MLLMs, Vision-Language-Action, Diffusion) đang chuyển từ việc "nhận thức thế giới" sang "tạo ra thế giới", đóng vai trò cốt lõi trong nhiều ứng dụng,,. Tuy nhiên, chúng thường được huấn luyện trên dữ liệu tĩnh. Khi đối mặt với môi trường thực tế liên tục thay đổi (tri thức mới, domain mới, ngôn ngữ mới), chúng buộc phải cập nhật,.
- **Mức độ nghiêm trọng:** Khi được tinh chỉnh (fine-tune) để học nhiệm vụ mới, các mô hình này gặp phải hiện tượng **"Quên thảm khốc" (Catastrophic Forgetting)** - suy giảm nghiêm trọng hoặc mất hẳn các năng lực cũ,,. Sự lãng quên này xảy ra ở hai chiều:
    - _Theo chiều dọc (Vertical Forgetting):_ Mất đi năng lực tổng quát (general capabilities/reasoning) khi đào tạo thích ứng vào một domain chuyên ngành hẹp (Luật, Y tế),,.
    - _Theo chiều ngang (Horizontal Forgetting):_ Quên đi nhiệm vụ hoặc tri thức cũ khi học chuỗi nhiệm vụ mới theo thời gian,,.

#### 2. Nguyên nhân do đâu?

- **Sự can thiệp trọng số (Weight Interference):** Quá trình tối ưu hóa bằng gradient trên dữ liệu mới vô tình ghi đè lên các biểu diễn không gian (representation space) chứa kiến thức của dữ liệu cũ do không có sự ràng buộc từ dữ liệu quá khứ,.
- **Dữ liệu Upstream không thể truy cập (Inaccessible Upstream Data):** Do vấn đề bản quyền, quyền riêng tư (GDPR) và dung lượng khổng lồ, các nhà phát triển (consumer) ở hạ nguồn không có quyền truy cập vào dữ liệu tiền huấn luyện (pre-training) gốc của các hãng lớn (như OpenAI, Google) để cùng huấn luyện lại (joint training),.
- **Quên chéo giai đoạn (Cross-stage/Unconscious Forgetting):** Quá trình đào tạo LLM bao gồm nhiều tầng (Pre-training $\rightarrow$ Instruction Tuning $\rightarrow$ Alignment). Việc tinh chỉnh lệnh hoặc căn chỉnh giá trị con người (RLHF) ở các giai đoạn sau có thể làm hỏng kiến thức nền hoặc phá vỡ các rào cản an toàn đã thiết lập ở giai đoạn trước,,.

#### 3. Các nhân tố nào đang tác động và mức độ tác động ra sao?

- **Sự chênh lệch quy mô (Scale Discrepancy):** Với các mô hình khổng lồ từ vài tỷ đến hàng trăm tỷ tham số, việc huấn luyện lại toàn bộ (full fine-tuning) tiêu tốn chi phí tính toán và phần cứng (GPU) khổng lồ, khiến các thuật toán CL truyền thống không thể scale,.
- **Thuế liên kết (Alignment Tax) và Xung đột Dẻo-Ổn định (Plasticity-Stability Dilemma):** Yêu cầu mô hình phải tuân thủ nghiêm ngặt tính an toàn (safety) làm cản trở khả năng tiếp thu tri thức mới linh hoạt, buộc phải liên tục đánh đổi giữa "Học mới" và "Giữ tính an toàn/logic",.
- **Sự phân mảnh Đa phương thức (Modality-specific Forgetting):** Trên các mô hình đa phương thức (MLLMs) hoặc Robot (VLA models), các module điều khiển hành động (action) hoặc phương thức mới thường bị quên với tốc độ nhanh hơn nhiều so với khả năng nhận thức ngôn ngữ-thị giác lõi,.

#### 4. Trong vấn đề này các đồng nghiệp khác đã làm gì và nhược điểm của chúng là gì?

Cộng đồng nghiên cứu đã nỗ lực giải quyết vấn đề bằng 4 nhóm phương pháp chính,,,,:

1. **Dựa trên Kiến trúc (Architecture-based):** Mở rộng mạng nơ-ron hoặc dùng tinh chỉnh tham số hiệu quả (PEFT/LoRA, Prompt Tuning, Mixture-of-Experts) để cấp phát các module riêng biệt cho từng tác vụ mới,,.
    - _Nhược điểm:_ Khi số lượng task tăng lên, mô hình phình to không giới hạn gây nghẽn bộ nhớ (Memory overhead). Việc định tuyến (router) để chọn đúng module (LoRA expert) khi inference trong môi trường không biết trước nhãn task (Domain-Incremental) là cực kỳ khó khăn,,.
2. **Dựa trên Phát lại (Replay-based):** Lưu trữ một bộ đệm nhỏ dữ liệu cũ (Experience Replay) hoặc dùng chính LLM sinh ra dữ liệu giả (Generative Replay) để ôn tập trong lúc học mới,,.
    - _Nhược điểm:_ Lưu dữ liệu thật vi phạm quyền riêng tư. Trong khi đó, dùng AI tự sinh dữ liệu ôn tập (Generative Replay) trong dài hạn sẽ dẫn đến "suy thoái mô hình" (Model Collapse) do phân phối dữ liệu bị méo mó, sinh ra ảo giác (hallucination),.
3. **Dựa trên Điều chuẩn (Regularization/Distillation-based):** Dùng các hàm phạt (penalty) hoặc chưng cất kiến thức (Knowledge Distillation) để giới hạn sự thay đổi của các tham số quan trọng,,.
    - _Nhược điểm:_ Thường kém hiệu quả khi đối mặt với sự thay đổi dữ liệu (domain shift) quá lớn trong các tác vụ AI Tạo sinh phức tạp,.
4. **Sử dụng Kiến thức Ngoại vi (External Knowledge):** Không can thiệp vào trọng số, mà dùng Retrieval-Augmented Generation (RAG) hoặc Tool-use (gọi API) để cấp nhật tri thức,,.
    - _Nhược điểm:_ Mô hình vẫn có thể bị quên cách dùng công cụ (Tool-use forgetting) hoặc bị giới hạn bởi context window của RAG,.

#### 5. Ý tưởng/giải pháp mới của bạn để khắc phục là gì và có khả thi không?

_(Gợi ý các hướng đi có tính "novelty" cao từ khoảng trống nghiên cứu của 4 papers)_

Từ các phân tích trên, bạn có thể định hình ý tưởng (Idea) cho bài báo của mình theo một trong các hướng khả thi sau:

- **Ý tưởng 1 (Tập trung vào PEFT/LoRA):** Đề xuất một kiến trúc "Mixture-of-Experts LoRA" có khả năng **gộp/nén (merge)** các experts thay vì phình to vô hạn,. _Tính khả thi:_ Rất cao, phù hợp với xu hướng đào tạo LLM giá rẻ hiện tại.
- **Ý tưởng 2 (Giải quyết Alignment Tax):** Đề xuất cơ chế _Cập nhật trực giao (Orthogonal Subspace Tuning)_,, tách biệt hoàn toàn không gian vector biểu diễn "kiến thức sự kiện" và không gian biểu diễn "giá trị đạo đức/an toàn". Khi học luật/y tế mới, chỉ cập nhật phần kiến thức mà không đụng vào phần an toàn. _Tính khả thi:_ Trung bình-Cao, giải quyết đúng pain-point của industry.
- **Ý tưởng 3 (Học liên tục cho MLLMs / Robot):** Đề xuất cơ chế _Cross-modal Generative Replay_, (sử dụng module text để sinh ra prompt, sau đó dùng Diffusion Model mỏng để sinh pseudo-image) nhằm ôn tập tri thức thị giác mà không cần lưu ảnh thật của người dùng. _Tính khả thi:_ Cao, đây là một mảnh đất (niche) còn rất mới (Vision-Language-Action Models) chưa có nhiều người khai phá,.
- **Ý tưởng 4 (Data Selection for Replay):** Đề xuất một thuật toán đo lường _Information Gain_ hoặc _Gradient Impact (như RHO-1)_, để lọc ra đúng 1% tokens quan trọng nhất đại diện cho task cũ để lưu vào replay buffer, giải quyết bài toán tốn bộ nhớ và tính toán. _Tính khả thi:_ Rất cao và dễ thực nghiệm chứng minh sự vượt trội về Compute Cost.

----
Để chỉ ra những thành tựu mà cộng đồng nghiên cứu đã đạt được trong việc giải quyết vấn đề "catastrophic forgetting" (quên thảm khốc), bạn nên hệ thống hóa các đóng góp của họ theo 3 khía cạnh cốt lõi: **Hệ thống phân loại phương pháp (Taxonomy)**, **Hệ thống đo lường (Metrics)**, và **Sự chuyển dịch tư duy (Paradigm Shifts)**.

Bạn có thể trình bày trong bài báo của mình theo cấu trúc sau:

### 1. Phân loại các chiến lược chống quên cốt lõi (Taxonomy of Techniques)

Các nhà nghiên cứu đã thành công trong việc xây dựng một kho tàng các kỹ thuật và quy tụ chúng thành 3-4 hướng tiếp cận chính để bảo vệ tri thức cũ:

- **Nhóm dựa trên Kiến trúc (Architecture-based):** Đây được xem là cách chống quên hiệu quả nhất hiện nay đối với LLMs. Thành tựu của họ là tận dụng các kỹ thuật tinh chỉnh tham số hiệu quả (PEFT như LoRA, Adapters) hoặc Mixture-of-Experts (MoE). Thay vì cập nhật toàn bộ mô hình, họ **đóng băng (freeze) xương sống của mô hình cũ và chỉ cấp phát/mở rộng các module nhỏ** cho các tác vụ mới, giúp cô lập tri thức và gần như đạt mức "zero-forgetting" (không quên).
- **Nhóm dựa trên Phát lại (Replay-based):** Lấy cảm hứng từ cơ chế khôi phục trí nhớ của hồi hải mã trong não người, họ đã phát triển các bộ đệm lưu trữ (memory buffers) để trộn dữ liệu cũ vào dữ liệu mới (Experience Replay). Đột phá hơn, để giải quyết vấn đề bảo mật dữ liệu, họ đã tạo ra **Generative Replay - dùng chính LLM (hoặc một mô hình phụ) để tự động sinh ra dữ liệu giả (pseudo-data)** đại diện cho task cũ nhằm mục đích tự ôn tập.
- **Nhóm dựa trên Điều chuẩn và Chưng cất (Regularization & Distillation):** Họ thiết kế các hàm phạt (penalty) như L2, EWC hoặc dùng Knowledge Distillation để **giới hạn sự xê dịch của các trọng số (parameters) hoặc đặc trưng (features)** được xem là quan trọng đối với các tác vụ cũ.
- **Nhóm sử dụng Kiến thức Ngoại vi (External Knowledge):** Một bước tiến thực tiễn là họ nhận ra có thể chống quên bằng cách hoàn toàn không can thiệp vào trọng số mô hình. Thay vào đó, họ trang bị cho LLM khả năng truy xuất RAG (Retrieval-Augmented Generation) hoặc sử dụng công cụ (Tool-use) để cập nhật kiến thức mới từ bên ngoài.

### 2. Thiết lập hệ thống đo lường minh bạch (Evaluation Metrics)

Để chứng minh các thuật toán trên thực sự giải quyết được việc quên, cộng đồng đã chuẩn hóa các công thức toán học để đo lường:

- **Đo lường mức độ quên cơ bản:** Họ sử dụng các chỉ số như **Forgetting Measure (FGT/FM)** (đo độ sụt giảm hiệu suất lớn nhất trên task cũ) và **Backward Transfer (BWT)** (đo lường sự ảnh hưởng của việc học task mới lên task cũ, BWT âm nghĩa là bị quên thảm khốc).
- **Đo lường "Quên chéo giai đoạn" cho LLM:** Với sự phức tạp của LLM, họ đã đề xuất thêm nhóm chỉ số **X-Delta** (như _General Ability Delta - GAD_, _Instruction Following Delta - IFD_, và _Safety Delta - SD_) để phát hiện xem quá trình fine-tune có vô tình làm mô hình quên đi khả năng suy luận nền tảng, quên cách tuân thủ mệnh lệnh, hoặc phá vỡ các rào cản an toàn hay không.

### 3. Sự chuyển dịch tư duy để phù hợp với LLM (Paradigm Shifts)

Bạn có thể nhấn mạnh rằng các nghiên cứu trước đây đã chỉ ra sự thay đổi lớn trong cách định nghĩa việc "quên" ở kỷ nguyên AI Tạo sinh:

- **Từ giới hạn bộ nhớ sang giới hạn tính toán:** Trong quá khứ, bài toán CL tập trung vào việc không có dung lượng để lưu data cũ. Ngày nay, các nhà nghiên cứu chỉ ra rằng với LLM, việc lưu trữ không còn là vấn đề (storage budget có thể là vô hạn đối với các công ty lớn), mà rào cản thực sự là **chi phí tính toán (computational efficiency)** để cập nhật lại mô hình.
- **Mở rộng từ mô hình phân biệt sang mô hình tạo sinh:** Vấn đề forgetting không chỉ dừng lại ở việc dự đoán sai nhãn (classification), mà các công trình gần đây đã tiến tới giải quyết việc "quên" trong các không gian sinh tạo phức tạp hơn như **Multimodal LLMs (quên phương thức hình ảnh/văn bản)**, **Vision-Language-Action (quên kỹ năng điều khiển robot)**, và **Diffusion Models (quên phong cách sinh ảnh)**.


- **Hướng 4: "Quên" có kiểm soát bằng Bộ nhớ Vector thao tác được (Machine Unlearning)**
    - _Research question:_ Kiến trúc nào cho phép LLM Đọc-Ghi-Xóa kiến thức lỗi thời theo thời gian thực mà không cần tính toán lại gradient?
    - _Ý tưởng:_ Episodic Memory Control lấy cảm hứng từ Hopfield Networks. Knowledge mới không đi vào FFN mà mã hóa thành slot trong Vector Memory. Khi cần Unlearn, drop slot đó đi thay vì re-train.
    - _Importance:_ Giải quyết nút thắt pháp lý GDPR (Quyền được lãng quên) tại EU. Giúp SaaS xóa lập tức thông tin user cũ khỏi hệ thống RAG nội bộ.