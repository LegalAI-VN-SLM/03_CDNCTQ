# 06 — Reviewer Q&A: Câu hỏi xoáy + Câu trả lời sẵn (để ôn & trả lời tại chỗ)

> Nguồn: mô phỏng hội đồng `ars-reviewer` (full) trên **Ch2 + Ch3**. Quyết định mô phỏng: **MAJOR REVISION** — định hướng tốt, cần vá 4 lỗ hổng trước bảo vệ.
> Cách dùng: đọc **câu hỏi → trả lời 3–5 câu** (đã viết sẵn để nói). 🔧 = việc cần vá trong proposal để câu trả lời có cơ sở. ✅ = đã vá.

---

## NHÓM 1 — Novelty & lý do tồn tại

### Q1. "Ngoài việc ghép các phương pháp có sẵn cho luật VN, đóng góp khoa học cốt lõi là gì?"
**Trả lời:** Đề tài không tuyên bố một thuật toán RAG mới. Đóng góp khoa học nằm ở **ba điểm**: (1) định nghĩa hai *construct* mới làm first-class — **legal importance** (đo bằng citation centrality + hiệu lực + rủi ro) và **legal temporal regime** (validity interval); (2) **hợp nhất** selective memory và controllable unlearning trong một khung non-parametric, trong đó *cập nhật luật* và *quên có chủ đích* dùng chung một cơ chế; (3) một **benchmark/giao thức đánh giá legal continual learning cho tiếng Việt** chưa từng có. Đây đúng vai một nghiên cứu *định hướng*: tái khung bài toán + bằng chứng, không phải chỉ engineering.
🔧 Một câu novelty sắc ở đầu 3.1 + Abstract (nên thêm).

### Q2. "Vì sao xứng tầm nghiên cứu chứ không phải triển khai kỹ thuật?"
**Trả lời:** Vì nó trả lời ba câu hỏi *chưa có lời giải thực nghiệm* cho miền luật VN: importance signal nào hiệu quả (CQ1), RAG có thực sự giảm forgetting so parametric trong điều kiện regime thời gian (CQ2), và retrieval-level unlearning đạt compliance tới đâu mà không hại năng lực khác (CQ3). Mỗi câu là một giả thuyết **bác bỏ được**, có biến kiểm soát và metric — đó là tiêu chí của nghiên cứu, không phải triển khai.

---

## NHÓM 2 — Mâu thuẫn lõi (Devil's Advocate)

### Q3. (CRITICAL) "Em nói frozen base không quên, nhưng vẫn train LoRA — vậy 'zero weight drift' có chính xác không?"
**Trả lời:** Cảm ơn thầy, đây là chỗ em cần nói chính xác hơn. **Trọng số *base* được đóng băng hoàn toàn** — đó là nơi chứa năng lực ngôn ngữ/suy luận chung, nên năng lực chung không bị quên. **LoRA là một adapter nhỏ tách rời**, chỉ học *hành vi* (cách lập luận, trích dẫn), **không** chứa facts pháp lý — facts nằm ở RAG. Vì vậy chính xác là **"không trôi dạt tri thức *facts*"**, và em đã sửa bảng so sánh ở 3.1 thành *"no factual drift (base frozen; behaviour via LoRA)"*. Còn forgetting *hành vi* khi học tuần tự nhiều ngành luật thì em xử lý bằng O-LoRA/N-LoRA (đã nêu ở 3.4).
✅ Đã sửa câu chữ bảng 3.1.

### Q4. "Số liệu của chính em cho thấy ReGrad đạt 84.87% trên luật, cao hơn RAG — vì sao vẫn loại ReGrad?"
**Trả lời:** Đúng, ReGrad mạnh về accuracy. Em loại nó khỏi *trục chính* vì hai lý do gắn với yêu cầu pháp lý: **(1) unlearning** — ReGrad chỉnh trọng số tạm thời nên "quên có chủ đích" rất khó, trong khi RAG chỉ cần gỡ entry; **(2) độ trễ inference** do áp gradient động lúc chạy, mâu thuẫn với ràng buộc latency + auditability của hệ pháp lý. Tuy nhiên em **thừa nhận chưa đo latency thực** — nên em sẽ **giữ ReGrad làm một baseline định lượng** (không chỉ đối chiếu định tính) và **đo latency + accuracy thật** để kết luận có cơ sở.
🔧 Nâng ReGrad thành baseline đo thật (cập nhật 3.1/Ch4).

