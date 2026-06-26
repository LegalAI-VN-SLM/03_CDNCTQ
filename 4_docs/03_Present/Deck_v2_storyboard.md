# Deck v2 — Storyboard (20 slide chính + ngân hàng phụ lục)

> Mục tiêu: **~20 slide chính** gọn cho 20 phút + **ngân hàng phụ lục** (sau slide Cảm ơn) để bung khi hội đồng hỏi.
> Cột **Nguồn**: `reuse pXXX` = tái dùng SVG hiện có (chỉ đổi số trang); `NEW` = vẽ mới; `merge` = gộp 2 slide cũ.
> Chất liệu phụ lục lit-review lấy từ `4_docs/02_reading_paper/Gemni_notebooklm/` (global_synthesis + 5 group).

---

## PHẦN A — DECK CHÍNH (~20 slide, trình bày tuyến tính)

| # | Slide | Nội dung cốt | Nguồn |
|---|---|---|---|
| 1 | Tiêu đề | Tên đề tài, badge, HV/GVHD | reuse p001 |
| 2 | Mục lục | 8 phần | reuse p001b |
| 3 | Bối cảnh & Bài toán kép | Luật non-stationary → vừa cập nhật vừa quên có chủ đích; dạy thẳng trọng số = quên thảm khốc | **merge** p003+p004 |
| 4 | Parametric vs Non-parametric | Tri thức nằm ĐÂU quyết định tất cả | reuse p005 |
| 5 | Mục tiêu + 4 Giả thuyết | Mục tiêu chung + bảng H1–H4 (bác bỏ được bằng số) | **merge** p006+p007 |
| 6 | Lược khảo 1 — Bức tranh | CPT→CIT→CA · 4 paradigm · dịch internal→external (RAG) | **merge** p008+p009 |
| 7 | Lược khảo 2 — Quên là quy luật | Inverse Linear Law + replay 0.5% cứu + loss landscape | reuse p010 |
| 8 | Lược khảo 3 — Đối chiếu & Data VN | ReGrad 84.87% (đắt) · VLQA/ViLegalNLI/LegalSLM (syllogism ~0.5) | **merge** p013+p015 |
| 9 | 4 khoảng trống | 4 lý do tồn tại của đề tài | reuse p016 |
| 10 | Phương pháp — RAG & đóng góp chèn đâu | RAG 3 bước + 2 hộp cam (unlearn/selective) chèn giữa | **merge** p018+p019 |
| 11 | Kiến trúc Hybrid | facts→RAG · hành vi→LoRA · base frozen | reuse p020 |
| 12 | Đơn vị tri thức + metadata | tuple ⟨e,v,[t],c,ρ,H,acl⟩ | reuse p021 |
| 13 | Selective Memory | importance I + decay + compliance hard-gate | **merge** p022+p023 |
| 14 | ⭐ **Thí nghiệm Forgetting (CORE)** | Nạp liên tục × 4 nhánh (CPT/ReGrad/vanilla/selective) × 3 task → đường quên + BWT. **Đây là trục lõi H1–H2** | **NEW** |
| 15 | (Phụ) Unlearning P+Q | xóa ≠ quên → đo rò rỉ TPR@1%FPR; temporal gating | reuse p024 (rút gọn) |
| 16 | LoRA + mô hình nhỏ | RAG=dùng luật nào · LoRA=lập luận; 1–4B đủ | reuse p026 |
| 17 | Giới hạn phạm vi | 3 task · luật VN · lao động/thuế (data scenario) · đánh đổi reasoning | reuse p028 |
| 18 | Kết quả mong đợi R1–R3 | đường forgetting · selective ±2%/index↓30% · Δ theo task | reuse p029 |
| 19 | Kinh phí & Kế hoạch 6 tháng | ~20tr · 8 milestone | reuse p031 |
| 20 | Kết luận | hybrid · 3 kết quả đo được · hợp xu thế 2026 | reuse p032 |
| 21 | 🙏 Cảm ơn | "Cảm ơn Hội đồng đã lắng nghe!" + email | reuse p033 |

→ **Trình bày: 20 slide nội dung** (1–20) + slide cảm ơn. Vừa 20 phút (~55s/slide).

---

## PHẦN B — NGÂN HÀNG PHỤ LỤC (sau Cảm ơn, bung khi hỏi)

