# 01 — Introduction (Hook → Problem → Goal)

## Slide 1 — Why legal LLMs, and why they break
- LLMs now power contract review, case-law retrieval, legal QA.
- But legal knowledge is **non-stationary**: statutes amended/replaced, precedents overturned, cases sealed.
- A static-weight model becomes outdated → **obsolete or wrong legal advice**.

💬 Kể: "AI pháp lý đang bùng nổ, nhưng luật thì đổi liên tục — model học xong là lỗi thời."
📌 `le-etal-2025-overview`, `nguyen2025vlqacomprehensivelargehighquality`

## Slide 2 — The dual challenge
- **Continuous update**: nạp luật/án lệ mới liên tục.
- **Intentional forgetting**: luật hết hiệu lực · sealed case · right-to-be-forgotten.
- Hai yêu cầu *ngược nhau* với cách học truyền thống.

💬 Kể: "Không chỉ phải nhớ thêm, mà còn phải **quên đúng thứ phải quên** — đây là điểm khó."

## Slide 2b — Why now (timeliness)
- **Adoption**: trợ lý pháp lý dựa trên LLM đang được dùng rộng.
- **Data mới**: VLQA / ViLegalNLI / LegalSLM vừa ra **2025–2026** → nền thực nghiệm sẵn sàng.
- **Quy định siết**: right-to-be-forgotten / data governance → nhu cầu "quên có kiểm soát" cấp thiết.

💬 Kể: "Đúng thời điểm — dữ liệu vừa có + quy định vừa siết → làm bây giờ là hợp lý nhất."
📌 `nguyen2025vlqa...`, `duong2026vilegalnli...`, `le-etal-2025-overview`

## Slide 3 — Why not just fine-tune (CPT/CIT)?
- Parametric continual learning ⇒ **catastrophic forgetting** (Inverse Linear Law).
- Weight updates tốn compute; **unlearning trên weight rất khó**, hại utility.

💬 Kể: "Dạy thẳng vào trọng số = vừa quên kiến thức cũ, vừa không gỡ được luật sai."
📌 `kalajdzievski2024scalinglawsforgettingfinetuning`, `li2024revisitingcatastrophicforgettinglarge`, `11207222`

## Slide 4 — The idea: hybrid, frozen-base
- **Freeze base LLM** → general reasoning intact.
- **Facts → external RAG memory** (cập nhật/unlearn = thao tác KB).
- **Behavior → LoRA adapter** (IFT, không nhớ facts).
- + **Selective memory** (ưu tiên điều luật quan trọng) + **unlearning gate**.

💬 Kể: "Tách bạch: facts ra ngoài (RAG), kỹ năng lập luận vào LoRA, base giữ nguyên."

## Slide 5 — Objectives
- **General:** non-parametric RAG continual-learning framework cho QA/suy luận luật VN, base frozen.
- **Specific:** (1) legal importance-based **selective memory**; (2) **controllable unlearning** (P+Q); (3) **evaluation protocol** legal-CL VN.

## Slide 6 — Research questions & hypotheses
- **CQ1/H1 — Selective memory:** feature importance nào (citation/risk/temporal) giảm index + giữ accuracy?
- **CQ2/H2 — RAG vs parametric:** RAG có giảm forgetting chung + xử lý regime thời gian tốt hơn CPT/CIT? (đo bằng chuỗi tuần tự **lao động→thuế**, chỉ số Backward Transfer)
- **CQ3/H3 — Unlearning:** P+Q quên được tới đâu mà không hại truy vấn khác?

💬 Kể: mỗi câu hỏi gắn 1 giả thuyết **bác bỏ được** → nghiên cứu nghiêm túc.

