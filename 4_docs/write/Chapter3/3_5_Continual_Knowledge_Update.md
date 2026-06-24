# 3.5 Continual Knowledge Update and Index Maintenance  ↔ `3_Methods_Models/3_5_Continual_Knowledge_Update.tex` (MỚI)

> **Trụ method 4** — cơ chế hiện thực mục tiêu "cập nhật liên tục" ở tầng KB (base frozen ⇒ update = thao tác KB). Trả lời lo ngại "không có chiến lược update thì retrieval suy thoái". | Nguồn: `li2025ticlmwebscalebenchmarktimecontinual`, nối `11207222` (unlearning).

---

## 0. Vì sao cần (failure mode nếu không update)
1. **Stale validity** (nguy hiểm nhất): luật cũ vẫn `v=1` → trích dẫn luật hết hiệu lực → sai compliance.
2. **Recall tụt**: điều luật mới chưa index → retriever không lấy được.
3. **Embedding drift**: thuật ngữ mới → encoder cũ embed kém → similarity lệch.
4. **Citation graph lỗi thời** → `c_i` sai → importance (3.2) sai.
5. **Query drift**: phân bố câu hỏi đổi theo thời sự pháp lý.

## 1. Chiến lược: Incremental Update Pipeline (có giám sát)
| Thành phần | Cơ chế |
|-----------|--------|
| Trigger | định kỳ (crawl công báo/portal) + event-driven (có văn bản mới) |
| Incremental index | segment → tag → embed → **upsert** đơn vị mới/đổi; không re-embed toàn bộ |
| **Supersession** | văn bản mới thay cũ ⇒ old `v=0`, `t_end`, link `superseded_by` = **temporal gating** (= unlearning 3.3) |
| Citation delta | thêm cạnh → recompute `c_i` **cục bộ** |
| Importance/decay | access-log driven (chạy nền, W4) |
| **Quality gate** | expert validate mẫu metadata + **canary query set** sau mỗi update |
| **Versioning/rollback** | KB snapshot theo version; fail canary → rollback; audit log |

## 2. Điểm hay: update và unlearning là **một bộ máy**
- "Cập nhật luật" (set `v=0` cho luật cũ) **= temporal unlearning**.
- LoRA train lại **hiếm** (đổi *behavior*); *facts* update liên tục qua pipeline, **không đụng weight**.

## 3. Figure (TikZ — đã có trong .tex)
Luồng: New doc → segment/tag/embed (upsert) → supersession (v=0) → citation delta → **canary pass?** → commit $v_{N+1}$ / rollback+alert. `\label{fig:update_pipeline}`.

## 4. Phạm vi & Future Work (ghi rõ)
- Đề cương đặc tả ở mức: trigger · incremental index · supersession · quality gate.
- **CHƯA đi sâu (hướng C):** *embedding-drift detection* + *crawl-scheduling / encoder-versioning policy* → **để làm ngay khi bắt đầu triển khai** (immediate implementation future work).

## 5. Đánh giá (gắn Ch4)
- **Time-continual** kiểu TiC-LM: đo recall, **staleness rate**, regime-shift accuracy **theo version KB** → chứng minh hệ không suy thoái.

## ❓ Q&A
**Q: Không update thì retrieval hỏng — bạn xử lý thế nào?**
→ Có **Incremental Update Pipeline** (upsert + supersession + canary + rollback), chạy định kỳ/event, base frozen nên rẻ; update luật cũ = temporal unlearning (cùng cơ chế).
**Q: Embedding cũ có lỗi thời không?**
→ Có rủi ro drift; **phát hiện drift + lịch re-embed/versioning là future work** làm khi triển khai (đã ghi rõ phạm vi).
