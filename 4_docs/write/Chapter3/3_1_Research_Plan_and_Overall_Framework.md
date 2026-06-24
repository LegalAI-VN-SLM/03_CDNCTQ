# 3.1 Research Plan and Overall Framework  ↔ `3_Methods_Models/3_1_Research_Plan_and_Overall_Framework.tex`

> Vai trò: khung tổng thể **hybrid (RAG + LoRA)**; định vị 3 trụ method (3.2 Selective Memory, 3.3 Unlearning, 3.4 LoRA) + **data/sampling** + **experimental design**. | Nguồn: `11328884`, `Sumida_2025`, `su2026retrievablegradients...` (đối chiếu), `nguyen2025vlqa...`, `biderman2024loralearnsforgets`.

---

## 3.1.1 Tuyên bố thiết kế cốt lõi (cần thuộc lòng)

> **Base LLM ĐÓNG BĂNG.** Tri thức pháp lý (facts) sống trong **KB phi tham số (RAG)**; **hành vi/kỹ năng suy luận** học bằng **LoRA adapter** (IFT, low-rank, base frozen). Mọi thao tác "học/quên facts" = sửa KB hoặc policy truy xuất, KHÔNG đụng trọng số base.

Phân vai hybrid (xem `biderman2024loralearnsforgets`): **facts → RAG** (cập nhật/unlearn dễ, không drift) · **behavior → LoRA** (r cao hợp IFT, không nhồi fact).

Hệ quả (3 lý do bảo vệ):
1. **Không weight drift / không catastrophic forgetting** năng lực chung — base frozen (đối lập CPT/CFT, `kalajdzievski2024...`).
2. **Cập nhật luật tức thì, rẻ** — thêm/sửa/xoá entry KB, không train lại.
3. **Quên có chủ đích là thao tác bậc nhất** — gỡ/ẩn entry + ràng buộc đầu ra.

## 3.1.2 Đơn vị tri thức & lược đồ metadata
KB = {u₁,…,u_N}. Mỗi đơn vị u_i (điều/khoản/án lệ):

| Ký hiệu | Trường | Dùng cho |
|---------|--------|----------|
| e_i | embedding | retrieval |
| v_i | validity ∈{0,1} | compliance gate (3.2), unlearning (3.3) |
| [t_start,t_end] | khoảng hiệu lực | regime thời gian |
| c_i | citation centrality | importance (3.2) |
| ρ_i | risk/applicability | importance (3.2) |
| H_i | access history | decay (3.2) |
| acl_i | access control | unlearning sealed (3.3) |

## 3.1.3 Kiến trúc & luồng dữ liệu (hybrid)
```
OFFLINE: Văn bản luật+án lệ → chuẩn hoá đơn vị → metadata(v,[t],c,ρ,acl) → citation graph → embedding → KB
         Legal instruction data (+replay 0.5%) → train LoRA (r=256, base frozen) → LoRA adapter   [3.4]
ONLINE:  Query q (+t_q)
         → [Retriever sim(q,e_i)]
         → [3.3 Unlearning gate P]  (loại/ẩn entry cấm)
         → [3.2 Selective Memory]   (re-rank importance + compliance hard-gate)
         → top-k context
         → [Base LLM + LoRA adapter + reasoning prompt + ràng buộc Q]   [3.4]
         → Câu trả lời (+ audit log) → cập nhật access log (cho decay)
```
Thứ tự: **P (unlearn) → compliance gate → selective re-rank → reasoning (base+LoRA)+Q**.

## 3.1.4 Vì sao hybrid RAG+LoRA (không ReGrad/CPT thuần)
| Tiêu chí | CPT/CFT | ReGrad | **RAG+LoRA (đề tài)** |
|---|---|---|---|
| Weight drift facts | có | không | **không (facts ở KB, base frozen)** |
| Cập nhật luật | train lại | thêm gradient | **sửa KB tức thì** |
| Unlearn | rất khó | khó | **gỡ entry — dễ** |
| Độ trễ inference | nền | cao | nền + tra cứu |
| Reasoning behavior | trong weight | trong weight | **LoRA adapter (IFT)** |

