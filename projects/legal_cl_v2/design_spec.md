# legal_cl - Design Spec

## I. Project Information

| Item | Value |
| ---- | ----- |
| **Project Name** | legal_cl |
| **Canvas Format** | PPT 16:9 (1280x720) |
| **Page Count** | 34 |
| **Design Style** | swiss-minimal |
| **Target Audience** | Literature Review Seminar Committee, Faculty of IT, Ton Duc Thang University |
| **Use Case** | Thesis Proposal Defense Seminar |
| **Delivery Purpose** | balanced |
| **Content Strategy** | Follow the slide structure from `deck_v1.html` verbatim, utilizing its exact titles, bullet points, formulas, tables, and SVGs. |
| **Created Date** | 20260624 |

---

## II. Canvas Specification

| Property | Value |
| -------- | ----- |
| **Format** | PPT 16:9 |
| **Dimensions** | 1280x720 |
| **viewBox** | `0 0 1280 720` |
| **Margins** | left/right 60px, top/bottom 50px |
| **Content Area** | 1160x620 |

---

## III. Visual Theme

### Theme Style

- **Mode**: briefing
- **Visual style**: swiss-minimal
- **Theme**: Light theme
- **Tone**: Academic, professional, legal, AI research

### Color Scheme

| Role | HEX | Purpose |
| ---- | --- | ------- |
| **Background** | `#FFFFFF` | Slide background |
| **Secondary bg** | `#F3F6FB` | Cards, backgrounds for tables, steps |
| **Primary** | `#1F4E79` | Titles, section markers (Navy) |
| **Accent** | `#2E75B6` | Highlights, keywords, link markers (Blue) |
| **Secondary accent** | `#EA580C` | Warnings, custom highlights, unlearning markers (Orange) |
| **Body text** | `#1A1A1A` | Main slide text (Ink) |
| **Secondary text** | `#667085` | Footnotes, citations, kicker text (Muted) |
| **Border/divider** | `#D7E0EE` | Table borders, card borders |
| **Success** | `#16A34A` | Compliant / OK state (Green) |
| **Warning** | `#DC2626` | Catastrophic forgetting / alert state (Red) |

---

## IV. Typography System

### Font Plan

- **Typography direction**: Clean academic sans-serif for main text and body, with classic Georgia serif for slide titles to convey legal authority.
- **Font Stack Title**: `"Segoe UI", Arial, sans-serif`
- **Font Stack Body**: `"Segoe UI", Arial, sans-serif`
- **Font Stack Code**: `Consolas, "Courier New", monospace`

### Typography Sizes (px)

- **title**: 42
- **subtitle**: 32
- **lead**: 28
- **body**: 24
- **annotation**: 18
- **footnote**: 16

---

## V. Layout System

We use a Swiss Minimal layout system with clean grids, cards, column distributions, and clear whitespace.
- **Title Slide**: Center-aligned typography, subtle decorative accent gradient background, clean hierarchy.
- **Content Slides**: Top section bar for section tracking. Left/right column layout for text vs tables/drawings. Kicker above the main slide title.
- **Drawing Slides**: Large SVG diagrams taking up the lower 60% of the slide area.

---

## VI. Icon Plan

- **Library**: `tabler-outline` (stroke-style)
- **Stroke Width**: `2`
- **Inventory**: `target`, `bolt`, `shield`, `users`, `chart-bar`, `lightbulb`, `book`, `search`, `brain`, `database`, `trash`, `clock`, `alert-triangle`, `check`, `x`, `file-text`, `arrow-right`, `device-desktop`, `lock`

---

## VII. Visualization Plan

- **Tables**: Styled with a `#F3F6FB` header, clean `#D7E0EE` borders, and highlighted rows using `#FFF7ED` or `#EAFAB0` with orange/green borders.
- **Flow Diagrams**: Rendered natively using SVG shapes, grouping boxes, and directional arrows.

---

## VIII. Image Resource List

