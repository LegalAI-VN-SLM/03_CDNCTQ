# 3.2 Selective RAG Memory Module  ↔ `3_Methods_Models/3_2_Selective_RAG_Memory.tex`

> Trụ method 1 — hiện thực lý thuyết 2.5. Gắn **CQ1 / H1**. | Nguồn: `Sumida2025_LUFY`, `Nguyen2025_VLQA`.

---

## 3.2.1 Trực giác (1 câu)
Không phải mọi điều luật/án lệ quan trọng như nhau với một truy vấn; ta **xếp hạng lại theo "importance pháp lý"** và **làm thưa bộ nhớ ít liên quan** để giảm nhiễu context — **nhưng tuyệt đối không bỏ sót luật còn hiệu lực**.

## 3.2.2 Importance score (bán hình thức)

Với đơn vị u_i, importance:

```
I_i = w1·norm(c_i)        # citation centrality (mức được dẫn chiếu)
    + w2·v_i              # còn hiệu lực (0/1)
    + w3·ρ_i              # mức rủi ro / áp dụng
    + w4·g(H_i, t)        # tần suất truy vấn có suy giảm theo thời gian
```

**Hai phương án ước lượng** (sẽ ablation để chứng minh, không ad hoc):
- *Rule-based*: trọng số w₁…w₄ **tinh chỉnh trên validation set** theo retrieval metric (Recall@K, MRR@10 — `Nguyen2025_VLQA`).
- *LLM-estimated*: dùng LLM chấm importance (như 6-metric của `Sumida2025_LUFY`), so sánh với rule-based.

## 3.2.3 Consolidation / Decay (đường cong quên nhận thức)

Đóng góp tần suất suy giảm theo thời gian, nhưng **được củng cố** khi truy vấn lặp lại (giống Ebbinghaus trong `Sumida2025_LUFY`):

```
g(H_i, t) = Σ_{τ ∈ H_i, τ < t}  exp(−λ · (t − τ))
```
- Truy vấn gần → đóng góp lớn; lâu không tra → mờ dần (decay).
- λ = tốc độ quên (siêu tham số, tune).

## 3.2.4 Phân tầng bộ nhớ (memory tiers) — KHÔNG xoá
- **Active (long-term semantic memory):** top-k theo I_i, truy xuất nhanh, ưu tiên.
- **Archive (cold tier):** dưới ngưỡng θ → **down-rank, vẫn truy xuất được**, không xoá.
→ "Quên" ở đây = **giảm nhiễu/kích thước index hoạt động**, KHÔNG = mất coverage pháp lý.

## 3.2.5 Điểm truy xuất cuối & COMPLIANCE HARD-GATE (điểm bảo vệ then chốt)

```
candidates = Retrieve_topM(q)                       # theo sim(q, e_i)
# HARD GATE: mọi đơn vị còn hiệu lực & khớp query LUÔN được giữ
must_keep  = { u_i ∈ candidates : v_i = 1 và khớp(q, u_i) }
# Re-rank phần còn lại theo importance
S(q, u_i)  = sim(q, e_i) + β·I_i
context    = must_keep ∪ Top_k( candidates \ must_keep theo S )
```

> **Validity là HARD gate; importance chỉ re-rank.** Điều luật còn hiệu lực không bao giờ bị decay loại bỏ → trả lời cho câu hỏi "decay làm mất luật quan trọng nhưng hiếm tra".

## 3.2.6 Pseudocode

```
function SELECTIVE_RETRIEVE(q, t_q):
    C = retriever.top_M(q)                      # ứng viên theo ngữ nghĩa
    C = unlearning_gate(C, q, t_q)              # 3.3: loại entry bị cấm (P)
    must = [u for u in C if u.v == 1 and matches(u, q)]   # compliance gate
    rest = [u for u in C if u not in must]
    for u in rest: u.score = sim(q,u.e) + β * importance(u, t_q)
    return must + top_k(rest by score)
```

## 3.2.7 Giả định & giới hạn
- Trọng số/score là **proxy** — citation centrality không hoàn hảo (điều bị cite nhiều ≠ luôn quan trọng) → giảm thiểu bằng kết hợp đa feature + ablation.
- Cần lịch sử truy vấn để học importance — khi chưa có người dùng thật, dùng query tổng hợp/log mô phỏng.

---

## ❓ Câu hỏi có thể bị hỏi (section này)

**Q: Decay có làm mất điều luật quan trọng nhưng ít tra cứu không?**
A: Không. **Validity là hard gate** (3.2.5): điều còn hiệu lực luôn giữ trong candidate dù I_i thấp; decay chỉ down-rank xuống archive *vẫn truy xuất được*, không xoá. Forgetting giảm nhiễu, không giảm coverage.

**Q: Trọng số importance w₁..w₄ lấy đâu ra, có phải tự nghĩ?**
A: Không ad hoc — tune trên validation theo Recall@K/MRR (rule-based) hoặc LLM-estimated; **ablation** đo đóng góp từng feature để minh chứng.

**Q: Citation centrality phản ánh tầm quan trọng pháp lý thật không?**
A: Chỉ là MỘT feature trong nhiều (kết hợp hiệu lực + rủi ro + tần suất). Là proxy có cơ sở (điều nền tảng hay được dẫn chiếu) nhưng không tuyệt đối → đã nêu ở giới hạn, có ablation kiểm chứng.

**Q: Khác gì re-ranking thông thường?**
A: Khác ở (1) feature **pháp lý đặc thù** (hiệu lực, citation graph, rủi ro), (2) **compliance hard-gate** theo hiệu lực, (3) cơ chế **consolidation/decay theo thời gian** cho miền luật — không chỉ là sim re-rank.