## Slide 7 — Scope & contributions
- **Scope:** luật VN (bộ luật, nghị định, thông tư, án lệ); tasks QA/NLI/reasoning; không generation dài. Framework **domain-general, không bó vào ngành nào**; **kịch bản test lấy data từ** sub-domain biến động nhiều (lao động, thuế) để dựng ca update / forgetting / unlearning.
- **3 đóng góp:** (1) legal selective memory (mở rộng Ebbinghaus sang luật + compliance gate); (2) retrieval-level unlearning gate (P+Q); (3) benchmark/protocol legal-CL VN.
- **💡 Significance:** *KH* — nối CL + RAG + legal temporal regimes cho low-resource VN; *thực tiễn* — trợ lý pháp lý tuân thủ, độ trễ thấp.
📌 `nguyen2025vlqa...`, `duong2026vilegalnli...`, `le-etal-2025-overview`

---

# 02 — Background (Survey arc → Evidence → 4 Gaps)

## Slide 1 — The survey arc (2024 → 2026)
- 5 landmark surveys chart the field: stage-aligned (Wu) → dual-axis (Shi) → internal/external (Zheng) → cross-modality (Guo) → lifelong **agents** (2026).
- Consensus: lifecycle **CPT → CIT → CA**; stability–plasticity dilemma; shift **parametric → external (RAG)**.

💬 Kể: "Lĩnh vực tiến hoá dần về **bộ nhớ ngoài** — đúng hướng đề tài."
📌 `wu2024...`, `shi2024...`, `zheng2024lifelong...`, `guo2025...`, `11328884`

## Slide 2 — Catastrophic forgetting is a *law*, not a bug
- **Inverse Linear Law**: học mới càng sâu ⇒ quên cũ càng nhiều; giảm rank/early-stop **không cứu**.
- Hình học: OOD tuning → **sharp minima** → quên; SAM → flat minima.
- Decoder-only + SFT-buffer kháng quên tốt hơn.

💬 Kể: "Mọi tinh chỉnh trọng số đều quên theo *tỷ lệ* — nên ta đóng băng base."
📌 `kalajdzievski2024...`, `li2024revisiting...`, `luo2025empirical...`

## Slide 3 — Cheap stabilizers + benchmark evidence
- **Replay 0.25–1%** đủ giữ zero-shot (rẻ).
- **O-LoRA giảm Forgetting Gradient ~40×** so LoRA thường (FG ~0.77 vs ~42).
- Benchmark (TRACE/ConTinTin): safety bền, **reasoning dễ quên nhất**.

📌 `scialom-etal-2022-fine`; FG% từ `repec:axf:aidtaa:v:3:y:2026:i:1:p:52-61`; `wang2023trace...`, `yin-etal-2022-contintin`

## Slide 4 — CPT mechanics & scaling laws
- **Stability gap** (do optimizer) → cần **re-warming** LR.
- Replay theo shift: weak ~5% → **strong/đa ngôn ngữ 15–25%** (tiếng Việt = strong shift).
- **D-CPT Law**: tính được tỷ lệ trộn tối ưu; **Law domain r ≈ 0.64** (64% luật + 36% general), R²>0.97.

💬 Kể: "Nếu có pha pretrain luật, đã có công thức tính % trộn — nhưng đề tài chính vẫn frozen+RAG."
📌 `gupta2023...`, `ibrahim2024...`, `zheng-etal-2024-breaking`, `que2024dcpt...`

## Slide 5 — Semi-parametric & RAG memory/unlearning
- **ReGrad** (gradient bank, đối chiếu): Law 84.87%, **zero weight drift** — nhưng độ trễ cao.
- **LUFY**: quên 90% lượt ít quan trọng → **+17% retrieval** (cảm hứng importance + decay).
- **RAG-based unlearning** (Wang): **USR 99.8%, ROUGE-L 0.10, utility giữ 53.8%** vs gradient-ascent quên kém + hại utility.

💬 Kể: "Bằng chứng mạnh nhất cho hướng RAG: unlearn gần tuyệt đối **mà không hại** năng lực khác."
📌 `su2026retrievablegradients...`, `Sumida_2025`, `11207222`

## Slide 6 — Vietnamese legal resources & the reasoning gap
- **VLQA** (59k điều luật) = KB; **ViLegalNLI** (Qwen-2.5 90.72%); **LegalSLM**.
- Phát hiện then chốt: NLI/QA ~0.95 nhưng **syllogism chỉ ~0.5–0.6**; CPT giúp 4B nhưng **hại 1.7B**.

