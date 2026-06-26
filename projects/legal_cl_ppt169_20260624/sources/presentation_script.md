# KỊCH BẢN THUYẾT TRÌNH BẢO VỆ ĐỀ CƯƠNG (bản chi tiết — giải thích từng ô + nguồn tham khảo)
**Đề tài:** Học liên tục & Quên thảm khốc trong Mô hình Ngôn ngữ cho Pháp luật Việt Nam
**Tác giả:** Hoàng Đình Quý Vũ · GVHD: PGS.TS. Lê Anh Cường
**Hội đồng:** Báo cáo Chuyên đề Lược khảo Tài liệu, Khoa CNTT, ĐH Tôn Đức Thắng (2026)

> Mỗi slide gồm: **🖥 Hiển thị** → **🔍 Giải thích từng ô/mục + ý nghĩa** → **📚 Tham khảo / lấy cảm hứng** → **🎤 Lời thoại** → **➡ Chuyển slide**.

---

## PHẦN 1 — GIỚI THIỆU (Slide 1–6)

### Slide 1 — Tiêu đề
**🖥 Hiển thị:** Tên đề tài, logo trường/khoa (góc phải-trên), tên học viên + GVHD, "Proposal Defense".
**🔍 Giải thích từng mục:** Tên đề tài nêu đủ 2 từ khóa lõi — *Continual Learning* và *Catastrophic Forgetting* — và miền áp dụng *Pháp luật Việt Nam*.
**📚 Tham khảo:** —
**🎤 Lời thoại:** *"Kính thưa Hội đồng, em là Hoàng Đình Quý Vũ. Hôm nay em xin trình bày đề cương đề tài 'Học liên tục và Quên thảm khốc trong Mô hình Ngôn ngữ cho Pháp luật Việt Nam', dưới hướng dẫn của PGS.TS. Lê Anh Cường."*
**➡ Chuyển slide:** *"Trước hết, vì sao mô hình ngôn ngữ dễ lỗi thời trong môi trường pháp lý."*

### Slide 2 — Bối cảnh (Why)
**🖥 Hiển thị:** Tiêu đề + 3 gạch đầu dòng + trích dẫn Le 2025, Nguyen 2025.
**🔍 Giải thích từng ô:**
- **Gạch 1:** LLM đang dùng cho hỏi-đáp luật, tra án lệ, soạn thảo.
- **Gạch 2:** luật *non-stationary* — sửa, thay thế, niêm phong (ý nghĩa: tri thức luật "biến động").
- **Gạch 3:** model trọng số tĩnh ⇒ tư vấn lỗi thời/sai (ý nghĩa: hệ quả pháp lý nghiêm trọng).
**📚 Tham khảo:** *"Bối cảnh dữ liệu luật VN em **tham khảo từ Le và cộng sự 2025 (LegalSLM)** và **Nguyen 2025 (VLQA)**."*
**🎤 Lời thoại:** *"LLM đang được dùng rộng trong pháp lý, nhưng tri thức luật có tính không tĩnh: liên tục sửa đổi, thay thế, niêm phong. Một mô hình trọng số tĩnh sẽ nhanh lỗi thời, và tư vấn theo luật hết hiệu lực gây hậu quả nghiêm trọng."*
**➡ Chuyển slide:** *"Ngoài việc phải cập nhật, hệ thống còn gặp một thách thức thứ hai mang tính đối nghịch."*

### Slide 3 — Vấn đề (What)
**🖥 Hiển thị:** Tiêu đề "Bài toán kép" + callout giải pháp hybrid.
**🔍 Giải thích từng ô:**
- **Cập nhật liên tục:** nạp luật/án lệ mới.
- **Quên có chủ đích:** luật hết hiệu lực · án mật · quyền được lãng quên.
- **Callout:** vì sao 2 yêu cầu này ngược với học truyền thống (dạy thẳng vào trọng số ⇒ quên thảm khốc + khó gỡ).
**📚 Tham khảo:** —
**🎤 Lời thoại:** *"Bài toán kép: vừa cập nhật liên tục, vừa phải quên có chủ đích. Hai yêu cầu này ngược với học máy truyền thống — dạy thẳng vào trọng số thì quên thảm khốc, mà gỡ tri thức khỏi trọng số lại rất khó."*
**➡ Chuyển slide:** *"Câu hỏi cốt lõi: tri thức nên nằm ở đâu để cập nhật và quên dễ nhất?"*

### Slide 4 — Khái niệm gốc: Parametric vs Non-parametric
**🖥 Hiển thị:** 2 panel — panel đỏ (Parametric) và panel xanh (Non-parametric).
**🔍 Giải thích từng ô:**
- **Panel đỏ "Parametric — học thuộc vào đầu":** facts nằm trong trọng số; thêm luật ⇒ **train lại** ⇒ quên thảm khốc, khó gỡ.
- **Panel xanh "Non-parametric — tra sổ tay":** LLM đóng băng + kho KB ngoài; thêm luật ⇒ **sửa CSDL** tức thì; không quên; unlearn = ẩn/xóa entry.
**📚 Tham khảo:** *"Cách phân loại trong/ngoài trọng số em **lấy theo Zheng và cộng sự 2024** (internal vs external knowledge)."*
**🎤 Lời thoại:** *"Cả đề tài xoay quanh một câu: tri thức nằm ĐÂU. Nếu trong trọng số (parametric) thì sửa khó, quên dễ. Nếu ở ngoài (non-parametric, RAG) thì sửa tức thì, không quên. Em chọn đưa facts ra ngoài, đóng băng base, và chỉ dùng LoRA cho hành vi."*
**➡ Chuyển slide:** *"Từ ý tưởng gốc này, em đặt ra ba mục tiêu cụ thể."*

