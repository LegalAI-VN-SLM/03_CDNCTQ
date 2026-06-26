---
updated:
  - 2026-06-26T22:11:05+07:00
  - 2026-06-26T21:56:29+07:00
  - 2026-06-26T21:40:59+07:00
  - 2026-06-26T21:21:29+07:00
  - 2026-06-26T21:17:16+07:00
---
# KỊCH BẢN THUYẾT TRÌNH BẢO VỆ ĐỀ CƯƠNG — DECK v2 (47 slide)
**Đề tài:** Học liên tục & Quên thảm khốc trong Mô hình Ngôn ngữ cho Pháp luật Việt Nam
**Tác giả:** Hoàng Đình Quý Vũ · GVHD: PGS.TS. Lê Anh Cường
**Hội đồng:** Chuyên đề Nghiên cứu Tổng quan, Khoa Công nghệ Thông tin, Trường Đại học Tôn Đức Thắng (2026)

> Mỗi slide: **🖥 Hiển thị** → **🔍 Giải thích** → **📚 Tham khảo** → **🎤 Lời thoại** → **➡ Chuyển slide**.
> **📎 Phụ lục:** slide chính nào có slide giải thích sâu hơn (sau Cảm ơn) sẽ có dòng này — *nhảy tới đúng số slide khi hội đồng hỏi*.
> **Cấu trúc:** Slide 1–24 trình bày · 25 Cảm ơn · **26–47 phụ lục** (bung khi hỏi).

---

## PHẦN 1 — GIỚI THIỆU (Slide 1–7)

---

### Slide 1 — Tiêu đề

**🖥 Hiển thị:** Tên đề tài · badge "Chuyên đề Nghiên cứu Tổng quan · Khoa CNTT · ĐH Tôn Đức Thắng · 2026" · HV + GVHD.

**🔍 Giải thích:** Tên đề tài nêu đủ 2 từ khóa lõi — *Continual Learning* (Học liên tục) và *Catastrophic Forgetting* (Quên thảm khốc) — và miền áp dụng *Pháp luật Việt Nam*.

**📚 Tham khảo:** —

**🎤 Lời thoại:** *"Kính thưa Hội đồng, em là Hoàng Đình Quý Vũ. Hôm nay em xin trình bày đề cương đề tài 'Học liên tục và Quên thảm khốc trong Mô hình Ngôn ngữ cho Pháp luật Việt Nam', dưới hướng dẫn của PGS.TS. Lê Anh Cường."*

**➡ Chuyển slide:** *"Trước hết, vì sao mô hình ngôn ngữ dễ lỗi thời trong môi trường pháp lý."*

---

### Slide 2 — Mục lục

**🖥 Hiển thị:** 8 phần (2 cột); phần 4 "Nội dung & Phương pháp" tô cam = trọng tâm.

**🔍 Giải thích:** Khung toàn bài chia làm 8 phần. Nhấn mạnh phần 4 là trọng tâm của buổi báo cáo đề cương, có phần phụ lục hỗ trợ phía sau.

**📚 Tham khảo:** —

**🎤 Lời thoại:** *"Bài trình bày gồm tám phần: từ bối cảnh và bài toán, qua mục tiêu–giả thuyết và lược khảo, trọng tâm là phần bốn về nội dung và phương pháp đề xuất, rồi tới giới hạn, kết quả mong đợi, kinh phí–kế hoạch, và cuối cùng là kết luận kèm phụ lục sơ đồ."*

**➡ Chuyển slide:** *"Trước hết, vì sao mô hình ngôn ngữ dễ lỗi thời trong môi trường pháp lý."*

---

### Slide 3 — Bối cảnh (Why)

**🖥 Hiển thị:** 3 gạch đầu dòng + trích Le 2025, Nguyen 2025.

**🔍 Giải thích:** LLM dùng cho hỏi-đáp/tra án lệ; luật *non-stationary* (sửa/thay/niêm phong); model trọng số tĩnh ⇒ tư vấn lỗi thời.

**📚 Tham khảo:** Bối cảnh dữ liệu luật VN lấy từ Le 2025 (LegalSLM) và Nguyen 2025 (VLQA).

**🎤 Lời thoại:** *"LLM đang được dùng rộng trong pháp lý, nhưng tri thức luật có tính không tĩnh: liên tục sửa đổi, thay thế, niêm phong. Một mô hình trọng số tĩnh sẽ nhanh lỗi thời, và tư vấn theo luật hết hiệu lực gây hậu quả nghiêm trọng."*

**➡ Chuyển slide:** *"Ngoài việc phải cập nhật, hệ thống còn gặp một thách thức thứ hai mang tính đối nghịch."*

---

### Slide 4 — Vấn đề (What): Bài toán kép

**🖥 Hiển thị:** 2 nhánh (cập nhật liên tục · quên có chủ đích) + callout hybrid.

**🔍 Giải thích:** Vừa nạp luật mới, vừa phải quên (luật hết hiệu lực/dữ liệu cá nhân); dạy thẳng trọng số ⇒ quên thảm khốc + khó gỡ.

**📚 Tham khảo:** —

**🎤 Lời thoại:** *"Bài toán kép: vừa cập nhật liên tục, vừa phải quên có chủ đích. Hai yêu cầu này ngược với học máy truyền thống — dạy thẳng vào trọng số thì quên thảm khốc, mà gỡ tri thức khỏi trọng số lại rất khó."*

**➡ Chuyển slide:** *"Câu hỏi cốt lõi: tri thức nên nằm ở đâu để cập nhật và quên dễ nhất?"*

---

### Slide 5 — Khái niệm gốc: Parametric vs Non-parametric

**🖥 Hiển thị:** Panel đỏ (parametric) vs xanh (non-parametric).

**🔍 Giải thích:** Tri thức trong trọng số (parametric) thì sửa khó, quên dễ; ngoài trọng số (non-parametric/RAG) thì sửa tức thì, không quên.

**📚 Tham khảo:** Phân loại trong/ngoài trọng số lấy theo Zheng 2024.

**🎤 Lời thoại:** *"Cả đề tài xoay quanh một câu: tri thức nằm ĐÂU. Nếu trong trọng số (parametric) thì sửa khó, quên dễ. Nếu ở ngoài (non-parametric, RAG) thì sửa tức thì, không quên. Em chọn đưa facts ra ngoài, đóng băng base, và chỉ dùng LoRA cho hành vi."*

**➡ Chuyển slide:** *"Từ ý tưởng gốc này, em đặt ra ba mục tiêu cụ thể."*

---

### Slide 6 — Mục tiêu nghiên cứu

**🖥 Hiển thị:** Mục tiêu chung + 3 mục tiêu (Selective · Forgetting study · Benchmark).