💬 Kể: "Truy xuất/phân loại tốt rồi, nhưng **lập luận tam đoạn luận còn yếu** — đây là khoảng trống."
📌 `nguyen2025vlqa...`, `duong2026vilegalnli...`, `le-etal-2025-overview`

## Slide 7 — Four research gaps (chốt chương → dẫn vào method)
1. **Low-resource VN** chưa được nghiên cứu CL đầy đủ.
2. **Controllable forgetting / right-to-be-forgotten** under-explored.
3. **Selective legal memory** (importance đặc thù luật) chưa ai làm.
4. Thiếu **benchmark legal-CL VN**.

💬 Kể: "4 khoảng trống này = 4 lý do tồn tại của đề tài."

---

# 03 — Proposed Methods (ĐI SÂU — phần lõi)

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
- Config: base instruct decoder-only **frozen (1–4B, ≤15B — low-resource)**, r=256/α=512, replay 0.5%, (tuỳ chọn SAM); học tuần tự **lao động→thuế** (cấu hình 2-task tối thiểu để *thấy* forgetting) → chống collision bằng O-LoRA/N-LoRA/SLIM.

💬 Kể: "LoRA dạy *cách lập luận pháp lý*, không nhồi luật vào trọng số — nên đổi luật không cần train lại."
📌 `biderman2024loralearnsforgets`, `hu2021lora...`, `wang-etal-2023-orthogonal`, `han-etal-2025-slim`

## Slide 7 — Data methodology pipeline (FIGURE)
- **Collection** → **Preprocessing/Indexing** (units+metadata+citation graph+embedding) → **Sampling** (topic-stratified tổng quát; **kịch bản test lấy data từ** sub-domain biến động nhiều — lao động/thuế; chuỗi tuần tự lao động→thuế đo Backward Transfer; forget↔retain matched; regime split; Scenario A/B/C) → **Analysis** (ablation ladder; metrics; test H1–H3; error analysis).
- Expert validate **5%** metadata (giảm rủi ro chính).

💬 Kể: chỉ figure pipeline, nhấn "chọn mẫu – thu thập – phân tích" đủ bộ ba.
📌 Figure `fig:data_pipeline` (3.1)

## Slide 8 — Why hybrid RAG+LoRA, not CPT/ReGrad (đánh đổi nói thẳng)
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

---

# 04 — Expected Outcomes & Plan

## Slide 1 — Deliverables ↔ acceptance thresholds (đo được)
- **Obj 1 — Selective memory module:** active index **giảm ≥30%** vs static RAG; **Recall@10 trong ±2%** so full-index.
- **Obj 2 — Unlearning gate (P+Q):** **ROUGE-L ≤0.15** trên forget set; **harmlessness ≥95%** baseline; **TPR ≤1% @1%FPR** dưới jailbreak.
- **Obj 3 — Evaluation suite:** chạy được trên **VLQA / ViLegalNLI / LegalSLM**; test scenarios **lấy data từ** sub-domain biến động nhiều (lao động, thuế); thêm eval **cross-domain tuần tự lao động→thuế** đo Backward Transfer (target near-zero factual forgetting vs parametric baseline).

💬 Kể: "Mỗi mục tiêu gắn ngưỡng số → nghiệm thu khách quan."
📌 `nguyen2025vlqa...`, `duong2026vilegalnli...`, `le-etal-2025-overview`

## Slide 2 — Academic & open-source outputs
- 1 hội nghị (PACLIC/KSE/VLSP) + 1 tạp chí ISI/Scopus.
- Hỗ trợ 1 luận văn ThS + 2 đồ án ĐH.
- GitHub: code + eval scripts + metadata-augmented index (tái lập được).

