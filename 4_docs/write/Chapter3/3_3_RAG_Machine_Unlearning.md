# 3.3 RAG Machine Unlearning  ↔ `3_Methods_Models/3_3_RAG_Machine_Unlearning.tex`

> Trụ method 2 — hiện thực lý thuyết 2.6. Gắn **CQ3 / H3**. | Nguồn: `Wang2026_RAGUnlearning`.

---

## 3.3.1 Phát biểu vấn đề (chính xác — quan trọng để không over-claim)

Mục tiêu unlearning ở đây là: **hệ thống KHÔNG truy xuất, KHÔNG grounding, KHÔNG trích dẫn** tri thức cần quên — chứ **không phải xoá tri thức khỏi trọng số** base LLM (base đóng băng). Đây là **"behavioral / retrieval-level unlearning"**, phù hợp use case compliance (không viện dẫn luật hết hiệu lực, không tiết lộ sealed case).

## 3.3.2 Taxonomy "forget request" (3 loại × cường độ khác nhau)

| Loại | Ví dụ | Cường độ quên |
|------|-------|----------------|
| **Temporal** (luật hết hiệu lực) | Điều X bị thay bởi văn bản mới | *Soft + temporal gate*: ẩn cho query hiện hành, **vẫn trả lời cho query "tại thời điểm 20XX"** |
| **Sealed / hạn chế** | Bản án niêm phong, confidential | *Access-control gate*: chỉ user có quyền mới truy xuất |
| **Personal data / right-to-be-forgotten** | Xoá thông tin cá nhân | *Hard delete*: gỡ hẳn entry khỏi KB |

## 3.3.3 Khung P + Q (theo `Wang2026_RAGUnlearning`)

Tri thức cần quên mô hình hoá thành cặp (P, Q):
- **P — thành phần truy xuất:** thao tác phía retriever để mục tiêu **không vào candidate set** (xoá / ẩn / down-rank tuyệt đối tuỳ cường độ ở 3.3.2).
- **Q — thành phần ràng buộc:** ràng buộc ở **prompt/đầu ra** để model **không dựa vào / không tiết lộ** tri thức đã quên, kể cả khi nó rò rỉ qua entry liên quan hoặc parametric memory.

```
unlearn(target):
    P: KB_policy[target] = {delete | hide | temporal_gate | acl_gate}
    Q: system_prompt += ràng buộc("không viện dẫn/không tiết lộ", target_class)
# Không cập nhật trọng số. Có thể đảo ngược (trừ hard delete).
```

## 3.3.4 Lớp lọc & kiểm toán
- **Compliance/privacy filter** ở đầu ra: chặn câu trả lời nếu phát hiện grounding vào entry đã unlearn.
- **Audit log**: ghi lại request, policy áp dụng, thời điểm → phục vụ giải trình pháp lý.

## 3.3.5 Tiêu chí & chỉ số đánh giá (dùng lại ở Ch4)
| Tiêu chí (`Wang2026_RAGUnlearning`) | Ý nghĩa | Chỉ số |
|---|---|---|
| Effectiveness | có thật sự quên? | USR, ROUGE-L (giảm) |
| Universality | quên qua mọi cách diễn đạt query | test paraphrase |
| **Harmlessness** | không hại truy vấn không liên quan | accuracy giữ nguyên trên control set |
| Robustness | chống jailbreak khai thác parametric | TPR@1%FPR, HOP |
| Simplicity | chi phí thấp, không train | thời gian/chi phí |

## 3.3.6 Giả định & giới hạn (NÊU TRƯỚC — đây là chỗ dễ bị hỏi xoáy nhất)
- (GH-U1) **Không đảm bảo xoá tri thức trong weight** base LLM. Nếu base đã pretrain trên luật đó, model có thể vẫn "biết". Ta giảm rò rỉ bằng Q + filter và **đo lượng rò rỉ còn lại** (robustness), không claim xoá tuyệt đối.
- (GH-U2) Hiệu quả phụ thuộc chất lượng phát hiện "entry liên quan" — quên không đủ rộng → rò rỉ; quên quá rộng → hại harmlessness. Trade-off này được đo trực tiếp.

---

## ❓ Câu hỏi có thể bị hỏi (section này)

**Q (câu xoáy nhất): Tri thức đã nằm trong weight của base LLM — gỡ khỏi KB thì model vẫn trả lời được, vậy "unlearning" của em có thật sự quên không?**
A: Trả lời thẳng và đúng phạm vi: hệ thống đảm bảo **không truy xuất / không grounding / không trích dẫn** entry đã quên (retrieval-level unlearning) — đủ cho use case compliance pháp lý. Nó **không** đảm bảo xoá tri thức khỏi trọng số; với rò rỉ parametric, ràng buộc Q + filter làm giảm, và ta **định lượng phần rò rỉ còn lại** bằng robustness (TPR@1%FPR, jailbreak HOP). Muốn đảm bảo mạnh ở mức trọng số cần model-level unlearning — **ngoài phạm vi**, đã ghi rõ ở Limitations. → Thể hiện mình hiểu giới hạn, hội đồng đánh giá cao hơn là vòng vo.

**Q: Vậy khác gì DELETE record trong database?**
A: Cái khó không phải xoá entry, mà là: (a) **harmlessness** — gỡ X không làm hỏng trả lời Y liên quan; (b) **universality** — quên qua mọi paraphrase; (c) **temporal regime** — quên luật cũ cho query hiện hành NHƯNG vẫn đúng cho query "tại 2010"; (d) **robustness** chống jailbreak. Đó là constrained optimization (P+Q), không phải DELETE đơn thuần.

**Q: Làm sao biết luật nào hết hiệu lực để gán metadata? Sai thì sao?**
A: Lấy từ chính văn bản (điều khoản hết hiệu lực, văn bản thay thế) + bán tự động + QC mẫu; thừa nhận sai metadata → sai compliance là **rủi ro chính** (đã ghi ở timeline), giảm thiểu bằng kiểm chứng thủ công tập trọng yếu.

**Q: Đảo ngược được không (nhỡ unlearn nhầm)?**
A: Có, trừ hard-delete (personal data). Temporal/sealed chỉ đổi policy → bật lại được; audit log truy vết được.
