# 🧠 Báo Cáo Tổng Hợp: Huấn Luyện Tiền Đề Liên Tục & Luật Tỷ Lệ (Group 3)

Tài liệu này tổng hợp **những phát hiện tâm đắc nhất** từ 10 bài báo chuyên sâu thuộc **Group 3: Continual Pre-Training (CPT) & Scaling Laws**. Đây là các kết quả thực nghiệm quy mô lớn giúp giải quyết bài toán: Làm thế nào để cập nhật kiến thức mới vào LLM hiệu quả nhất ở pha Pre-training mà không cần chạy lại từ đầu.

---

## 📌 Bản Đồ Insights & Các Phát Hiện Cốt Lõi (CPT)

```mermaid
mindmap
  root((Continual Pre-Training))
    Learning Rate Schedules
      Re-warming: Bat buoc phai re-warm LR de khoi dong optimizer
      Stability Gap: WU=0 gay loss spike tam thoi do dong luc hoc optimizer
      Infinite LR: Giai phap duy tri LR cao, khong can biet token budget truoc
    Replay Strategies
      Compute-equivalent Replay: So sanh cong bang giu nguyen tong compute
      Weak Shift: Chi can 5% replay de match Union Baseline (1/2 compute)
      Strong Shift (Language): Can 15-25% replay de giữ vung tri thuc cu
    Domain-Specific Scaling
      D-CPT Law: Them mixture ratio 'r' vao cong thuc Chinchilla
      DLC (Domain-specific Learnable Coefficient): Characterize do kho cua domain
      Predict optimal rd: Tinh ra ty le tron toi uu truoc khi train thuc te
```

---

## 1. 📈 Kỹ Thuật Điều Chỉnh Learning Rate & Hiện Tượng Stability Gap
*Nguồn nghiên cứu: "Continual Pre-Training of LLMs: How to (Re)warm Your Model?" (Pythia 410M)*

*   **Bắt buộc phải Re-warm:** Khi bắt đầu tiếp tục huấn luyện trên dữ liệu mới ($D_1$) từ một checkpoint đã hội tụ ($M_0$), learning rate đã bị decay về mức tối thiểu. Chúng ta bắt buộc phải tăng lại learning rate (**Re-warming**) lên mức cực đại (`MaxLR`) rồi thực hiện decay lại để kích hoạt lại khả năng học tập của optimizer.
*   **Hiện tượng Stability Gap:** Nếu bỏ qua bước warm-up (thiết lập $WU = 0\%$), mô hình sẽ gặp hiện tượng **loss spike (bùng nổ loss)** cực kỳ hỗn loạn trong khoảng 5-10 tỷ token đầu tiên trước khi tự hồi phục. Đây là một khiếm khuyết động lực học của optimizer (AdamW) chứ không phải do sự lệch pha dữ liệu (data shift), vì hiện tượng này vẫn xảy ra khi re-warm trên cùng một tập dữ liệu cũ.
*   **MaxLR là nút điều chỉnh cốt lõi:** `MaxLR` là hyperparameter quan trọng nhất kiểm soát sự đánh đổi giữa **Plasticity** (khả năng học nhanh dữ liệu mới) và **Stability** (bảo toàn tri thức cũ).
*   **Infinite LR Schedules:** Đề xuất giải pháp sử dụng LR không giới hạn: **warm-up $\rightarrow$ constant LR $\rightarrow$ annealing cuối** (khi chuẩn bị deploy). Phương pháp này giúp loại bỏ hoàn toàn hiện tượng loss spike khi đổi dataset và không yêu cầu phải biết trước tổng lượng token budget của hệ thống.

---

## 2. 🔁 Phát Lại Tương Đương Tính Toán (Compute-equivalent Replay)
*Nguồn nghiên cứu: "Simple and Scalable Strategies to Continually Pre-train LLMs" (10B Model)*

*   **Khái niệm mới:** Thử nghiệm sử dụng **Compute-equivalent Replay** (giữ nguyên tổng chi phí tính toán FLOPs bằng cách giảm bớt lượng token mới tương đương với lượng token cũ được phát lại).
*   **Hiệu quả vượt trội của 5% Replay:** 
    *   Đối với **Weak Shift (lệch pha nhẹ - ví dụ cùng ngôn ngữ English):** Chỉ cần **5% replay** dữ liệu cũ là đủ để mô hình đạt kết quả tương đương ($AVG\ Loss = 1.89$ so với $1.87$ của Union Baseline) trong khi chỉ tốn **nửa chi phí tính toán (1/2 compute)** so với việc huấn luyện lại từ đầu trên hợp của hai tập dữ liệu ($D_0 \cup D_1$).