### Slide 5 — Mục tiêu nghiên cứu
**🖥 Hiển thị:** Tiêu đề + 3 thẻ MT1 (xanh dương) · MT2 (cam) · MT3 (xanh lá).
**🔍 Giải thích từng ô:**
- **MT1 — Kho RAG chọn lọc:** xây kho ngoài có importance + decay + compliance gate (ý nghĩa: "bộ nhớ" hệ thống).
- **MT2 — Đo quên khi nạp liên tục:** nạp dần × 4 nhóm (CPT/ReGrad/vanilla/selective) × 3 task, đo Backward Transfer (ý nghĩa: trái tim đề tài).
- **MT3 — Soi trần lập luận:** retrieval cứu QA/NLI tới đâu, có vượt syllogism không (ý nghĩa: phát hiện giới hạn RAG).
**📚 Tham khảo / cảm hứng:** *"Cách xây kho MT1 em **lấy cảm hứng từ ReGrad (Su 2026)** — họ dựng gradient-bank, em dựng kho phi-tham số. Thí nghiệm nạp-liên-tục MT2 em **tham khảo khuôn Bảng B của ReGrad**."*
**🎤 Lời thoại:** *"Mục tiêu tổng quát: chứng minh RAG phi-tham số giữ tri thức luật khi nạp liên tục mà không quên, đo trên QA/NLI/Syllogism. Cụ thể qua ba mục tiêu trên màn hình."*
**➡ Chuyển slide:** *"Ba mục tiêu này được phát biểu thành các giả thuyết bác bỏ được."*

### Slide 6 — Câu hỏi / Giả thuyết nghiên cứu
**🖥 Hiển thị:** Bảng 4 hàng H1–H4 × cột (Giả thuyết · Nội dung · Đo bằng).
**🔍 Giải thích từng hàng:**
- **H1 — Forgetting:** CPT nạp nhiều ⇒ quên thảm khốc. Đo: BWT, perf↓.
- **H2 — RAG né quên:** RAG base frozen không quên trọng số. Đo: BWT≈0.
- **H3 — Selective cứu RAG (hàng cam nổi bật):** kho phình → vanilla tụt recall; selective giữ. Đo: Recall@K, index size.
- **H4 — Trần lập luận:** RAG mạnh QA > NLI > Syllogism. Đo: Δ acc theo task.
**📚 Tham khảo:** *"Chỉ số Backward Transfer em **lấy theo chuẩn các survey continual learning (Wu 2024)**."*
**🎤 Lời thoại:** *"Bốn giả thuyết, mỗi cái gắn một chỉ số đo được nên đều bác bỏ được bằng số. H3 (in đậm cam) là chỗ selective memory có lý do tồn tại; H4 là phát hiện về trần lập luận."*
**➡ Chuyển slide:** *"Để có cơ sở, em lược khảo bức tranh nghiên cứu 2024–2026."*

---

## PHẦN 2 — LƯỢC KHẢO TÀI LIỆU (Slide 7–15)

### Slide 7 — Lược khảo (1/9): Survey arc 2024→2026
**🖥 Hiển thị:** Sơ đồ vòng đời CPT→CIT→CA (mũi tên đỏ "cross-stage forgetting") + bảng 5 survey.
**🔍 Giải thích từng ô:**
- **Sơ đồ 3 hộp:** CPT (nạp fact) → CIT (học task) → CA (căn chỉnh giá trị); mũi tên đỏ vòng lại = quên xuyên giai đoạn.
- **Bảng 5 survey:** mỗi hàng = 1 khảo sát, cột "trục phân loại" + "đóng góp chính"; hàng Zheng 2024 (cam) = dịch sang tri thức ngoài.
**📚 Tham khảo:** *"Em **tham khảo 5 khảo sát**: Wu 2024, Shi 2024, Zheng 2024, Guo 2025 và một khảo sát 2026 về lifelong agents."*
**🎤 Lời thoại:** *"Cộng đồng đồng thuận chia vòng đời huấn luyện thành CPT–CIT–CA, và rủi ro lớn là quên xuyên giai đoạn. Mấu chốt là đánh đổi ổn định–linh hoạt: học mới càng sâu thì quên cũ càng nhiều."*
**➡ Chuyển slide:** *"Bế tắc đó thúc đẩy một xu hướng dịch chuyển công nghệ."*

### Slide 8 — Lược khảo (2/9): Internal → External
**🖥 Hiển thị:** Sơ đồ 2 nhánh Internal (parametric) vs External (non-parametric, highlight RAG).
**🔍 Giải thích từng ô:**
- **Nhánh Internal:** CPT/CFT, LoRA — tri thức trong trọng số.
- **Nhánh External (nổi bật):** RAG = facts, Tool/API — tri thức ngoài.
- **3 dịch chuyển:** task-specific→agnostic; full-FT→PEFT; internal→external.
**📚 Tham khảo:** *"Khung internal/external và '3 dịch chuyển' em **lấy trực tiếp từ Zheng và cộng sự 2024**."*
**🎤 Lời thoại:** *"Zheng 2024 chỉ ra xu hướng dịch từ nhồi facts vào trọng số sang lưu ở bộ nhớ ngoài. Trong miền cần cập nhật tức thì như luật, đây là hướng đúng — và là nền cho thiết kế của em."*
**➡ Chuyển slide:** *"Nhưng nếu vẫn tinh chỉnh trọng số thì quên ra sao? Có bằng chứng số."*

### Slide 9 — Lược khảo (3/9): Quên là quy luật + replay
**🖥 Hiển thị:** Bảng Replay ratio (4 hàng) + Inverse Linear Law + loss landscape.
**🔍 Giải thích từng ô:**
- **Cột "Replay Ratio":** % dữ liệu cũ trộn lại khi học mới. **Cột "Zero-shot Acc":** năng lực tổng quát còn giữ.
- **Hàng 0% (đỏ) → 22.4%:** không ôn = quên thảm khốc. **Hàng 0.5% → 53.4% (cam):** chỉ ôn nửa phần trăm đã cứu gần hết. Hàng 1% → 54.8%; 5% → 55.1% (bão hòa).
**📚 Tham khảo:** *"Bảng replay em **tham khảo Scialom 2022**; quy luật tuyến tính nghịch (Inverse Linear Law) **lấy từ Kalajdzievski 2024**."*
**🎤 Lời thoại:** *"Quên là quy luật, không phải lỗi tinh chỉnh được: học mới càng sâu, quên cũ càng nhiều. Điểm sáng là chỉ cần ôn 0.5% dữ liệu cũ đã giữ được hơn nửa năng lực — một ổn định hóa cực rẻ."*
**➡ Chuyển slide:** *"Vậy PEFT/LoRA kiểm soát quên tốt tới đâu?"*

