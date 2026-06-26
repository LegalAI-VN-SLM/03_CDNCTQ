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
- + **Selective memory** (ưu tiên điều luật quan trọng) + **unlearning gate** (phụ).

💬 Kể: "Tách bạch: facts ra ngoài (RAG), kỹ năng lập luận vào LoRA, base giữ nguyên."

## Slide 5 — Objectives
- **General:** dùng **RAG phi-tham số (base frozen)** chống quên thảm khốc khi nạp tri thức luật liên tục, đo trên QA/NLI/Syllogism.
- **Specific:** (1) **xây kho RAG chọn lọc** (importance + decay + compliance — như ReGrad xây bank nhưng phi-tham số); (2) **đo forgetting khi nạp liên tục** (RAG vs CPT vs ReGrad × 3 task); (3) **soi trần lập luận** (retrieval cứu QA/NLI tới đâu, có vượt syllogism không).

## Slide 6 — Research questions & hypotheses
- **H1 — Forgetting:** CPT nạp nhiều ⇒ **quên thảm khốc** (sụp như Bảng B ReGrad). Đo: BWT, perf↓.
- **H2 — RAG né quên:** RAG base frozen **không quên trọng số**, perf ổn định theo lượng nạp.
- **H3 — Selective cứu RAG:** kho phình → vanilla RAG tụt recall; **selective giữ recall** (index↓≥30%, ±2%).
- **H4 — Trần lập luận:** RAG mạnh ở **QA** > NLI > **Syllogism** (retrieval không thay được suy luận).

💬 Kể: mỗi giả thuyết **bác bỏ được bằng số** → nghiên cứu nghiêm túc. Khuôn lấy từ Bảng B của ReGrad (nạp liên tục → đo quên).

## Slide 7 — Scope & contributions
- **Scope:** luật VN; **3 task QA/NLI/Syllogism**; không generation dài. Framework **domain-general**; **kịch bản nạp liên tục lấy data từ** sub-domain biến động nhiều (lao động, thuế).
- **3 đóng góp:** (1) **kho RAG chọn lọc** chống "quên kiểu RAG"; (2) **bằng chứng định lượng** RAG né quên thảm khốc trên 3 task luật VN (đối chiếu CPT/ReGrad); (3) **phát hiện trần lập luận** — retrieval cứu QA/NLI nhưng chạm trần syllogism.
- **💡 Significance:** *KH* — nối CL + RAG + legal reasoning cho low-resource VN; *thực tiễn* — trợ lý pháp lý cập nhật được, độ trễ thấp.
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
- **ReGrad** (gradient bank, đối chiếu): Law 84.87%, **zero weight drift** — nhưng độ trễ cao; **Bảng B: nạp 10K → CPT sụp 27.80, ReGrad 44.05** (khuôn thí nghiệm forgetting).
- **LUFY**: quên 90% lượt ít quan trọng → **+17% retrieval** (cảm hứng importance + decay).
- **RAG-based unlearning** (Wang): **USR 99.8%, ROUGE-L 0.10, utility giữ 53.8%** vs gradient-ascent quên kém + hại utility.

💬 Kể: "Bảng B của ReGrad = khuôn đo quên thảm khốc khi nạp liên tục — đề tài đi xa hơn với RAG thuần."
📌 `su2026retrievablegradients...`, `Sumida_2025`, `11207222`

## Slide 6 — Vietnamese legal resources & the reasoning gap
- **VLQA** (59k điều luật) = KB; **ViLegalNLI** (Qwen-2.5 90.72%); **LegalSLM**.
- Phát hiện then chốt: NLI/QA ~0.95 nhưng **syllogism chỉ ~0.5–0.6**; CPT giúp 4B nhưng **hại 1.7B**.

💬 Kể: "Truy xuất/phân loại tốt rồi, nhưng **lập luận tam đoạn luận còn yếu** — đây là khoảng trống (= hook H4)."
📌 `nguyen2025vlqa...`, `duong2026vilegalnli...`, `le-etal-2025-overview`