*   **Rào cản từ Strong Shift (Lệch pha ngôn ngữ):**
    *   Khi chuyển đổi ngôn ngữ (ví dụ từ English sang German), hiện tượng quên lãng xảy ra cực kỳ khốc liệt. Mô hình cần tối thiểu **15% đến 25% replay** dữ liệu cũ để duy trì sự ổn định.

---

## 3. ⚖️ Luật Tỷ Lệ Tinh Chỉnh Domain (D-CPT Law)
*Nguồn nghiên cứu: "D-CPT Law: Domain-specific Continual Pre-Training Scaling Law..." (Qwen-1.5)*

*   **Công thức D-CPT Law (L3):** Mở rộng định luật Chinchilla bằng cách đưa tham số tỷ lệ trộn dữ liệu chuyên ngành (`r`) vào phương trình:
    $$L(N, D, r) = E + \frac{A}{N^\alpha} + \frac{B \cdot r^\eta}{D^\beta} + \frac{C}{(r+\epsilon)^\gamma}$$
    Công thức này đạt độ chính xác khớp thực nghiệm cực cao ($R^2 > 0.97$).
*   **Domain-specific Learnable Coefficient (DLC):** Giới thiệu hệ số DLC ($K$) hoạt động như "dấu vân tay" đặc trưng cho độ khó học của từng domain. Chỉ cần tốn **1% chi phí huấn luyện thử nghiệm nhỏ**, ta có thể tính ra $K$ và dùng nó để dự đoán chính xác loss cho mô hình ở quy mô lớn hơn (như 7B) hoặc các domain chưa từng thấy.
*   **Ứng dụng thực tế:** 
    *   **Kiểm soát đánh đổi:** Tự động tính ra tỷ lệ trộn tối ưu để đạt loss domain thấp nhất dưới điều kiện giữ mức suy hao dữ liệu chung dưới một ngưỡng $T\%$ cho trước.
    *   **Giới hạn dữ liệu:** Tìm ra tỷ lệ trộn tốt nhất khi lượng dữ liệu chuyên ngành bị giới hạn (ví dụ chỉ có 5B tokens).

---

## 💡 Đề Xuất Áp Dụng Cho VN-Legal-AI

1.  **Chiến lược trộn dữ liệu ngôn ngữ (Strong Shift):** Do tiếng Việt là một ngôn ngữ khác biệt hoàn toàn với tri thức English chiếm đa số trong các base model, đây là kịch bản **Strong Shift**. Chúng ta cần thiết lập tỷ lệ trộn dữ liệu general tiếng Việt tối thiểu từ **15% đến 25%** trong suốt pha Continual Pre-training để tránh mô hình bị quên ngôn ngữ và mất khả năng suy luận logic chung.
2.  **Thiết lập LR schedule:** Áp dụng cơ chế **Linear Warm-up (0.5% - 1.0% tổng tokens)** khi bắt đầu học tập dữ liệu Luật Việt Nam để tránh hiện tượng **Stability Gap** (loss spike). Tối ưu hóa `MaxLR` ở mức vừa phải để cân bằng việc học thuật ngữ luật mới và giữ tri thức nền.
3.  **Tối ưu hóa tỷ lệ trộn bằng D-CPT Law:** Trước khi chạy CPT chính thức trên quy mô lớn, hãy chạy các thí nghiệm nhỏ (0.5B parameters) trên các tỷ lệ trộn khác nhau (ví dụ: 10% luật + 90% tổng hợp, 30% luật + 70% tổng hợp...) trong 5K-10K steps để fit các tham số của công thức D-CPT Law. Từ đó tính ra chính xác tỷ lệ trộn tối ưu cho mô hình đích (7B hoặc 8B) nhằm tiết kiệm hàng trăm triệu chi phí GPU thử nghiệm mò mẫm.
4.  **Kiểm tra Tokenizer:** Vì tiếng Việt có cấu trúc âm tiết ghép, việc dùng nguyên bản tokenizer của các English base model sẽ cực kỳ tốn token và làm giảm tốc độ học. Cần mở rộng tokenizer tiếng Việt trước khi chạy continual pre-training.