**🔍 Giải thích:** MT1 xây kho RAG chọn lọc, MT2 đo quên khi nạp liên tục và MT3 đánh giá trần lập luận RAG.

**📚 Tham khảo:** —

**🎤 Lời thoại:** *"Mục tiêu tổng quát: chứng minh RAG phi-tham số giữ tri thức luật khi nạp liên tục mà không quên, đo trên QA/NLI/Syllogism. Cụ thể qua ba mục tiêu trên màn hình."*

**➡ Chuyển slide:** *"Ba mục tiêu này được phát biểu thành các giả thuyết bác bỏ được."*

---

### Slide 7 — Giả thuyết H1–H4

**🖥 Hiển thị:** Bảng 4 hàng H1–H4 × (nội dung · đo bằng).

**🔍 Giải thích:** H1 CPT quên · H2 RAG né (BWT≈0) · H3 selective cứu recall · H4 trần lập luận.

**📚 Tham khảo:** Chỉ số Backward Transfer theo chuẩn survey CL (Wu 2024).

**🎤 Lời thoại:** *"Bốn giả thuyết, mỗi cái gắn một chỉ số đo được nên đều bác bỏ được bằng số. H3 (in đậm cam) là chỗ selective memory có lý do tồn tại; H4 là phát hiện về trần lập luận."*

**➡ Chuyển slide:** *"Để có cơ sở, em lược khảo bức tranh nghiên cứu 2024–2026."*

---

## PHẦN 2 — LƯỢC KHẢO TÀI LIỆU (Slide 8–14)

---

### Slide 8 — Lược khảo (1/6): Survey arc

**🖥 Hiển thị:** Sơ đồ CPT→CIT→CA + bảng 5 survey.

**🔍 Giải thích:** Vòng đời 3 giai đoạn; rủi ro quên xuyên giai đoạn; mỗi survey một đóng góp.

**📚 Tham khảo:** 5 khảo sát: Wu/Shi/Zheng 2024, Guo 2025, Zheng 2026 (agent).

**🎤 Lời thoại:** *"Cộng đồng đồng thuận chia vòng đời huấn luyện thành CPT–CIT–CA, và rủi ro lớn là quên xuyên giai đoạn. Mấu chốt là đánh đổi ổn định–linh hoạt: học mới càng sâu thì quên cũ càng nhiều."*

**📎 Phụ lục:** slide **26** (internal→external chi tiết) · slide **9** ngay sau (Nhóm 1 sâu).

**➡ Chuyển slide:** *"Đi sâu Nhóm 1 một chút."*

---

### Slide 9 — Lược khảo: Nhóm 1 chi tiết

**🖥 Hiển thị:** 3-stage + 4 trường phái chống quên (Rehearsal/Regularization/Architecture/External Memory).

**🔍 Giải thích:** Nhấn trường phái **External Memory (RAG)** = hướng đề tài; xu hướng 2026 lên Agent + LUFY.

**📚 Tham khảo:** Wu/Shi/Zheng/Guo; LUFY (Sumida 2025).

**🎤 Lời thoại:** *"Bên cạnh ba giai đoạn CPT-CIT-CA, ta có bốn trường phái chống quên chính là Rehearsal, Regularization, Architecture và External Memory. Đề tài của em chọn trường phái External Memory, cụ thể là RAG phi tham số. Em lấy cảm hứng từ cơ chế bộ nhớ chọn lọc của LUFY năm 2025."*

**➡ Chuyển slide:** *"Nếu vẫn sửa trọng số thì quên ra sao?"*

---

### Slide 10 — Lược khảo (2/6): Quên là quy luật

**🖥 Hiển thị:** Bảng replay + Inverse Linear Law + loss landscape.

**🔍 Giải thích:** Học mới càng sâu quên cũ càng nhiều; replay 0.5% cứu gần hết; cực tiểu sắc vs phẳng.

**📚 Tham khảo:** Scialom 2022 (replay); Kalajdzievski 2024 (inverse linear law); Li 2024 (loss landscape/SAM).

**🎤 Lời thoại:** *"Quên là quy luật, không phải lỗi tinh chỉnh được: học mới càng sâu, quên cũ càng nhiều. Điểm sáng là chỉ cần ôn 0.5% dữ liệu cũ đã giữ được hơn nửa năng lực — một ổn định hóa cực rẻ."*

**📎 Phụ lục:** slide **27** (D-CPT scaling chi tiết).

**➡ Chuyển slide:** *"PEFT/LoRA kiểm soát quên tới đâu?"*

---

### Slide 11 — Lược khảo (3/6): PEFT / LoRA

**🖥 Hiển thị:** Bảng FG% Full-FT vs LoRA vs O-LoRA.

**🔍 Giải thích:** LoRA thường vẫn quên >40%; O-LoRA giảm ~40 lần; nhưng facts cần rank ~2000 nên LoRA chỉ hợp học hành vi.

**📚 Tham khảo:** Biderman 2024 (learns less, forgets less); Wang 2023 (O-LoRA); Yang 2024 (N-LoRA).

**🎤 Lời thoại:** *"Slide này trình bày về độ dốc gây quên (Forgetting Gradient - FG%). Khi tinh chỉnh toàn bộ hoặc dùng LoRA thông thường, mô hình vẫn bị quên nghiêm trọng, lên tới trên 40%. Ngược lại, phương pháp O-LoRA nhờ học trực giao đã giảm quên đến 40 lần, đưa tỷ lệ quên về dưới 1%. Tuy nhiên, vì độ phức tạp hay hạng nội tại (intrinsic rank) của tri thức pháp luật lên tới khoảng 2000, các adapter LoRA thông thường với rank nhỏ dưới 256 chỉ học được cách lập luận (tức là học hành vi) chứ không thể nhét dữ kiện pháp luật mới vào trọng số. Đây là cơ sở để em quyết định giữ tri thức ở kho ngoài RAG và chỉ dùng LoRA làm công cụ học hành vi."*

**📎 Phụ lục:** slide **26** (internal→external) · slide **28** (RAG memory/unlearning).

**➡ Chuyển slide:** *"Có một hướng bán-tham số đáng để đối chiếu trực tiếp: ReGrad."*

---

### Slide 12 — Lược khảo (4/6): ReGrad (đối chiếu)

**🖥 Hiển thị:** Bảng CPT vs RAG vs ReGrad (General/Law/Drift).

