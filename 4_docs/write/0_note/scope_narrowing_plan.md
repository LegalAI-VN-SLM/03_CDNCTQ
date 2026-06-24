# Định vị phạm vi — KHÔNG thu hẹp scope; lao động/thuế = NGUỒN DATA cho kịch bản test

> ✅ **CHỐT lại (2026-06-24, bản cuối):** **KHÔNG gói gọn** framework vào ngành nào. Framework **domain-general**.
> Lao động & thuế **không phải scope/testbed** — chúng chỉ là **nơi lấy data để dựng kịch bản test** (update / forgetting / unlearning), chọn vì *biến động nhiều* (sửa, bãi bỏ, dữ liệu cá nhân).
> Cần ≥2 sub-domain để **đo catastrophic forgetting** (chuỗi lao động→thuế, Backward Transfer). Phủ thêm ngành = *mở rộng dữ liệu*, không đổi bản chất. Giữ 6 tháng.
> 3.4 sequential: **GIỮ SỐNG** (là kịch bản test 2-task).
>
> **Đã áp framing "data scenario" vào .tex:** `1_4` (Data Subject + mục *Evaluation Data Scenarios*) · `1_2` (Obj 3) · `05_Abstract` · `3_1` (Sampling + Threats: *scenario coverage*) · `3_4` (sequential test scenario) · `4_3` (WP1 + Milestone 1) · `4_1` (Deliverable 3).
> **Đã đồng bộ slide + note:** report `00,01,03,04,05,06` · `research_approach/02`.
>
> ⚠️ Lý do đổi: framing "testbed thu hẹp 2 ngành" lúc trước **bị coi là gói gọn quá** → reviewer dễ chê hẹp. Sửa thành "*nguồn data cho kịch bản test*" để giữ novelty general mà vẫn khả thi.
>
> ---
> _Bên dưới là các bản dự định cũ (1 ngành → 2 ngành testbed), giữ lại để tham chiếu tiến trình:_

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
