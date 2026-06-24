# Thu hẹp testbed — ĐÃ CHỐT & ĐÃ ÁP VÀO .tex

> ✅ **CHỐT (2026-06-24):** testbed = **2 ngành** — **Lao động (primary)** + **Thuế (secondary)**. Giữ **6 tháng**. Framework vẫn domain-general.
> Vai trò: Lao động = full corpus + cả 3 scenario A/B/C; Thuế = **slice cô đọng**, là task thứ 2 trong chuỗi tuần tự (Lao động → Thuế) để **đo catastrophic forgetting (Backward Transfer)** → đúng tinh thần Continual Learning, vẫn khả thi 6 tháng.
> 3.4 multi-sector: **GIỮ SỐNG** (vì có 2 task tuần tự).
>
> **Đã sửa các file:** `1_4` (Data Subject + Sectoral Scope item) · `1_2` (Objective 3) · `05_Abstract` · `3_1` (Sampling + Threats to Validity: thêm sector-coverage) · `3_4` (ví dụ sequential = Lao động→Thuế) · `4_3` (WP1 + Milestone 1) · `4_1` (Deliverable 3 + threshold forgetting).
>
> ---
> _Bản dự định gốc (giữ lại để tham chiếu) — lúc đầu định 1 ngành:_

---

## 1. Nguyên tắc (phát biểu để không mất novelty)
- Method (selective memory + unlearning + LoRA + update pipeline) **không phụ thuộc ngành luật** → vẫn general.
- Chỉ giới hạn **nơi chứng minh**: *"Framework tổng quát; minh chứng trên **một phân ngành đại diện**."*
- Câu chốt sẽ thêm vào Scope: generalization sang ngành khác = **future work**.

## 2. Đề xuất phân ngành (cần bạn xác nhận)
**Khuyến nghị #1: Pháp luật Lao động & Tiền lương (Labor & Employment).**
Lý do — kích hoạt đủ **cả 3 trụ method** + có dữ liệu:
| Tiêu chí | Lao động |
|----------|----------|
| Sửa đổi nhiều (→ update + temporal regime) | ✅ (Bộ luật Lao động 2019, nhiều nghị định/thông tư cập nhật) |
| Bãi bỏ/thay thế rõ (→ temporal unlearning) | ✅ |
| Dữ liệu cá nhân (→ right-to-be-forgotten) | ✅ (hồ sơ lao động, tiền lương) |
| Compliance thực tiễn | ✅ (HR, bảo hiểm, tranh chấp) |
| Có trong VLQA | ✅ ~5.2% |

**Phương án thay thế:**
- **Thuế (Tax):** sửa đổi *rất* nhiều + dữ liệu thu nhập cá nhân — mạnh, nhưng cần kiểm dữ liệu trong VLQA (thuế có thể nằm rải trong Commerce/Business).
- **Doanh nghiệp (Business & Corporate):** nhiều dữ liệu hơn (VLQA 7.3%), sửa đổi nhiều — nhưng yếu mảng "dữ liệu cá nhân".

> ❓ **Cần bạn chốt:** Lao động (khuyến nghị) / Thuế / Doanh nghiệp / ngành khác?

## 3. Dự định sửa từng file (.tex) — sẽ làm SAU khi bạn duyệt
| File | Sửa gì (dự định) |
|------|------------------|
| **1_4_Research_subjects_and_scope.tex** | "Data Subject": thu hẹp còn **phân ngành X** (nêu các văn bản chính của ngành); thêm 1 câu *"framework general, testbed = X; mở rộng ngành khác = future work"*. |
| **1_1_Reason / 1_2_Objectives** | Chỉnh 1–2 câu cho nhất quán ("trên miền X" thay vì "toàn luật VN") — *nhẹ*, giữ ý chính. |
| **3_1 (Data Collection, Sampling, Analysis)** | Corpus = X; **3 scenario A/B/C cụ thể hoá trên X**: A = ca sửa Bộ luật/nghị định X; B = forget set = điều khoản X đã bãi bỏ / hồ sơ cá nhân; C = stress index trên X. |
| **3_4 (LoRA, multi-sector)** | Ghi rõ: dùng **1 sector** ⇒ phần O-LoRA/N-LoRA/SLIM (học tuần tự đa-ngành) chuyển thành **future work** (vì chỉ 1 ngành). *(Hoặc nếu sau bạn muốn 2 ngành thì giữ.)* |
| **3.1 Threats to Validity** | Thêm 1 dòng **external validity**: kết quả trên 1 ngành → *generalization sang ngành khác cần kiểm chứng thêm*. |
| **4_3 (WP1 / timeline)** | Phạm vi crawl/index = X (giảm khối lượng dữ liệu → khả thi 6 tháng). |
| **4_1 / Abstract** | Đổi "Vietnamese legal" → "Vietnamese **[X] law**" ở chỗ mô tả phạm vi minh chứng (giữ "framework general"). |

## 4. Cái KHÔNG đổi (giữ nguyên)
- Toàn bộ cơ chế method (importance score, P+Q, decay, compliance gate, update pipeline) — general.
- Các section lý thuyết Ch2 — general.
- 3 mục tiêu / 3 câu hỏi nghiên cứu — chỉ đổi *miền minh chứng*, không đổi bản chất.

## 5. Lợi ích (để trả lời hội đồng)
- Vá rủi ro #1 (**metadata hiệu lực khả thi** ở quy mô 1 ngành).
- 3 trụ method có **ca thật** để minh chứng (sửa luật, bãi bỏ, dữ liệu cá nhân).
- Feasibility 6 tháng thuyết phục hơn.
- Vẫn claim general → không hạ novelty.

---
> **Next:** bạn xác nhận (a) chốt phân ngành nào, (b) 3_4 chuyển multi-sector thành future work hay giữ. Xong mình sửa .tex theo đúng bảng ở mục 3.
