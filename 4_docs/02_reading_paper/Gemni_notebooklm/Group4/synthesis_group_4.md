# 🧠 Báo Cáo Tổng Hợp: Tinh Chỉnh Tham Số Hiệu Quả (PEFT/LoRA) & Học Liên Tục (Group 4)

Tài liệu này tổng hợp **những phát hiện tâm đắc nhất** từ 8 bài báo chuyên sâu thuộc **Group 4: PEFT / LoRA & Prompt-Based Continual Learning**. Nhóm nghiên cứu này tập trung vào các giải pháp kỹ thuật sử dụng Adapter (như LoRA) hoặc Prompts để cô lập, bảo vệ tham số và giải quyết hiện tượng quên lãng thảm họa khi học nhiều tác vụ tuần tự.

---

## 📌 Bản Đồ Insights & Các Phát Hiện Cốt Lõi (PEFT/LoRA)

```mermaid
mindmap
  root((PEFT & Continual Learning))
    LoRA Properties
      Learns Less & Forgets Less: LoRA co xap xi hang thap, khong phu hop CPT
      IFT Efficiency: LoRA hang cao (r>=256) tuong duong Full FT trong IFT
      Preserves Generation Diversity: LoRA tranh sup do phan phoi tu vung
    Parameter Collision
      Sequential update tren cung LoRA gay ghi de tham so
      Orthogonal Projection: O-LoRA chieu gradients len khong gian truc giao
    Architecture Expansion
      Task-specific Experts: Phan chia token sang cac LoRA experts rieng biet
      Progressive Prompts: Tang dan cac soft prompt tokens theo nhiem vu moi
```

---

## 1. ⚖️ Thuộc Tính Của LoRA: "Học Ít Hơn & Quên Ít Hơn" (Learns Less & Forgets Less)
*Nguồn nghiên cứu: "LoRA Learns Less and Forgets Less" (LLaMA-2-7B)*

*   **Sự hụt hơi của LoRA trong CPT:** 
    *   Trong kịch bản **Tiếp tục tiền huấn luyện (Continued Pretraining - CPT)** với quy mô hàng tỷ token, LoRA bị giới hạn hiệu năng nghiêm trọng và **không thể bắt kịp tinh chỉnh toàn phần (Full Fine-Tuning)** bất kể cấu hình rank $r$ lớn thế nào.
    *   *Nguyên nhân vật lý (SVD Analysis):* Phân tích SVD trên ma trận thay đổi trọng số $\Delta W$ của Full FT chỉ ra rằng cập nhật toàn phần có hạng nội tại cực kỳ cao (cần rank $r \approx 2000$ để giải thích 90% biến động). Do đó ma trận hạng thấp của LoRA ($r \le 256$) không đủ dung lượng để hấp thụ lượng kiến thức khổng lồ của miền dữ liệu mới.
*   **Sự xuất sắc của LoRA trong IFT:**
    *   Trong kịch bản **Tinh chỉnh chỉ thị (Instruction Fine-Tuning - IFT)** với quy mô dữ liệu nhỏ hơn, LoRA hạng cao ($r \ge 256$) và hệ số $\alpha = 2r$ đạt hiệu năng hoàn toàn tương đương với Full FT.
*   **Kháng quên và Bảo tồn độ đa dạng:**
    *   LoRA là một bộ điều chuẩn chống quên xuất sắc, bảo tồn tri thức cũ tốt hơn nhiều so với Full FT hay các bộ regularization kinh điển.
    *   Full FT thường gây ra hiện tượng **sụp đổ phân phối đầu ra (diversity collapse)** - mô hình bị overfit nặng vào các mẫu câu trả lời cố định. LoRA bảo tồn hoàn hảo độ đa dạng sinh từ (generation diversity) của base model.

---

## 2. 💥 Xung Đột Tham Số & Giải Pháp Không Gian Trực Giao (Orthogonal Subspace)
*Nguồn nghiên cứu: "Orthogonal Subspace Continual Learning" (O-LoRA / Parameter Collision)*

