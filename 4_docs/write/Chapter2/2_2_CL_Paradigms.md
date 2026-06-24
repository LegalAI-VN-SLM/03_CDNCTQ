# Continual Learning Paradigms: CPT, CIT, and CA  ↔ `2_chapters/2_Background/2_2_CL_Paradigms.tex`

> Vai trò: tầng giữa phễu — mô tả vòng đời 3 giai đoạn + động lực học của sự quên + benchmark. Đây là phần "nền cơ chế" trước khi vào giải pháp. | Nhóm nguồn: Group 1, 2, 3

## Ý lớn cần viết

### 1. Khung "Căn chỉnh ba giai đoạn" (Three-Stage Alignment)
- Tóm tắt: CPT (facts/domain/language, self-supervised) → CIT (tasks/skills, SFT) → CA (values/preferences, RLHF/DPO).
- Paper nguồn: `Wu2024_CL_Survey`, `Shi2024_CL_Survey`
- Cần viết: định nghĩa từng pha + kỹ thuật đặc trưng; ánh xạ trực tiếp vào pipeline VN-Legal (CPT bộ luật → CIT tác vụ luật → CA an toàn tư pháp).

### 2. Cross-Stage Forgetting (rủi ro cốt lõi)
- Tóm tắt: tối ưu pha sau (CIT/CA) phá tri thức/an toàn pha trước (CPT); cần đánh giá chéo giai đoạn.
- Paper nguồn: `Wu2024_CL_Survey`
- Cần viết: cảnh báo rủi ro + nhu cầu cross-stage evaluation (test lại điều luật cơ bản sau CIT/CA).

### 3. Động lực học của catastrophic forgetting (vì sao quên)
- Tóm tắt: Inverse Linear Law (học mới tốt = quên cũ sâu); hình học loss landscape (OOD → sharp minima → SAM tìm flat minima); decoder-only kháng quên hơn encoder-decoder.
- Paper nguồn: `Kalajdzievski2024_ScalingForgetting`, `Li2024_RevisitingCF`, `Luo2025_EmpCF`
- Cần viết: giải thích nguyên nhân vật lý của quên → biện minh vì sao không thể chỉ "dừng sớm/giảm rank".

### 4. Vai trò của Replay/Rehearsal (ổn định rẻ)
- Tóm tắt: 0.25–1% replay đủ giữ zero-shot (CFT); CPT cần 5% (weak shift) → 15–25% (strong shift/đa ngôn ngữ).
- Paper nguồn: `Scialom2022_FineTuned`, `Ibrahim2024_StrategiesCPT`, `Zheng2024_CrossLingualCPT`
- Cần viết: nêu replay là baseline rẻ; lưu ý tiếng Việt là Strong Shift → cần tỷ lệ replay cao.

### 5. Cơ chế CPT: Stability Gap & Learning-rate schedule
- Tóm tắt: re-warming/re-decay bắt buộc; WU=0 gây loss spike; Stability Gap là do optimizer (AdamW momentum), không phải data shift; Infinite LR schedule.
- Paper nguồn: `Gupta2023_CPT_Warmup`, `Yildiz2025_InvestigatingCPT`, `Du2024_UnlockingCL`, `Guo2024_MitigateStabilityGap`
- Cần viết: cơ chế tối ưu hoá khi CPT → lưu ý kỹ thuật cho pha CPT bộ luật VN.

### 6. Scaling laws đặc thù miền: D-CPT Law
- Tóm tắt: đưa tỷ lệ trộn `r` vào Chinchilla (R²>0.97); DLC dự đoán domain mới với 1% chi phí; benchmark thời gian TiC-LM; scaling law cho injection.
- Paper nguồn: `Que2024_DCPTLaw`, `Li2025_TiCLM`, `Bethune2025_ScalingLawsInjection`, `Peng2024_ScalableLM`
- Cần viết: nêu D-CPT Law làm công cụ tính tỷ lệ trộn tối ưu cho luật VN (kết nối sang 2.7).

### 7. Benchmarks đánh giá quên (TRACE, ConTinTin)
- Tóm tắt: TRACE (8 task, đo ΔR general/instruction/safety); ConTinTin (CL từ instruction); logit-based eval nhạy hơn ground-truth; safety bền — reasoning dễ quên.
- Paper nguồn: `Wang2023_TRACE`, `Yin2022_ConTinTin`
- Cần viết: giới thiệu giao thức đo + chỉ số (OP, BWT, FWT, ΔR) dùng lại ở Ch3/Ch4.

## Khoảng trống / câu hỏi mở
- Benchmark tĩnh dễ rò rỉ vào pretraining → cần decontamination động.
- Trade-off Safety vs Capabilities chưa có khung thống nhất.
- Tokenizer tiếng Việt: mở rộng từ vựng làm gián đoạn biểu diễn token cũ.
