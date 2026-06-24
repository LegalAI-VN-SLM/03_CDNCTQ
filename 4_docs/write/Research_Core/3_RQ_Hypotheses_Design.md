# 3. Research Questions, Hypotheses & Thiết kế kiểm chứng (sắc, falsifiable)

> Mục đích: mỗi giả thuyết phải **kiểm chứng được** và **bác bỏ được**. Hội đồng đánh giá cao giả thuyết nêu rõ *điều kiện bác bỏ* và *biến kiểm soát*. Đây là khung "đúng kiểu nghiên cứu".

---

## Khung chung cho mỗi giả thuyết
Mỗi H ghi: **IV** (biến độc lập – cái ta thay đổi) · **DV** (biến phụ thuộc – cái ta đo) · **Control** (giữ cố định) · **Hướng kỳ vọng** · **Điều kiện BÁC BỎ** (falsification) · **Confound cần khử**.

---

## RQ1 → H1: Selective legal memory (gắn NDNC4)

**RQ1:** Thước đo importance đặc thù luật (citation, hiệu lực, rủi ro) + decay có giúp RAG pháp lý **giảm nhiễu/kích thước memory** mà **không giảm độ chính xác và không vi phạm compliance** không?

**H1:** Importance-weighted selective memory + decay (có compliance hard-gate) **giữ hoặc cải thiện** accuracy QA/retrieval so với static RAG, đồng thời **giảm kích thước active index**.

| Thành phần | Cụ thể |
|---|---|
| IV | chính sách memory: {static RAG} vs {selective + decay + compliance-gate} |
| DV | Recall@K, MRR@10, QA accuracy; **kích thước active index**; **compliance rate** |
| Control | cùng base LLM, cùng KB, cùng retriever backbone, cùng prompt |
| Hướng kỳ vọng | accuracy(selective) ≥ accuracy(static) **và** index(selective) < index(static) **và** compliance không giảm |
| **Bác bỏ nếu** | selective làm **giảm** accuracy, **hoặc** làm tụt coverage luật còn hiệu lực (compliance ↓) |
| Confound | sức mạnh retriever (khử bằng cố định backbone); rò rỉ test→train (decontaminate) |

Ablation: bỏ từng feature importance (citation / hiệu lực / rủi ro); quét λ (decay); bật/tắt compliance-gate.

---

## RQ2 → H2: Frozen-RAG vs parametric về sự quên (gắn NDNC6, đối chiếu CPT/CFT)

**RQ2:** So với CPT/CFT trên cùng dữ liệu luật, frozen-RAG có **giảm quên năng lực tổng quát** không, và đánh đổi accuracy luật ra sao?

**H2:** Frozen-RAG cho **forgetting năng lực chung ≈ 0**, thấp hơn rõ rệt CPT/CFT; nhưng (nuance cần nói trước) có thể **kém hơn ở chiều sâu suy luận** do "learns less".

| Thành phần | Cụ thể |
|---|---|
| IV | phương pháp đưa tri thức luật: {frozen-RAG} vs {CPT} vs {CFT/LoRA} |
| DV | **forgetting** năng lực chung (FGT/BWT trên benchmark tổng quát) + **legal accuracy** (VLQA/ViLegalNLI/LegalSLM) |
| Control | cùng base LLM khởi điểm, cùng corpus luật, cùng tập đánh giá |
| Hướng kỳ vọng | FGT(RAG) ≪ FGT(CPT,CFT); legal-acc(RAG) cạnh tranh nhưng có thể thấp hơn ở syllogism |
| **Bác bỏ nếu** | RAG **cũng** quên năng lực chung tương đương CPT/CFT, **hoặc** CPT/CFT **không** quên đáng kể |
| Confound | "lượng tri thức luật thực sự dùng được" — đo legal-acc song song để không nhầm "quên ít" với "học ít" (`Biderman2024_LoRALearnsLess`) |

> Đây là điểm trung thực then chốt: H2 **không** nói RAG thắng tuyệt đối — nó nói RAG **đổi quên-chung lấy chiều-sâu-suy-luận**. Nêu trước để không bị "bắt bài".

---

## RQ3 → H3: Retrieval-level unlearning có đạt tiêu chí compliance? (gắn NDNC5)

**RQ3:** Mô hình hoá unlearning thành ràng buộc P+Q trên KB (base đóng băng) có đạt **effectiveness + harmlessness + robustness** tương đương/tốt hơn model-level unlearning với **chi phí thấp hơn** không?

**H3:** P+Q retrieval-level unlearning **quên hiệu quả** target (USR/ROUGE-L↓) **mà không hại** retain set (harmlessness giữ), chi phí thấp, ở mức **behavioral** (không đảm bảo xoá parametric — đo phần rò rỉ).

| Thành phần | Cụ thể |
|---|---|
| IV | cơ chế unlearning: {none} vs {P only} vs {P+Q} vs {model-level baseline nếu khả thi} |
| DV | **effectiveness** (USR, ROUGE-L↓ trên forget set); **harmlessness** (accuracy retain set không đổi); **robustness** (TPR@1%FPR, HOP dưới paraphrase/jailbreak); **cost** |
| Control | cùng KB, cùng base, cùng forget/retain split |
| Hướng kỳ vọng | forget↓ mạnh, retain không đổi, robustness chấp nhận được, cost ≪ model-level |
| **Bác bỏ nếu** | quên không đạt, **hoặc** retain set bị hại (harmlessness ↓), **hoặc** dễ jailbreak khôi phục |
| Confound | **rò rỉ parametric** (base "nhớ" sẵn) → **đo và báo cáo như cận trên**, không che giấu |

Ablation: P-only vs P+Q (đo đóng góp ràng buộc Q); temporal-gate (quên hiện hành nhưng giữ trả lời "tại 20XX").

---

## Bảng tổng: Mục tiêu × RQ × H × NDNC × Metric chính

| MT | RQ | H | NDNC | Metric quyết định |
|---|---|---|---|---|
| MT1 | RQ1 | H1 | NDNC4 | Recall@K, MRR, index size, compliance rate |
| MT3 | RQ2 | H2 | NDNC6 | FGT/BWT (general) + legal accuracy |
| MT2 | RQ3 | H3 | NDNC5 | USR/ROUGE-L↓, harmlessness, TPR@1%FPR |

## Diễn giải kết quả NULL (sự trưởng thành nghiên cứu — nên chuẩn bị)
- Nếu **H1 null** (selective không hơn static): vẫn có giá trị — chứng minh importance pháp lý chưa đủ tín hiệu → hướng nghiên cứu feature tốt hơn.
- Nếu **H2 cho thấy RAG kém legal-acc**: khẳng định đánh đổi "learns less" → biện luận cho hybrid trong future work.
- Nếu **H3 robustness yếu** (rò rỉ parametric cao): định lượng được giới hạn của retrieval-level unlearning → đóng góp cảnh báo cho cộng đồng.

> Thông điệp bảo vệ: "Mỗi giả thuyết đều **bác bỏ được**; kể cả kết quả null cũng cho kết luận khoa học." — đây là dấu hiệu nghiên cứu nghiêm túc.