## Slide 7 — Four research gaps (chốt chương → dẫn vào method)
1. **Low-resource VN** chưa được nghiên cứu CL đầy đủ.
2. **RAG chống quên thảm khốc khi nạp liên tục** chưa đo trên luật VN.
3. **Selective legal memory** (importance đặc thù luật) chưa ai làm.
4. Thiếu **benchmark legal-CL VN** trên 3 task (đặc biệt syllogism).

💬 Kể: "4 khoảng trống này = 4 lý do tồn tại của đề tài."

---

# 03 — Proposed Methods (ĐI SÂU — phần lõi)

## Slide 1 — Core design statement (thuộc lòng)
- **Base LLM frozen.** Facts → **non-parametric RAG**; behavior → **LoRA**; nạp/sửa facts = sửa KB, **không đụng weight base**.
- 3 hệ quả: (1) **không weight drift / không quên thảm khốc** dù nạp liên tục; (2) cập nhật luật tức thì; (3) selective memory giữ recall khi kho phình.

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

💬 Kể (góc sâu): "Vai trò = **chống quên kiểu RAG**: kho nạp liên tục phình to → nhiễu → vanilla tụt recall; selective giữ recall mà validity vẫn là bất biến cứng."
📌 cảm hứng `Sumida_2025`; Algorithm `alg:selective`

## Slide 5 — (Phụ) Controllable Unlearning (P+Q) — temporal gating khi supersession
- 3 loại forget: **Temporal gating** (luật hết hiệu lực, vẫn trả lời "tại 20XX") · **Access-control** (sealed, theo `acl`) · **Index deletion** (personal data).
- **P (retrieval):** $KB_T=\{u\notin T \wedge \mathrm{Authorize}(u,q,t_q)\}$ — entry cấm không vào candidate.
- **Q (output):** ràng buộc prompt không viện dẫn/không tiết lộ $T$ (chặn rò rỉ parametric).
- Filter đầu ra + **immutable audit log** (giải trình pháp lý; đảo ngược được trừ hard-delete).

💬 Kể (trung thực): "Đây là phần **phụ** — temporal gating gắn vào lúc nạp luật mới thay luật cũ; **behavioral unlearning**, không claim xoá khỏi weight."
📌 `11207222` (USR 99.8%, utility giữ)

## Slide 6 — Module 3: LoRA behavioural adapter (vì sao cần)
- Phân vai: **RAG = dùng điều luật nào; LoRA = lập luận thế nào** (syllogism: đại–tiểu tiền đề–kết luận, trích dẫn, tuân ràng buộc Q).
- **Vì sao behavior chứ không facts:** SVD — CPT cần rank ~2000, **IFT chỉ ≤32** ⇒ LoRA (r=256) hợp IFT, không đủ nhớ facts.
- Config: base instruct decoder-only **frozen (1–4B, ≤15B — low-resource)**, r=256/α=512, replay 0.5%, (tuỳ chọn SAM); đa-ngành tuần tự → chống collision bằng O-LoRA/N-LoRA/SLIM.

💬 Kể: "LoRA dạy *cách lập luận pháp lý*, không nhồi luật vào trọng số — nên đổi luật không cần train lại."
📌 `biderman2024loralearnsforgets`, `hu2021lora...`, `wang-etal-2023-orthogonal`, `han-etal-2025-slim`

## Slide 7 — Data methodology pipeline (FIGURE)
- **Collection** → **Preprocessing/Indexing** (units+metadata+citation graph+embedding) → **Sampling** (topic-stratified tổng quát; **kịch bản nạp liên tục lấy data từ** sub-domain biến động nhiều — lao động/thuế; snapshot kho ở nhiều mức nạp; forget↔retain matched; regime split) → **Analysis** (ablation ladder; metrics; test H1–H4; error analysis).
- Expert validate **5%** metadata (giảm rủi ro chính).

💬 Kể: chỉ figure pipeline, nhấn "chọn mẫu – thu thập – phân tích" đủ bộ ba.
📌 Figure `fig:data_pipeline` (3.1)

