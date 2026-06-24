# 3.1 Research Plan and Overall Framework  ↔ `3_Methods_Models/3_1_Research_Plan_and_Overall_Framework.tex`

> Vai trò: khung tổng thể RAG bán-tham-số; định vị 2 trụ method (3.2 Selective Memory, 3.3 Unlearning). | Nguồn: `Zheng2026_AgentLifelongSurvey`, `Sumida2025_LUFY`, `Su2026_ReGrad` (đối chiếu), `Nguyen2025_VLQA`.

---

## 3.1.1 Tuyên bố thiết kế cốt lõi (cần thuộc lòng)

> **Base LLM ĐÓNG BĂNG. Mọi tri thức pháp lý sống trong Knowledge Base (KB) phi tham số. Mọi thao tác "học / quên" = sửa KB hoặc chính sách truy xuất, KHÔNG đụng trọng số.**

Hệ quả (đây là 3 lý do bảo vệ chính):
1. **Không weight drift / không catastrophic forgetting** năng lực tổng quát — vì không cập nhật trọng số (đối lập CPT/CFT, xem `Kalajdzievski2024_ScalingForgetting`, `Gupta2023_CPT_Warmup`).
2. **Cập nhật luật tức thì, rẻ** — thêm/sửa/xoá entry trong KB, không cần train lại.
3. **Quên có chủ đích là thao tác bậc nhất** — gỡ/ẩn entry + ràng buộc đầu ra, thay vì gradient ascent đắt đỏ trên weight.

## 3.1.2 Đơn vị tri thức & lược đồ metadata

KB = {u₁, …, u_N}. Mỗi **đơn vị tri thức** u_i (điều/khoản luật hoặc án lệ) gắn:

| Ký hiệu | Trường | Mô tả | Dùng cho |
|---------|--------|-------|----------|
| e_i | embedding | vector ngữ nghĩa | retrieval |
| v_i | validity flag ∈ {0,1} | còn hiệu lực? | **compliance gate** (3.2), unlearning (3.3) |
| [t_start, t_end] | khoảng hiệu lực | ngày có/hết hiệu lực | xử lý **regime theo thời gian** |
| c_i | citation centrality | mức được dẫn chiếu trong citation graph | importance (3.2) |
| ρ_i | risk/applicability | mức rủi ro/áp dụng | importance (3.2) |
| H_i | access history | lịch sử truy vấn (timestamps) | consolidation/decay (3.2) |
| acl_i | access control | công khai / hạn chế | unlearning sealed (3.3) |

## 3.1.3 Kiến trúc & luồng dữ liệu

```
                 ┌─────────────────────── OFFLINE / INDEXING ───────────────────────┐
  Văn bản luật → │ Chuẩn hoá đơn vị → Gán metadata (v, [t], c, ρ, acl) → Citation    │
  + án lệ        │ graph → Embedding → KB có metadata                                  │
                 └────────────────────────────────────────────────────────────────────┘
                                              │
  ┌──────────────────────────── ONLINE / INFERENCE ─────────────────────────────────┐
  │ Query q (+ mốc thời gian t_q tuỳ chọn)                                            │
  │     │                                                                            │
  │     ▼                                                                            │
  │ [Retriever] sim(q, e_i)                                                           │
  │     │                                                                            │
  │     ▼                                                                            │
  │ [3.3 Unlearning gate] ── loại/ẩn entry theo forget-policy (P) ───────────────┐   │
  │     │                                                                        │   │
  │     ▼                                                                        │   │
  │ [3.2 Selective Memory] re-rank theo importance I_i + compliance hard-gate    │   │
  │     │                          (giữ luật còn hiệu lực dù I thấp)             │   │
  │     ▼                                                                        │   │
  │ Top-k context ──► [Frozen LLM + reasoning prompt + ràng buộc Q] ──► Câu trả lời │
  │                                                                  (+ audit log) │
  └────────────────────────────────────────────────────────────────────────────────┘
```

Thứ tự quan trọng: **Unlearning gate (P) chạy trước** để entry cấm không bao giờ vào candidate set; **Selective re-rank** chạy sau trên tập hợp lệ; **ràng buộc Q** áp ở bước sinh.

## 3.1.4 Vì sao RAG-centric, không phải ReGrad/parametric (luận cứ thiết kế)

| Tiêu chí | CPT/CFT (parametric) | ReGrad (gradient bank, bán-tham-số tạm) | **RAG (đề tài)** |
|----------|----------------------|------------------------------------------|------------------|
| Weight drift / quên chung | Có (nặng) | Tránh được | **Không (frozen)** |
| Cập nhật luật | train lại | thêm gradient | **sửa KB tức thì** |
| Unlearn | rất khó | khó | **gỡ entry — dễ** |
| Độ trễ inference | nền | **cao** (áp gradient runtime) | nền + tra cứu |
| Chiều sâu suy luận nội tại | cao | cao | **bị chặn bởi base LLM** ⚠️ |

→ Đề tài ưu tiên *cập nhật liên tục + quên có chủ đích + chi phí thấp*, nên RAG là lựa chọn nhất quán. ReGrad giữ ở 2.4 làm **đối chiếu**, không phải method.

## 3.1.5 Giả định & giới hạn (chủ động nêu để không bị hỏi bất ngờ)
- (GĐ1) Chất lượng metadata hiệu lực đủ tin cậy — **rủi ro chính**, ảnh hưởng compliance.
- (GĐ2) Retriever đủ tốt để đưa đúng điều luật vào context (lỗi retrieval → lỗi câu trả lời).
- (GH1) Năng lực suy luận pháp lý bị chặn bởi base LLM (xem `Le2025_LegalSLM`: SLM tụt ~50% ở tam đoạn luận).
- (GH2) Unlearning kiểm soát **grounding/trích dẫn**, không đảm bảo xoá tri thức trong weight (xem 3.3 + 3_0).

---

## ❓ Câu hỏi có thể bị hỏi (section này)

**Q: Đề tài có gì mới khi RAG + unlearning + selective memory đều đã có người làm?**
A: Đóng góp là **kết hợp + thích ứng miền + đánh giá**, không claim thuật toán RAG mới: (1) chuyển importance/forgetting từ hội thoại (LUFY) sang **đơn vị pháp lý** với importance đặc thù (citation + hiệu lực + rủi ro) và **compliance gate theo hiệu lực**; (2) hợp nhất selective memory + unlearning trong một khung, xử lý **regime pháp lý theo thời gian**; (3) bộ **metric/benchmark legal-CL tiếng Việt**.

**Q: RAG chỉ là chắp vá, sao không dạy hẳn model thuộc luật?**
A: CPT/CFT gây quên năng lực chung + weight drift (dẫn `Kalajdzievski2024_ScalingForgetting`), tốn compute, và *không thể unlearn* khi luật đổi/sealed. Mục tiêu đề tài là cập nhật liên tục + quên có chủ đích → RAG (semi-parametric có chủ đích) hợp hơn, không phải chắp vá.

**Q: Frozen LLM thì reasoning có đủ cho luật không?**
A: Đây là giới hạn đã biết (GH1). Selective RAG cải thiện *chất lượng context* (đúng điều, đúng hiệu lực) nên giảm lỗi do retrieve sai; nhưng reasoning bị chặn bởi base → chọn base instruct decoder-only đủ mạnh + reasoning-augmented prompting, và nêu rõ ở Limitations. Không over-claim.