This project does not require external bitmap images. All diagrams and mathematical formulas will be rendered natively using SVG elements or inline math text.

---

## IX. Outline

All 34 slides follow the structure defined in `4_docs/03_Present/slides/deck_v1.html`:

### Page 1: Title Slide
- **Title**: Học liên tục & Quên thảm khốc trong Mô hình Ngôn ngữ cho Pháp luật Việt Nam
- **Subtitle**: Continual Learning and Catastrophic Forgetting in Language Models for Vietnamese Law
- **Presenter**: Hoàng Đình Quý Vũ — 252805008 · GVHD: PGS.TS. Lê Anh Cường
- **Badge**: LITERATURE REVIEW SEMINAR · KHOA CNTT · ĐH TÔN ĐỨC THẮNG · 2026

### Page 2: Bối cảnh (Why)
- **Section**: 1 · Giới thiệu (What & Why)
- **Kicker**: Bối cảnh — Why
- **Title**: Luật đổi liên tục → mô hình học xong là tư vấn lỗi thời
- **Bullets**:
  - LLM đang dùng cho hỏi-đáp, tra án lệ, soạn thảo pháp lý.
  - Tri thức luật non-stationary (luôn biến động): sửa, thay thế, án niêm phong.
  - Mô hình trọng số tĩnh ⇒ tư vấn lỗi thời hoặc sai luật — hệ quả pháp lý nghiêm trọng.
- **Citation**: Le et al. 2025; Nguyen 2025 (VLQA)

### Page 3: Vấn đề (What)
- **Section**: 1 · Giới thiệu (What & Why)
- **Kicker**: Vấn đề — What
- **Title**: Bài toán kép: vừa CẬP NHẬT vừa QUÊN có chủ đích — ngược học truyền thống
- **Bullets**:
  - Cập nhật liên tục: nạp luật / án lệ mới.
  - Quên có chủ đích: luật hết hiệu lực · án niêm phong · right-to-be-forgotten (quyền được lãng quên).
  - Dạy thẳng vào trọng số ⇒ quên thảm khốc + khó gỡ luật sai.
- **Callout**: **What:** framework **hybrid** — đóng băng base, facts → RAG, hành vi → LoRA. **Why:** tránh quên thảm khốc & cho phép quên/cập nhật rẻ.

### Page 4: Khái niệm gốc
- **Section**: 1 · Giới thiệu (khái niệm nền)
- **Kicker**: Khái niệm gốc
- **Title**: Tri thức nằm ĐÂU quyết định tất cả: trong trọng số (parametric) hay ngoài (non-parametric)
- **Diagram**: Parametric vs Non-parametric comparison SVG diagram
- **Callout**: Cả luận văn = lập luận chuyển facts từ TRONG trọng số ra NGOÀI. LoRA cũng là tham số nhưng chỉ học hành vi, không chứa facts.

### Page 5: Mục tiêu nghiên cứu
- **Section**: 2 · Mục tiêu nghiên cứu
- **Kicker**: Mục tiêu
- **Title**: Một framework non-parametric, base đóng băng, cập nhật & quên không retrain
- **Bullets**:
  - **Mục tiêu chung:** thiết kế–hiện thực–đánh giá framework RAG-centric cho QA/suy luận luật VN, base frozen.
  - **MT1 — Selective RAG Memory:** điểm importance pháp lý + consolidation/decay + compliance hard-gate.
  - **MT2 — Controllable Unlearning:** cơ chế P+Q cho luật hết hiệu lực / mật / dữ liệu cá nhân.
  - **MT3 — Benchmark & Validation:** pipeline đo trên dữ liệu luật VN.

### Page 6: Giả thuyết / Câu hỏi
- **Section**: 3 · Giả thuyết / Câu hỏi
- **Kicker**: Suy đoán chờ kiểm chứng
- **Title**: Khi tri thức luật nạp LIÊN TỤC, cách nào tránh quên thảm khốc trên 3 task?
- **Table**: Hypotheses grid (H1, H2, H3, H4) with metrics
- **Callout**: Hook: retrieval cứu được task truy xuất (QA) — nhưng chạm trần ở lập luận đa bước (syllogism). Mỗi H bác bỏ được bằng số.

