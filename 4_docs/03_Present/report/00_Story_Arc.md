# 00 — Story Arc & Deck Map (sống lưng để kể chuyện)

> Folder này đúc kết từ `.tex` Chương 1–4 thành **vật liệu sinh slide**. Mỗi file = một khối; mỗi `## Slide` = một slide gợi ý (tiêu đề + bullet tiếng Anh sẵn dùng + 💬 cách kể tiếng Việt + 📌 dẫn chứng).

## One-liner (câu chốt của cả đề tài)
> *Tri thức pháp lý biến động nhanh; LLM phải **cập nhật liên tục** và **quên có chủ đích** mà **không quên thảm khốc**. Giải pháp: **đóng băng base LLM**, đẩy **facts** vào **bộ nhớ RAG chọn lọc + unlearn được**, dùng **LoRA cho hành vi** — cho **luật tiếng Việt** (framework **domain-general, không gói gọn ngành nào**; lao động & thuế chỉ là **nguồn data để dựng kịch bản test**: update / forgetting / unlearning).*

## Mạch kể 8 nhịp (deck flow)
| # | Nhịp | File |
|---|------|------|
| 1 | **Hook** — Legal AI gặp luật biến động → tư vấn lỗi thời | 01 |
| 2 | **Dual challenge** — vừa update vừa "quên có chủ đích" | 01 |
| 3 | **Vì sao không parametric** — catastrophic forgetting (bằng chứng) | 02 |
| 4 | **Ý tưởng** — hybrid: RAG (facts) + LoRA (behavior), base frozen | 01/03 |
| 5 | **Bối cảnh & khoảng trống** — survey arc → 4 gaps | 02 |
| 6 | **Phương pháp (đi sâu)** — kiến trúc + 3 module + data pipeline | 03 |
| 7 | **Đánh giá & kết quả mong đợi** — baseline/metrics/threshold | 04 |
| 8 | **Đóng góp + kế hoạch + Q&A** | 04/05 |

## 3 câu hỏi proposal phải trả lời (lăng kính hội đồng)
| Câu hỏi | Trả lời ở slide |
|---|---|
| **Why needed?** | 01 Slide 1–3 + gaps (02 Slide 7) |
| **Why now?** | 01 Slide 2b |
| **Why me?** | 04 Slide 6 (closing) |
> Abstract / Intro / Closing phải truyền 3 ý này **ngay**; mỗi claim kèm 1 dẫn chứng (📌). Liêm chính AI: nêu ở Declaration + 04 Slide 6.

## Nguyên tắc kể (storytelling)
- **Vấn đề trước, giải pháp sau**: đừng mở bằng kiến trúc; mở bằng "luật đổi → model lỗi thời + không quên được".
- **Mỗi claim một bằng chứng số**: dùng các con số ở 📌 (FG% 40×, USR 99.8%, D-CPT r≈0.64, syllogism ~0.5…).
- **Tension → resolution**: nêu đánh đổi thật (frozen ⇒ reasoning bị chặn) rồi nói cách giảm thiểu → tạo độ tin.
- **Một hình "đắt" mỗi phần**: F kiến trúc (Ch3), F loss-landscape (Ch2), F data pipeline (Ch3), bảng so paradigm.

## Tài liệu nguồn để tham khảo khi dựng slide
- Chi tiết method/đối chiếu: `4_docs/write/Chapter3/*`, `Research_Core/*`, `Chapter3/Workflows/*`.
- Bằng chứng số: `4_docs/write/Chapter2/2_*` (bảng số liệu thật) + synthesis `02_reading_paper/Gemni_notebooklm/synthesis_cap_mh/*`.