## Slide 8 — Bốn arm khi NẠP LIÊN TỤC (khuôn Bảng B ReGrad) — đánh đổi nói thẳng
| | CPT/CIT | ReGrad | vanilla RAG | **selective RAG (đề tài)** |
|---|---|---|---|---|
| Khi kho/tri thức lớn dần | **quên thảm khốc** | tránh (đắt) | không quên weight, **nhiễu↑** | **giữ recall** |
| Cập nhật luật | train lại | thêm gradient | sửa KB | **sửa KB tức thì** |
| Chi phí | cao | **cao** (bank) | thấp | thấp |
| Reasoning | trong weight | trong weight | base | **base (bị chặn)** ⚠️ |

💬 Kể (tạo độ tin): "CPT nạp nhiều = **sụp như Bảng B**; RAG né quên nhưng kho phình thì **nhiễu** → selective cứu. Đánh đổi thật: frozen ⇒ reasoning bị chặn bởi base — **nêu rõ ở Limitations**."
📌 Table `tab:methodology_comparison` (3.1)

## Slide 9 — Continual knowledge update (giữ retrieval luôn tươi)
- Vì sao cần: không update ⇒ luật cũ `v=1` (sai compliance), recall tụt, citation/importance lỗi thời.
- **Incremental pipeline:** crawl/event → upsert đơn vị mới → **supersession** (luật cũ `v=0` = temporal unlearning) → citation delta → **canary test** → commit / **rollback**.
- **Update và unlearning là một bộ máy**; base frozen ⇒ update rẻ, **không retrain**; LoRA train lại hiếm.
- **Future work:** embedding-drift detection + crawl-scheduling/versioning (chưa đi sâu).

💬 Kể: "Đây là phần 'continual' thật sự — cập nhật ở tầng KB; và nó dùng chung cơ chế với unlearning."
📌 Figure `fig:update_pipeline` (3.5); time-continual eval `li2025ticlm...`

---

# 04 — Expected Outcomes & Plan

## Slide 1 — Kết quả mong đợi ↔ tiêu chí đo được
- **R1 — Đường forgetting (nạp liên tục):** CPT **sụp** ở mức nạp cao (BWT âm, như Bảng B ReGrad); **RAG không quên** (perf ổn/tăng). Vẽ trên cả 3 task.
- **R2 — Selective vs vanilla RAG:** kho phình → vanilla **tụt Recall@K**; selective giữ **±2%** + active index **↓≥30%**.
- **R3 — Δ cải thiện theo task:** **QA > NLI > Syllogism** → bằng chứng **trần lập luận**. Đối chiếu **ReGrad** (bán-tham số).
- *(Phụ)* unlearning = temporal gating khi supersession.

💬 Kể: "Mỗi kết quả gắn con số đo được → nghiệm thu khách quan."
📌 `nguyen2025vlqa...`, `duong2026vilegalnli...`, `le-etal-2025-overview`

## Slide 2 — Academic & open-source outputs (student-scale)
- **Hoàn thành luận văn thạc sĩ** này (đây là output chính).
- (Phấn đấu) **1 báo cáo hội nghị/workshop trong nước** (VLSP/KSE) — không cam kết tạp chí ISI/Scopus trong 6 tháng.
- **GitHub**: code + eval scripts + index tái lập được → đóng góp tái lập cho legal-CL VN.

## Slide 3 — Beneficiaries (Who)
- **Cộng đồng NLP/AI pháp lý:** benchmark + metric chuẩn để so các retrieval/memory paradigm.
- **Lập trình hệ thống pháp lý:** tái dùng selective memory + cơ chế cập nhật (giảm chi phí giữ tri thức tươi).
- **Cơ quan tuân thủ/bảo mật:** ngân hàng, toà án, công ty luật — không viện dẫn luật hết hiệu lực.