### Slide 10 — Lược khảo (4/9): PEFT/LoRA + O-LoRA
**🖥 Hiển thị:** Bảng FG% (3 hàng) × 3 cột (Domain/Reasoning/Reading).
**🔍 Giải thích từng ô:**
- **Cột "FG%":** Forgetting Gradient (Độ dốc gây quên) — đo lường mức độ thay đổi trọng số làm mất đi kiến thức cũ (FG% càng thấp thì mô hình càng giữ kiến thức cũ tốt).
- **Hàng Full-FT (42.45) & LoRA-r64 (40.43):** Tinh chỉnh toàn bộ mô hình (Full-FT) hoặc tinh chỉnh LoRA rank 64 thông thường vẫn gây quên kiến thức cũ cực kỳ nghiêm trọng (trên 40%).
- **Hàng O-LoRA (xanh) (0.77 / 0.25 / -0.24):** Giảm quên từ 40 đến 50 lần nhờ cơ chế tối ưu hóa trực giao với không gian tác vụ cũ. Đặc biệt, chỉ số âm (-0.24%) cho thấy năng lực đọc hiểu còn tăng nhẹ sau khi học tác vụ mới.
- **Callout:** Hạng nội tại (intrinsic rank) của tri thức sự kiện (facts) rất lớn (~2000), trong khi LoRA chỉ chạy hiệu quả ở rank nhỏ ($r \le 256$). Điều này chứng minh LoRA rank nhỏ chỉ đủ để học cách lập luận (hành vi), không thể dùng LoRA để nhồi nhét tri thức luật pháp mới vào trọng số.
**📚 Tham khảo / cảm hứng:** *"Bảng FG% em **tham khảo bài tổng hợp 2026** (repec); 'LoRA learns less, forgets less' **lấy từ Biderman 2024**; O-LoRA/N-LoRA từ Wang 2023 và Yang 2024."*
**🎤 Lời thoại:** *"Slide này trình bày về độ dốc gây quên (Forgetting Gradient - FG%). Khi tinh chỉnh toàn bộ hoặc dùng LoRA thông thường, mô hình vẫn bị quên nghiêm trọng, lên tới trên 40%. Ngược lại, phương pháp O-LoRA nhờ học trực giao đã giảm quên đến 40 lần, đưa tỷ lệ quên về dưới 1%. Tuy nhiên, vì độ phức tạp hay hạng nội tại (intrinsic rank) của tri thức pháp luật lên tới khoảng 2000, các adapter LoRA thông thường với rank nhỏ dưới 256 chỉ học được cách lập luận (tức là học hành vi) chứ không thể nhét dữ kiện pháp luật mới vào trọng số. Đây là cơ sở để em quyết định giữ tri thức ở kho ngoài RAG và chỉ dùng LoRA làm công cụ học hành vi."*
**➡ Chuyển slide:** *"Nếu muốn pretrain luật thì trộn dữ liệu bao nhiêu? Có công thức."*

### Slide 11 — Lược khảo (5/9): CPT scaling + D-CPT Law
**🖥 Hiển thị:** Công thức D-CPT Law: $L(N, D, r) = E + A N^{-\alpha} + B \cdot r^{\eta} D^{-\beta} + C (r + \epsilon)^{-\gamma}$ + Bảng tỷ lệ tối ưu (Chemistry, Law) + ghi chú R² > 0.97 + cột phải Stability Gap (Khe hở ổn định).
**🔍 Giải thích từng ô:**
- **Giải thích công thức:** Dự đoán độ lỗi kiểm thử (Loss - $L$) của mô hình dựa trên số tham số ($N$), lượng dữ liệu ($D$) và tỷ lệ trộn dữ liệu luật mới ($r$). Trong đó $E, A, B, C, \alpha, \beta, \gamma, \eta, \epsilon$ là các tham số tối ưu hóa được khớp từ thực nghiệm nhỏ.
- **Cột "Ràng buộc":** các điều kiện ràng buộc đầu vào (như độ lệch trôi dưới 3% cho Chemistry; quy mô ngữ liệu 2 tỷ token cho Law).
- **Cột "Dự đoán r / Thực r":** tỷ lệ trộn tối ưu giữa dữ liệu luật và dữ liệu chung giúp mô hình học luật tốt nhất mà không quên kiến thức chung. Hàng Law (cam) cho thấy tỷ lệ dự đoán 64% dữ liệu luật cực kỳ khớp với thực nghiệm thực tế (0.640 vs 0.638).
- **Stability Gap (Khe hở ổn định):** hiện tượng độ lỗi tăng vọt khi bắt đầu pha học tiếp theo, đòi hỏi kỹ thuật khởi động lại tốc độ học (re-warming learning rate).
**📚 Tham khảo:** *"Công thức D-CPT Law em **tham khảo Que 2024**; stability gap **từ Gupta 2023**."*
**🎤 Lời thoại:** *"Slide này trình bày Công thức D-CPT Law dùng để dự đoán độ lỗi L (lốt) của mô hình dựa trên số tham số N, kích thước dữ liệu D và tỷ lệ trộn dữ liệu chuyên ngành r. Công thức cụ thể là: L(N, D, r) bằng E cộng với A nhân N mũ trừ alpha, cộng B nhân r mũ eta nhân D mũ trừ beta, cộng C nhân mở ngoặc r cộng epsilon đóng ngoặc mũ trừ gamma. Nhờ công thức này, ta dự đoán được tỷ lệ trộn tối ưu để pretrain luật là 64% dữ liệu luật và 36% dữ liệu chung để mô hình ít bị quên nhất, với hệ số xác định R bình phương (R²) cực cao trên không phẩy chín bảy. Dù vậy, trong đề tài này em chọn giải pháp RAG phi tham số để tri thức nằm ở ngoài, tránh hoàn toàn rủi ro này."*
**➡ Chuyển slide:** *"Có một hướng bán-tham số đáng để đối chiếu trực tiếp: ReGrad."*