**🔍 Giải thích:** 
- **Cơ chế ReGrad (Retrievable Gradients - 2026):** Thay vì tinh chỉnh trực tiếp làm thay đổi vĩnh viễn trọng số (CPT), ReGrad lưu các gradient học được từ tri thức mới vào một ngân hàng gradient ngoài (gradient bank). Khi có câu hỏi, hệ thống truy xuất gradient liên quan và áp dụng động (on-the-fly) vào trọng số để mô hình sinh câu trả lời, sau đó hoàn tác.
- **Đánh đổi:** Đạt độ chính xác cao trên luật (84.87%) mà không bị trôi dạt trọng số nền (zero weight drift), nhưng chi phí tính toán cực lớn, độ trễ (latency) cao khi suy luận và rất khó thực hiện unlearning (xóa tri thức).

**📚 Tham khảo:** Su 2026 (Retrievable Gradients) — cũng là khuôn Bảng B cho thí nghiệm của em.

**🎤 Lời thoại:** *"CPT làm năng lực chung sụp còn 25.67% — đúng quên thảm khốc. ReGrad đạt luật 84.87% không trôi trọng số, nhưng phải duy trì ngân hàng gradient khổng lồ và độ trễ cao. Đó là lý do em chọn RAG: rẻ hơn, cập nhật tức thì, quên/gỡ dễ."*

**📎 Phụ lục:** slide **28** (RAG memory) · slide **35** (continual pipeline) · Q&A slide **43** (sao loại ReGrad).

**➡ Chuyển slide:** *"Dữ liệu luật VN sẵn sàng tới đâu?"*

---

### Slide 13 — Lược khảo (5/6): Dữ liệu luật VN

**🖥 Hiển thị:** ViLegalNLI + LegalSLM + callout.

**🔍 Giải thích:** NLI/QA ~0.95 nhưng tam đoạn luận chỉ ~0.5; CPT giúp 4B nhưng hại 1.7B.

**📚 Tham khảo:** VLQA (Nguyen 2025), ViLegalNLI (Duong 2026), LegalSLM (Le 2025).

**🎤 Lời thoại:** *"Truy xuất và phân loại đã tốt (~0.95), nhưng tam đoạn luận chỉ ~0.5, và CPT giúp model lớn nhưng hại model nhỏ. Khoảng trống lập luận này chính là hook H4 của em."*

**📎 Phụ lục:** slide **40** (quy mô dữ liệu thạc sĩ).

**➡ Chuyển slide:** *"Bốn khoảng trống rút ra dẫn thẳng vào phương pháp."*

---

### Slide 14 — Lược khảo (6/6): 4 khoảng trống

**🖥 Hiển thị:** 4 card khoảng trống.

**🔍 Giải thích:** 1) Low-resource VN chưa nghiên cứu CL đủ. 2) RAG chống quên liên tục chưa đo. 3) Selective legal memory chưa ai làm. 4) Thiếu benchmark legal-CL VN.

**📚 Tham khảo:** —

**🎤 Lời thoại:** *"Bốn khoảng trống này là bốn lý do tồn tại của đề tài, và chúng dẫn thẳng vào framework hybrid RAG-centric ở phần phương pháp."*

**➡ Chuyển slide:** *"Trước khi vào phương pháp, em tóm tắt nội dung nghiên cứu thành ba gói công việc."*

---

## PHẦN 3 — NỘI DUNG & PHƯƠNG PHÁP (Slide 15–19)

---

### Slide 15 — RAG cho vào đâu trong pipeline

**🖥 Hiển thị:** Khung RAG (Retrieve→Augment) + 2 hộp cam (Unlearning P · Selective+Compliance) chèn giữa + LLM frozen+LoRA.

**🔍 Giải thích:** Đề tài KHÔNG sửa RAG, mà biến đổi tập kết quả giữa Retrieve↔Augment.

**📚 Tham khảo:** —

**🎤 Lời thoại:** *"Em không sửa RAG. Em biến đổi tập kết quả ở khoảng giữa Retrieve và Augment — chèn lọc quên và chọn lọc/tuân thủ vào đó. LLM vẫn đóng băng."*

**📎 Phụ lục:** slide **30** (RAG 3 bước cơ bản) · slide **36** (Fig 3.2 vận hành 6 bước).

**➡ Chuyển slide:** *"Nhìn tổng thể, đây là kiến trúc hybrid tách facts và hành vi."*

---

### Slide 16 — Kiến trúc Hybrid

**🖥 Hiển thị:** Base frozen ở giữa · FACTS→RAG · HÀNH VI→LoRA.

**🔍 Giải thích:** Đóng băng mô hình nền để tránh quên năng lực chung, lưu tri thức sự kiện ở kho ngoài RAG, dùng LoRA cho hành vi lập luận.

**📚 Tham khảo:** —

**🎤 Lời thoại:** *"Ba bảo đảm: base frozen ⇒ không quên chung; facts ở RAG ⇒ cập nhật/unlearn tức thì; hành vi ở LoRA ⇒ lập luận đúng văn phong luật."*

**📎 Phụ lục:** slide **38** (Fig 4.1 WP).

**➡ Chuyển slide:** *"Để làm được, mỗi điều luật phải là một đơn vị tri thức có metadata đặc thù."*

---

### Slide 17 — Đơn vị tri thức & metadata

**🖥 Hiển thị:** Tuple u = ⟨e, v, [t], c, ρ, H, acl⟩.

**🔍 Giải thích:** 
- **Khái niệm Đơn vị Tri thức ($u_i$):** Là một Điều hoặc Khoản luật nguyên tử được trích xuất từ văn bản thô. RAG thông thường chỉ lưu text và vector nhúng, không đủ đáp ứng tính tuân thủ pháp lý nghiêm ngặt.
- **Ý nghĩa của bộ tham số (Metadata Tuple):**
  - **$e$ (Embedding):** Vector nhúng đặc trưng cho nội dung ngữ nghĩa để tra cứu.
  - **$v$ (Validity):** Trạng thái hiệu lực (1 = còn hiệu lực, 0 = đã bãi bỏ). Giúp unlearn lập tức bằng cách ẩn/xóa.
  - **$[t]$ (Temporal Regime):** Khoảng thời gian có hiệu lực $[t_{start}, t_{end}]$, đảm bảo tra cứu đúng luật tại thời điểm xảy ra vụ việc.
  - **$c$ (Citation Centrality):** Độ quan trọng cấu trúc, tính toán từ đồ thị dẫn chiếu chéo giữa các văn bản luật.
  - **$\rho$ (Risk/Hierarchy):** Thứ bậc nguồn luật (Hiến pháp > Luật > Nghị định > Thông tư) để giải quyết xung đột luật.
  - **$H$ (Access History):** Lịch sử truy vấn dùng để tính toán hàm suy hao (decay) bộ nhớ.
  - **$acl$ (Access Control):** Phân quyền truy cập (công khai, nội bộ, án mật).

**📚 Tham khảo:** —