*   **Hiện tượng Parameter Collision (Xung đột tham số):** Khi học tuần tự nhiều tác vụ trên cùng một mạng LoRA dùng chung, các bước gradient sau sẽ trực tiếp **ghi đè và phá hủy** các trọng số LoRA đã học ở tác vụ trước, gây ra hiện tượng quên lãng thảm họa ngay lập tức.
*   **Giải pháp O-LoRA (Orthogonal LoRA):**
    *   Ý tưởng chính: Chiếu (project) các cập nhật trọng số của tác vụ mới lên một **không gian trực giao (orthogonal subspace)** với các tác vụ cũ.
    *   Mỗi khi học xong một tác vụ, mô hình sẽ đóng băng (freeze) các hướng gradient đã sử dụng. Tác vụ tiếp theo chỉ được phép tối ưu hóa trên không gian bù trực giao còn lại. Cơ chế này giúp loại bỏ hoàn toàn hiện tượng ghi đè tham số và triệt tiêu 100% sự quên lãng do xung đột trọng số.

---

## 3. 🧩 Mở Rộng Kiến Trúc: Prompts Lũy Tiến & LoRA Đa Chuyên Gia (MoE)
*Nguồn nghiên cứu: "Progressive Prompts" & "SLIM (Soft LoRA)"*

*   **Progressive Prompts (Prompts Lũy tiến):** Thay vì tinh chỉnh trọng số mô hình, phương pháp này đóng băng hoàn toàn LLM và chỉ huấn luyện các **soft prompt tokens** gắn thêm vào input đầu vào. 
    *   Mỗi tác vụ mới sẽ được cấp một tập hợp các soft prompt tokens riêng biệt và được nối tiếp (concatenate) tuần tự vào sau các prompt của tác vụ cũ. Điều này đảm bảo tri thức của các tác vụ cũ được cô lập hoàn toàn vật lý và truyền dẫn tri thức tiến (forward transfer) cực tốt.
*   **SLIM / Soft LoRA (LoRA Đa chuyên gia):**
    *   Thay vì cập nhật một bộ LoRA duy nhất, hệ thống duy trì một cụm các **LoRA Experts (chuyên gia)** riêng biệt cho từng tác vụ.
    *   Sử dụng một cơ chế định tuyến mềm (routing) để phân bổ các token đầu vào đến các LoRA Expert tương ứng dựa trên đặc trưng ẩn của chúng. Kỹ thuật này giúp phân tán tri thức vào các chuyên gia chuyên biệt, loại bỏ xung đột tham số trong khi vẫn giữ nguyên số lượng tham số huấn luyện ở mỗi bước.

---

## 💡 Đề Xuất Áp Dụng Cho VN-Legal-AI

1.  **Nói KHÔNG với LoRA trong pha CPT Luật:** Khi chạy Continual Pre-training trên hàng tỷ token văn bản luật thô của Việt Nam, **bắt buộc phải sử dụng Full Fine-Tuning** (hoặc unfreeze tầng Embeddings và các khối MLP lớn). Việc dùng LoRA ở pha này sẽ làm suy giảm nghiêm trọng khả năng hấp thụ các cấu trúc ngữ pháp và thuật ngữ pháp lý Việt Nam đặc thù.
2.  **Sử dụng LoRA Hạng Cao ($r \ge 256$) trong pha IFT:** Khi chuyển sang pha tinh chỉnh chỉ dẫn (IFT) để dạy mô hình giải các tác vụ luật cụ thể (Q&A luật, tóm tắt bản án), hãy sử dụng **LoRA toàn diện (all linear modules)** với cấu hình rank lớn ($r=256, \alpha=512$, learning rate lớn gấp 10 lần Full FT). Việc này giúp mô hình thích ứng nhanh với các mẫu câu hỏi luật mà không làm sụp đổ độ đa dạng sinh từ (diversity collapse) và không làm mất đi khả năng lập luận ngôn ngữ chung.
3.  **Áp dụng cơ chế Trực giao hoặc Experts khi học nhiều ngành luật tuần tự:** Nếu bạn có kế hoạch huấn luyện mô hình chuyên sâu cho nhiều phân ngành luật khác nhau theo trình tự thời gian (ví dụ: Luật Hình sự $\rightarrow$ Luật Dân sự $\rightarrow$ Luật Doanh nghiệp):
    *   Không nên dùng chung một adapter LoRA duy nhất.
    *   Nên sử dụng cơ chế **O-LoRA (chiếu gradient trực giao)** hoặc thiết lập **MoE LoRA (Multi-task Experts)** để mỗi ngành luật được lưu trữ ở một phân vùng tham số riêng biệt, tránh xung đột ghi đè dữ liệu.