## Slide 3 — Beneficiaries (Who)
- **Cộng đồng NLP/AI pháp lý:** benchmark + metric chuẩn để so các retrieval/memory paradigm.
- **Lập trình hệ thống pháp lý:** tái dùng selective memory + unlearning gate (giảm chi phí đảm bảo compliance).
- **Cơ quan tuân thủ/bảo mật:** ngân hàng, toà án, công ty luật — đảm bảo không rò rỉ/không viện dẫn thông tin mật hoặc luật hết hiệu lực.

## Slide 4 — Work packages & timeline (6 tháng)
- **WP1 Selective RAG memory** (crawl/parse/index, citation graph, importance + decay) — crawl **ưu tiên** sub-domain biến động nhiều (lao động, thuế) cung cấp kịch bản test; **pipeline domain-general**.
- **WP2 Unlearning gate** (P+Q, compliance filter, audit log).
- **WP3 Benchmark & validation** (static-RAG baseline, ablation, VLQA/ViLegalNLI/LegalSLM).
- 8 milestone theo tuần (corpus W3 → KB W5 → baseline W9 → modules W12 → tuning W15 → unlearning W18 → benchmark W21 → report W24).

💬 Kể: dependency WP1→WP3, WP2→WP3.

## Slide 5 — Budget & risks
- Tổng ~**1,25 tỷ VND (~$50k)**: nhân sự / compute (A100 thuê + workstation) / dữ liệu+expert validation / công bố.
- **Rủi ro chính:** metadata sai (→ double-pass verify 5%); retrieval recall (→ hybrid dense+BM25); compute (→ pre-compute embedding, Bayesian search); access-control leakage (→ automated audit).

💬 Kể: nhấn rủi ro metadata = rủi ro số 1, đã có biện pháp.

## Slide 6 — Closing (3 câu hỏi proposal phải trả lời)
- **Why needed:** luật biến động + phải "quên có chủ đích"; parametric → quên thảm khốc / khó unlearn.
- **Why now:** data VN mới (2025–2026) + quy định siết → nền thực nghiệm + nhu cầu hội tụ.
- **Why me:** đã làm **khóa luận về RAG** (miền thông tin trường ĐH) → nắm nền tảng, nay chuyển/mở rộng sang **miền luật** (khó hơn), dưới hướng dẫn GV chuyên NLP, dùng dataset VN công khai.
- **Value:** no factual (weight) drift + compliance-aware forgetting + reproducible protocol — novel về KH và có giá trị thực tiễn cho legal-AI VN.

💬 Kể: slide chốt — đọc 4 gạch này là hội đồng nắm trọn giá trị + thấy bạn đủ năng lực.
🔖 Liêm chính: AI chỉ hỗ trợ tìm/cấu trúc/ngôn ngữ; ý tưởng + framework + phân tích là của tác giả (xem Declaration).

---

# 05 — Talking Points & Anticipated Q&A

## Slide 1 — Anticipated Q&A 1: "RAG đã có rồi — xoá index đâu phải mới? Phần nào là nghiên cứu?"
- Không claim RAG mới. Xoá index/ACL/audit = **code, không nhận là đóng góp**.
- Temporal phải **giữ-mà-chặn**; xoá thật thì model **vẫn lộ từ trọng số** → em **đo rò rỉ**.
- Nghiên cứu = 3 câu hỏi bác bỏ được: nén index (H1) · forgetting (H2) · leakage (H3).

💬 Kể: "Xóa index hay ACL chỉ là code. Điểm nghiên cứu là đo và chặn rò rỉ parametric thông qua khung unlearning P+Q."

## Slide 2 — Anticipated Q&A 2: "Model nhỏ 1–4B đủ suy luận luật không? 6 tháng kịp không?"
- Em **không đặt cược vào suy luận của base** — facts ở ngoài nên không cần model lớn (đúng luận điểm).
- Baseline parametric chạy **cùng cỡ nhỏ** ⇒ chênh lệch do **cơ chế**, không do model.
- Khả thi 6 tháng: base frozen, kịch bản test tập trung sub-domain biến động nhiều.

💬 Kể: "Facts để ngoài RAG giúp giảm tải cho trọng số, dùng model 1-4B vẫn khả thi và tiết kiệm compute."