### Page 7: Lược khảo tài liệu (1/9)
- **Section**: 4 · Lược khảo tài liệu
- **Kicker**: Survey arc 2024 → 2026
- **Title**: Năm khảo sát lớn hội tụ: vòng đời CPT→CIT→CA + rủi ro quên xuyên giai đoạn
- **Diagram**: CPT -> CIT -> CA diagram
- **Table**: Landmark surveys (Wu, Shi, Zheng, Guo, Zheng 2026)
- **Citation**: wu2024 · shi2024 · zheng2024 · guo2025 · 11328884

### Page 8: Lược khảo tài liệu (2/9)
- **Section**: 4 · Lược khảo tài liệu
- **Kicker**: Xu hướng then chốt
- **Title**: Cộng đồng dịch từ tri thức TRONG trọng số → NGOÀI (RAG) — nền cho đề tài
- **Columns**:
  - Left: Diagram Internal vs External
  - Right: Key points (Zheng 2024, 4 paradigms, Stability-plasticity trade-off)
- **Citation**: zheng2024lifelong (internal/external taxonomy)

### Page 9: Lược khảo tài liệu (3/9)
- **Section**: 4 · Lược khảo tài liệu
- **Kicker**: Cơ chế quên
- **Title**: Tinh chỉnh trọng số quên theo TỶ LỆ — nhưng replay 0.5% đã cứu gần hết
- **Columns**:
  - Left: Replay % vs Zero-shot Acc table
  - Right: Key points (Inverse Linear Law, sharp vs flat minima, decoder-only)
- **Citation**: kalajdzievski2024 · li2024revisiting · scialom2022

### Page 10: Lược khảo tài liệu (4/9)
- **Section**: 4 · Lược khảo tài liệu
- **Kicker**: PEFT / LoRA
- **Title**: LoRA thường VẪN quên; O-LoRA giảm Forgetting Gradient ~40 lần
- **Table**: Full FT vs LoRA vs O-LoRA FG% table
- **Callout**: ≈40× giảm quên. Nhưng intrinsic rank của facts ≈ 2000 ⇒ LoRA (r≤256) KHÔNG đủ chứa facts → chỉ hợp học hành vi. N-LoRA giảm collision thêm 58.1×.
- **Citation**: biderman2024 · wang2023 O-LoRA · yang2024 N-LoRA · FG% repec:2026

### Page 11: Lược khảo tài liệu (5/9)
- **Section**: 4 · Lược khảo tài liệu
- **Kicker**: CPT & Scaling Laws
- **Title**: CPT khả thi nhưng đắt; D-CPT Law tính được tỷ lệ trộn tối ưu (luật ≈ 64%)
- **Columns**:
  - Left: CPT constraints table (Chemistry vs Law)
  - Right: Key points (Stability gap, Replay ratios, D-CPT law domain r ≈ 0.64)
- **Citation**: gupta2023 · ibrahim2024 · que2024 D-CPT · li2025 TiC-LM

### Page 12: Lược khảo tài liệu (6/9)
- **Section**: 4 · Lược khảo tài liệu
- **Kicker**: Semi-parametric (đối chiếu)
- **Title**: ReGrad đạt luật 84.87% KHÔNG drift — nhưng latency cao & khó unlearn
- **Table**: Standard CPT vs RAG vs ReGrad accuracy & drift table
- **Callout**: CPT làm general sụp còn 25.67% (drift). ReGrad mạnh nhưng phải duy trì gradient-bank + latency cao + không unlearn được → đề tài chọn RAG để được update tức thì + unlearn dễ.
- **Citation**: su2026 RetrievableGradients (ReGrad)

