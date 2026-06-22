---
updated:
  - 2026-06-20T15:45:11+07:00
---
# Tóm tắt ngắn

- **Vấn đề:** Các mô hình AI Tạo sinh (LLMs, MLLMs, VLA, Diffusion) gặp phải hiện tượng "quên thảm khốc" (catastrophic forgetting) khi được cập nhật liên tục để học kiến thức, miền dữ liệu hoặc nhiệm vụ mới. Việc đào tạo lại từ đầu (retraining from scratch) là phi thực tế do chi phí tính toán khổng lồ.
- **Phương pháp:** Các bài khảo sát hệ thống hóa quy trình học liên tục (CL) thành các giai đoạn (Pre-training, Instruction Tuning, Alignment) và phân loại các kỹ thuật chống quên thành 4 nhóm chính: Dựa trên kiến trúc (Architecture-based như LoRA, MoE), Điều chuẩn (Regularization-based), Phát lại (Replay-based), và Kiến thức ngoại vi (External Knowledge như RAG, Tool-use).
- **Kết quả chính:** Hiện tại, kiến trúc mở rộng (đặc biệt là PEFT/LoRA) và Phát lại sinh tạo (Generative Replay) là những hướng đi hiệu quả nhất để cân bằng giữa việc học mới và giữ kiến thức cũ. Tuy nhiên, bài toán CL cho AI tạo sinh phức tạp hơn nhiều so với AI phân loại do phải đối mặt với sự "quên chéo giai đoạn" (cross-stage forgetting), "thuế liên kết" (alignment tax), và sự xói mòn kiến thức đa phương thức

---