### Q5. "Selective RAG không nâng được năng lực suy luận của base — vậy giải quyết tam đoạn luận (chỗ yếu nhất) bằng cách nào?"
**Trả lời:** Em không over-claim chỗ này — đây là **giới hạn đã biết (GH1)**: LegalSLM cho thấy syllogism chỉ ~0.5–0.6. Selective RAG cải thiện *chất lượng context* (đúng điều luật, đúng hiệu lực) nên **giảm lỗi do retrieve sai**, và LoRA dạy *định dạng lập luận* (đại–tiểu tiền đề–kết luận); nhưng chiều sâu suy luận vẫn bị chặn bởi base. Em chọn base instruct đủ mạnh và **ghi rõ ở Threats to Validity + Limitations**; nâng reasoning sâu (vd reasoning-augmented decoding) là **future work**.

---

## NHÓM 3 — Methodology (R1)

### Q6. "Importance score là tổng tuyến tính có trọng số — học/validate thế nào để không circular? Có ground truth không?"
**Trả lời:** Không có ground-truth tuyệt đối cho "importance pháp lý" — em coi nó là **proxy**. Để tránh circular: trọng số được **tune trên development set tách riêng**, từng feature được **ablation** để đo đóng góp, và **đánh giá retrieval báo cáo trên truy vấn unseen** (không phải tập đã tune). Em cũng so hai phương án rule-based vs LLM-estimated. Em đã ghi điều này thành mục **Threats to Validity (construct validity)**.
✅ Đã thêm Threats to Validity.

### Q7. "VLQA vừa là KB truy xuất vừa là tập test — chống contamination/rò rỉ thế nào?"
**Trả lời:** Em tách **train/dev/test có decontaminate**, và **báo cáo recall trên truy vấn held-out** không xuất hiện lúc tune. KB và tập câu hỏi được phân tách để câu trả lời không "thấy trước" nhãn. Đây cũng nằm trong Threats to Validity (internal validity).
✅ Đã ghi.

### Q8. "Baseline CPT/CFT trên luật VN tốn compute — 6 tháng + 200h A100 có thực tế không?"
**Trả lời:** Em ưu tiên theo nấc: chạy **static-RAG baseline + selective + unlearning** trước (rẻ, vì base frozen), và CPT/CFT baseline chạy ở **quy mô nhỏ/đại diện** (mô hình nhỏ, tập con) chỉ để đo *xu hướng forgetting*, không cần full-scale. Nếu compute thiếu, CPT full là phương án mở rộng. Kế hoạch + rủi ro compute đã ghi ở Ch4.
🔧 Nói rõ trong Ch4 rằng baseline parametric chạy *scaled-down*.

---

## NHÓM 4 — Domain luật VN (R2)

### Q9. (điểm yếu chí mạng) "Làm sao xác định & cập nhật hiệu lực cho toàn bộ kho luật VN đáng tin? Sai metadata thì cả hệ sụp."
**Trả lời:** Em đồng ý đây là **rủi ro số một**. Em **không** cần gắn metadata hoàn hảo cho *toàn bộ* kho luật ngay: phần thực nghiệm chỉ cần dữ liệu ở **các kịch bản test** (lấy từ sub-domain biến động nhiều như lao động/thuế), nên khối lượng metadata cần kiểm chứng là **khả thi trong 6 tháng** — framework vẫn domain-general, không bị bó vào đó. Hướng xử lý: (1) **lấy hiệu lực từ chính văn bản** — điều khoản quy định hết hiệu lực, văn bản thay thế — chứ không đoán; (2) **expert spot-check 5%** + canary regression set sau mỗi cập nhật; (3) hệ có **versioning + rollback** nên sai thì khôi phục được. Em **không giả định metadata hoàn hảo** — đã đưa thành *assumption GĐ1* và một mục *Threats to Validity* riêng. Phủ metadata cho quy mô toàn kho là vấn đề *kỹ thuật mở rộng*, không phải rào cản phương pháp.
✅ Đã ghi Threats to Validity + GĐ1 + Evaluation Data Scenarios (1.4).