## 3.1.5 Giả định & giới hạn
- (GĐ1) Chất lượng metadata hiệu lực — **rủi ro chính**.
- (GĐ2) Retriever đủ tốt (lỗi retrieval → lỗi answer).
- (GH1) Reasoning chặn bởi base+LoRA (LegalSLM: syllogism ~0.5).
- (GH2) Unlearning ở mức **behavioral** (grounding), không xoá parametric.

---

## 3.1.6 Data and Sampling Design (chọn mẫu & dữ liệu)
> Phần *methodology* về dữ liệu. Chi tiết thực thi/threshold ở **Ch4** (cross-ref).

**Nguồn dữ liệu (corpora):**
- Văn bản quy phạm + án lệ công khai VN (~2005→nay), chuẩn hoá thành đơn vị + metadata.
- KB truy xuất: **VLQA** (59k điều luật) `nguyen2025vlqa...`.

**Datasets đánh giá (mẫu tác vụ):**
| Dataset | Task | Vai trò |
|---|---|---|
| VLQA | legal QA + retrieval | KB + đo Recall@K/MRR/accuracy |
| ViLegalNLI | legal NLI | đo suy luận (đã lọc lexical shortcut) |
| LegalSLM | QA/NLI/syllogism | đo lập luận đa bước + baseline |

**Continual scenarios (cách lấy mẫu kịch bản):**
- **Scenario A — Knowledge update:** thêm/sửa luật vào KB; tạo cặp regime trước/sau sửa (split theo `t`).
- **Scenario B — Unlearning case:** chọn **forget set** (luật hết hiệu lực / sealed / personal) + **retain set** (truy vấn liên quan không bị quên) để đo harmlessness.
- **Scenario C — Selective memory stress:** index lớn, kiểm decay/consolidation giữ compliance.

**Sampling/validation:** chuyên gia kiểm metadata hiệu lực trên **mẫu ngẫu nhiên ~5%** (giảm rủi ro GĐ1); split forget/retain cân bằng theo topic (VLQA 9 nhóm).

## 3.1.7 Experimental Design and Evaluation Metrics
> Thiết kế *phương pháp* đánh giá. Ngưỡng nghiệm thu cụ thể + timeline ở **Ch4 (4.1, 4.3)**.

**Baselines & variants (ablation tăng dần):**
1. Static RAG (cùng base+LoRA, cùng KB) — mốc.
2. + Selective memory (importance + decay) — H1.
3. + Unlearning (P+Q) — H3.
4. Đối chiếu: CPT/CFT (đo forgetting chung) — H2; ReGrad (tham chiếu 2.5).

**Metrics (theo nhóm):**
| Nhóm | Chỉ số |
|---|---|
| Accuracy/retrieval | Recall@K, MRR@10, QA/NLI accuracy, syllogism score |
| Forgetting | FG% / BWT trên năng lực chung |
| Compliance | % trả lời dùng luật còn hiệu lực; tránh luật bãi bỏ |
| Unlearning | USR↑, ROUGE-L↓, harmlessness (retain acc), TPR@1%FPR↓ |
| Cost | kích thước active index, độ trễ retrieval |

**Map giả thuyết ↔ chỉ số** (chi tiết ở `Research_Core/3_RQ_Hypotheses_Design.md`): H1↔(accuracy+index+Recall); H2↔(FG/BWT vs CPT/CFT); H3↔(USR/ROUGE-L/harmlessness/TPR).

---

## ❓ Câu hỏi có thể bị hỏi
**Q: Đề tài mới gì?** → kết hợp + thích ứng miền + đánh giá: legal importance + compliance gate; hợp nhất selective memory + unlearning + regime thời gian; benchmark legal-CL VN.
**Q: Sao không CPT cho thuộc luật?** → CPT quên + drift + khó unlearn; facts ở RAG, behavior ở LoRA.
**Q: LoRA cũng sửa weight, sao gọi frozen?** → **base frozen**; LoRA là adapter nhỏ *thêm vào*, tách rời được, học *behavior* không phải facts (xem 3.4).
**Q: Frozen base thì reasoning đủ không?** → giới hạn đã biết (GH1); LoRA + selective context giảm lỗi; nêu rõ ở Limitations.