### Slide 12 — Lược khảo (6/9): ReGrad (đối chiếu)
**🖥 Hiển thị:** Bảng 3 hàng (CPT/RAG/ReGrad) × 4 cột (General/Law/Overall/Drift).
**🔍 Giải thích từng ô/cột:**
- **Cột General:** đo mất năng lực chung (dấu hiệu quên). **Cột Law:** năng lực miền đích. **Cột Overall:** trung bình. **Cột Drift?:** có trôi trọng số không.
- **Hàng CPT 25.67% (drift CÓ):** general sụp = quên thảm khốc. **Hàng ReGrad 84.87% (KHÔNG drift):** mạnh nhất nhưng đắt (gradient-bank + latency).
**📚 Tham khảo / cảm hứng:** *"Bảng này em **tham khảo trực tiếp Su và cộng sự 2026 (Retrievable Gradients)** trên LLaMA-8B. Đây cũng là **khuôn em lấy cảm hứng** cho thí nghiệm nạp-liên-tục của mình."*
**🎤 Lời thoại:** *"CPT làm năng lực chung sụp còn 25.67% — đúng quên thảm khốc. ReGrad đạt luật 84.87% không trôi trọng số, nhưng phải duy trì ngân hàng gradient khổng lồ và độ trễ cao. Đó là lý do em chọn RAG: rẻ hơn, cập nhật tức thì, quên/gỡ dễ."*
**➡ Chuyển slide:** *"Bằng chứng mạnh nhất cho hướng RAG nằm ở khả năng quên có kiểm soát."*

### Slide 13 — Lược khảo (7/9): RAG memory & unlearning
**🖥 Hiển thị:** Bảng 3 hàng (Gradient ascent/μ-unlearning/RAG) × 4 cột (ROUGE-L/USR/TPR/Utility).
**🔍 Giải thích từng ô/cột:**
- **ROUGE-L↓:** còn tái tạo nội dung đã quên không (thấp=tốt). **USR↑:** tỷ lệ quên thành công. **TPR↓:** rò rỉ. **Utility:** năng lực còn giữ.
- **Hàng RAG (xanh) 0.10/99.8%/1.3%/53.8%:** quên gần tuyệt đối **mà giữ utility** — điều gradient-ascent (40.2%) không làm được.
**📚 Tham khảo / cảm hứng:** *"Bảng unlearning em **tham khảo Wang 2025** (RAG-based unlearning). Cơ chế bộ nhớ chọn lọc của em **lấy cảm hứng từ LUFY (Sumida 2025)** — họ quên 90% lượt ít quan trọng để tăng 17% truy xuất."*
**🎤 Lời thoại:** *"RAG-unlearning đạt USR 99.8% và ROUGE-L 0.10 mà không hại năng lực nền, trong khi sửa trọng số vừa quên kém vừa tàn phá utility. LUFY là nguồn cảm hứng cho selective memory của em."*
**➡ Chuyển slide:** *"Cuối cùng, dữ liệu luật Việt Nam đã sẵn sàng tới đâu?"*

### Slide 14 — Lược khảo (8/9): Dữ liệu luật VN + reasoning gap
**🖥 Hiển thị:** 2 bảng cạnh nhau — ViLegalNLI (2 model) và LegalSLM (2 model) + callout.
**🔍 Giải thích từng ô:**
- **ViLegalNLI:** CafeBERT (110M) 87.49% · Qwen-2.5 (72B) 90.72% — phân loại tốt.
- **LegalSLM:** cột "Lập luận" — Qwen-3-4B 0.595, Qwen-3-1.7B chỉ **0.384 (sụp)** — *lập luận tam đoạn luận còn yếu*.
- **Callout:** VLQA 59k điều = KB; NLI/QA ~0.95 nhưng syllogism ~0.5; CPT giúp 4B nhưng hại 1.7B.
**📚 Tham khảo:** *"Em **tham khảo 3 bộ dữ liệu VN**: VLQA (Nguyen 2025), ViLegalNLI (Duong 2026), LegalSLM (Le 2025)."*
**🎤 Lời thoại:** *"Truy xuất và phân loại đã tốt (~0.95), nhưng tam đoạn luận chỉ ~0.5, và CPT giúp model lớn nhưng hại model nhỏ. Khoảng trống lập luận này chính là hook H4 của em."*
**➡ Chuyển slide:** *"Bốn khoảng trống rút ra dẫn thẳng vào phương pháp."*

### Slide 15 — Lược khảo (9/9): Bốn khoảng trống
**🖥 Hiển thị:** 4 khoảng trống đánh số.
**🔍 Giải thích từng ô:**
- **(1)** Low-resource VN chưa nghiên cứu CL đủ. **(2)** RAG chống quên-khi-nạp-liên-tục chưa đo trên luật VN. **(3)** Selective legal memory chưa ai làm. **(4)** Thiếu benchmark legal-CL VN trên 3 task.
**📚 Tham khảo:** *"Bốn khoảng trống tổng hợp từ các survey ở Slide 7–8."*
**🎤 Lời thoại:** *"Bốn khoảng trống này là bốn lý do tồn tại của đề tài, và chúng dẫn thẳng vào framework hybrid RAG-centric ở phần phương pháp."*
**➡ Chuyển slide:** *"Trước khi vào phương pháp, em tóm tắt nội dung nghiên cứu thành ba gói công việc."*

---

## PHẦN 3 — NỘI DUNG & PHƯƠNG PHÁP (Slide 16–27)