### Page 13: Lược khảo tài liệu (7/9)
- **Section**: 4 · Lược khảo tài liệu
- **Kicker**: RAG memory & Unlearning
- **Title**: Bằng chứng mạnh nhất: RAG-unlearning đạt USR 99.8% mà GIỮ utility
- **Table**: Gradient ascent vs mu-unlearning vs RAG-based P+Q table
- **Callout**: Unlearning gần tuyệt đối MÀ KHÔNG hại utility — điều gradient-ascent không làm được. LUFY: quên 90% lượt ít quan trọng → +17% retrieval (cảm hứng importance + decay).
- **Citation**: 11207222 (Wang, RAG unlearning) · Sumida_2025 (LUFY)

### Page 14: Lược khảo tài liệu (8/9)
- **Section**: 4 · Lược khảo tài liệu
- **Kicker**: Dữ liệu luật Việt Nam
- **Title**: Data VN sẵn sàng, nhưng lập luận tam đoạn luận chỉ ~0.5 — khoảng trống lõi
- **Columns**:
  - Left: ViLegalNLI Table (CafeBERT vs Qwen-2.5)
  - Right: LegalSLM Table (Qwen-3-4B vs Qwen-3-1.7B)
- **Callout**: VLQA 59k điều luật = KB. NLI/QA ~0.95 nhưng syllogism chỉ 0.5–0.6; CPT giúp 4B nhưng HẠI 1.7B → tinh chỉnh không phải lúc nào cũng tốt.
- **Citation**: nguyen2025 VLQA · duong2026 ViLegalNLI · le2025 LegalSLM

### Page 15: Lược khảo tài liệu (9/9)
- **Section**: 4 · Lược khảo tài liệu
- **Kicker**: Chốt chương → 4 khoảng trống
- **Title**: Bốn khoảng trống = bốn lý do tồn tại của đề tài
- **Bullets**:
  - 1. Low-resource VN chưa được nghiên cứu học liên tục đầy đủ.
  - 2. Controllable forgetting / quyền được lãng quên còn under-explored.
  - 3. Selective legal memory — importance đặc thù luật (hiệu lực/trích dẫn/rủi ro) chưa ai làm.
  - 4. Thiếu benchmark legal-CL cho tiếng Việt (đặc biệt metadata hiệu lực/thời gian).
- **Callout**: → Bốn khoảng trống này dẫn thẳng vào framework hybrid RAG-centric ở phần Phương pháp.

### Page 16: Nội dung nghiên cứu
- **Section**: 5 · Nội dung nghiên cứu
- **Kicker**: Ba gói công việc
- **Title**: Ba Work Package bám sát ba mục tiêu
- **Table**: WP vs Content vs Objective mapping

### Page 17: Phương pháp nghiên cứu (1/10)
- **Section**: 6 · Phương pháp nghiên cứu
- **Kicker**: RAG là gì?
- **Title**: RAG = ba bước: Retrieve → Augment → Generate (tra cứu rồi mới trả lời)
- **Diagram**: Retrieve -> Augment -> Generate workflow
- **Callout**: Ví von: luật sư tra tủ hồ sơ rồi mới tư vấn — không "nhớ thuộc lòng" luật mà tra cứu. Nhờ vậy đổi luật = đổi hồ sơ, không cần dạy lại não.

### Page 18: Phương pháp nghiên cứu (2/10)
- **Section**: 6 · Phương pháp nghiên cứu
- **Kicker**: RAG cho vào đâu? · RAG làm gì trong pipeline?
- **Title**: RAG là lớp BỌC quanh LLM; đóng góp của đề tài chèn vào GIỮA Retrieve ↔ Augment
- **Diagram**: Full RAG pipeline showing orange contribution boxes in the middle
- **Callout**: RAG làm gì: ① tra KB lấy điều luật liên quan, ② chèn vào prompt — rồi LLM đóng băng mới sinh trả lời. Đề tài KHÔNG sửa RAG, mà biến đổi tập kết quả giữa ① và ② (cam) + vòng lặp cập nhật/unlearning về KB.