**🎤 Lời thoại:** *"Mỗi điều luật u_i (u y) được biểu diễn dưới dạng bộ các tham số gồm: vector nhúng e, trạng thái hiệu lực v, khoảng thời gian t, độ trung tâm trích dẫn c, mức độ rủi ro rho (rô), lịch sử truy cập H, và quyền truy cập acl. Chính siêu dữ liệu đặc thù luật này phân biệt kho của em với RAG thông thường."*

**➡ Chuyển slide:** *"Trên nền metadata đó là module bộ nhớ chọn lọc."*

---

### Slide 18 — Selective Memory

**🖥 Hiển thị:** Importance + decay + compliance hard-gate.

**🔍 Giải thích:** 
- **Vấn đề "quên kiểu RAG" (Retrieval Interference):** Khi nạp tri thức liên tục, kho dữ liệu phình to khiến bộ truy xuất bị nhiễu. Điều luật cũ nhưng quan trọng dễ bị "chôn vùi" dưới các tài liệu mới có độ tương đồng ngữ nghĩa ảo cao, làm tụt chỉ số Recall@K.
- **Giải pháp Selective Memory (3 trụ cột):**
  1. **Importance (Điểm quan trọng):** Kết hợp độ trung tâm trích dẫn (citation centrality), thứ bậc hiệu lực (Hiến pháp > Luật > Nghị định) để lọc nhiễu.
  2. **Decay (Suy hao nhận thức):** Dùng đường quên Ebbinghaus giảm dần điểm ưu tiên của các tài liệu ít được tra cứu, giúp thu gọn kích thước index hoạt động (active index) $\ge 30\%$.
  3. **Compliance Hard-Gate (Cổng cứng):** Cờ hiệu lực $v=1$ đảm bảo các điều luật còn hiệu lực luôn được giữ lại trong prompt gửi cho LLM, không bao giờ bị rớt ra ngoài dù điểm tương đồng thấp.

**📚 Tham khảo:** Cảm hứng LUFY (Sumida 2025).

**🎤 Lời thoại:** *"Điểm quan trọng I (ai) được tính bằng tổng w một nhân c mũ (ĉ) cộng w hai nhân v cộng w ba nhân rho (rô) cộng w bốn nhân hàm suy hao g(H, t). Trong đó, hàm g(H, t) tính theo đường quên Ebbinghaus bằng tổng các e mũ trừ lambda nhân với t trừ tau. Khi xếp hạng lại, điểm S (ét) bằng độ tương đồng sim cộng beta nhân với I (ai). Với cơ chế này, 'quên' chỉ là giảm nhiễu xếp hạng khi kho phình to, chứ không mất độ bao phủ vì luật còn hiệu lực luôn được giữ qua cổng cứng. Đây là chỗ selective memory cứu RAG khỏi 'quên kiểu RAG'."*

**📎 Phụ lục:** slide **31** (công thức I + Algorithm) · slide **32–33** (unlearning P+Q).

**➡ Chuyển slide:** *"Và đây là thí nghiệm chính."*

---

### Slide 19 — ⭐ Thí nghiệm Forgetting (trục lõi)

**🖥 Hiển thị:** Đường cong forgetting + 4 nhánh (CPT/ReGrad/vanilla/selective) × 3 task.

**🔍 Giải thích:** Nạp liên tục theo snapshot, đo Backward Transfer; CPT sụp (H1), RAG ổn (H2).

**📚 Tham khảo:** Khuôn lấy từ Bảng B của ReGrad (Su 2026) nhưng phi-tham số.

**🎤 Lời thoại:** *"Thí nghiệm chính: nạp tri thức liên tục, so bốn nhóm trên ba tác vụ. Tinh chỉnh CPT sụp như quy luật; RAG né quên nhưng kho phình thì nhiễu, nên cơ chế chọn lọc selective cứu lại hiệu năng."*

**📎 Phụ lục:** slide **39** (benchmark 5 lớp) · slide **40** (quy mô data) · slide **34** (phương pháp luận dữ liệu).

**➡ Chuyển slide:** *"Hành vi lập luận được dạy qua LoRA."*

---

## PHẦN 4 — KẾT QUẢ, KẾ HOẠCH & KẾT LUẬN (Slide 20–24)

---

### Slide 20 — LoRA + mô hình nhỏ

**🖥 Hiển thị:** Phân vai RAG/LoRA + SVD rank + config 1–4B.

**🔍 Giải thích:** LoRA rank thấp thích hợp cho hành vi lập luận thay vì nhồi nhét sự kiện. Facts lưu ở ngoài giúp giảm quy mô mô hình xuống còn 1-4B tham số.

**📚 Tham khảo:** —

**🎤 Lời thoại:** *"LoRA dạy cách lập luận pháp lý chứ không nhồi luật vào trọng số. Em cấu hình LoRA với rank r (e-rờ) bằng 256 và alpha bằng 512 để học hành vi. Vì facts được đưa ra ngoài RAG, mô hình nhỏ từ 1 đến 4 tỷ tham số là đủ và đúng tinh thần tối ưu tài nguyên."*

**📎 Phụ lục:** slide **26–28** (PEFT/LoRA lit chi tiết).

**➡ Chuyển slide:** *"Em nói rõ giới hạn phạm vi để không over-claim."*

---

### Slide 21 — Giới hạn phạm vi

**🖥 Hiển thị:** Nội dung/không gian/thời gian + đánh đổi.

**🔍 Giải thích:** Giới hạn trong 3 task và dữ liệu luật VN (tập trung lao động & thuế). Đánh đổi: năng lực suy luận bị chặn bởi mô hình nền.

**📚 Tham khảo:** —

**🎤 Lời thoại:** *"Em giới hạn rõ: 3 task, luật VN, kịch bản test lấy từ sub-domain biến động nhiều. Đánh đổi thật là reasoning bị chặn bởi base — em nêu thẳng chứ không giấu."*

**➡ Chuyển slide:** *"Kết quả mong đợi gắn với các tiêu chí số."*

---

### Slide 22 — Kết quả mong đợi (R1–R3)

**🖥 Hiển thị:** Bảng R1/R2/R3 + callout sản phẩm.

**🔍 Giải thích:** R1 là đường cong forgetting. R2 là so sánh selective vs vanilla RAG. R3 chứng minh trần lập luận.

**📚 Tham khảo:** —

**🎤 Lời thoại:** *"Kết quả mong đợi gồm: đường quên của RAG ổn định so với CPT bị sụp. Thứ hai, selective memory giữ chỉ số Recall tại K (Recall@K) lệch không quá cộng trừ 2%, trong khi giảm dung lượng index tối thiểu 30%. Thứ ba, độ chênh lệch delta (Δ) giữa các nhiệm vụ chứng minh trần lập luận giảm dần từ QA đến NLI và Syllogism. Sản phẩm ở quy mô sinh viên là luận văn thạc sĩ và mã nguồn mở."*

