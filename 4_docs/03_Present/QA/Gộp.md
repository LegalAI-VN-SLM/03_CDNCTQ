---
updated:
  - 2026-06-25T23:45:54+07:00
  - 2026-06-25T23:43:23+07:00
  - 2026-06-25T23:40:54+07:00
  - 2026-06-25T23:31:30+07:00
---

# BẢNG TRA CỨU NHANH CÂU HỎI PHẢN BIỆN (TOC)

## 📌 Câu hỏi mở đầu
*   [Q0. "Cổng quên" (Unlearning Gate) trong nghiên cứu này có liên quan gì đến RAG không?](#Q0)

## 📌 Nhóm 1 — Novelty & Lý do tồn tại
*   [Q1. "Ngoài việc ghép các phương pháp có sẵn cho luật VN, đóng góp khoa học cốt lõi là gì?"](#Q1)
*   [Q2. "Vì sao xứng tầm nghiên cứu chứ không phải triển khai kỹ thuật?"](#Q2)

## 📌 Nhóm 2 — Mâu thuẫn lõi (Devil's Advocate)
*   [Q3. (CRITICAL) "Em nói frozen base không quên, nhưng vẫn train LoRA — vậy 'zero weight drift' có chính xác không?"](#Q3)
*   [Q4. "Số liệu của chính em cho thấy ReGrad đạt 84.87% trên luật, cao hơn RAG — vì sao vẫn loại ReGrad?"](#Q4)
*   [Q5. "Selective RAG không nâng được năng lực suy luận của base — vậy giải quyết tam đoạn luận (chỗ yếu nhất) bằng cách nào?"](#Q5)

## 📌 Nhóm 3 — Phương pháp nghiên cứu (Methodology)
*   [Q6. "Importance score là tổng tuyến tính có trọng số — học/validate thế nào để không circular? Có ground truth không?"](#Q6)
*   [Q7. "VLQA vừa là KB truy xuất vừa là tập test — chống contamination/rò rỉ thế nào?"](#Q7)
*   [Q8. "Baseline CPT/CFT trên luật VN tốn compute — 6 tháng + 200h A100 có thực tế không?"](#Q8)

## 📌 Nhóm 4 — Miền tri thức Luật Việt Nam
*   [Q9. (CRITICAL) "Làm sao xác định & cập nhật hiệu lực cho toàn bộ kho luật VN đáng tin? Sai metadata thì cả hệ sụp."](#Q9)
*   [Q10. "Vì sao mô hình quên nhận thức (Ebbinghaus) lại hợp với văn bản luật? Luật hiếm tra cứu nhưng tối quan trọng thì sao?"](#Q10)

## 📌 Nhóm 5 — Bảo mật & Hệ thống (Privacy/Systems)
*   [Q11. "Q chỉ là ràng buộc prompt — jailbreak là lộ. Chứng minh robustness trên luật VN thế nào?"](#Q11)
*   [Q12. "Right-to-be-forgotten mà audit log vẫn lưu yêu cầu/nội dung — có mâu thuẫn riêng tư không?"](#Q12)

---

## 📌 Sổ tay chuẩn bị bảo vệ & Giải thích Sơ đồ
*   [Tổng hợp các việc cần vá (vá lỗi proposal)](#Patch)
*   [3 nguyên tắc vàng khi trả lời Hội đồng](#Principles)
*   [Bảng tra cứu thuật ngữ & ký hiệu toán học](#Keywords)
*   [Figure 3.1: Overall Framework (Kiến trúc tĩnh)](#Fig31)
*   [Figure 3.2: Operational Workflow (Vòng đời 1 câu hỏi - Động)](#Fig32)
*   [Figure 3.3: Data Methodology Pipeline (Quy trình thực nghiệm)](#Fig33)
*   [Phân tích chi tiết ký hiệu toán học](#Terms)
*   [Mỗi hình một câu chốt (để nhớ nhanh)](#Chot)

---

# CÂU HỎI MỞ ĐẦU: LIÊN QUAN GIỮA CỔNG QUÊN VÀ RAG

### Q0
**Q0. "Cổng quên" (Unlearning Gate) trong nghiên cứu này có liên quan gì đến RAG không?**

Có, **"cổng quên" (Unlearning Gate)** trong nghiên cứu này có liên quan trực tiếp và chặt chẽ đến RAG. Thực chất, nó được xây dựng hoàn toàn dựa trên cơ chế của RAG thay vì can thiệp vào mạng neural của mô hình ngôn ngữ. Bài báo gọi phương pháp này là **RAG Machine Unlearning** (Học quên bằng RAG).

Thay vì phải xóa bỏ dữ liệu khỏi trọng số (weights) của LLM - một việc cực kỳ tốn kém, phức tạp và dễ làm giảm độ thông minh của mô hình - cổng quên này lợi dụng chính luồng hoạt động của RAG để "chặn" thông tin,. 

Cụ thể, cổng quên can thiệp vào RAG thông qua khung tối ưu hóa **$P + Q$**, tác động vào cả 2 khâu của RAG (Truy xuất và Sinh văn bản):

**1. Liên quan đến khâu Truy xuất của RAG (Retriever Policy $P$)**
*   Cổng quên đóng vai trò như một màng lọc phi tham số (Non-Parametric Control) ngay tại cơ sở dữ liệu Vector của RAG. 
*   Khi có một điều luật bị bãi bỏ, bản án bị niêm phong, hoặc yêu cầu xóa dữ liệu cá nhân, chính sách $P$ sẽ tự động khoanh vùng và "giấu" các tài liệu này đi. Kết quả là khi người dùng đặt câu hỏi, bộ truy xuất (Retriever) của RAG sẽ hoạt động trên một cơ sở dữ liệu đã bị giới hạn, hoàn toàn không thể tìm thấy và trích xuất các thông tin cấm đó.

**2. Liên quan đến khâu Sinh văn bản của RAG (Prompt Constraints $Q$)**
*   Để phòng hờ trường hợp LLM vô tình nhớ lại kiến thức cấm từ đợt tiền huấn luyện (pre-training) hoặc rò rỉ ngữ cảnh, cổng quên áp dụng ràng buộc $Q$ vào khâu tạo Prompt của RAG,.
*   Nó sẽ tự động nối thêm một lệnh cấm (negative constraints) vào System Prompt trước khi gửi cho LLM, ép buộc mô hình phải từ chối sinh ra thông tin liên quan đến dữ liệu đã bị cấm và chuyển hướng sang các điều luật đang có hiệu lực.

**Tóm lại:** 
Cổng quên trong đề cương này **chính là một màng lọc bảo mật được đặt vào giữa pipeline của RAG** (ngay sau bước Retrieve và trước bước đưa context cho LLM đọc). Việc dùng RAG để "học quên" giúp hệ thống có thể cập nhật, ẩn, hoặc xóa các điều luật cũ/nhạy cảm ngay lập tức với chi phí gần như bằng không, đồng thời bảo vệ LLM gốc không bị "quên thảm khốc" (catastrophic forgetting) các kỹ năng suy luận cơ bản,.

---------------


------------

# 06 — Reviewer Q&A: Câu hỏi xoáy + Câu trả lời sẵn (để ôn & trả lời tại chỗ)

> Nguồn: mô phỏng hội đồng `ars-reviewer` (full) trên **Ch2 + Ch3**. Quyết định mô phỏng: **MAJOR REVISION** — định hướng tốt, cần vá 4 lỗ hổng trước bảo vệ.
> Cách dùng: đọc **câu hỏi → trả lời 3–5 câu** (đã viết sẵn để nói). 🔧 = việc cần vá trong proposal để câu trả lời có cơ sở. ✅ = đã vá.

---

## NHÓM 1 — Novelty & lý do tồn tại

### Q1
**Q1. "Ngoài việc ghép các phương pháp có sẵn cho luật VN, đóng góp khoa học cốt lõi là gì?"**

**Trả lời:** Đề tài không tuyên bố một thuật toán RAG mới. Đóng góp khoa học nằm ở **ba điểm**: (1) định nghĩa hai *construct* mới làm first-class — **legal importance** (đo bằng citation centrality + hiệu lực + rủi ro) và **legal temporal regime** (validity interval); (2) **hợp nhất** selective memory và controllable unlearning trong một khung non-parametric, trong đó *cập nhật luật* và *quên có chủ đích* dùng chung một cơ chế; (3) một **benchmark/giao thức đánh giá legal continual learning cho tiếng Việt** chưa từng có. Đây đúng vai một nghiên cứu *định hướng*: tái khung bài toán + bằng chứng, không phải chỉ engineering.
🔧 Một câu novelty sắc ở đầu 3.1 + Abstract (nên thêm).

### Q2
**Q2. "Vì sao xứng tầm nghiên cứu chứ không phải triển khai kỹ thuật?"**

**Trả lời:** Vì nó trả lời ba câu hỏi *chưa có lời giải thực nghiệm* cho miền luật VN: importance signal nào hiệu quả (CQ1), RAG có thực sự giảm forgetting so parametric trong điều kiện regime thời gian (CQ2), và retrieval-level unlearning đạt compliance tới đâu mà không hại năng lực khác (CQ3). Mỗi câu là một giả thuyết **bác bỏ được**, có biến kiểm soát và metric — đó là tiêu chí của nghiên cứu, không phải triển khai.

---

## NHÓM 2 — Mâu thuẫn lõi (Devil's Advocate)

### Q3
**Q3. (CRITICAL) "Em nói frozen base không quên, nhưng vẫn train LoRA — vậy 'zero weight drift' có chính xác không?"**

**Trả lời:** Cảm ơn thầy, đây là chỗ em cần nói chính xác hơn. **Trọng số *base* được đóng băng hoàn toàn** — đó là nơi chứa năng lực ngôn ngữ/suy luận chung, nên năng lực chung không bị quên. **LoRA là một adapter nhỏ tách rời**, chỉ học *hành vi* (cách lập luận, trích dẫn), **không** chứa facts pháp lý — facts nằm ở RAG. Vì vậy chính xác là **"không trôi dạt tri thức *facts*"**, và em đã sửa bảng so sánh ở 3.1 thành *"no factual drift (base frozen; behaviour via LoRA)"*. Còn forgetting *hành vi* khi học tuần tự nhiều ngành luật thì em xử lý bằng O-LoRA/N-LoRA (đã nêu ở 3.4).
✅ Đã sửa câu chữ bảng 3.1.

### Q4
**Q4. "Số liệu của chính em cho thấy ReGrad đạt 84.87% trên luật, cao hơn RAG — vì sao vẫn loại ReGrad?"**

**Trả lời:** Đúng, ReGrad mạnh về accuracy. Em loại nó khỏi *trục chính* vì hai lý do gắn với yêu cầu pháp lý: **(1) unlearning** — ReGrad chỉnh trọng số tạm thời nên "quên có chủ đích" rất khó, trong khi RAG chỉ cần gỡ entry; **(2) độ trễ inference** do áp gradient động lúc chạy, mâu thuẫn với ràng buộc latency + auditability của hệ pháp lý. Tuy nhiên em **thừa nhận chưa đo latency thực** — nên em sẽ **giữ ReGrad làm một baseline định lượng** (không chỉ đối chiếu định tính) và **đo latency + accuracy thật** để kết luận có cơ sở.
🔧 Nâng ReGrad thành baseline đo thật (cập nhật 3.1/Ch4).

### Q5
**Q5. "Selective RAG không nâng được năng lực suy luận của base — vậy giải quyết tam đoạn luận (chỗ yếu nhất) bằng cách nào?"**

**Trả lời:** Em không over-claim chỗ này — đây là **giới hạn đã biết (GH1)**: LegalSLM cho thấy syllogism chỉ ~0.5–0.6. Selective RAG cải thiện *chất lượng context* (đúng điều luật, đúng hiệu lực) nên **giảm lỗi do retrieve sai**, và LoRA dạy *định dạng lập luận* (đại–tiểu tiền đề–kết luận); nhưng chiều sâu suy luận vẫn bị chặn bởi base. Em chọn base instruct đủ mạnh và **ghi rõ ở Threats to Validity + Limitations**; nâng reasoning sâu (vd reasoning-augmented decoding) là **future work**.

---

## NHÓM 3 — Methodology (R1)

### Q6
**Q6. "Importance score là tổng tuyến tính có trọng số — học/validate thế nào để không circular? Có ground truth không?"**

**Trả lời:** Không có ground-truth tuyệt đối cho "importance pháp lý" — em coi nó là **proxy**. Để tránh circular: trọng số được **tune trên development set tách riêng**, từng feature được **ablation** để đo đóng góp, và **đánh giá retrieval báo cáo trên truy vấn unseen** (không phải tập đã tune). Em cũng so hai phương án rule-based vs LLM-estimated. Em đã ghi điều này thành mục **Threats to Validity (construct validity)**.
✅ Đã thêm Threats to Validity.

### Q7
**Q7. "VLQA vừa là KB truy xuất vừa là tập test — chống contamination/rò rỉ thế nào?"**

**Trả lời:** Em tách **train/dev/test có decontaminate**, và **báo cáo recall trên truy vấn held-out** không xuất hiện lúc tune. KB và tập câu hỏi được phân tách để câu trả lời không "thấy trước" nhãn. Đây cũng nằm trong Threats to Validity (internal validity).
✅ Đã ghi.

### Q8
**Q8. "Baseline CPT/CFT trên luật VN tốn compute — 6 tháng + 200h A100 có thực tế không?"**

**Trả lời:** Em ưu tiên theo nấc: chạy **static-RAG baseline + selective + unlearning** trước (rẻ, vì base frozen), và CPT/CFT baseline chạy **scaled-down cùng cỡ mô hình nhỏ (1–4B, ≤15B)** với hệ chính → so sánh công bằng, chỉ đo *xu hướng forgetting*, không cần full-scale. Base frozen ⇒ chi phí chính là index 1 lần. Nếu compute thiếu, CPT full là phương án mở rộng.
✅ Đã ghi rõ ở 3.1 / 3.4 / 4.3: model 1–4B (≤15B), baseline parametric *scaled-down* cùng cỡ.

---

## NHÓM 4 — Domain luật VN (R2)

### Q9
**Q9. (điểm yếu chí mạng) "Làm sao xác định & cập nhật hiệu lực cho toàn bộ kho luật VN đáng tin? Sai metadata thì cả hệ sụp."**

**Trả lời:** Em đồng ý đây là **rủi ro số một**. Em **không** cần gắn metadata hoàn hảo cho *toàn bộ* kho luật ngay: phần thực nghiệm chỉ cần dữ liệu ở **các kịch bản test** (lấy từ sub-domain biến động nhiều như lao động/thuế), nên khối lượng metadata cần kiểm chứng là **khả thi trong 6 tháng** — framework vẫn domain-general, không bị bó vào đó. Hướng xử lý: (1) **lấy hiệu lực từ chính văn bản** — điều khoản quy định hết hiệu lực, văn bản thay thế — chứ không đoán; (2) **expert spot-check 5%** + canary regression set sau mỗi cập nhật; (3) hệ có **versioning + rollback** nên sai thì khôi phục được. Em **không giả định metadata hoàn hảo** — đã đưa thành *assumption GĐ1* và một mục *Threats to Validity* riêng. Phủ metadata cho quy mô toàn kho là vấn đề kỹ thuật mở rộng, không phải rào cản phương pháp.
✅ Đã ghi Threats to Validity + GĐ1 + Evaluation Data Scenarios (1.4).

### Q10
**Q10. "Vì sao mô hình quên nhận thức (Ebbinghaus) lại hợp với văn bản luật? Luật hiếm tra cứu nhưng tối quan trọng thì sao?"**

**Trả lời:** Em phân tách hai thứ: **validity là hard gate** — điều luật *đang hiệu lực* **không bao giờ** bị loại dù importance thấp; decay chỉ tác động *xếp hạng* trong tập hợp lệ và đẩy xuống **archive (vẫn truy xuất được, không xoá)**. Ebbinghaus chỉ là cảm hứng cho thành phần *tần suất*, đóng vai phụ trong tổng importance, không phải tiêu chí duy nhất. Em sẽ **ablation** để chứng minh decay không làm tụt recall các điều luật hiếm-mà-quan-trọng; nếu có, em giảm trọng số decay.

---

## NHÓM 5 — Privacy/Systems (R3)

### Q11
**Q11. "Q chỉ là ràng buộc prompt — jailbreak là lộ. Chứng minh robustness trên luật VN thế nào (không mượn số English)?"**

**Trả lời:** Đúng, unlearning của em ở mức **behavioral**, không xoá khỏi trọng số — em nói rõ phạm vi này. Phòng thủ là **kép**: P (gỡ/ẩn entry khỏi truy xuất) + Q (ràng buộc đầu ra) + **filter đầu ra**. Em **không dùng số English làm kết luận**; các số như USR/TPR chỉ là *động lực*, và em cam kết **đo robustness (TPR@1%FPR dưới paraphrase/jailbreak) trên chính VLQA/ViLegalNLI**. Phần rò rỉ parametric còn lại được **báo cáo như cận trên**, không che giấu.
✅ External validity đã ghi ở Threats to Validity.

### Q12
**Q12. "Right-to-be-forgotten mà audit log vẫn lưu yêu cầu/nội dung — có mâu thuẫn riêng tư không?"**

**Trả lời:** Audit log chỉ lưu **metadata thao tác** (timestamp, loại request, entry-id, trạng thái) để giải trình, **không lưu nội dung nhạy cảm** đã xoá; với hard-delete (personal data) thì entry bị gỡ khỏi KB + index. Em sẽ ghi rõ chính sách log tối thiểu hoá dữ liệu trong 3.3.
🔧 Thêm 1 câu "log minimization" vào 3.3.

---

## Patch
**Tổng hợp việc cần vá (để câu trả lời có cơ sở)**

| # | Việc | Trạng thái |
|---|------|-----------|
| 1 | Bảng 3.1: "zero weight drift" → **"no factual drift"** | ✅ đã sửa |
| 2 | Thêm **Threats to Validity** (3.1) | ✅ đã thêm |
| 3 | Nâng **ReGrad thành baseline đo thật** (latency+acc) | 🔧 cần cập nhật 3.1/Ch4 |
| 4 | Câu **novelty sắc** ở 3.1 + Abstract | 🔧 nên thêm |
| 5 | Ch4/3.x: baseline parametric **scaled-down**, model **1–4B (≤15B)** | ✅ đã thêm |
| 6 | 3.3: thêm câu **log minimization** | 🔧 nên thêm |

## Principles
**3 nguyên tắc khi trả lời (đọc trước khi vào phòng)**

1. **Nói rõ phạm vi, không over-claim** (đặc biệt câu Q3, Q5, Q11) → ghi điểm.
2. **Mỗi câu gắn 1 việc đã làm/sẽ làm** (Threats to Validity, ablation, đo lại trên VN).
3. **Thừa nhận rủi ro #1 (metadata)** một cách chủ động → thể hiện hiểu sâu.

----------------

-------------

# Figures
**QA — Giải thích Figure 3.1 / 3.2 / 3.3 (từ khoá → đi sâu, có ví dụ luật VN)**

> Cách dùng: **Phần 1** tra nhanh nghĩa mọi từ khoá/ký hiệu xuất hiện trên 3 hình. **Phần 2–4** đi sâu từng khối của từng hình: *Nó LÀ GÌ (cụ thể) · LÀM GÌ · ví dụ luật VN · VÌ SAO đặt ở đó*. **Phần 5** là ký hiệu toán.
> Quy ước ví dụ: dùng **Bộ luật Lao động 2019**, **Nghị định 145/2020/NĐ-CP**, **Luật Thuế TNCN** làm minh hoạ.

---

## Keywords
**1. TỪ KHOÁ — bảng tra nhanh**

### 1a. Khối/nhãn trên hình
| Từ khoá | Nghĩa cụ thể (1 dòng) |
|---|---|
| **Vietnamese Legal Corpus** | Kho văn bản gốc: Luật, Nghị định, Thông tư, Bản án/Án lệ — dạng thô chưa xử lý |
| **Structure Normalization** | Cắt văn bản dài thành **đơn vị** (Điều/Khoản) + chuẩn hoá định dạng |
| **Metadata Tagging** | Gắn nhãn cho mỗi đơn vị: còn hiệu lực không, ngày hiệu lực, quyền truy cập… |
| **Citation Graph** | Đồ thị "văn bản A dẫn chiếu văn bản B" → đo độ quan trọng cấu trúc |
| **Embedding Generation** | Biến nội dung đơn vị thành **vector số** để tìm theo ngữ nghĩa |
| **Knowledge Base (KB)** | Kho đã xử lý: tập các đơn vị tri thức, mỗi đơn vị kèm metadata |
| **Semantic Retriever** | Bộ tìm kiếm theo *nghĩa* (không phải khớp từ khoá) |
| **Unlearning Gate (P)** | Cổng **lọc bỏ** đơn vị bị cấm/đã xoá/đã thay theo *policy P* |
| **Selective Memory** | Bộ **xếp hạng lại** đơn vị theo độ quan trọng pháp lý |
| **Compliance Gate** | "Cổng cứng": **luôn giữ** luật còn hiệu lực liên quan, không cho rớt |
| **Frozen Base LLM** | Model ngôn ngữ nền, **đóng băng trọng số** (không huấn luyện lại) |
| **LoRA (adapter)** | Mô-đun nhỏ gắn thêm, học **cách hành xử** (format, trích dẫn), không chứa fact |
| **Audit Logging** | Ghi nhật ký truy vết: dùng điều luật nào để trả lời (cho compliance) |
| **Continual update** | Cơ chế cập nhật KB liên tục khi có luật mới (không retrain model) |
| **Supersession** | "Thay thế": luật mới ra → luật cũ bị đánh dấu hết hiệu lực |
| **Canary** | Bộ test "chim hoàng yến": kiểm tra nhanh trước khi commit bản KB mới |
| **Rollback** | Hoàn tác về bản KB trước nếu canary thất bại |

### 1b. Ký hiệu toán (chi tiết ở Phần 5)
| Ký hiệu | Nghĩa |
|---|---|
| `q`, `t_q` | câu hỏi người dùng · mốc thời gian của câu hỏi |
| `e_i` | embedding (vector) của đơn vị tri thức thứ *i* |
| `sim(q, e_i)` | độ tương đồng ngữ nghĩa giữa query và đơn vị *i* |
| `v_i` | cờ hiệu lực: 1 = còn hiệu lực, 0 = đã bãi bỏ/thay |
| `[t_start, t_end]` | khoảng thời gian đơn vị có hiệu lực |
| `c_i` | citation centrality — độ trung tâm trong đồ thị dẫn chiếu |
| `ρ_i` | risk/applicability weight — trọng số theo thứ bậc & rủi ro |
| `H_i` | access history — lịch sử truy xuất (cho decay) |
| `acl_i` | access control list — quyền truy cập |
| `I_i` | **importance score** tổng hợp (xếp hạng selective) |
| `P` | Filter Policy — chính sách lọc (unlearning) trên *tập tài liệu* |
| `Q` | Prompt Constraints — ràng buộc trên *cách sinh câu trả lời* |
| `β` | hệ số cân bằng giữa "giống nghĩa" và "quan trọng pháp lý" |
| `top-N`, `top-K` | số đơn vị lấy ra ở bước retrieve (N rộng) và đưa vào LLM (K hẹp) |
| `BWT` | Backward Transfer — đo "học mới làm tụt cũ bao nhiêu" |
| `FGT` | Forgetting — mức quên năng lực chung |

---

## Fig31
**2. FIGURE 3.1 — Overall Framework (kiến trúc tĩnh)**

> Đọc: 2 cột. Trái = **OFFLINE** (xây kho, làm trước). Phải = **ONLINE** (chạy khi có câu hỏi). Nối nhau đúng **1 chỗ**: mũi tên *Retrieve*.

### Cột OFFLINE INDEXING

**① Vietnamese Legal Corpus (Laws, Decrees, Precedents)**
- *Là gì:* tập văn bản pháp luật **thô**, tải từ cổng chính phủ/công báo.
- *Cụ thể:* "Bộ luật Lao động 2019" (Luật), "Nghị định 145/2020" (Nghị định), "Thông tư 10/2020" (Thông tư), bản án (Precedent).
- *Vì sao là điểm bắt đầu:* đây là **nguồn fact** duy nhất — toàn bộ tri thức nằm ở đây, KHÔNG nằm trong trọng số model.

**② Structure Normalization & Metadata Tagging**
- *Là gì:* hai việc. (a) **Cắt** văn bản thành **đơn vị nguyên tử** = 1 Điều hoặc 1 Khoản hoặc 1 đoạn bản án. (b) **Gắn metadata** cho từng đơn vị.
- *Cụ thể:* "Điều 90 Bộ luật Lao động 2019 (tiền lương)" → 1 đơn vị, gắn `v=1` (còn hiệu lực), `t_start = 2021-01-01`, `acl = public`.
- *Vì sao:* model retrieve theo **đơn vị nhỏ** mới chính xác (không thể đưa cả bộ luật vào). Metadata là thứ sau này cho phép lọc theo *hiệu lực/thời gian/quyền*.

**③ Citation Graph Construction & Embedding Generation**
- *Citation Graph:* dựng đồ thị "văn bản nào dẫn chiếu văn bản nào". Nút = đơn vị; cạnh = lời dẫn chiếu. Từ đó tính `c_i` (đơn vị bị dẫn nhiều = trung tâm = quan trọng).
  - *Cụ thể:* Nghị định 145/2020 hướng dẫn Điều 90 → cạnh từ NĐ145 đến Điều 90; Điều 90 bị nhiều văn bản dẫn → `c` cao.
- *Embedding Generation:* mỗi đơn vị → 1 **vector `e_i`** bằng bi-encoder tiếng Việt (mã hoá *nghĩa*).
- *Vì sao:* `c_i` cho biết "quan trọng theo cấu trúc luật", `e_i` cho phép "tìm theo nghĩa" — hai trụ của bước xếp hạng sau này.

**④ Metadata-Augmented Knowledge Base (KB)**
- *Là gì:* kho cuối cùng. Mỗi đơn vị là **tuple** `⟨e, v, [t], c, ρ, H, acl⟩` (xem Phần 5).
- *Vì sao "metadata-augmented":* khác RAG thường (chỉ có text+vector), KB này mỗi mẩu còn mang **hiệu lực, thời gian, quyền, độ quan trọng** → đủ thông tin để *lọc tuân thủ* và *xếp hạng*.

### Cột ONLINE INFERENCE

**⑤ User Query q (Time-stamp t_q)**
- *Là gì:* câu hỏi + **mốc thời gian** muốn áp dụng.
- *Cụ thể:* "Lương tối thiểu vùng I năm 2022 là bao nhiêu?" với `t_q = 2022`. Mốc `t_q` để hệ thống biết phải dùng luật **đang hiệu lực tại 2022**, không phải bản 2024.

**⑥ Semantic Retriever (sim(q, e_i))**
- *Là gì:* tính `sim(q, e_i)` (cosine) giữa query và mọi `e_i`, lấy các đơn vị giống nghĩa nhất.
- *Vì sao đầu tiên:* phải có **tập ứng viên** trước đã, rồi mới lọc/xếp. Lấy *rộng* (top-N) để không bỏ sót.

**⑦ Unlearning Gate (3.3) (Filter Policy P)**
- *Là gì:* cổng **bỏ đi** các đơn vị mà policy `P` cấm: đã bãi bỏ, bị niêm phong, dữ liệu cá nhân phải xoá.
- *Cụ thể:* nếu mức lương cũ đã bị NĐ mới thay → `P` loại bản cũ khỏi tập ứng viên (trừ khi `t_q` rơi vào lúc bản cũ còn hiệu lực).
- *Vì sao đặt TRƯỚC selective:* phải vứt cái **không được phép** trước, rồi mới ưu tiên trong phần còn lại — để rác không bao giờ tới bước reasoning.

**⑧ Selective Memory (3.2) (Rerank & Compliance Gate)**
- *Rerank:* sắp xếp lại theo `sim + β·I_i` — vừa giống nghĩa vừa quan trọng pháp lý.
- *Compliance Gate (hard-gate):* **bắt buộc giữ** luật còn hiệu lực khớp câu hỏi, dù điểm số thấp.
- *Vì sao:* tránh hai lỗi — (a) bỏ sót điều luật cốt lõi vì nó "ít giống chữ"; (b) trả lời bằng luật phụ mà quên luật chính.

**⑨ Frozen Base LLM + LoRA (Behaviour adapter; Constraints Q)**
- *Frozen Base LLM:* model nền **đóng băng** — không bao giờ huấn luyện lại trên luật.
- *LoRA:* adapter nhỏ học **hành vi** (đọc context, viết theo dạng tam đoạn luận, trích dẫn đúng), **không** chứa fact.
- *Constraints Q:* ràng buộc lúc sinh (vd. "chỉ trả lời dựa trên context được cấp", "không suy đoán điều khoản không có").
- *Vì sao tách base/LoRA:* fact biến động → để ở KB (cập nhật rẻ); cách hành xử ổn định → để ở LoRA (train 1 lần). Base đóng băng ⇒ **không quên năng lực chung**.

**⑩ Reasoned Response & Audit Logging**
- *Là gì:* câu trả lời có lập luận + **trích dẫn điều luật**, kèm **log** ghi rõ dùng đơn vị nào.
- *Vì sao bắt buộc:* miền pháp lý cần **truy vết được** ("vì sao máy nói thế") — cho kiểm toán tuân thủ.

> **Tóm tắt thiết kế 3.1:** tách offline/online để cho thấy **mọi thứ biến động ở KB, model bất biến**. Thứ tự cổng (retrieve → lọc cấm → ưu tiên+giữ luật hiệu lực → sinh) đảm bảo *không bao giờ* lập luận trên tài liệu sai/cấm.

---

## Fig32
**3. FIGURE 3.2 — Operational Workflow (vòng đời 1 query, động)**

> Cùng các khối như 3.1 nhưng **đánh số 1–6** để kể "một câu hỏi đi qua hệ thống theo thời gian". Thêm **vòng cập nhật** ở dưới.

**KB / Offline indexing (strip trên):** nguồn nuôi bước Retrieve (đã giải thích ở 3.1).

**① Query — `q + query time t_q`**
- Nhận câu hỏi + mốc thời gian. *Đây là input.*

**② Retrieve — `sim(q, e_i) → top-N`**
- Lấy **N** đơn vị giống nghĩa nhất (N rộng, vd. 50). *Mục tiêu: phủ đủ, chấp nhận lẫn rác.*

**③ Unlearning gate — `Policy P: drop forgotten / unauthorized entries`**
- Bỏ đơn vị **đã quên (forgotten)** = đã bãi bỏ/yêu cầu xoá, và **không được phép (unauthorized)** = vượt quyền `acl`.
- *P tác động lên TẬP TÀI LIỆU* (vào/ra), không đụng câu chữ trả lời.

**④ Selective + Compliance — `re-rank by I_i; hard-keep in-force law`**
- Xếp hạng phần còn lại theo importance `I_i`; **giữ cứng** luật còn hiệu lực.
- *Kết quả: một danh sách gọn, đúng, ưu tiên điều cốt lõi.*

**⑤ Reason — `frozen base + LoRA + constraint Q`**
- LLM (đóng băng) + LoRA đọc context, sinh lập luận; `Q` ràng buộc output.
- *Q tác động lên CÁCH SINH* (khác P ở bước ③ tác động lên tài liệu) → đây là cặp **"P+Q"**.

**⑥ Answer + Audit — `cited answer + audit log`**
- Trả lời có trích dẫn + ghi log. *Đây là output.*

**Vòng dưới — Continual update & unlearning** `upsert · supersession (v=0) · canary · rollback`
- **upsert:** thêm/cập nhật đơn vị mới vào KB (vd. NĐ mới ra).
- **supersession (`v=0`):** luật cũ bị thay → đặt `v=0` (hết hiệu lực) + ghi `t_end` — đây chính là **temporal unlearning** (quên theo thời gian).
- **canary → rollback:** chạy bộ test nhanh; đạt thì commit bản KB mới, trượt thì hoàn tác.
- *Vì sao vẽ thành vòng:* cho thấy "continual" thật sự nằm ở **tầng KB**, quay lại offline indexing, **không retrain model** → cập nhật tức thì, chi phí ~0.

> **Tại sao P ở bước ③ còn Q ở bước ⑤:** unlearning có 2 tầng — chặn **truy xuất** (P, lọc tài liệu) và chặn **phát ngôn** (Q, ràng buộc sinh). Tách ra để đo được đóng góp từng cái (ablation "P-only vs P+Q").

---

## Fig33
**4. FIGURE 3.3 — Data Methodology Pipeline (cách làm thí nghiệm)**

> Đây là góc **nghiên cứu**: dữ liệu từ đâu → xử lý → lấy mẫu → phân tích. Trái→phải 4 chặng.

**① Collection** — `Gov portals & gazettes · Laws/decrees/circulars/precedents · Raw legal corpus`
- *Là gì:* thu thập văn bản từ **cổng chính phủ + công báo**.
- *Cụ thể:* tải Bộ luật Lao động, các Nghị định/Thông tư hướng dẫn, một số bản án lao động.
- *Vì sao tách riêng chặng:* để minh bạch nguồn (external validity) — số liệu sau này đo trên dữ liệu **thật, có nguồn**.

**② Preprocessing & Indexing** — `Segment→units · Normalize · Metadata (v,[t],c,ρ,acl) · Citation graph + embedding → KB`
- *Segment→units:* cắt thành Điều/Khoản (như 3.1-②).
- *Metadata `(v,[t],c,ρ,acl)`:* gắn 5 nhãn lõi cho mỗi đơn vị (xem Phần 5).
- *→ KB:* kết quả là cùng cái KB của 3.1.
- *Mũi tên nét đứt "expert validation (5%)":* chuyên gia **kiểm tay 5%** mẫu metadata (đặc biệt hiệu lực + quyền truy cập).
- *Vì sao có 5% kiểm tay:* compliance phụ thuộc hoàn toàn vào metadata đúng — đây là **điểm yếu chí mạng**, nên phải đo độ tin cậy của khâu gắn nhãn.

**③ Sampling** — `Topic-stratified train/dev/test · Forget↔retain (matched) · Regime split (validity) · Scenarios A/B/C`
- *Topic-stratified split:* chia train/dev/test **cân theo chủ đề** để không lệch.
- *Forget↔retain (matched):* tạo cặp **tập-cần-quên** và **tập-cần-giữ** *tương đương về chủ đề/kích thước* → đo unlearning công bằng (quên cái cần quên mà KHÔNG hại cái cần giữ).
- *Regime split (validity):* chia theo `[t_start,t_end]` để dựng **"trước/sau khi luật sửa"** → đo trả lời đúng theo mốc thời gian.
- *Scenario A/B/C:* 3 kịch bản — **A** = nạp luật mới (knowledge update); **B** = ca unlearning (luật bãi bỏ/dữ liệu cá nhân); **C** = stress selective memory (kho phình to).
- *Vì sao thiết kế mẫu cầu kỳ vậy:* mỗi cách chia phục vụ **một giả thuyết/rủi ro cụ thể**, không phải chia ngẫu nhiên cho có.

**④ Analysis** — `Ablation ladder vs static RAG · Metric groups · Test H1–H4 · Error analysis (syllogism)`
- *Ablation ladder:* bật/tắt từng mô-đun (static RAG → +selective → +unlearning) để **cô lập đóng góp** từng phần.
- *Metric groups:* 5 nhóm — retrieval/accuracy · forgetting (FGT/BWT) · compliance · unlearning · cost.
- *Test H1–H4:* gắn số đo vào 4 giả thuyết (H1 CPT quên · H2 RAG né quên · H3 selective cứu recall · H4 trần lập luận).
- *Error analysis (syllogism):* soi kỹ ca tam đoạn luận sai — điểm yếu đã biết của model nhỏ.
- *Vì sao Analysis ở cuối:* toàn bộ pipeline tồn tại để **phục vụ kiểm định H1–H4**; đặt cuối nhấn mạnh "thu thập → cuối cùng để chứng minh giả thuyết".

---

## Terms
**5. KÝ HIỆU TOÁN — đi sâu**

### Tuple một đơn vị tri thức: `u_i = ⟨e_i, v_i, [t_start,t_end], c_i, ρ_i, H_i, acl_i⟩`
| Ký hiệu | Là gì (cụ thể) | Dùng để |
|---|---|---|
| `e_i ∈ ℝ^D` | vector embedding của nội dung đơn vị | tìm theo nghĩa (`sim`) |
| `v_i ∈ {0,1}` | 1 = còn hiệu lực, 0 = bãi bỏ/thay | compliance hard-gate; temporal unlearning |
| `[t_start, t_end]` | ngày hiệu lực → ngày hết hiệu lực | truy vấn theo mốc `t_q` |
| `c_i ∈ [0,1]` | độ trung tâm trong đồ thị dẫn chiếu | thành phần của importance `I` |
| `ρ_i ∈ [0,1]` | trọng số theo thứ bậc (Hiến pháp>Luật>NĐ>TT) & rủi ro | thành phần của `I` |
| `H_i` | tập timestamp các lần đơn vị được dùng | cơ chế **decay** (ít dùng → hạ hạng) |
| `acl_i` | quyền truy cập (public/restricted/sealed) | chặn rò rỉ vượt quyền |

### Các toán tử/chính sách
- **`sim(q, e_i)`** — cosine giữa query và đơn vị. Cao = giống nghĩa.
- **`I_i` (importance)** — điểm tổng hợp từ `c_i`, `ρ_i`, hiệu lực, lịch sử `H_i`. *Quyết định thứ hạng trong tập hợp lệ.*
- **`β`** — hệ số trong khoá xếp hạng `sim(q,e_i) + β·I_i`: β nhỏ → ưu tiên giống nghĩa; β lớn → ưu tiên quan trọng pháp lý.
- **`P` (Filter Policy)** — hàm lọc trên **tập tài liệu**: bỏ `v=0`, vượt `acl`, hết hạn theo `t_q`. *Đây là "unlearning đầu vào".*
- **`Q` (Prompt Constraints)** — ràng buộc trên **đầu ra**: cấm dùng thông tin ngoài context, cấm nêu nội dung đã unlearn. *Đây là "unlearning đầu ra".*
- **`top-N` vs `top-K`** — N (rộng, ở Retrieve) để phủ đủ; K (hẹp, đưa vào LLM) để gọn context. Selective memory là cái thu N→K mà giữ đúng.

### Chỉ số đo quên
- **`BWT` (Backward Transfer)** — sau khi học task/luật mới, perf trên task cũ **tăng hay giảm** bao nhiêu. **Âm mạnh = quên thảm khốc** (lõi H1). RAG kỳ vọng `BWT ≈ 0` (H2).
- **`FGT` (Forgetting)** — mức tụt năng lực **chung** (benchmark tổng quát) sau khi nạp luật.

---

## Chot
**6. Một câu chốt cho mỗi hình (để nhớ)**
- **3.1 =** *"Hệ thống gồm gì, đóng băng ở đâu"* — kiến trúc tĩnh, offline↔online.
- **3.2 =** *"Một câu hỏi chạy ra sao + hệ tự làm mới thế nào"* — vận hành động, 6 bước + vòng cập nhật.
- **3.3 =** *"Làm thí nghiệm thế nào để chứng minh H1–H4"* — phương pháp dữ liệu.