### Page 19: Phương pháp nghiên cứu (3/10)
- **Section**: 6 · Phương pháp nghiên cứu
- **Kicker**: Kiến trúc hybrid
- **Title**: Tách đôi: facts → RAG (cập nhật được), hành vi → LoRA; base ở giữa KHÔNG đổi
- **Diagram**: Frozen Base / RAG Facts / LoRA behavior architecture
- **Callout**: Ba bảo đảm: base frozen ⇒ không quên chung · facts ở RAG ⇒ cập nhật/unlearn tức thì · hành vi ở LoRA ⇒ lập luận đúng văn phong luật. SVD: facts cần rank ~2000 nên không thể nhét vào LoRA.

### Page 20: Phương pháp nghiên cứu (4/10)
- **Section**: 6 · Phương pháp nghiên cứu
- **Kicker**: Đơn vị tri thức + metadata
- **Title**: Mỗi điều luật = đơn vị có hiệu lực + thời gian + vị trí trích dẫn
- **Formula**: $u_i = \langle e_i, v_i, [t_{start},t_{end}], c_i, \rho_i, H_i, acl_i \rangle$
- **Bullets**:
  - v hiệu lực (hard gate) · [t] regime thời gian · c citation centrality
  - ρ rủi ro/thứ bậc (Hiến pháp>Luật>NĐ>TT) · H lịch sử truy vấn (decay) · acl phân quyền
- **Callout**: Đây chính là thứ RAG generic không có — metadata pháp lý làm nền cho selective memory & unlearning.

### Page 21: Phương pháp nghiên cứu (5/10)
- **Section**: 6 · Phương pháp nghiên cứu
- **Kicker**: Selective memory · cơ chế
- **Title**: "Quên" = giảm nhiễu xếp hạng; luật còn hiệu lực đi qua CỔNG CỨNG, không bao giờ rơi
- **Diagram**: Flowchart showing hard gate for v=1 vs importance thresholding
- **Callout**: RQ1 (H1): importance có nén index ≥30% mà giữ Recall@10 ±2% không? w1..w4 rule-based vs LLM-estimated → ablation.

### Page 22: Phương pháp nghiên cứu (6/10)
- **Section**: 6 · Phương pháp nghiên cứu
- **Kicker**: Selective Legal Memory
- **Title**: Importance + decay re-rank, nhưng luật hiệu lực là CỔNG CỨNG
- **Formula**: $I_i = w_1\hat{c_i} + w_2 v_i + w_3\rho_i + w_4\,g(H_i,t)$ and $g(H_i,t)=\sum e^{-\lambda(t-\tau)}$
- **Bullets**:
  - Re-rank: S = sim(q, e_i) + β * I_i; tiers Active top-k / Archive (down-rank, không xoá).
  - Compliance hard-gate: v_i=1 & khớp ⇒ luôn giữ, dù I thấp.
  - w1..w4: rule-based vs LLM-estimated → ablation (đây là RQ1).
- **Callout**: "Quên" = giảm nhiễu xếp hạng, KHÔNG mất coverage. (Algorithm 3.1)
- **Citation**: cảm hứng Sumida_2025 (LUFY)