**➡ Chuyển slide:** *"Ai sẽ hưởng lợi từ kết quả này?"*

---

### Slide 23 — Kinh phí & Kế hoạch 6 tháng

**🖥 Hiển thị:** Bảng chi phí + 8 milestone + rủi ro.

**🔍 Giải thích:** Tổng kinh phí khoảng 20 triệu cho tính toán và dữ liệu. Lộ trình triển khai rõ ràng qua 8 mốc quan trọng trong 6 tháng.

**📚 Tham khảo:** —

**🎤 Lời thoại:** *"Quy mô đồ án sinh viên: tổng khoảng 20 triệu, lương bằng 0, dùng Colab và RunPod thay vì A100. Vì base đóng băng nên chi phí chính chỉ là index một lần. Rủi ro lớn nhất là metadata hiệu lực, đã có biện pháp."*

**📎 Phụ lục:** slide **44** (Q&A metadata) · slide **40** (quy mô data).

**➡ Chuyển slide:** *"Tóm lại toàn bộ đề tài trong ba ý."*

---

### Slide 24 — Kết luận

**🖥 Hiển thị:** 3 dải kết luận.

**🔍 Giải thích:** Chốt ba đóng góp lớn: kiến trúc hybrid chống quên, ba kết quả thực nghiệm kiểm chứng giả thuyết, và xác định trần lập luận.

**📚 Tham khảo:** —

**🎤 Lời thoại:** *"Ba ý chốt: kiến trúc hybrid chống quên thảm khốc; ba kết quả đo được; và phát hiện rằng retrieval cứu QA/NLI nhưng chạm trần ở lập luận. Em thừa nhận giới hạn để bảo vệ vững."*

**➡ Chuyển slide:** *"Đây là các tài liệu tham khảo chính."*

---

### Slide 25 — 🙏 Cảm ơn

**🖥 Hiển thị:** "Cảm ơn Hội đồng đã lắng nghe!" + email + sẵn sàng nhận câu hỏi.

**🔍 Giải thích:** Kết thúc phần thuyết trình chính và chuyển sang phiên hỏi đáp.

**📚 Tham khảo:** —

**🎤 Lời thoại:** *"Em xin cảm ơn Hội đồng và sẵn sàng tiếp nhận câu hỏi, phản biện."*

---

## PHỤ LỤC — BUNG KHI HỘI ĐỒNG HỎI (Slide 26–46)

> *Cách dùng: nghe câu hỏi → nhảy tới đúng số slide bên dưới.*

---

### Slide 26 — (PL Lược khảo) Internal → External

**🖥 Hiển thị:** Sơ đồ 2 nhánh Internal (parametric) vs External (non-parametric, highlight RAG) + 3 dịch chuyển.

**🔍 Giải thích:** Nhánh Internal gồm CPT/CFT, LoRA (tri thức trong trọng số). Nhánh External gồm RAG (facts), Tool/API (tri thức ngoài). Dịch chuyển tri thức ra ngoài là xu hướng thiết yếu để giữ mô hình gọn nhẹ.

**📚 Tham khảo:** Zheng và cộng sự 2024.

**🎤 Lời thoại:** *"Zheng 2024 chỉ ra xu hướng dịch từ nhồi facts vào trọng số sang lưu ở bộ nhớ ngoài. Trong miền cần cập nhật tức thì như luật, đây là hướng đúng — và là nền cho thiết kế của em."*

---

### Slide 27 — (PL Lược khảo) CPT Scaling + D-CPT Law

**🖥 Hiển thị:** Công thức D-CPT Law: $L(N, D, r) = E + A N^{-\alpha} + B \cdot r^{\eta} D^{-\beta} + C (r + \epsilon)^{-\gamma}$ + Bảng tỷ lệ tối ưu (Chemistry, Law) + ghi chú R² > 0.97 + cột Stability Gap.

**🔍 Giải thích:** Công thức ước lượng loss (L) khi tinh chỉnh tiếp tục (CPT) với tỷ lệ trộn dữ liệu luật (r). Hàng Law dự đoán r = 64% khớp thực tế 0.638. Hiện tượng Stability Gap xuất hiện khi bắt đầu pha học tiếp theo.

**📚 Tham khảo:** Que 2024, Gupta 2023.

**🎤 Lời thoại:** *"Slide này trình bày Công thức D-CPT Law dùng để dự đoán độ lỗi L (lốt) của mô hình dựa trên số tham số N, kích thước dữ liệu D và tỷ lệ trộn dữ liệu chuyên ngành r. Công thức cụ thể là: L(N, D, r) bằng E cộng với A nhân N mũ trừ alpha, cộng B nhân r mũ eta nhân D mũ trừ beta, cộng C nhân mở ngoặc r cộng epsilon đóng ngoặc mũ trừ gamma. Nhờ công thức này, ta dự đoán được tỷ lệ trộn tối ưu để pretrain luật là 64% dữ liệu luật và 36% dữ liệu chung để mô hình ít bị quên nhất, với hệ số xác định R bình phương (R²) cực cao trên không phẩy chín bảy. Dù vậy, trong đề tài này em chọn giải pháp RAG phi tham số để tri thức nằm ở ngoài, tránh hoàn toàn rủi ro này."*

---

### Slide 28 — (PL Lược khảo) RAG memory & Unlearning

**🖥 Hiển thị:** Bảng 3 hàng (Gradient ascent / μ-unlearning / RAG) × 4 cột (ROUGE-L↓ / USR↑ / TPR↓ / Utility).

**🔍 Giải thích:** RAG đạt USR 99.8% và ROUGE-L 0.10 mà không hại năng lực nền (Utility), vượt trội so với GA (40.2%). Bộ nhớ chọn lọc (selective memory) lấy cảm hứng từ LUFY (quên 90% lượt ít quan trọng để tăng 17% hiệu suất truy xuất).

**📚 Tham khảo:** Wang 2025 (RAG-based unlearning), LUFY (Sumida 2025).

**🎤 Lời thoại:** *"RAG-unlearning đạt USR chín mươi chín phẩy tám phần trăm và ROUGE-L không phẩy mười mà không hại năng lực nền, trong khi sửa trọng số vừa quên kém vừa tàn phá utility. LUFY là nguồn cảm hứng cho selective memory của em."*

---

### Slide 29 — Nội dung 3 Work Package

**🖥 Hiển thị:** Bảng 3 hàng WP1 (Selective RAG), WP2 (Forgetting Study), WP3 (Benchmarking) × (Nội dung · MT tương ứng).

**🔍 Giải thích:** WP1 xây dựng nền tảng bộ nhớ chọn lọc và cổng tuân thủ. WP2 chạy thực nghiệm nạp liên tục qua 4 nhánh × 3 task. WP3 phân tích kết quả, tìm trần lập luận và đối chiếu chi phí.