### Slide 16 — Nội dung nghiên cứu (3 Work Package)
**🖥 Hiển thị:** Bảng 3 hàng WP1/WP2/WP3 × (Nội dung · ↔MT).
**🔍 Giải thích từng ô:**
- **WP1 — Xây kho RAG chọn lọc (Mục tiêu 1):** Xây dựng nền tảng dữ liệu cho hệ thống bao gồm thu thập luật (crawl), phân mảnh (chunking), đánh chỉ mục ngữ nghĩa (indexing). Đặc biệt tích hợp đồ thị trích dẫn (citation graph) để xác định độ trung tâm c, hàm suy hao Ebbinghaus g(H,t) để mô phỏng sự suy giảm tần suất truy cập, và cổng tuân thủ hiệu lực luật cứng.
- **WP2 — Thí nghiệm forgetting (Mục tiêu 2):** Chạy thực nghiệm chính bằng cách nạp dần tri thức luật qua thời gian. So sánh trực tiếp 4 nhánh (CPT, ReGrad, vanilla RAG, và selective RAG) trên 3 tác vụ pháp lý (Hỏi đáp QA, suy luận tự nhiên NLI, và tam đoạn luận Syllogism).
- **WP3 — Phân tích & benchmark (Mục tiêu 3):** Đánh giá đường cong quên (forgetting curves) của các nhánh, tìm ra điểm chênh lệch hiệu năng để phát hiện trần lập luận pháp lý của RAG (đặc biệt trên tác vụ Tam đoạn luận), đồng thời đối chiếu hiệu năng và chi phí vận hành với giải pháp bán tham số ReGrad.
**📚 Tham khảo:** —
**🎤 Lời thoại:** *"Nội dung nghiên cứu được chia làm ba gói công việc chính. Gói công việc một là xây dựng kho dữ liệu RAG có chọn lọc để làm giá đỡ tri thức, tích hợp đồ thị trích dẫn và cổng hiệu lực. Gói công việc hai là thiết lập môi trường thực nghiệm nạp tri thức liên tục để đo lường độ quên trên ba tác vụ cốt lõi ở cả bốn phương pháp so sánh. Gói công việc ba là phân tích định lượng kết quả thực nghiệm để xác định giới hạn lập luận của RAG và đối chiếu hiệu năng chi phí với baseline ReGrad."*
**➡ Chuyển slide:** *"Đi vào phương pháp — và trước hết, RAG thực chất là gì."*

### Slide 17 — RAG là gì? (3 bước)
**🖥 Hiển thị:** Sơ đồ 3 bước: Câu hỏi → ① Retrieve → ② Augment → ③ Generate, có KB ở trên.
**🔍 Giải thích từng ô:**
- **① Retrieve (Truy xuất):** biến câu hỏi thành vector, tìm trong KB các điều luật giống nhất → top-N.
- **② Augment (Tăng cường):** chèn các đoạn tìm được vào prompt làm "ngữ cảnh".
- **③ Generate (Sinh):** LLM đọc ngữ cảnh + câu hỏi → trả lời có căn cứ, trích dẫn.
**📚 Tham khảo:** *"Khái niệm RAG (Retrieval-Augmented Generation) là chuẩn ngành; em trình bày lại 3 bước cho rõ."*
**🎤 Lời thoại:** *"RAG gồm ba bước: tra cứu, chèn ngữ cảnh, rồi sinh câu trả lời. Ví von như luật sư tra tủ hồ sơ rồi mới tư vấn — nhờ vậy đổi luật chỉ là đổi hồ sơ, không cần dạy lại não."*
**➡ Chuyển slide:** *"Vậy đóng góp của em nằm ở đâu trong quy trình RAG?"*

### Slide 18 — RAG cho vào đâu trong pipeline?
**🖥 Hiển thị:** Sơ đồ: khung RAG (Retrieve→Augment) bọc quanh; 2 hộp cam (Unlearning P · Selective+Compliance) chèn vào giữa; LLM frozen+LoRA (xanh lá) ở cuối; vòng lặp nét đứt về KB.
**🔍 Giải thích từng ô:**
- **Khung xanh "RAG":** phần kinh điển (Retrieve + Augment) — *không phải đóng góp*.
- **2 hộp cam (chèn giữa Retrieve↔Augment):** Unlearning gate (P) lọc luật quên/cấm; Selective+Compliance ưu tiên, giữ luật hiệu lực — *đây là đóng góp*.
- **Hộp xanh lá:** LLM đóng băng + LoRA sinh trả lời.
- **Vòng lặp nét đứt:** cập nhật/unlearning về KB, không đụng trọng số.
**📚 Tham khảo:** —
**🎤 Lời thoại:** *"Em không sửa RAG. Em biến đổi tập kết quả ở khoảng giữa Retrieve và Augment — chèn lọc quên và chọn lọc/tuân thủ vào đó. LLM vẫn đóng băng."*
**➡ Chuyển slide:** *"Nhìn tổng thể, đây là kiến trúc hybrid tách facts và hành vi."*

### Slide 19 — Kiến trúc Hybrid
**🖥 Hiển thị:** Base LLM đóng băng ở giữa; FACTS→RAG (cam) bên trái; HÀNH VI→LoRA (xanh) bên phải.
**🔍 Giải thích từng ô:**
- **Hộp giữa "Base LLM đóng băng":** không bao giờ đổi.
- **Mũi tên trái "FACTS→RAG":** cập nhật/unlearn = sửa KB (non-parametric).
- **Mũi tên phải "HÀNH VI→LoRA":** học cách lập luận, không nhớ facts (parametric nhỏ).
**📚 Tham khảo / cảm hứng:** *"Tách facts–hành vi em **lấy cảm hứng từ Biderman 2024** (LoRA hợp học hành vi, không hợp nhồi facts)."*
**🎤 Lời thoại:** *"Ba bảo đảm: base frozen ⇒ không quên chung; facts ở RAG ⇒ cập nhật/unlearn tức thì; hành vi ở LoRA ⇒ lập luận đúng văn phong luật."*
**➡ Chuyển slide:** *"Để làm được, mỗi điều luật phải là một đơn vị tri thức có metadata đặc thù."*