### B1. Lược khảo sâu (theo 5 nhóm paper — từ reading notes)
| # | Slide | Chất liệu (paper) | Nguồn |
|---|---|---|---|
| P1 | Nhóm 1 · Surveys CL | 3-stage CPT/CIT/CA · 4 paradigm · Agent 2026 (Wu/Shi/Zheng/Guo/Zheng26) | **NEW** |
| P2 | Nhóm 2 · Cơ chế quên | loss landscape sharp/flat + SAM · Inverse Linear Law · replay (Li24/Kalajdzievski/Scialom) | reuse p011-ish/NEW |
| P3 | Nhóm 3 · CPT Scaling | D-CPT Law (công thức) · Stability Gap (Que/Gupta/Ibrahim) | reuse p012 |
| P4 | Nhóm 4 · PEFT/LoRA | FG% O-LoRA · SVD rank~2000 · SLIM/N-LoRA (Biderman/Wang23/Han/Yang) | reuse p011 |
| P5 | Nhóm 5 · Bán tham số | ReGrad (gradient bank) · LUFY (quên 90%→+17%) (Su/Sumida) | reuse p014 |
| P6 | Dữ liệu VN chi tiết | VLQA 59k · ViLegalNLI 42k (PMI) · LegalSLM rubric | reuse p015 |

### B2. Phương pháp sâu
| # | Slide | Nội dung | Nguồn |
|---|---|---|---|
| P7 | Selective — công thức đầy đủ | I = w₁ĉ+w₂v+w₃ρ+w₄g(H,t) + Algorithm 1 | reuse p023 |
| P8 | Unlearning — 3 chế độ + 5 tiêu chí | Temporal/ACL/Index-delete · P+Q · 5 criteria | reuse p025 |
| P9 | Continual update pipeline | upsert·supersession(v=0)·canary·rollback (Fig 3.5) | **NEW** (vẽ từ tex 3.5) |
| P10 | Sơ đồ vận hành (Fig 3.2) | 6 bước + vòng cập nhật | reuse p035 |
| P11 | Pipeline dữ liệu (Fig 3.3) | Collection→Sampling→Analysis | reuse p036 |
| P12 | Phụ thuộc WP (Fig 4.1) | WP1→WP2(Forgetting)→WP3 | reuse p037 |
| P13 | ⭐ Thiết kế Benchmark 5 lớp | KB+supersession · t_q · snapshot · forget/retain · regime+stress (từ Gộp.md QB) | **NEW** |
| P14 | Quy mô dữ liệu (thạc sĩ) | 3–6k đơn vị metadata · ~2–3.5k câu hỏi · 4 snapshot | **NEW** |

### B3. Phòng hỏi xoáy (Q&A defense — từ Gộp.md)
| # | Slide | Câu | Nguồn |
|---|---|---|---|
| P15 | "Mới gì?" (novelty) | không claim RAG mới; bằng chứng forgetting + selective + trần lập luận (Q1) | **NEW** |
| P16 | "Sao loại ReGrad?" + "zero-drift?" | latency/unlearn khó + no factual drift (Q3,Q4) | **NEW** |
| P17 | "Metadata sai thì sụp?" | rủi ro #1; lấy từ văn bản + LLM/spot-check 5% + versioning (Q9) | **NEW** |
| P18 | "Jailbreak lộ?" + log RTBF | P+Q+filter; đo TPR@1%FPR; log minimization (Q11,Q12) | **NEW** |
| P19 | References | danh sách chọn lọc | reuse p034 |

→ **Phụ lục: ~19 slide** (P1–P19). Tổng deck v2 ≈ **40 slide** (20 chính + cảm ơn + 19 phụ lục).

---

## Tổng kết build
- **Tái dùng (chỉ đổi số trang / rút gọn):** ~24 slide từ deck hiện có.
- **Gộp (merge 2→1):** 5 slide chính (3, 5, 6, 8, 10, 13).
- **VẼ MỚI (~8):** S14 Forgetting-core · P1 Nhóm1 · P9 Continual-pipeline · P13 Benchmark-5lớp · P14 Quy-mô-data · P15–P18 Q&A (4).
- **Chất liệu mới lấy từ:** `global_synthesis.md` (bảng 36 paper, D-CPT, ReGrad, cross-group connections) + `Gộp.md` (QB benchmark, Q1/Q3/Q4/Q9/Q11/Q12).

**Cách triển khai đề xuất:** giữ project hiện tại; tạo thư mục build mới hoặc đổi thứ tự `page_*.svg`. Vì đổi thứ tự + thêm 8 SVG là nhiều, mình build **theo lô**: (1) 8 slide mới → (2) merge 5 slide chính → (3) sắp lại thứ tự + export.