**📚 Tham khảo:** —

**🎤 Lời thoại:** *"Nội dung nghiên cứu được chia làm ba gói công việc chính. Gói công việc một là xây dựng kho dữ liệu RAG có chọn lọc để làm giá đỡ tri thức, tích hợp đồ thị trích dẫn và cổng hiệu lực. Gói công việc hai là thiết lập môi trường thực nghiệm nạp tri thức liên tục để đo lường độ quên trên ba tác vụ cốt lõi ở cả bốn phương pháp so sánh. Gói công việc ba là phân tích định lượng kết quả thực nghiệm để xác định giới hạn lập luận của RAG và đối chiếu hiệu năng chi phí với baseline ReGrad."*

---

### Slide 30 — RAG là gì (3 bước)

**🖥 Hiển thị:** Sơ đồ 3 bước: Câu hỏi → ① Retrieve (Truy xuất) → ② Augment (Tăng cường) → ③ Generate (Sinh), có KB ở trên.

**🔍 Giải thích:** ① Retrieve: tìm top-N tài liệu liên quan ngữ nghĩa. ② Augment: chèn ngữ cảnh vào prompt. ③ Generate: LLM sinh câu trả lời có trích dẫn từ ngữ cảnh.

**📚 Tham khảo:** Khái niệm chuẩn ngành.

**🎤 Lời thoại:** *"RAG gồm ba bước: tra cứu, chèn ngữ cảnh, rồi sinh câu trả lời. Ví von như luật sư tra tủ hồ sơ rồi mới tư vấn — nhờ vậy đổi luật chỉ là đổi hồ sơ, không cần dạy lại não."*

---

### Slide 31 — Selective Memory: công thức đầy đủ

**🖥 Hiển thị:** Công thức Importance $I = w_1 \hat{c} + w_2 v + w_3 \rho + w_4 g(H, t)$ và đường quên Ebbinghaus $g(H, t) = \sum e^{-\lambda(t-\tau)}$, cùng Algorithm 1.

**🔍 Giải thích:** $I$ được cấu thành từ: độ trung tâm trích dẫn $\hat{c}$, trạng thái hiệu lực $v$, độ rủi ro $\rho$ và hàm suy hao $g(H, t)$ mô phỏng độ quên tự nhiên.

**📚 Tham khảo:** Sumida 2025 (LUFY).

**🎤 Lời thoại:** *"Điểm quan trọng I (ai) được tính bằng tổng w một nhân c mũ (ĉ) cộng w hai nhân v cộng w ba nhân rho (rô) cộng w bốn nhân hàm suy hao g(H, t). Trong đó, hàm g(H, t) tính theo đường quên Ebbinghaus bằng tổng các e mũ trừ lambda nhân với t trừ tau. Khi xếp hạng lại, điểm S (ét) bằng độ tương đồng sim cộng beta nhân với I (ai). Với cơ chế này, 'quên' chỉ là giảm nhiễu xếp hạng khi kho phình to, chứ không mất độ bao phủ vì luật còn hiệu lực luôn được giữ qua cổng cứng. Đây là chỗ selective memory cứu RAG khỏi 'quên kiểu RAG'."*

---

### Slide 32 — Unlearning: xóa ≠ quên