### Page 23: Phương pháp nghiên cứu (7/10)
- **Section**: 6 · Phương pháp nghiên cứu
- **Kicker**: Unlearning · điểm mấu chốt
- **Title**: Xóa khỏi index ≠ mô hình QUÊN — vì nó vẫn lộ từ trọng số → phải ĐO rò rỉ
- **Diagram**: Deleting from index vs LLM weights leakages (Rò rỉ #1, Rò rỉ #2)
- **Callout**: Xóa index / ACL = code, không phải đóng góp. Temporal phải giữ-mà-chặn (trả lời "luật 2015"). Phần nghiên cứu = đo & giảm rò rỉ (behavioral unlearning, không claim xóa khỏi trọng số).

### Page 24: Phương pháp nghiên cứu (8/10)
- **Section**: 6 · Phương pháp nghiên cứu
- **Kicker**: Module 2 · Unlearning
- **Title**: P+Q: ba chế độ quên — và ĐO rò rỉ chứ không tin việc đã xoá
- **Columns**:
  - Left: Bullets (Temporal, Access-control, Index-delete) & formulas: $KB_T = \{u \notin T \wedge Authorize(u,q,t_q)\}$, $Prompt_T = Sys \cup Constraint(T) \cup Ctx \cup q$
  - Right: Metrics table (Effectiveness, Universality, Harmlessness, Robustness, Simplicity)
- **Callout**: behavioral unlearning — không claim xoá khỏi trọng số; rò rỉ được đo (H3).
- **Citation**: 11207222 (P+Q framework)

### Page 25: Phương pháp nghiên cứu (9/10)
- **Section**: 6 · Phương pháp nghiên cứu
- **Kicker**: Module 3 · LoRA + base nhỏ
- **Title**: LoRA học HÀNH VI (rank thấp); mô hình nhỏ 1–4B đủ vì facts để ở ngoài
- **Bullets**:
  - Phân vai: RAG = dùng luật nào · LoRA = lập luận thế nào (tam đoạn luận, trích dẫn).
  - SVD: facts cần rank ~2000; hành vi (IFT) rank ≤32 ⇒ LoRA r=256 hợp behavior, không nhồi facts.
  - Base frozen 1–4B (≤15B) — đúng môi trường low-resource; baseline parametric chạy cùng cỡ → so sánh công bằng.
- **Callout**: Đa-ngành tuần tự (lao động→thuế) → chống collision bằng O-LoRA/N-LoRA/SLIM (đo Backward Transfer = H2).
- **Citation**: biderman2024 · hu2021 LoRA · han2025 SLIM

### Page 26: Phương pháp nghiên cứu (10/10)
- **Section**: 6 · Phương pháp nghiên cứu
- **Kicker**: Phương pháp luận dữ liệu
- **Title**: Thu nhập–Chọn mẫu–Phân tích; RAG-centric cân bằng tốt nhất cho luật
- **Columns**:
  - Left: Key points (Collection, Sampling, Analysis, Expert validation 5%)
  - Right: Methodology comparison table (CPT/CIT vs ReGrad vs RAG+LoRA)
- **Citation**: Table 3.1 (tab:methodology_comparison) · Figure 3.x data pipeline

### Page 27: Giới hạn phạm vi
- **Section**: 7 · Giới hạn phạm vi (Where & When)
- **Kicker**: Giới hạn
- **Title**: Phạm vi nội dung, không gian, thời gian — và đánh đổi nói thẳng
- **Bullets**:
  - Nội dung: QA / NLI / suy luận rule-based; không generation dài. Parametric (CPT/ReGrad) chỉ là baseline đối chiếu.
  - Không gian (Where): pháp luật VN, tiếng Việt; kịch bản test lấy data từ lao động/thuế (data scenario, không phải scope thu hẹp).
  - Thời gian (When): văn bản 2005→nay; chọn mốc sửa đổi lớn tạo "legal time-regime".
  - Đánh đổi: reasoning bị chặn bởi base frozen — nêu rõ ở Limitations.

### Page 28: Kết quả mong đợi
- **Section**: 8 · Kết quả mong đợi
- **Kicker**: Ngưỡng nghiệm thu
- **Title**: Mỗi mục tiêu gắn một ngưỡng số → nghiệm thu khách quan
- **Table**: Expected outcomes and quantification (D1, D2, D3)
- **Callout**: + 1 hội nghị (PACLIC/KSE/VLSP) · 1 tạp chí ISI/Scopus · GitHub tái lập được.

### Page 29: Đối tượng thụ hưởng
- **Section**: 9 · Đối tượng thụ hưởng (Who)
- **Kicker**: Ai hưởng lợi
- **Title**: Ba nhóm thụ hưởng: nghiên cứu · kỹ sư hệ thống · cơ quan tuân thủ
- **Bullets**:
  - Cộng đồng NLP/AI pháp lý VN: benchmark + metric chuẩn để so các retrieval/memory paradigm.
  - Kỹ sư hệ thống pháp lý: tái dùng selective memory + unlearning gate → giảm chi phí đảm bảo compliance.
  - Cơ quan tuân thủ/bảo mật (ngân hàng, toà án, công ty luật): không rò rỉ/không viện dẫn thông tin mật hoặc luật hết hiệu lực.

### Page 30: Dự trù kinh phí
- **Section**: 10 · Dự trù kinh phí (How much) + Kế hoạch
- **Kicker**: Kinh phí & timeline 6 tháng
- **Title**: Khả thi 6 tháng: base frozen + mô hình nhỏ ⇒ chi phí chính là index 1 lần
- **Columns**:
  - Left: Cost estimation table
  - Right: 8 milestones & Risk factors

### Page 31: Tổng kết
- **Section**: Tổng kết
- **Kicker**: Chốt
- **Title**: Một bộ nhớ pháp lý biết quên đúng luật, đúng lúc, và giải trình được
- **Bullets**:
  - Đóng băng base → không quên thảm khốc; facts ở RAG → cập nhật/unlearn tức thì.
  - Ba đóng góp đo được: selective memory (H1) · forgetting RAG vs parametric (H2) · unlearning leakage (H3).
  - Đúng hướng 2026: rời trọng số → bộ nhớ ngoài; thừa nhận thẳng giới hạn → vững khi bảo vệ.
- **Citation**: ✉ hoangdinhquyvu.snape.22@gmail.com · Góp ý rất hoan nghênh

### Page 32: References
- **Section**: Tài liệu tham khảo
- **Kicker**: References (chọn lọc)
- **Title**: References
- **Columns**:
  - Left List: Wu 2024, Zheng 2024, Kalajdzievski 2024, Li 2024, Biderman 2024, Que 2024
  - Right List: Su 2026, Wang 2025, Sumida 2025, Nguyen 2025, Duong 2026, Le 2025, Han 2025, Yang 2024

### Page 33: Phụ lục Q&A (1)
- **Section**: Phụ lục — phòng hỏi xoáy
- **Kicker**: Q&A 1
- **Title**: "RAG đã có rồi — xoá index đâu phải mới? Phần nào là nghiên cứu?"
- **Bullets**:
  - Không claim RAG mới. Xoá index/ACL/audit = code, không nhận là đóng góp.
  - Temporal phải giữ-mà-chặn; xoá thật thì model vẫn lộ từ trọng số → em đo rò rỉ.
  - Nghiên cứu = 3 câu hỏi bác bỏ được: nén index (H1) · forgetting (H2) · leakage (H3).

### Page 34: Phụ lục Q&A (2)
- **Section**: Phụ lục — phòng hỏi xoáy
- **Kicker**: Q&A 2
- **Title**: "Model nhỏ 1–4B đủ suy luận luật không? 6 tháng kịp không?"
- **Bullets**:
  - Em không đặt cược vào suy luận của base — facts ở ngoài nên không cần model lớn (đúng luận điểm).
  - Baseline parametric chạy cùng cỡ nhỏ ⇒ chênh lệch do cơ chế, không do model.
  - Khả thi 6 tháng: base frozen, kịch bản test tập trung sub-domain biến động nhiều.

---

## X. Speaker Notes

All speaker notes are written in Vietnamese as shown in the `legal_cl_source.md` (e.g. `💬 Kể: ...`). They will be split and output to the `notes/` directory.

---

## XI. Tech Constraints

1. SVGs must be well-formed and valid XML.
2. Responsive dimensions with clean viewBox `0 0 1280 720`.
3. Embedded fonts must use web-safe fallbacks: `"Segoe UI", Arial, sans-serif`.
4. Embedded diagrams from `deck_v1.html` must preserve coordinates and layout alignment.
