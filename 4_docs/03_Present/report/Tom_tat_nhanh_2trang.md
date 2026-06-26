---
updated:
  - 2026-06-26T08:33:16+07:00
---
# Tóm tắt nhanh — Báo cáo Chuyên đề (theo deck trình bày)

**Đề tài:** Học liên tục & Quên thảm khốc trong Mô hình Ngôn ngữ cho Pháp luật Việt Nam
*(Continual Learning and Catastrophic Forgetting in Language Models for Vietnamese Law)*
**Học viên:** Hoàng Đình Quý Vũ — 252805008 · **GVHD:** PGS.TS. Lê Anh Cường
**Đơn vị:** Khoa Công nghệ Thông tin, Trường Đại học Tôn Đức Thắng · 2026

> **Một câu:** Khi tri thức pháp lý được **nạp liên tục**, tinh chỉnh trọng số (parametric) **quên thảm khốc**; đề tài dùng **RAG phi-tham số** (base đóng băng + kho chọn lọc) để giữ tri thức mà không quên, đo trên **ba tác vụ QA · NLI · Tam đoạn luận**.

---

## 1. Giới thiệu & Bài toán
LLM đang được dùng cho hỏi-đáp luật, tra án lệ, soạn thảo. Nhưng tri thức luật **không tĩnh** (non-stationary): liên tục sửa đổi, thay thế, niêm phong. Một mô hình trọng số tĩnh sẽ **lỗi thời** và tư vấn theo luật hết hiệu lực — hậu quả pháp lý nghiêm trọng. Bài toán mang tính **kép và đối nghịch**: vừa phải **cập nhật liên tục**, vừa phải **quên có chủ đích** (luật hết hiệu lực, dữ liệu cá nhân). Dạy thẳng vào trọng số thì **quên thảm khốc** và **khó gỡ** tri thức ra.

## 2. Mục tiêu & Giả thuyết
**Mục tiêu chung:** thiết kế–hiện thực–đánh giá một framework **RAG-centric, base đóng băng**, cập nhật & quên **không retrain**, cho QA/suy luận luật VN. Bốn giả thuyết **bác bỏ được bằng số**:

| | Giả thuyết | Đo bằng |
|---|---|---|
| **H1** | CPT/CIT nạp liên tục → **quên thảm khốc** | Backward Transfer (BWT) âm, perf↓ |
| **H2** | Frozen-RAG **né quên** (vs parametric cùng cỡ) | BWT ≈ 0 vs baseline |
| **H3** | Kho phình → vanilla RAG tụt recall; **selective giữ recall** | Recall@K, index size |
| **H4** | **Trần lập luận**: retrieval mạnh QA > NLI > Syllogism | Δ accuracy theo task |

## 3. Lược khảo tài liệu (2024–2026)
- Vòng đời huấn luyện **CPT → CIT → CA** với rủi ro **quên xuyên giai đoạn**; đánh đổi ổn định–linh hoạt: học mới càng sâu, quên cũ càng nhiều (*Kalajdzievski 2024 — inverse linear law*).
- Xu hướng dịch tri thức **trong trọng số → ngoài (RAG)** (*Zheng 2024*).
- **LoRA "learns less, forgets less"** (*Biderman 2024*): hợp học **hành vi** (rank thấp), KHÔNG hợp nhồi **facts** (intrinsic rank ~2000).
- **ReGrad** (*Su 2026*) đạt luật 84.87% không drift nhưng **đắt + latency cao** → đề tài chọn RAG để cập nhật/unlearn rẻ.
- Dữ liệu VN: **VLQA** (~59k điều), **ViLegalNLI**, **LegalSLM** — NLI/QA ~0.95 nhưng **tam đoạn luận chỉ ~0.5** (khoảng trống lõi cho H4).

