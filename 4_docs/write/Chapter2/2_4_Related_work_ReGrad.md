# Retrievable Gradients & Dynamic Weight Editing (ReGrad)  ↔ `2_chapters/2_Background/2_4_Related_work_ReGrad.tex`

> Vai trò: **đối chiếu** — đại diện họ bán-tham-số "gradient bank"; KHÔNG phải method của đề tài (đề tài RAG-centric). Dùng để so sánh và biện minh lựa chọn. | Nhóm nguồn: Group 5

## Ý lớn cần viết

### 1. Vấn đề Weight Drift tích luỹ trong CPT/CFT tuần tự
- Tóm tắt: cập nhật trọng số liên tục tích luỹ trôi dạt → quên thảm khốc; cần tách "cập nhật" khỏi "trọng số vĩnh viễn".
- Paper nguồn: `Su2026_ReGrad` (đặt vấn đề), nối `Yang2024_ParameterCollision`
- Cần viết: nêu động lực của ReGrad như một cách né weight drift.

### 2. Cơ chế ReGrad: Gradient Bank + truy xuất động lúc inference
- Tóm tắt: tiền tính gradient LoRA của từng tài liệu, lưu offline (Gradient Bank); khi inference truy xuất gradient liên quan, cộng tạm vào W đóng băng rồi rollback → đạt chất lượng cập nhật cấp tham số mà không trôi trọng số vĩnh viễn.
- Paper nguồn: `Su2026_ReGrad`
- Cần viết: mô tả pipeline 2 pha (offline meta-learning + test-time adaptation); minh hoạ bằng sơ đồ.

### 3. So sánh ReGrad vs RAG (điểm bản lề cho đề tài)
- Tóm tắt: cả hai đều "bán-tham-số" và tránh weight drift; ReGrad chỉnh trọng số tạm thời (parametric tạm), RAG tiêm tri thức qua context (phi tham số).
- Paper nguồn: `Su2026_ReGrad` + tổng hợp Group 1
- Cần viết: bảng so sánh trục Memory Persistence (vĩnh viễn / tạm thời / phi tham số); kết luận vì sao đề tài chọn RAG (đơn giản hơn, độ trễ thấp hơn, dễ kiểm soát/unlearn).

## Khoảng trống / câu hỏi mở
- Độ trễ suy luận cao khi áp gradient động tại runtime → khó triển khai thời gian thực (điểm yếu chính so với RAG).
