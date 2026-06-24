# 05 — Talking Points & Anticipated Q&A (cầm tay khi bảo vệ)

> Dùng khi hội đồng hỏi xoáy. Mỗi câu: **trả lời gọn + dẫn chứng + (nếu cần) thừa nhận giới hạn**. Nguyên tắc: nói thẳng giới hạn > vòng vo.

## 3 talking points cốt lõi (lặp lại xuyên bài)
1. **Facts → RAG, behavior → LoRA, base frozen** ⇒ không quên chung + cập nhật/unlearn dễ.
2. **Validity là hard gate; importance chỉ re-rank** ⇒ forgetting giảm nhiễu, không mất luật còn hiệu lực.
3. **Unlearning ở mức behavioral** (đo rò rỉ), không over-claim xoá weight.

---

## Q&A — Novelty & lý do
**Q: Đề tài mới gì khi RAG/unlearning/selective memory đã có?**
→ Novelty = **kết hợp + thích ứng miền + đánh giá**: legal importance (citation+hiệu lực+rủi ro) + **compliance gate**; hợp nhất memory+unlearning + **regime thời gian**; **benchmark legal-CL VN**. Không claim thuật toán RAG mới.

**Q: Sao không CPT cho model "thuộc luật"?**
→ CPT quên + drift + khó unlearn (`kalajdzievski2024...`); facts ở RAG cập nhật tức thì, behavior ở LoRA. Không phải chắp vá — semi-parametric có chủ đích.

**Q: Bỏ ReGrad dù đã đọc kỹ?**
→ ReGrad tránh drift nhưng **độ trễ inference cao** + khó unlearn; giữ làm **đối chiếu** (Law 84.87% nhưng latency). Đề tài ưu tiên độ trễ thấp + unlearn dễ.

## Q&A — Method (đi sâu)
**Q (xoáy nhất): Tri thức trong weight base, gỡ KB thì model vẫn trả lời được — unlearning có thật không?**
→ Đây là **retrieval-level unlearning**: đảm bảo không truy xuất/grounding/trích dẫn (đủ compliance). KHÔNG xoá khỏi weight; rò rỉ parametric giảm bằng ràng buộc Q + filter và **đo định lượng** (TPR@1%FPR). Mạnh ở mức weight cần model-level unlearning — **ngoài phạm vi**. 📌 `11207222`: USR 99.8%, utility giữ.

**Q: Decay làm mất điều luật quan trọng nhưng ít tra?**
→ Không. **Validity = hard gate**; luật còn hiệu lực luôn giữ; decay chỉ down-rank archive *vẫn truy xuất*, không xoá.

**Q: Importance score w₁..w₄ có ad hoc?**
→ Không — tune trên validation (Recall@K/MRR) hoặc LLM-estimated; có **ablation** từng feature.

**Q: LoRA cũng sửa weight, sao gọi frozen?**
→ **Base frozen**; LoRA là adapter nhỏ *thêm vào*, tách rời được, học *behavior* không facts. SVD: CPT cần rank~2000, IFT ≤32 (`biderman2024...`) → để facts ở RAG là đúng vai.

## Q&A — Đánh giá & khả thi
**Q: Frozen base thì reasoning đủ cho luật?**
→ Giới hạn đã biết (LegalSLM syllogism ~0.5). LoRA + selective context giảm lỗi; chọn base mạnh; nêu rõ Limitations. Không over-claim.

**Q: Đo "compliance" khách quan thế nào?**
→ Trên test có nhãn hiệu lực: % trả lời dùng luật còn hiệu lực, tránh luật bãi bỏ, xử lý đúng theo mốc thời gian.

**Q: Baseline có công bằng?**
→ Cùng base+LoRA+KB, chỉ khác module selective/unlearning → cô lập đóng góp.

**Q: 6 tháng làm nổi không?**
→ Timeline tuần + 8 milestone + bảng rủi ro; phạm vi giới hạn QA/NLI/reasoning; **kịch bản test tập trung vào sub-domain biến động nhiều (lao động, thuế)** → khối lượng dữ liệu thực nghiệm vừa phải, khả thi 6 tháng (framework vẫn **domain-general**).

**Q: Phạm vi luật có hẹp/rộng quá không? Sao lại lao động + thuế?**
→ Framework **domain-general, không bó vào ngành nào**. Lao động & thuế **không phải scope** — chúng chỉ là **nơi lấy data để dựng kịch bản test**, chọn vì *biến động nhiều* (sửa/bãi bỏ + dữ liệu cá nhân) nên cho ca **update / unlearning / forgetting** tự nhiên. Cần ≥2 sub-domain để **đo catastrophic forgetting** thật (chuỗi lao động→thuế, Backward Transfer) — 1 cái thì không demo được "học mới quên cũ". Phương pháp áp dụng được cho ngành khác; phủ thêm ngành chỉ là *mở rộng dữ liệu*, không đổi bản chất.

**Q: Không cập nhật data thì retrieval hỏng dần — chiến lược là gì?** (Section 3.5)
→ **Incremental Update Pipeline**: crawl/event → upsert đơn vị mới → **supersession** (luật cũ `v=0`, = temporal unlearning) → citation-graph delta → **canary test** → commit/rollback, có versioning + audit. Base frozen ⇒ update rẻ, không retrain. **Phần embedding-drift detection + crawl/versioning là future work làm khi bắt đầu triển khai** (chưa đi sâu).

## Nếu kết quả NULL (trưởng thành nghiên cứu)
- H1 null → importance chưa đủ tín hiệu (hướng feature mới).
- H2 cho thấy RAG kém legal-acc → khẳng định đánh đổi "learns less" → hybrid future work.
- H3 robustness yếu → định lượng giới hạn retrieval-level unlearning (đóng góp cảnh báo).