## 4. Nội dung & Phương pháp đề xuất *(trọng tâm)*
**Kiến trúc hybrid:** *facts → RAG (phi-tham số, cập nhật được)*, *hành vi → LoRA*, **base LLM ở giữa đóng băng**.
- **Đơn vị tri thức** giàu metadata: `u = ⟨e, v, [t], c, ρ, H, acl⟩` (embedding, hiệu lực, thời gian, citation-centrality, rủi ro/thứ bậc, lịch sử truy cập, phân quyền).
- **Selective Memory:** điểm importance `I = w₁ĉ + w₂v + w₃ρ + w₄g(H,t)` (decay Ebbinghaus) + **compliance hard-gate** (luật còn hiệu lực luôn giữ). "Quên" = giảm nhiễu xếp hạng, **không** mất coverage.
- **Unlearning (phụ):** P+Q — lọc tài liệu (P) + ràng buộc sinh (Q); là **temporal gating**, đo rò rỉ bằng TPR@1%FPR, không claim xoá khỏi trọng số.
- **Thí nghiệm chính:** nạp tri thức **liên tục** × **4 nhánh** (CPT · ReGrad · vanilla-RAG · selective-RAG) × **3 task** (QA/NLI/Syllogism) → vẽ đường quên, đo BWT.
- **Mô hình nhỏ 1–4B** (≤15B, low-resource); baseline parametric chạy **cùng cỡ** để so sánh công bằng.

## 5. Giới hạn phạm vi
Nội dung: QA/NLI/suy luận rule-based, **không** generation dài. Không gian: luật VN; kịch bản test lấy data từ **lao động & thuế** (data scenario, **không** thu hẹp ngành — framework domain-general). Thời gian: 2005→nay. **Đánh đổi nói thẳng:** reasoning bị chặn bởi base frozen (nêu rõ ở Limitations).

## 6. Kết quả mong đợi & Đối tượng thụ hưởng
- **R1 — Đường forgetting:** CPT sụp (BWT âm), RAG ổn định — trên 3 task.
- **R2 — Selective vs vanilla:** vanilla tụt Recall@K; selective giữ **±2%**, **index ↓ ≥30%**.
- **R3 — Δ theo task:** QA > NLI > Syllogism = bằng chứng **trần lập luận**; đối chiếu ReGrad.
- **Thụ hưởng:** cộng đồng NLP/AI pháp lý (benchmark + metric chuẩn) · kỹ sư hệ thống (tái dùng selective memory + cơ chế cập nhật) · cơ quan tuân thủ (không viện dẫn luật hết hiệu lực).

## 7. Kinh phí & Kế hoạch 6 tháng
Quy mô **đồ án sinh viên**: tổng **~20 triệu (~$800)**, lương = 0; dùng Colab Pro+/RunPod thay A100. Vì base đóng băng, chi phí chính là **index một lần**. Timeline: corpus (W3) → KB (W5) → baseline (W9) → đo forgetting → benchmark → báo cáo (W24). Rủi ro lớn nhất: **độ chính xác metadata hiệu lực** → giảm bằng lấy trực tiếp từ văn bản + spot-check 5%.

## 8. Kết luận
- **Kiến trúc hybrid** đóng băng base ⇒ chống quên thảm khốc; facts ở RAG ⇒ cập nhật/unlearn tức thì.
- **Ba kết quả đo được:** CPT quên / RAG né quên (H1–H2) · selective cứu recall (H3) · trần lập luận ở syllogism (H4).
- **Hợp xu thế 2026:** dịch tri thức ra bộ nhớ ngoài; **thừa nhận thẳng giới hạn** để bảo vệ vững.

---

### Đóng góp (nói gọn khi hỏi "mới gì?")
1. **Không** claim thuật toán RAG mới. Đóng góp = **bằng chứng định lượng** RAG né quên thảm khốc khi **nạp liên tục** trên luật VN (khuôn Bảng B của ReGrad nhưng **phi-tham số**).
2. **Selective legal memory** chống "quên kiểu RAG" (kho phình → recall tụt).
3. Phát hiện **trần lập luận** — retrieval cứu QA/NLI nhưng chạm trần ở tam đoạn luận.

