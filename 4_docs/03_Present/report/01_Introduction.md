# 01 — Introduction (Hook → Problem → Goal)

## Slide 1 — Why legal LLMs, and why they break
- LLMs now power contract review, case-law retrieval, legal QA.
- But legal knowledge is **non-stationary**: statutes amended/replaced, precedents overturned, cases sealed.
- A static-weight model becomes outdated → **obsolete or wrong legal advice**.

💬 Kể: "AI pháp lý đang bùng nổ, nhưng luật thì đổi liên tục — model học xong là lỗi thời."
📌 `le-etal-2025-overview`, `nguyen2025vlqacomprehensivelargehighquality`

## Slide 2 — The dual challenge
- **Continuous update**: nạp luật/án lệ mới liên tục.
- **Intentional forgetting**: luật hết hiệu lực · sealed case · right-to-be-forgotten.
- Hai yêu cầu *ngược nhau* với cách học truyền thống.

💬 Kể: "Không chỉ phải nhớ thêm, mà còn phải **quên đúng thứ phải quên** — đây là điểm khó."

## Slide 2b — Why now (timeliness)
- **Adoption**: trợ lý pháp lý dựa trên LLM đang được dùng rộng.
- **Data mới**: VLQA / ViLegalNLI / LegalSLM vừa ra **2025–2026** → nền thực nghiệm sẵn sàng.
- **Quy định siết**: right-to-be-forgotten / data governance → nhu cầu "quên có kiểm soát" cấp thiết.

💬 Kể: "Đúng thời điểm — dữ liệu vừa có + quy định vừa siết → làm bây giờ là hợp lý nhất."
📌 `nguyen2025vlqa...`, `duong2026vilegalnli...`, `le-etal-2025-overview`

## Slide 3 — Why not just fine-tune (CPT/CIT)?
- Parametric continual learning ⇒ **catastrophic forgetting** (Inverse Linear Law).
- Weight updates tốn compute; **unlearning trên weight rất khó**, hại utility.

💬 Kể: "Dạy thẳng vào trọng số = vừa quên kiến thức cũ, vừa không gỡ được luật sai."
📌 `kalajdzievski2024scalinglawsforgettingfinetuning`, `li2024revisitingcatastrophicforgettinglarge`, `11207222`

## Slide 4 — The idea: hybrid, frozen-base
- **Freeze base LLM** → general reasoning intact.
- **Facts → external RAG memory** (cập nhật/unlearn = thao tác KB).
- **Behavior → LoRA adapter** (IFT, không nhớ facts).
- + **Selective memory** (ưu tiên điều luật quan trọng) + **unlearning gate**.

💬 Kể: "Tách bạch: facts ra ngoài (RAG), kỹ năng lập luận vào LoRA, base giữ nguyên."

## Slide 5 — Objectives
- **General:** non-parametric RAG continual-learning framework cho QA/suy luận luật VN, base frozen.
- **Specific:** (1) legal importance-based **selective memory**; (2) **controllable unlearning** (P+Q); (3) **evaluation protocol** legal-CL VN.

## Slide 6 — Research questions & hypotheses
- **CQ1/H1 — Selective memory:** feature importance nào (citation/risk/temporal) giảm index + giữ accuracy?
- **CQ2/H2 — RAG vs parametric:** RAG có giảm forgetting chung + xử lý regime thời gian tốt hơn CPT/CIT? (đo bằng chuỗi tuần tự **lao động→thuế**, chỉ số Backward Transfer)
- **CQ3/H3 — Unlearning:** P+Q quên được tới đâu mà không hại truy vấn khác?

💬 Kể: mỗi câu hỏi gắn 1 giả thuyết **bác bỏ được** → nghiên cứu nghiêm túc.

## Slide 7 — Scope & contributions
- **Scope:** luật VN (bộ luật, nghị định, thông tư, án lệ); tasks QA/NLI/reasoning; không generation dài. **Testbed thu hẹp = lao động (primary, đủ 3 scenario) + thuế (secondary, slice cô đọng, là task thứ 2 đo forgetting)**; framework domain-general, mở rộng ngành khác = future work.
- **3 đóng góp:** (1) legal selective memory (mở rộng Ebbinghaus sang luật + compliance gate); (2) retrieval-level unlearning gate (P+Q); (3) benchmark/protocol legal-CL VN.
- **💡 Significance:** *KH* — nối CL + RAG + legal temporal regimes cho low-resource VN; *thực tiễn* — trợ lý pháp lý tuân thủ, độ trễ thấp.
📌 `nguyen2025vlqa...`, `duong2026vilegalnli...`, `le-etal-2025-overview`