### Q10. "Vì sao mô hình quên nhận thức (Ebbinghaus) lại hợp với văn bản luật? Luật hiếm tra cứu nhưng tối quan trọng thì sao?"
**Trả lời:** Em phân tách hai thứ: **validity là hard gate** — điều luật *đang hiệu lực* **không bao giờ** bị loại dù importance thấp; decay chỉ tác động *xếp hạng* trong tập hợp lệ và đẩy xuống **archive (vẫn truy xuất được, không xoá)**. Ebbinghaus chỉ là cảm hứng cho thành phần *tần suất*, đóng vai phụ trong tổng importance, không phải tiêu chí duy nhất. Em sẽ **ablation** để chứng minh decay không làm tụt recall các điều luật hiếm-mà-quan-trọng; nếu có, em giảm trọng số decay.

---

## NHÓM 5 — Privacy/Systems (R3)

### Q11. "Q chỉ là ràng buộc prompt — jailbreak là lộ. Chứng minh robustness trên luật VN thế nào (không mượn số English)?"
**Trả lời:** Đúng, unlearning của em ở mức **behavioral**, không xoá khỏi trọng số — em nói rõ phạm vi này. Phòng thủ là **kép**: P (gỡ/ẩn entry khỏi truy xuất) + Q (ràng buộc đầu ra) + **filter đầu ra**. Em **không dùng số English làm kết luận**; các số như USR/TPR chỉ là *động lực*, và em cam kết **đo robustness (TPR@1%FPR dưới paraphrase/jailbreak) trên chính VLQA/ViLegalNLI**. Phần rò rỉ parametric còn lại được **báo cáo như cận trên**, không che giấu.
✅ External validity đã ghi ở Threats to Validity.

### Q12. "Right-to-be-forgotten mà audit log vẫn lưu yêu cầu/nội dung — có mâu thuẫn riêng tư không?"
**Trả lời:** Audit log chỉ lưu **metadata thao tác** (timestamp, loại request, entry-id, trạng thái) để giải trình, **không lưu nội dung nhạy cảm** đã xoá; với hard-delete (personal data) thì entry bị gỡ khỏi KB + index. Em sẽ ghi rõ chính sách log tối thiểu hoá dữ liệu trong 3.3.
🔧 Thêm 1 câu "log minimization" vào 3.3.

---

## Tổng hợp việc cần vá (để câu trả lời có cơ sở)
| # | Việc | Trạng thái |
|---|------|-----------|
| 1 | Bảng 3.1: "zero weight drift" → **"no factual drift"** | ✅ đã sửa |
| 2 | Thêm **Threats to Validity** (3.1) | ✅ đã thêm |
| 3 | Nâng **ReGrad thành baseline đo thật** (latency+acc) | 🔧 cần cập nhật 3.1/Ch4 |
| 4 | Câu **novelty sắc** ở 3.1 + Abstract | 🔧 nên thêm |
| 5 | Ch4: nói baseline parametric chạy **scaled-down** | 🔧 nên thêm |
| 6 | 3.3: thêm câu **log minimization** | 🔧 nên thêm |

## 3 nguyên tắc khi trả lời (đọc trước khi vào phòng)
1. **Nói rõ phạm vi, không over-claim** (đặc biệt câu Q3, Q5, Q11) → ghi điểm.
2. **Mỗi câu gắn 1 việc đã làm/sẽ làm** (Threats to Validity, ablation, đo lại trên VN).
3. **Thừa nhận rủi ro #1 (metadata)** một cách chủ động → thể hiện hiểu sâu.