## Slide 4 — Work packages & timeline (6 tháng)
- **WP1 Xây kho RAG chọn lọc** (crawl/parse/index, citation graph, importance + decay, compliance gate) — crawl ưu tiên sub-domain biến động nhiều (lao động, thuế).
- **WP2 Thí nghiệm forgetting** (nạp liên tục × 4 arm CPT/ReGrad/vanilla-RAG/selective-RAG × 3 task).
- **WP3 Phân tích & benchmark** (đường forgetting, Δ theo độ khó task, VLQA/ViLegalNLI/LegalSLM).
- 8 milestone theo tuần (corpus W3 → KB W5 → baseline W9 → selective W12 → continual-loading W15 → đo forgetting W18 → benchmark W21 → report W24).

💬 Kể: dependency WP1→WP2→WP3.

## Slide 5 — Budget & risks
- Tổng **~20 triệu VND (~$800)** — quy mô **đồ án sinh viên** (lương = 0): Colab Pro+ (~4,8tr) · cloud GPU RunPod/L4/4090 (~1,2tr) · thù lao SV luật tình nguyện (~4tr) · OCR API (~2,5tr) · in ấn/đi lại/workshop. *(khớp Bảng 4_4)*
- **Rủi ro chính:** metadata sai (→ double-pass verify 5%); retrieval recall (→ hybrid dense+BM25); compute (→ pre-compute embedding, mô hình nhỏ 1–4B).

💬 Kể: nhấn rủi ro metadata = rủi ro số 1; budget **student-scale**, không cần A100.

## Slide 6 — Closing (3 câu hỏi proposal phải trả lời)
- **Why needed:** luật biến động + nạp liên tục; parametric → **quên thảm khốc** / khó unlearn.
- **Why now:** data VN mới (2025–2026) + quy định siết → nền thực nghiệm + nhu cầu hội tụ.
- **Why me:** đã làm **khóa luận về RAG** (miền thông tin trường ĐH) → nắm nền tảng, nay chuyển sang **miền luật** (khó hơn), dưới hướng dẫn GV chuyên NLP, dùng dataset VN công khai.
- **Value:** no factual (weight) drift + bằng chứng RAG né quên trên 3 task + reproducible protocol — novel về KH và giá trị thực tiễn cho legal-AI VN.

💬 Kể: slide chốt — đọc 4 gạch này là hội đồng nắm trọn giá trị + thấy bạn đủ năng lực.
🔖 Liêm chính: AI chỉ hỗ trợ tìm/cấu trúc/ngôn ngữ; ý tưởng + framework + phân tích là của tác giả (xem Declaration).

---

# 05 — Talking Points & Anticipated Q&A

## Slide 1 — Anticipated Q&A 1: "RAG đã có rồi — em nghiên cứu gì mới?"
- Không claim thuật toán RAG mới. Đóng góp = **đo RAG chống quên thảm khốc khi nạp liên tục** trên luật VN (chưa ai làm) — khuôn từ Bảng B ReGrad nhưng phi-tham số.
- **Selective RAG** chống "quên kiểu RAG" (kho phình → recall tụt) — cái vanilla RAG không xử được.
- Hook: **retrieval cứu QA/NLI nhưng chạm trần ở syllogism** (H4) — bác bỏ được bằng số.

💬 Kể: "Cái mới không phải thuật toán RAG, mà là **bằng chứng định lượng** RAG né forgetting trên 3 task luật VN + phát hiện trần lập luận."

## Slide 2 — Anticipated Q&A 2: "Model nhỏ 1–4B đủ suy luận luật không? 6 tháng kịp không?"
- Em **không đặt cược vào suy luận của base** — facts ở ngoài nên không cần model lớn (đúng luận điểm).
- Baseline parametric chạy **cùng cỡ nhỏ** ⇒ chênh lệch do **cơ chế**, không do model.
- Khả thi 6 tháng: base frozen, kịch bản nạp liên tục tập trung sub-domain biến động nhiều; budget student-scale (~20tr).

💬 Kể: "Facts để ngoài RAG giúp giảm tải cho trọng số, dùng model 1-4B vẫn khả thi và tiết kiệm compute."
