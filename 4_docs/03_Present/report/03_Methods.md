# 03 — Proposed Methods (ĐI SÂU — phần lõi)

> Đây là trọng tâm bài. Không nói sơ — mỗi module nêu **cơ chế + công thức + lý do thiết kế + đánh đổi**.

## Slide 1 — Core design statement (thuộc lòng)
- **Base LLM frozen.** Facts → **non-parametric RAG**; behavior → **LoRA**; learn/forget facts = sửa KB/policy, **không đụng weight base**.
- 3 hệ quả: (1) không weight drift / không quên chung; (2) cập nhật luật tức thì; (3) **unlearning là thao tác bậc nhất**.

💬 Kể: "Một câu định hình cả chương: đóng băng base, tách facts ra ngoài."

## Slide 2 — Overall hybrid architecture (FIGURE đắt)
- Offline: corpus → đơn vị tri thức + metadata → citation graph + embedding → **KB**; + train **LoRA (IFT)**.
- Online: query → retriever → **unlearning gate (P)** → **selective memory + compliance gate** → context → **base+LoRA + ràng buộc Q** → answer + audit.
- Thứ tự khoá: **P → compliance gate → selective re-rank → reasoning+Q**.

💬 Kể: chỉ vào sơ đồ, đi theo dòng dữ liệu 1 lần.
📌 Figure `fig:overall_architecture` (3.1)

## Slide 3 — Knowledge unit & metadata (vì sao "đặc thù luật")
- $u_i = \langle e_i, v_i, [t_{start},t_{end}], c_i, \rho_i, H_i, acl_i\rangle$.
- **v** (hiệu lực) = hard gate; **[t]** = regime thời gian; **c** = citation centrality; **ρ** = rủi ro/thứ bậc; **H** = lịch sử truy vấn; **acl** = phân quyền.

💬 Kể: "Mỗi điều luật là một đơn vị có *hiệu lực + thời gian + vị trí trong mạng trích dẫn* — đây là cái RAG generic không có."

## Slide 4 — Module 1: Selective Legal Memory (cơ chế)
- **Importance:** $I_i = w_1\hat{c_i} + w_2 v_i + w_3\rho_i + w_4\,g(H_i,t)$ (rule-based *hoặc* LLM-estimated → ablation).
- **Decay (Ebbinghaus):** $g(H_i,t)=\sum_{\tau<t} e^{-\lambda(t-\tau)}$; truy vấn lặp → consolidate.
- **Tiers:** Active top-k / Archive (down-rank, **không xoá**).
- **Compliance hard-gate:** $v_i{=}1$ & khớp ⇒ **luôn giữ** dù $I_i$ thấp; re-rank: $S=\mathrm{sim}+\beta I_i$.

💬 Kể (góc sâu): "Forgetting ở đây là **giảm nhiễu index**, KHÔNG giảm coverage — validity là *bất biến cứng*, importance chỉ xếp hạng trong tập hợp lệ."
📌 cảm hứng `Sumida_2025`; Algorithm `alg:selective`

## Slide 5 — Module 2: Controllable Unlearning (P+Q)
- 3 loại forget: **Temporal gating** (luật hết hiệu lực, vẫn trả lời "tại 20XX") · **Access-control** (sealed, theo `acl`) · **Index deletion** (personal data).
- **P (retrieval):** $KB_T=\{u\notin T \wedge \mathrm{Authorize}(u,q,t_q)\}$ — entry cấm không vào candidate.
- **Q (output):** ràng buộc prompt không viện dẫn/không tiết lộ $T$ (chặn rò rỉ parametric).
- Filter đầu ra + **immutable audit log** (giải trình pháp lý; đảo ngược được trừ hard-delete).

💬 Kể (góc sâu + trung thực): "Đây là **behavioral unlearning** — đảm bảo *không truy xuất/không trích dẫn*, **không** claim xoá khỏi weight; phần rò rỉ được **đo** bằng TPR@1%FPR."
📌 `11207222` (USR 99.8%, utility giữ)

## Slide 6 — Module 3: LoRA behavioural adapter (vì sao cần)
- Phân vai: **RAG = dùng điều luật nào; LoRA = lập luận thế nào** (syllogism: đại–tiểu tiền đề–kết luận, trích dẫn, tuân ràng buộc Q).
- **Vì sao behavior chứ không facts:** SVD — CPT cần rank ~2000, **IFT chỉ ≤32** ⇒ LoRA (r=256) hợp IFT, không đủ nhớ facts.
- Config: base instruct decoder-only frozen, r=256/α=512, replay 0.5%, (tuỳ chọn SAM); học tuần tự **lao động→thuế** (cấu hình 2-task tối thiểu để *thấy* forgetting) → chống collision bằng O-LoRA/N-LoRA/SLIM.

💬 Kể: "LoRA dạy *cách lập luận pháp lý*, không nhồi luật vào trọng số — nên đổi luật không cần train lại."
📌 `biderman2024loralearnsforgets`, `hu2021lora...`, `wang-etal-2023-orthogonal`, `han-etal-2025-slim`

## Slide 7 — Data methodology pipeline (FIGURE)
- **Collection** → **Preprocessing/Indexing** (units+metadata+citation graph+embedding) → **Sampling** (topic-stratified tổng quát; **kịch bản test lấy data từ** sub-domain biến động nhiều — lao động/thuế; chuỗi tuần tự lao động→thuế đo Backward Transfer; forget↔retain matched; regime split; Scenario A/B/C) → **Analysis** (ablation ladder; metrics; test H1–H3; error analysis).
- Expert validate **5%** metadata (giảm rủi ro chính).

💬 Kể: chỉ figure pipeline, nhấn "chọn mẫu – thu thập – phân tích" đủ bộ ba.
📌 Figure `fig:data_pipeline` (3.1)

## Slide 8 — Why hybrid RAG+LoRA, not CPT/ReGrad (đánh đổi nói thẳng)
| | CPT/CIT | ReGrad | **RAG+LoRA (đề tài)** |
|---|---|---|---|
| Quên chung | nặng | tránh | **không (frozen)** |
| Cập nhật luật | train lại | thêm gradient | **sửa KB tức thì** |
| Unlearn | rất khó | khó | **gỡ entry — dễ** |
| Độ trễ | nền | **cao** | nền+tra cứu |
| Reasoning | trong weight | trong weight | **base+LoRA (bị chặn bởi base)** ⚠️ |

💬 Kể (tạo độ tin): "Đánh đổi thật: frozen ⇒ reasoning bị chặn bởi base. Ta giảm thiểu bằng LoRA + context chất lượng, và **nêu rõ ở Limitations**."
📌 Table `tab:methodology_comparison` (3.1)

## Slide 9 — Continual knowledge update (giữ retrieval luôn tươi)
- Vì sao cần: không update ⇒ luật cũ `v=1` (sai compliance), recall tụt, citation/importance lỗi thời.
- **Incremental pipeline:** crawl/event → upsert đơn vị mới → **supersession** (luật cũ `v=0` = temporal unlearning) → citation delta → **canary test** → commit / **rollback**.
- **Update và unlearning là một bộ máy**; base frozen ⇒ update rẻ, **không retrain**; LoRA train lại hiếm.
- **Future work (làm khi bắt đầu):** embedding-drift detection + crawl-scheduling/versioning (hướng C, chưa đi sâu).

💬 Kể: "Đây là phần 'continual' thật sự — cập nhật ở tầng KB; và nó dùng chung cơ chế với unlearning."
📌 Figure `fig:update_pipeline` (3.5); time-continual eval `li2025ticlm...`