### Slide 20 — Đơn vị tri thức & metadata
**🖥 Hiển thị:** Công thức tuple u_i = ⟨e, v, [t], c, ρ, H, acl⟩ + giải thích.
**🔍 Giải thích từng ký hiệu:**
- **e:** embedding (vector ngữ nghĩa). **v:** hiệu lực (cổng cứng). **[t]:** khoảng thời gian hiệu lực (regime). **c:** citation centrality. **ρ:** rủi ro/thứ bậc. **H:** lịch sử truy vấn (decay). **acl:** phân quyền.
**📚 Tham khảo:** *"Đây là phần em **tự thiết kế** (đặc thù luật) — RAG generic không có metadata hiệu lực/thời gian/trích dẫn."*
**🎤 Lời thoại:** *"Mỗi điều luật u_i (u y) được biểu diễn dưới dạng bộ các tham số gồm: vector nhúng e, trạng thái hiệu lực v, khoảng thời gian t, độ trung tâm trích dẫn c, mức độ rủi ro rho (rô), lịch sử truy cập H, và quyền truy cập acl. Chính siêu dữ liệu đặc thù luật này phân biệt kho của em với RAG thông thường."*
**➡ Chuyển slide:** *"Trên nền metadata đó là module bộ nhớ chọn lọc."*

### Slide 21 — Selective Memory (cơ chế)
**🖥 Hiển thị:** Công thức importance + decay + luồng hard-gate.
**🔍 Giải thích từng ô:**
- **Công thức I = w₁ĉ + w₂v + w₃ρ + w₄g(H,t):** điểm quan trọng = trích dẫn + hiệu lực + rủi ro + tần suất (decay).
- **g(H,t)=Σe^(−λ(t−τ)):** đường quên Ebbinghaus.
- **Hard-gate:** v=1 & còn hiệu lực ⇒ luôn giữ dù I thấp; re-rank S=sim+βI.
**📚 Tham khảo / cảm hứng:** *"Cơ chế suy giảm trí nhớ em **lấy cảm hứng từ LUFY (Sumida 2025)**, nhưng mở rộng từ hội thoại sang *đơn vị luật* và thêm cổng tuân thủ."*
**🎤 Lời thoại:** *"Điểm quan trọng I (ai) được tính bằng tổng w một nhân c mũ (ĉ) cộng w hai nhân v cộng w ba nhân rho (rô) cộng w bốn nhân hàm suy hao g(H, t). Trong đó, hàm g(H, t) tính theo đường quên Ebbinghaus bằng tổng các e mũ trừ lambda nhân với t trừ tau. Khi xếp hạng lại, điểm S (ét) bằng độ tương đồng sim cộng beta nhân với I (ai). Với cơ chế này, 'quên' chỉ là giảm nhiễu xếp hạng khi kho phình to, chứ không mất độ bao phủ vì luật còn hiệu lực luôn được giữ qua cổng cứng. Đây là chỗ selective memory cứu RAG khỏi 'quên kiểu RAG'."*
**➡ Chuyển slide:** *"Module này là trục chính trong phần thực nghiệm."*

### Slide 22 — Module 1 (trục chính)
**🖥 Hiển thị:** Tóm tắt selective memory + mục tiêu H3.
**🔍 Giải thích từng ô:**
- **Importance + decay:** nén index khi kho lớn dần. **Compliance hard-gate:** giữ luật hiệu lực. **Mục tiêu H3:** index↓≥30%, Recall ±2%.
**📚 Tham khảo / cảm hứng:** *"Tiếp nối cảm hứng LUFY (Sumida 2025); kiểm chứng bằng ablation feature."*
**🎤 Lời thoại:** *"Vai trò của selective memory: khi nạp liên tục làm kho phình, vanilla RAG bị nhiễu và tụt recall; selective giữ recall sắc. Đây là một trong những đóng góp đo được."*
**➡ Chuyển slide:** *"Phần phụ — unlearning — em trình bày ngắn để minh bạch."*

