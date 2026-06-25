# TRỤC ĐỀ TÀI (bản chốt hướng) — RAG chống QUÊN THẢM KHỐC, đo trên QA/NLI/Syllogism

> **Quyết định (2026-06-24):** Đề tài **vẫn về catastrophic forgetting** (giữ tên). Khuôn thí nghiệm lấy cảm hứng **Bảng B của ReGrad** (nạp tri thức liên tục → đo quên), nhưng phương pháp là **RAG phi-tham số** (rẻ hơn ReGrad, cập nhật/unlearn được), đo trên **3 task lõi: QA · NLI · Syllogism**.
> - **Selective memory** = chống "quên kiểu RAG" (kho phình → nhiễu → tụt recall) → có vai trò rõ.
> - **Unlearning** = temporal gating khi nạp luật mới thay luật cũ (gắn vào "nạp liên tục"), không phải trục riêng.

---

## 1. One-liner
> *Khi tri thức pháp lý được **nạp liên tục**, tinh chỉnh trọng số (parametric) **quên thảm khốc**; đề tài dùng **RAG phi-tham số** (base đóng băng + kho chọn lọc) để giữ tri thức mà không quên, và đo hiệu quả trên **ba tác vụ QA, NLI, tam đoạn luận** — soi xem retrieval cứu được task nào, chạm trần lập luận ở đâu.*

## 2. Khuôn từ ReGrad (Group5/paper7) — Bảng B
Nạp liên tục 2.5K→10K văn bản (LLaMA-8B):
| Nạp | CPT | ReGrad |
|---|---|---|
| 2.5K | 30.73 | 37.71 |
| 5K | 31.41 | 39.64 |
| 7.5K | 31.87 | 42.60 |
| **10K** | **27.80 ⬇** | **44.05 ⬆** |

→ CPT nạp nhiều = **sụp (quên thảm khốc)**; ReGrad né bằng **Gradient Bank** (bán-tham số, θ_q=θ*−α*⊙g(q), hủy sau khi sinh). **Đề tài đi xa hơn: RAG thuần** (kho văn bản/vector) — rẻ, cập nhật/unlearn tức thì.

## 3. Bốn cách tiếp cận khi NẠP LIÊN TỤC (arms)
| Arm | Hành vi khi kho/tri thức lớn dần | Cơ chế |
|---|---|---|
| **CPT/CIT** (parametric) | **quên thảm khốc** (perf sụp) | weight drift |
| **ReGrad** (bán-tham số, đối chiếu) | không drift, **đắt** (gradient bank, phụ thuộc retriever, lưu trữ lớn) | gradient tạm |
| **Vanilla RAG** (frozen + kho phẳng) | không quên trọng số; **kho phình → nhiễu → recall tụt** | external memory |
| **Selective RAG** ⭐ (importance+decay+compliance) | giữ recall sắc dù kho lớn | bộ nhớ chọn lọc |

## 4. Câu hỏi & giả thuyết
**RQ0:** Khi tri thức luật nạp liên tục, cách nào tránh quên thảm khốc trên QA/NLI/Syllogism, và kho **chọn lọc** có giữ retrieval sắc ở chỗ kho **phẳng** bị nhiễu không?

- **H1 (forgetting):** CPT **quên thảm khốc** khi nạp nhiều (perf sụp, BWT âm mạnh) — như Bảng B.
- **H2 (RAG né quên):** RAG frozen **không quên trọng số**; perf ổn định/tăng theo lượng tri thức.
- **H3 (selective cứu RAG):** vanilla RAG **tụt recall khi kho phình**; selective RAG giữ recall (index ↓ nhiễu) → đây là chỗ selective memory sống.
- **H4 (trần lập luận):** cải thiện **phụ thuộc task** — mạnh ở QA, vừa ở NLI, **yếu ở Syllogism** (retrieval không thay được suy luận của base nhỏ/frozen).

## 5. RAG đóng vai gì ở mỗi task (đo cái gì)
| Task | Dataset | RAG cấp | Model tự lo | Metric |
|---|---|---|---|---|
| **QA** | VLQA (59k điều) | điều luật liên quan | tổng hợp + trích dẫn | Recall@K, MRR, EM/F1 |
| **NLI** | ViLegalNLI (bỏ lexical shortcut) | điều luật làm bằng chứng | phán đoán entail/contradict/neutral | Acc, F1 |
| **Syllogism** | LegalSLM | đại tiền đề (rule) | dựng tiểu tiền đề + kết luận | syllogism score |

## 6. Thiết kế thí nghiệm (ma trận chính)
**Trục nạp liên tục** (snapshot kho ở nhiều mức, vd 25/50/75/100% tri thức) × **4 arm (CPT/ReGrad/Vanilla-RAG/Selective-RAG)** × **3 task**.
- Vẽ **đường perf theo lượng tri thức nạp** (giống Bảng B) cho từng task → thấy CPT sụp, RAG phẳng/tăng.
- Đo **forgetting**: Backward Transfer + độ tụt ở mức nạp cao nhất.
- Đo **selective vs vanilla**: kích thước index + Recall@K khi kho lớn dần.
- Vẽ **Δ cải thiện theo độ khó task** (QA→NLI→Syll) → trần lập luận.

## 7. "2 cái RAG" (cách bạn nói) = 2 phần nghiên cứu
1. **Xây kho RAG** (thu thập → đơn vị + metadata → embedding + citation graph → selective memory) — *tương tự ReGrad xây Gradient Bank, nhưng phi-tham số.*
2. **Đo forgetting của RAG trên 3 task** — đường nạp-liên-tục, so với CPT (quên) và ReGrad (bán-tham số).

## 8. Các mảnh cũ tổ chức lại
- **Selective memory** → **chống quên kiểu RAG** (vai trò rõ trong H3), vẫn là module nhưng gắn thẳng vào mạch forgetting.
- **ReGrad** → đối chiếu bán-tham số (2.5 đã có; có thể nâng thành arm đo thật).
- **Unlearning** → **temporal gating** khi supersession (luật mới thay cũ) trong lúc nạp liên tục — phần phụ, không phải trục.
- **Lit review Ch2** → giữ gần nguyên (đã có Bảng FG forgetting, ReGrad Bảng, LegalSLM syllogism ~0.5 = hook).

## 9. Nếu duyệt → tex cần sửa
- `1_2`/`1_3`: RQ0 + H1–H4 (forgetting + task + selective).
- `3_1` Experimental Design: ma trận nạp-liên-tục × 4 arm × 3 task.
- `4_1` Expected Results: đường forgetting + Δ theo độ khó.
- `2_5` ReGrad: nâng thành arm đối chiếu (Bảng B).
- `3_2` Selective memory: nối rõ "chống tụt recall khi kho phình".
- Tên đề tài + Abstract: **GIỮ** (vẫn catastrophic forgetting).
