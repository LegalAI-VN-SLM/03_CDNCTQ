---
updated:
  - 2026-06-20T15:43:04+07:00
  - 2026-06-20T15:42:19+07:00
  - 2026-06-20T15:40:58+07:00
---
- **Hướng 14: Đảo ngược sự suy giảm An toàn (Safety-Decay Reversal via Continuous Alignment)**
    - _Research question:_ Làm sao phát hiện sớm và sửa chữa ngay lập tức khi LLM bắt đầu có dấu hiệu bị "đầu độc" (poisoned) hoặc mất đi Safety Alignment do học liên tục?
    - _Ý tưởng:_ Continuous Safety Probing. Chạy ngầm một tác vụ Red-teaming tự động sau mỗi K bước update. Nếu chỉ số Safety Delta giảm dưới ngưỡng, kích hoạt một mini-batch DPO (Direct Preference Optimization) trên buffer dữ liệu an toàn cốt lõi.
    - _Importance:_ Tuân thủ tiêu chuẩn AI Act. Bảo vệ thương hiệu cho doanh nghiệp bằng cách duy trì rào cản đạo đức của Chatbot 24/7 bất chấp data cập nhật có chứa bias.

- **Hướng 15: Phân bổ Tài nguyên Động cho CL (Computationally Budgeted CL)**
    - _Research question:_ Dưới ràng buộc ngân sách phần cứng (FLOPs/VRAM) cố định, làm sao cân bằng giữa việc tiếp thu data mới và phát lại data cũ?
    - _Ý tưởng:_ RL-based Resource Router. Huấn luyện một policy nhỏ quyết định xem với một batch data mới: nên update trọng số (tốn FLOPs), hay đẩy vào RAG database (tốn Storage), hay bỏ qua (nếu information gain thấp).
    - _Importance:_ Tối ưu hóa Green AI và chi phí hạ tầng (Infra cost). Giúp các công ty startup vận hành model AI cập nhật liên tục mà không lo hóa đơn Cloud AWS bùng nổ.
- **Hướng 4: "Quên" có kiểm soát bằng Bộ nhớ Vector thao tác được (Machine Unlearning)**
    - _Research question:_ Kiến trúc nào cho phép LLM Đọc-Ghi-Xóa kiến thức lỗi thời theo thời gian thực mà không cần tính toán lại gradient?
    - _Ý tưởng:_ Episodic Memory Control lấy cảm hứng từ Hopfield Networks. Knowledge mới không đi vào FFN mà mã hóa thành slot trong Vector Memory. Khi cần Unlearn, drop slot đó đi thay vì re-train.
    - _Importance:_ Giải quyết nút thắt pháp lý GDPR (Quyền được lãng quên) tại EU. Giúp SaaS xóa lập tức thông tin user cũ khỏi hệ thống RAG nội bộ.