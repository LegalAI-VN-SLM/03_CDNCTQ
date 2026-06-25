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
- **General:** dùng **RAG phi-tham số (base frozen)** chống quên thảm khốc khi nạp tri thức luật liên tục, đo trên QA/NLI/Syllogism.
- **Specific:** (1) **xây kho RAG chọn lọc** (importance + decay + compliance — như ReGrad xây bank nhưng phi-tham số); (2) **đo forgetting khi nạp liên tục** (RAG vs CPT vs ReGrad × 3 task); (3) **soi trần lập luận** (retrieval cứu QA/NLI tới đâu, có vượt syllogism không).

## Slide 6 — Research questions & hypotheses
- **H1 — Forgetting:** CPT nạp nhiều ⇒ **quên thảm khốc** (sụp như Bảng B ReGrad). Đo: BWT, perf↓.
- **H2 — RAG né quên:** RAG base frozen **không quên trọng số**, perf ổn định theo lượng nạp.
- **H3 — Selective cứu RAG:** kho phình → vanilla RAG tụt recall; **selective giữ recall** (index↓≥30%, ±2%).
- **H4 — Trần lập luận:** RAG mạnh ở **QA** > NLI > **Syllogism** (retrieval không thay được suy luận).

💬 Kể: mỗi giả thuyết **bác bỏ được bằng số** → nghiên cứu nghiêm túc. Khuôn lấy từ Bảng B của ReGrad (nạp liên tục → đo quên).

## Slide 7 — Scope & contributions
- **Scope:** luật VN; **3 task QA/NLI/Syllogism**; không generation dài. Framework **domain-general**; **kịch bản nạp liên tục lấy data từ** sub-domain biến động nhiều (lao động, thuế).
- **3 đóng góp:** (1) **kho RAG chọn lọc** chống "quên kiểu RAG"; (2) **bằng chứng định lượng** RAG né quên thảm khốc trên 3 task luật VN (đối chiếu CPT/ReGrad); (3) **phát hiện trần lập luận** — retrieval cứu QA/NLI nhưng chạm trần syllogism.
- **💡 Significance:** *KH* — nối CL + RAG + legal temporal regimes cho low-resource VN; *thực tiễn* — trợ lý pháp lý tuân thủ, độ trễ thấp.
📌 `nguyen2025vlqa...`, `duong2026vilegalnli...`, `le-etal-2025-overview`