**🖥 Hiển thị:** Sơ đồ: Xóa chunk khỏi KB → LLM vẫn rò rỉ (rò rỉ #1 từ trọng số do pretrain, rò rỉ #2 do suy luận bắc cầu) → kết luận đo TPR.

**🔍 Giải thích:** Xóa index chỉ ẩn tri thức trong RAG. LLM vẫn có thể nhớ lại từ pretrain hoặc tự bắc cầu suy luận. Cần đo lường thực tế thông qua chỉ số TPR@1%FPR để đánh giá mức độ rò rỉ tri thức.

**📚 Tham khảo:** Wang 2025.

**🎤 Lời thoại:** *"Xóa index chỉ là lập trình — không phải đóng góp. Vì mô hình vẫn lộ fact từ trọng số, nên em đo rò rỉ chứ không tin việc chỉ xóa cơ sở dữ liệu. Đây là cách tiếp cận unlearning hành vi, không tuyên bố xóa sạch khỏi trọng số."*

---

### Slide 33 — P+Q & 5 tiêu chí

**🖥 Hiển thị:** 3 chế độ quên (Temporal gating, Access-control, Index deletion) + Công thức P, Q + bảng 5 tiêu chí.

**🔍 Giải thích:** Chính sách P chặn retrieval ở KB. Chính sách Q ràng buộc prompt đầu ra. 5 tiêu chí đánh giá unlearning, nổi bật là Robustness đo bằng TPR@1%FPR dưới tấn công jailbreak.

**📚 Tham khảo:** Wang 2025.

**🎤 Lời thoại:** *"P+Q là phòng thủ kép. Bộ lọc P giới hạn kho tri thức chỉ gồm các đơn vị hợp lệ và thỏa mãn phân quyền. Q là ràng buộc ở prompt để tránh sinh ra dữ liệu cũ. Ta đo độ bền bỉ bằng tỷ lệ dương tính thật TPR tại một phần trăm tỷ lệ dương tính giả FPR dưới tấn công jailbreak."*

---

### Slide 34 — Phương pháp luận dữ liệu + 4 arm

**🖥 Hiển thị:** Quy trình Collection→Sampling→Analysis + bảng so sánh 4 arm.

**🔍 Giải thích:** Quy trình xử lý dữ liệu với kiểm định 5% thủ công. Bảng 4 arm chỉ ra CPT quên thảm khốc, ReGrad đắt đỏ, vanilla RAG bị nhiễu và selective RAG giữ recall ổn định nhất.

**📚 Tham khảo:** Su 2026 (ReGrad).

**🎤 Lời thoại:** *"Thí nghiệm chính: nạp tri thức liên tục, so bốn nhóm trên ba tác vụ. Tinh chỉnh CPT sụp như quy luật; RAG né quên nhưng kho phình thì nhiễu, nên cơ chế chọn lọc selective cứu lại hiệu năng."*

---

### Slide 35 — Continual update pipeline (Fig 3.5)

**🖥 Hiển thị:** Quy trình 4 thao tác chính ở tầng cơ sở tri thức (Knowledge Base): Upsert (thêm mới điều luật), Supersession (đánh dấu v=0 cho luật cũ bị thay thế), Canary (kiểm thử trên tập nhỏ), Rollback (phục hồi phiên bản cũ).

**🔍 Giải thích:** "Continual" (liên tục) được thực thi hoàn toàn ở tầng ngoài (non-parametric), không sửa đổi trọng số mô hình. Nhờ thế, việc cập nhật diễn ra tức thì với độ trễ thấp và có khả năng khôi phục khi gặp sự cố dữ liệu.

**📚 Tham khảo:** —

**🎤 Lời thoại:** *"Quy trình cập nhật liên tục được thực hiện hoàn toàn ở tầng ngoài với bốn thao tác: thêm mới, cập nhật luật thay thế, kiểm tra thử nghiệm và cho phép rollback. Nhờ vậy, tri thức được cập nhật lập tức và an toàn."*

---

### Slide 36 — Fig 3.2 · Sơ đồ vận hành 6 bước

**🖥 Hiển thị:** Sơ đồ 6 bước (2 hàng): ①Query → ②Retrieve → ③Unlearning gate → ④Selective+Compliance → ⑤Reason → ⑥Answer+Audit; KB/offline ở trên, vòng continual-update ở dưới.

**🔍 Giải thích:** Phân tách rõ ràng giữa chính sách P (lọc tài liệu ở khâu truy xuất, bước 3) và chính sách Q (ràng buộc sinh ở khâu prompt, bước 5). LLM đóng băng hoàn toàn.

**📚 Tham khảo:** —

**🎤 Lời thoại:** *"Đây là sơ đồ vận hành chi tiết: một truy vấn đi qua sáu bước; unlearning lọc tài liệu ở bước ba, ràng buộc sinh ở bước năm; vòng dưới là cập nhật liên tục ở tầng kho, không đụng trọng số."*

---

### Slide 37 — Fig 3.3 · Pipeline phương pháp dữ liệu

**🖥 Hiển thị:** 4 chặng Thu thập → Xử lý/Chỉ mục → Lấy mẫu → Phân tích; nhánh nét đứt "LLM tự kiểm trước · spot-check 5% metadata".

**🔍 Giải thích:** Quy trình thu thập và dán nhãn hiệu lực tự động bằng LLM, sau đó được chuyên gia kiểm chứng 5%. Tập lấy mẫu được thiết kế để cô lập và kiểm định trực tiếp các giả thuyết H1-H4.

**📚 Tham khảo:** —

**🎤 Lời thoại:** *"Quy trình dữ liệu gồm thu thập, xử lý–chỉ mục, lấy mẫu và phân tích; metadata được LLM tự kiểm trước rồi spot-check năm phần trăm; tất cả phục vụ kiểm định bốn giả thuyết."*

---

### Slide 38 — Fig 4.1 · Phụ thuộc 3 Work Package

**🖥 Hiển thị:** WP1 Selective RAG → WP2 Forgetting Study (cam, trục lõi) → WP3 Benchmarking; nhãn mũi tên: RAG arm, Forgetting curves, Reranked candidates.

**🔍 Giải thích:** Unlearning hạ xuống mô-đun phụ trong WP1 (temporal gating); WP2 — đo quên khi nạp liên tục — là trục chính.

**📚 Tham khảo:** —

**🎤 Lời thoại:** *"Ba gói công việc: WP1 xây kho chọn lọc, WP2 là trục lõi đo quên khi nạp tri thức liên tục, WP3 benchmark trên ba bộ dữ liệu; unlearning chỉ là mô-đun phụ trong WP1."*

---

### Slide 39 — ⭐ Thiết kế Benchmark 5 lớp

**🖥 Hiển thị:** Sơ đồ 5 lớp benchmark: Lớp 1 (KB & chuỗi Supersession) · Lớp 2 (Truy vấn theo $t_q$ & 3 task) · Lớp 3 (Lịch nạp liên tục) · Lớp 4 (Cặp Forget/Retain matched) · Lớp 5 (Cặp Temporal & Selective-stress).

**🔍 Giải thích:** Benchmark đặc thù cho Continual RAG: có trục thời gian, nhãn hiệu lực và lịch nạp thay vì chỉ có query-passage-answer tĩnh. Lớp 4 đo unlearning bằng TPR@1%FPR; Lớp 5 đo sự phình to của index.

**📚 Tham khảo:** —

**🎤 Lời thoại:** *"Đóng góp benchmark không phải 'thêm một tập QA', mà là bộ dữ liệu có trục thời gian, nhãn hiệu lực, lịch nạp liên tục và cặp forget/retain — đúng những thứ cần để đo quên, temporal correctness, selective recall và unlearning rò rỉ mà benchmark RAG thường thiếu hoàn toàn."*

---

### Slide 40 — Quy mô dữ liệu (thạc sĩ)

**🖥 Hiển thị:** Thống kê quy mô dự kiến: 3,000–6,000 đơn vị tri thức có metadata (luật, nghị định, thông tư); 2,000–3,500 câu hỏi (held-out); 4 snapshot nạp (25%, 50%, 75%, 100%).

**🔍 Giải thích:** Quy mô vừa vặn cho một luận văn thạc sĩ trong vòng 6 tháng. Không giả định quét sạch toàn bộ kho luật Việt Nam mà tập trung sâu vào các sub-domain thường xuyên biến động (lao động, thuế) để đảm bảo tính khả thi và độ chính xác của metadata.

**📚 Tham khảo:** —

**🎤 Lời thoại:** *"Về quy mô, hệ thống sử dụng khoảng ba đến sáu nghìn đơn vị tri thức có gắn siêu dữ liệu và hai nghìn đến ba nghìn rưỡi câu hỏi. Đây là quy mô phù hợp cho một đồ án thạc sĩ sáu tháng, tập trung vào hai miền biến động mạnh là lao động và thuế."*

---

### Slide 41 — Đối tượng thụ hưởng

**🖥 Hiển thị:** 3 nhóm thụ hưởng: Cộng đồng NLP/AI pháp lý · Kỹ sư hệ thống AI · Các cơ quan tuân thủ pháp lý.

**🔍 Giải thích:** Cộng đồng nhận được bộ benchmark và metric chuẩn. Kỹ sư hệ thống có thể tái sử dụng cơ chế selective memory và cập nhật KB. Cơ quan tuân thủ được bảo đảm không bị viện dẫn các điều luật đã hết hiệu lực.

**📚 Tham khảo:** —

**🎤 Lời thoại:** *"Kết quả phục vụ ba nhóm: cộng đồng nghiên cứu, kỹ sư hệ thống pháp lý, và các cơ quan cần tuân thủ như ngân hàng, tòa án, công ty luật."*

---

### Slide 42 — (Q&A) "Mới gì?"

**🖥 Hiển thị:** Câu hỏi "Đóng góp khoa học mới là gì?" + 3 điểm trả lời cốt lõi: (1) Bằng chứng quên khi nạp liên tục · (2) Selective memory chống quên-kiểu-RAG · (3) Phát hiện trần lập luận H4.

**🔍 Giải thích:** Không tuyên bố mô hình hay thuật toán RAG mới. Điểm mới là nghiên cứu định lượng hiện tượng quên trong RAG dưới lịch nạp liên tục, đề xuất selective memory giảm dung lượng index, và chỉ ra giới hạn lập luận của RAG.

**📚 Tham khảo:** Su 2026 (ReGrad), Sumida 2025 (LUFY).

**🎤 Lời thoại:** *"Em không claim thuật toán RAG mới. Đóng góp là bằng chứng định lượng RAG né quên trên luật VN, selective chống quên-kiểu-RAG, và phát hiện trần lập luận — cả bốn giả thuyết đều bác bỏ được bằng số."*

---

### Slide 43 — (Q&A) "Sao loại ReGrad / zero-drift?"

**🖥 Hiển thị:** Bảng so sánh RAG vs ReGrad về: Accuracy · Latency (Độ trễ) · Unlearning (Học quên) · Chi phí tính toán.

**🔍 Giải thích:** ReGrad tối ưu độ chính xác nhưng đắt đỏ về tính toán, độ trễ suy luận động lớn, và unlearn khó khăn vì đã đổi trọng số tạm thời. RAG phi tham số có factual drift bằng 0 (vì base frozen), sửa đổi KB tức thì, dễ dàng thực thi unlearning bằng cách xóa entry.

**📚 Tham khảo:** Su 2026 (ReGrad).

**🎤 Lời thoại:** *"ReGrad tối ưu độ chính xác nhưng em loại khỏi trục chính vì hai lý do pháp lý: unlearning khó do đã đổi trọng số tạm thời, và độ trễ suy luận động lớn không hợp với hệ thống thực tế. Tuy nhiên em giữ ReGrad làm baseline định lượng, đo cả latency và accuracy thật để đối chiếu khách quan."*

---

### Slide 44 — (Q&A) "Metadata sai thì sụp?"

**🖥 Hiển thị:** Sơ đồ giải pháp quản lý rủi ro metadata: (1) Trích xuất tự động từ văn bản chính thức → (2) LLM tự kiểm tra đối chiếu → (3) Expert spot-check 5% ngẫu nhiên → (4) Đóng gói phiên bản có hỗ trợ Rollback.

**🔍 Giải thích:** Thừa nhận metadata không chính xác là rủi ro hàng đầu. Hệ thống giải quyết bằng quy trình kiểm định đa lớp, hạn chế phạm vi thực nghiệm trong sub-domain lao động & thuế để kiểm soát chất lượng, đồng thời có cơ chế rollback để sửa sai.

**📚 Tham khảo:** —

**🎤 Lời thoại:** *"Sai lệch siêu dữ liệu là rủi ro lớn nhất. Để giải quyết, em trích xuất trực tiếp từ văn bản, cho LLM tự đối chiếu, chuyên gia kiểm tra năm phần trăm và luôn lưu trữ phiên bản để sẵn sàng rollback khi có lỗi."*

---

### Slide 45 — (Q&A) "Jailbreak lộ? Log RTBF?"

**🖥 Hiển thị:** Mô hình phòng thủ unlearning 3 lớp: Bộ lọc truy xuất P (gỡ entry khỏi KB) · Ràng buộc sinh Q (prompt constraint) · Bộ lọc đầu ra (Output filter) + Đồ thị log tối thiểu hóa (minimization).

**🔍 Giải thích:** Unlearning mức hành vi được phòng thủ đa tầng để chống jailbreak, đo lường độ rò rỉ qua chỉ số TPR@1%FPR. Log hệ thống chỉ lưu mã hash và siêu dữ liệu thao tác, hoàn toàn không lưu trữ nội dung nhạy cảm của người dùng (RTBF).

**📚 Tham khảo:** Wang 2025.

**🎤 Lời thoại:** *"Để chống rò rỉ thông tin khi bị tấn công jailbreak, em áp dụng phòng thủ ba lớp gồm bộ lọc truy xuất, ràng buộc sinh và bộ lọc đầu ra, đo bằng chỉ số TPR. Nhật ký hệ thống cũng chỉ lưu mã băm siêu dữ liệu để tuân thủ quyền được lãng quên."*

---

### Slide 46 — (Q&A) "Chỉ số BWT và FGT được tính toán như thế nào?"

**🖥 Hiển thị:** Công thức Backward Transfer (BWT) và Forgetting (FGT) + Sơ đồ ma trận đánh giá CL $R_{i,j}$ + Ví dụ thực tế.

**🔍 Giải thích:** Ma trận $R_{i,j}$ thể hiện hiệu năng của mô hình trên tác vụ $j$ sau khi học xong tác vụ $i$. Backward Transfer (BWT) đo lường mức độ ảnh hưởng của việc học tác vụ mới lên hiệu năng của các tác vụ cũ: $BWT = \frac{1}{T-1} \sum_{i=1}^{T-1} (R_{T,i} - R_{i,i})$. BWT âm thể hiện hiện tượng quên kiến thức cũ. Forgetting (FGT) đo mức độ quên trung bình tại thời điểm hiện tại: $FGT = \frac{1}{T-1} \sum_{j=1}^{T-1} \max_{k \in \{j,...,T-1\}} (R_{k,j} - R_{T,j})$.

**📚 Tham khảo:** Khảo sát Continual Learning của Wu và cộng sự (2024), Chaudhry (2018).

**🎤 Lời thoại:** *"Để đo lường định lượng độ quên, em sử dụng hai chỉ số chuẩn trong học liên tục là Backward Transfer viết tắt là BWT và Forgetting viết tắt là FGT dựa trên ma trận hiệu năng R i j. Công thức BWT bằng một trên T trừ một, nhân với tổng từ i bằng một đến T trừ một của hiệu số R T i trừ đi R i i. Hiểu đơn giản là nếu sau khi học luật Thuế mà năng lực làm luật Lao động trước đó bị giảm từ tám mươi xuống năm mươi phần trăm, thì BWT sẽ âm ba mươi phần trăm, tương ứng với mức độ quên."*

---

### Slide 47 — References

**🖥 Hiển thị:** Danh sách các tài liệu tham khảo cốt lõi nhất xếp theo các nhóm nghiên cứu (Surveys, Forgetting, PEFT/LoRA, Continual RAG, Dữ liệu Việt Nam).

**🔍 Giải thích:** Tổng hợp các bài báo nền tảng làm cơ sở lý thuyết cho toàn bộ đề cương, được dẫn chiếu trực tiếp xuyên suốt các slide.

**📚 Tham khảo:** —

**🎤 Lời thoại:** *"Đây là các tài liệu nền tảng em đã tham khảo và lấy cảm hứng xuyên suốt đề tài."*
