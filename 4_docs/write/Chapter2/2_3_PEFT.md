# Parameter-Efficient Fine-Tuning in Continual Learning  ↔ `2_chapters/2_Background/2_3_PEFT.tex`

> Vai trò: tầng "giải pháp parametric" trong phễu — PEFT/LoRA và giới hạn của chúng, làm bệ phóng lập luận chuyển sang bán-tham-số/RAG. | Nhóm nguồn: Group 4 (+ Group 5: O-LoRA)

## Ý lớn cần viết

### 1. LoRA: nền tảng tinh chỉnh hiệu quả tham số
- Tóm tắt: đóng băng W gốc, học 2 ma trận hạng thấp; giảm ~10.000× tham số huấn luyện.
- Paper nguồn: `Hu2021_LoRA`
- Cần viết: định nghĩa LoRA + vì sao là lựa chọn mặc định cho thích ứng miền.

### 2. "LoRA học ít hơn & quên ít hơn" — và giới hạn dung lượng
- Tóm tắt: SVD của ΔW Full-FT có hạng nội tại ~2000 → LoRA r≤256 KHÔNG đủ hấp thụ tri thức CPT; nhưng LoRA r cao rất tốt cho IFT và bảo tồn generation diversity.
- Paper nguồn: `Biderman2024_LoRALearnsLess`
- Cần viết: luận điểm bản lề — LoRA không thay được Full-FT ở pha CPT luật, nhưng tốt ở pha IFT.

### 3. Parameter Collision & Orthogonal Subspace (O-LoRA)
- Tóm tắt: học tuần tự trên cùng LoRA gây ghi đè; O-LoRA chiếu cập nhật mới lên không gian con trực giao → triệt tiêu can thiệp chéo.
- Paper nguồn: `Wang2023_OrthogonalSubspace`, `Yang2024_ParameterCollision`, `Zhang2026_OLoRA_Forgetting`
- Cần viết: cơ chế cô lập tham số + cảnh báo O-LoRA nhạy siêu tham số (sụp đổ trên tác vụ logic/toán khi α lớn).

### 4. Mở rộng kiến trúc: Multi-Expert & Prompt-based
- Tóm tắt: SLIM/Soft LoRA định tuyến token tới LoRA experts; Progressive Prompts đóng băng LLM, nối soft prompt theo task; tầng cao cần nhiều expert hơn (MoLA); I-LoRA tích hợp liên tầng.
- Paper nguồn: `Han2025_SLIM`, `Razdaibiedina2023_ProgressivePrompts`, `Gao2024_HigherLayersLoRA`, `Ren2024_PETForgetting`
- Cần viết: điểm các hướng cô lập cứng/mềm; phù hợp khi học nhiều phân ngành luật tuần tự.

### 5. Chuyển ý: giới hạn của parametric → cần bán-tham-số
- Tóm tắt: đa-adapter tốn GPU đỉnh, nhạy siêu tham số, vẫn đổi trọng số; → dẫn sang ReGrad (2.4) và RAG (2.5).
- Paper nguồn: tổng hợp Group 4 + `Su2026_ReGrad`
- Cần viết: đoạn pivot kết section — đây là lý do đề tài KHÔNG chọn parametric làm trục chính.

## Khoảng trống / câu hỏi mở
- Chi phí bộ nhớ GPU của hệ đa-adapter/MoE làm mất lợi ích PEFT.
- Ràng buộc trực giao cực nhạy alpha/rank → bất ổn trên tác vụ suy luận.