### Slide 23 — (Phụ) Unlearning: xóa ≠ quên
**🖥 Hiển thị:** Sơ đồ: Xóa chunk → LLM vẫn lộ (rò rỉ #1 từ trọng số, #2 từ chunk liên quan) → kết luận đo TPR.
**🔍 Giải thích từng ô:**
- **Hộp "Xóa chunk khỏi KB":** thao tác lập trình dễ.
- **Hộp xanh "LLM đóng băng":** vẫn "biết" fact đã xóa (đã pretrain).
- **2 bong bóng đỏ:** rò rỉ #1 từ trọng số, #2 suy bắc cầu.
- **Kết luận cam:** đóng góp H3 = đo rò rỉ bằng TPR@1%FPR.
**📚 Tham khảo:** *"Khung unlearning P+Q em **tham khảo Wang 2025**; em định vị đây là phần phụ."*
**🎤 Lời thoại:** *"Xóa index chỉ là lập trình — không phải đóng góp. Vì model vẫn lộ fact từ trọng số, nên em ĐO rò rỉ chứ không tin việc đã xóa. Đây là behavioral unlearning, không claim xóa khỏi trọng số."*
**➡ Chuyển slide:** *"Chi tiết cơ chế P+Q và 5 tiêu chí đánh giá."*

### Slide 24 — (Phụ) P+Q & 5 tiêu chí
**🖥 Hiển thị:** 3 chế độ quên (trái) + công thức P, Q + bảng 5 tiêu chí (phải).
**🔍 Giải thích từng ô:**
- **3 chế độ:** Temporal gating (giữ-mà-chặn) · Access-control (theo acl, là code) · Index deletion (xóa thật, là code).
- **P (retrieval):** loại entry cần quên khỏi candidate. **Q (output):** ràng buộc prompt không viện dẫn.
- **Bảng 5 tiêu chí:** Effectiveness, Universality, Harmlessness, **Robustness (TPR@1%FPR — đỏ)**, Simplicity.
**📚 Tham khảo:** *"P+Q và 5 tiêu chí em **tham khảo Wang 2025 (RAG unlearning)**."*
**🎤 Lời thoại:** *"P+Q là phòng thủ kép. Bộ lọc P giới hạn kho tri thức K-B_T chỉ gồm các đơn vị u không nằm trong tập cần quên T và thỏa mãn điều kiện phân quyền Authorize cho câu hỏi q tại thời điểm t_q. Q là ràng buộc ở prompt. Ta đo độ bền bỉ bằng tỷ lệ dương tính thật TPR tại một phần trăm tỷ lệ dương tính giả FPR dưới tấn công jailbreak."*
**➡ Chuyển slide:** *"Hành vi lập luận được dạy qua LoRA."*

### Slide 25 — Module 3: LoRA + mô hình nhỏ
**🖥 Hiển thị:** Phân vai RAG/LoRA + SVD rank + config (1–4B).
**🔍 Giải thích từng ô:**
- **Phân vai:** RAG = dùng luật nào; LoRA = lập luận thế nào.
- **SVD:** facts cần rank ~2000, hành vi (IFT) ≤32 ⇒ LoRA r=256 hợp behavior.
- **Config:** base instruct decoder-only frozen **1–4B (≤15B — low-resource)**, replay 0.5%.
**📚 Tham khảo / cảm hứng:** *"Phân tích rank em **lấy từ Biderman 2024**; LoRA gốc **Hu 2021**; học đa-ngành tuần tự **tham khảo O-LoRA (Wang 2023)/SLIM (Han 2025)**."*
**🎤 Lời thoại:** *"LoRA dạy cách lập luận pháp lý chứ không nhồi luật vào trọng số. Em cấu hình LoRA với rank r (e-rờ) bằng 256 và alpha bằng 512 để học hành vi. Vì facts được đưa ra ngoài RAG, mô hình nhỏ từ 1 đến 4 tỷ tham số là đủ và đúng tinh thần tối ưu tài nguyên."*
**➡ Chuyển slide:** *"Tổng hợp lại bằng phương pháp luận dữ liệu và bảng so sánh."*

### Slide 26 — Phương pháp luận dữ liệu + so sánh 4 arm
**🖥 Hiển thị:** Quy trình Collection→Sampling→Analysis + bảng so sánh 4 arm.
**🔍 Giải thích từng ô:**
- **Collection:** crawl + metadata + expert validate 5%.
- **Sampling:** kịch bản nạp liên tục lấy data từ sub-domain biến động nhiều (lao động/thuế).
- **Analysis:** ablation ladder, test H1–H4.
- **Bảng 4 arm:** CPT (quên thảm khốc) · ReGrad (đắt) · vanilla RAG (nhiễu↑) · **selective RAG (giữ recall)**.
**📚 Tham khảo / cảm hứng:** *"Khuôn nạp-liên-tục **lấy cảm hứng từ Bảng B ReGrad (Su 2026)**."*
**🎤 Lời thoại:** *"Thí nghiệm chính: nạp tri thức liên tục, so 4 nhóm trên 3 task. CPT sụp như Bảng B; RAG né quên nhưng kho phình thì nhiễu, nên selective cứu."*
**➡ Chuyển slide:** *"Em nói rõ giới hạn phạm vi để không over-claim."*

### Slide 27 — Giới hạn phạm vi (Where & When)
**🖥 Hiển thị:** Nội dung/không gian/thời gian + đánh đổi.
**🔍 Giải thích từng ô:**
- **Nội dung:** QA/NLI/suy luận; không generation dài. **Không gian:** luật VN; data từ lao động/thuế (kịch bản test, không phải scope thu hẹp). **Thời gian:** 2005→nay. **Đánh đổi:** reasoning bị chặn bởi base frozen — nêu ở Limitations.
**📚 Tham khảo:** —
**🎤 Lời thoại:** *"Em giới hạn rõ: 3 task, luật VN, kịch bản test lấy từ sub-domain biến động nhiều. Đánh đổi thật là reasoning bị chặn bởi base — em nêu thẳng chứ không giấu."*
**➡ Chuyển slide:** *"Kết quả mong đợi gắn với các tiêu chí số."*

---

## PHẦN 4 — KẾT QUẢ, KẾ HOẠCH & KẾT LUẬN (Slide 28–34)

### Slide 28 — Kết quả mong đợi (R1–R3)
**🖥 Hiển thị:** Bảng R1/R2/R3 + callout outputs.
**🔍 Giải thích từng ô:**
- **R1 — Đường forgetting:** CPT sụp (BWT âm), RAG không quên — vẽ trên 3 task.
- **R2 — Selective vs vanilla:** vanilla tụt Recall@K; selective giữ ±2%, index↓≥30%.
- **R3 — Δ theo task:** QA > NLI > Syllogism = bằng chứng trần lập luận; đối chiếu ReGrad.
- **Callout outputs (student-scale):** hoàn thành luận văn + phấn đấu 1 báo cáo hội nghị trong nước + GitHub.
**📚 Tham khảo:** —
**🎤 Lời thoại:** *"Kết quả mong đợi gồm: đường quên của RAG ổn định so với CPT bị sụp. Thứ hai, selective memory giữ chỉ số Recall tại K (Recall@K) lệch không quá cộng trừ 2%, trong khi giảm dung lượng index tối thiểu 30%. Thứ ba, độ chênh lệch delta (Δ) giữa các nhiệm vụ chứng minh trần lập luận giảm dần từ QA đến NLI và Syllogism. Sản phẩm ở quy mô sinh viên là luận văn thạc sĩ và mã nguồn mở."*
**➡ Chuyển slide:** *"Ai sẽ hưởng lợi từ kết quả này?"*

### Slide 29 — Đối tượng thụ hưởng (Who)
**🖥 Hiển thị:** 3 nhóm thụ hưởng.
**🔍 Giải thích từng ô:**
- **Cộng đồng NLP/AI pháp lý:** benchmark + metric chuẩn. **Kỹ sư hệ thống:** tái dùng selective memory + cơ chế cập nhật. **Cơ quan tuân thủ:** không viện dẫn luật hết hiệu lực.
**📚 Tham khảo:** —
**🎤 Lời thoại:** *"Kết quả phục vụ ba nhóm: cộng đồng nghiên cứu, kỹ sư hệ thống pháp lý, và các cơ quan cần tuân thủ như ngân hàng, tòa án, công ty luật."*
**➡ Chuyển slide:** *"Về tính khả thi — kinh phí và lộ trình."*

### Slide 30 — Kinh phí & lộ trình (How much)
**🖥 Hiển thị:** Bảng chi phí (5 dòng) + timeline 4 mốc + rủi ro.
**🔍 Giải thích từng ô:**
- **Nhân sự:** 0 (SV + GVHD, không lương). **Compute:** Colab Pro+ · RunPod (6,0tr). **Dữ liệu:** SV tình nguyện + OCR (6,5tr). **In ấn/đi lại/workshop:** 7,5tr. **Tổng: ~20 triệu (~$800)**.
- **Timeline:** corpus W3 → KB W5 → baseline W9 → đo forgetting → benchmark → báo cáo W24.
- **Rủi ro #1:** metadata hiệu lực → lấy từ văn bản + expert spot-check 5%.
**📚 Tham khảo:** —
**🎤 Lời thoại:** *"Quy mô đồ án sinh viên: tổng khoảng 20 triệu, lương bằng 0, dùng Colab và RunPod thay vì A100. Vì base đóng băng nên chi phí chính chỉ là index một lần. Rủi ro lớn nhất là metadata hiệu lực, đã có biện pháp."*
**➡ Chuyển slide:** *"Tóm lại toàn bộ đề tài trong ba ý."*

### Slide 31 — Kết luận
**🖥 Hiển thị:** 3 dải kết luận + thẻ liên hệ.
**🔍 Giải thích từng ô:**
- **Dải 1 — Hybrid:** đóng băng base ⇒ chống quên thảm khốc; facts ở RAG ⇒ cập nhật/unlearn rẻ.
- **Dải 2 — 3 kết quả đo được:** CPT quên/RAG né (H1-H2) · selective cứu (H3) · trần lập luận ở syllogism (H4).
- **Dải 3 — Hợp xu thế 2026:** dịch tri thức ra ngoài; thừa nhận giới hạn để tạo độ tin.
**📚 Tham khảo:** —
**🎤 Lời thoại:** *"Ba ý chốt: kiến trúc hybrid chống quên thảm khốc; ba kết quả đo được; và phát hiện rằng retrieval cứu QA/NLI nhưng chạm trần ở lập luận. Em thừa nhận giới hạn để bảo vệ vững."*
**➡ Chuyển slide:** *"Đây là các tài liệu tham khảo chính."*

### Slide 32 — Tài liệu tham khảo
**🖥 Hiển thị:** Danh sách references chọn lọc.
**🔍 Giải thích:** Các nguồn đã trích trong bài — survey (Wu/Zheng), forgetting (Kalajdzievski/Li), LoRA (Biderman), D-CPT (Que), ReGrad (Su 2026), unlearning (Wang 2025), LUFY (Sumida 2025), dữ liệu VN (Nguyen/Duong/Le).
**📚 Tham khảo:** (chính là slide này)
**🎤 Lời thoại:** *"Đây là các tài liệu nền tảng em đã tham khảo và lấy cảm hứng xuyên suốt đề tài."*
**➡ Chuyển slide:** *"Em xin sẵn sàng nhận câu hỏi từ Hội đồng."*

### Slide 33 — (Phụ lục) Q&A 1: "Em nghiên cứu gì mới?"
**🖥 Hiển thị:** Câu hỏi + 3 điểm trả lời.
**🔍 Giải thích từng ô:**
- **Điểm 1:** không claim RAG mới; đóng góp = đo RAG chống quên thảm khốc khi nạp liên tục (chưa ai làm) — khuôn Bảng B ReGrad nhưng phi-tham số.
- **Điểm 2:** selective RAG chống "quên kiểu RAG" (kho phình → recall tụt).
- **Điểm 3:** hook trần lập luận (H4) — retrieval không thay được suy luận.
**📚 Tham khảo / cảm hứng:** *"Nhấn rõ: khuôn thí nghiệm lấy cảm hứng từ ReGrad (Su 2026); selective từ LUFY (Sumida 2025)."*
**🎤 Lời thoại:** *"Em không claim thuật toán RAG mới. Đóng góp là bằng chứng định lượng RAG né quên trên luật VN, selective chống quên-kiểu-RAG, và phát hiện trần lập luận — cả bốn giả thuyết đều bác bỏ được bằng số."*
**➡ Chuyển slide:** *(câu hỏi tiếp theo)*

### Slide 34 — (Phụ lục) Q&A 2: Mô hình nhỏ & khả thi
**🖥 Hiển thị:** Câu hỏi + 3 điểm trả lời.
**🔍 Giải thích từng ô:**
- **Điểm 1:** không đặt cược vào suy luận của base — facts ở ngoài nên không cần model lớn.
- **Điểm 2:** baseline parametric chạy cùng cỡ nhỏ ⇒ chênh lệch do cơ chế, không do model.
- **Điểm 3:** khả thi 6 tháng — base frozen, kịch bản tập trung, budget ~20 triệu.
**📚 Tham khảo:** —
**🎤 Lời thoại:** *"Em dùng mô hình 1–4B một cách có chủ đích: vì facts ở RAG nên không cần model lớn, và baseline parametric chạy cùng cỡ để so sánh công bằng. Đó cũng là lý do đề tài khả thi trong 6 tháng với chi phí thấp."*
**➡ Chuyển slide:** *"Em xin cảm ơn Hội đồng đã lắng nghe."*

---

> **Lưu ý trình bày:** ở các slide lược khảo (7–14), luôn mở đầu bằng *"Em tham khảo / lấy cảm hứng từ [tác giả, năm]..."* trước khi giải thích số liệu — vừa minh bạch nguồn, vừa thể hiện đã đọc kỹ.
