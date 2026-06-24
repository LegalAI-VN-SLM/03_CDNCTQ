# 07 — Glossary (giải thích thuật ngữ cho người ngoài ngành)

> Giải thích **ngắn gọn, dễ hiểu**. Từ khó có kèm ví dụ đời thường (🔸).

---

## A. Khái niệm AI cơ bản
- **LLM (Large Language Model)** — Mô hình ngôn ngữ lớn (như ChatGPT): AI học từ rất nhiều văn bản để hiểu và sinh ngôn ngữ.
- **Token / Tokenizer** — Token là "mẩu chữ" mô hình đọc (gần như từ/âm tiết); tokenizer là bộ cắt văn bản thành token.
- **Fine-tuning (tinh chỉnh)** — Huấn luyện thêm một mô hình đã có sẵn để nó giỏi hơn ở một việc/lĩnh vực cụ thể.
- **Weights / Parameters (trọng số/tham số)** — Các con số bên trong mô hình, chính là "trí nhớ" và "kỹ năng" của nó. Huấn luyện = chỉnh các số này.
- **Frozen base (đóng băng nền)** — Giữ nguyên trọng số gốc, không sửa. 🔸 Như khoá tủ hồ sơ lại, không động vào.
- **Zero-shot** — Mô hình làm được việc mới mà *không* cần học thêm ví dụ nào.
- **Encoder vs Decoder (khác nhau ở đâu)** — Hai "nửa" của mô hình ngôn ngữ:
  - **Encoder (bộ mã hoá) = người ĐỌC–HIỂU.** Đọc cả câu cùng lúc, nén thành "ý hiểu" bên trong; giỏi việc *phân loại/đánh giá* (vd: câu này đúng hay sai luật). **Không** tự viết văn ra. 🔸 Như người đọc xong tóm tắt ý trong đầu, không nói ra. Ví dụ: BERT, PhoBERT.
  - **Decoder (bộ giải mã) = người VIẾT–NÓI.** Sinh chữ *lần lượt từng từ*, đoán từ kế tiếp; giỏi *trả lời/viết*. 🔸 Như người kể chuyện nói tiếp từng chữ. Ví dụ: GPT, LLaMA, Qwen.
  - **Encoder–Decoder** = đọc-hiểu rồi viết-lại → hợp *dịch/tóm tắt*. Ví dụ: T5, mT0.
  - **Decoder-only** = chỉ dùng phần "viết-nói" nhưng đủ mạnh để vừa hiểu vừa trả lời → **kiến trúc của hầu hết LLM hiện đại**.
  - *Liên quan đề tài:* thực nghiệm cho thấy **decoder-only "quên" ít hơn** encoder–decoder khi học tiếp ⇒ đề tài chọn **base decoder-only (instruct)**.

## B. Học liên tục & sự quên
- **Continual Learning (CL) — Học liên tục** — *Mục tiêu*: cho mô hình tiếp thu kiến thức mới *theo thời gian* mà không train lại toàn bộ. **Không bắt buộc là SFT** — có thể làm bằng CPT (tự giám sát), CIT/SFT (có giám sát), CA (RL/DPO), hoặc **không train gì** (cập nhật kho RAG, như đề tài này dùng cho *facts*). SFT (qua LoRA) chỉ dùng cho *hành vi*, không dùng nhớ facts.
- **Catastrophic Forgetting — Quên thảm khốc** — Học cái mới làm mô hình *quên* cái cũ. 🔸 Như học tiếng Nhật xong quên mất tiếng Anh.
- **Weight drift — Trôi dạt trọng số** — Trọng số bị "trôi" dần khi cập nhật liên tục, gây quên/sai.
- **Stability–Plasticity dilemma** — Thế lưỡng nan: vừa phải *ổn định* (giữ cái cũ) vừa *mềm dẻo* (học cái mới) — hai thứ ngược nhau.
- **CPT / CIT / CA** — Ba pha học liên tục: **CPT** = nạp *kiến thức* mới (văn bản thô); **CIT/IFT** = dạy *kỹ năng làm việc* (theo hướng dẫn); **CA** = chỉnh *giá trị/an toàn*.
- **Replay / Rehearsal — Ôn tập** — Trộn lại một ít dữ liệu cũ khi học mới để đỡ quên. 🔸 Như ôn bài cũ xen kẽ học bài mới.
- **Stability gap / Re-warming** — Khi học tiếp, loss (sai số) bị "nhảy vọt" tạm thời; *re-warming* = tăng lại tốc độ học từ từ để tránh cú nhảy đó.
- **Loss landscape — Cảnh quan mất mát** — Hình dung "địa hình" sai số của mô hình; **sharp** (vực nhọn) dễ quên, **flat** (thung lũng phẳng) bền hơn. **SAM** = thuật toán ép tìm vùng phẳng.
- **Inverse Linear Law** — Quy luật: học cái mới càng *sâu* thì quên cái cũ càng *nhiều* (tỉ lệ thuận).

## C. Phương pháp của đề tài
- **RAG (Retrieval-Augmented Generation)** — Thay vì bắt mô hình "nhớ hết", ta *tra cứu* tài liệu liên quan rồi đưa vào cho nó trả lời. 🔸 Như luật sư tra tủ hồ sơ trước khi tư vấn.
- **Parametric / Non-parametric / Semi-parametric** — *Parametric* = kiến thức nằm trong trọng số (trong "não"); *non-parametric* = nằm ngoài (kho tra cứu); *semi-parametric* = kết hợp cả hai.
- **PEFT** — Nhóm kỹ thuật tinh chỉnh *tiết kiệm*, chỉ chỉnh một phần nhỏ thay vì cả mô hình.
- **LoRA (Low-Rank Adaptation)** — Một kiểu PEFT: gắn thêm một "miếng vá" nhỏ để dạy *hành vi/cách làm*, giữ nguyên mô hình gốc. 🔸 Như dán sticky-note hướng dẫn lên cuốn sách mà không sửa sách.
- **O-LoRA / N-LoRA / SLIM / MoE** — Các biến thể LoRA giúp học *nhiều việc tuần tự* mà không đè lên nhau (mỗi việc một "ngăn" riêng).
- **Knowledge Base (KB) — Kho tri thức** — Cơ sở dữ liệu chứa các điều luật/án lệ để tra cứu.
- **Embedding / Bi-encoder / Vector index** — Biến văn bản thành dãy số (vector) để máy đo "độ giống nhau"; *vector index* = kho chứa các vector đó để tìm nhanh.
- **Retriever / Re-ranking** — Bộ *truy xuất* tài liệu giống câu hỏi; *re-ranking* = xếp lại thứ tự kết quả cho tốt hơn.
- **Machine Unlearning — Học quên** — Làm cho mô hình *quên có chủ đích* một thông tin (vd luật hết hiệu lực, dữ liệu cá nhân).
- **Hybrid framework (của đề tài)** — Kết hợp: **RAG** giữ *sự thật pháp lý* (dễ cập nhật/xoá) + **LoRA** lo *cách lập luận*, base **đóng băng**.

## D. Cơ chế đặc thù pháp lý (trong đề tài)
- **Selective memory — Bộ nhớ chọn lọc** — Không giữ mọi thứ ngang nhau; ưu tiên điều luật quan trọng, làm "mờ" cái ít liên quan.
- **Importance score — Điểm quan trọng** — Điểm cho mỗi điều luật, tính từ: được dẫn chiếu nhiều không, còn hiệu lực không, mức rủi ro, tần suất tra.
- **Citation graph / centrality** — Mạng "điều nào dẫn chiếu điều nào"; *centrality* cao = điều luật nền tảng, hay được viện dẫn.
- **Validity flag / interval — Hiệu lực** — Đánh dấu luật còn hay hết hiệu lực, và khoảng thời gian có hiệu lực.
- **Temporal regime — "Chế độ" theo thời gian** — Phân biệt luật *trước/sau* khi sửa đổi để trả lời đúng theo mốc thời gian. 🔸 "Thuế suất *hiện nay*" vs "thuế suất *năm 2012*".
- **Compliance hard-gate — Cổng tuân thủ cứng** — Quy tắc bắt buộc: luật *đang hiệu lực* thì **không bao giờ** bị bỏ sót, dù điểm quan trọng thấp.
- **Consolidation / Decay (Ebbinghaus)** — Truy vấn lặp → *củng cố* (nhớ chắc); lâu không dùng → *phai dần*; lấy cảm hứng từ đường cong quên của con người. (Ở đây *phai* = hạ thứ hạng, **không xoá**.)
- **P + Q (cơ chế unlearning)** — **P** = chặn không *lấy ra* tài liệu cần quên; **Q** = ràng buộc *không nói* về nó. 🔸 Rút hồ sơ khỏi tủ (P) + dặn "đừng nhắc vụ này" (Q).
- **Behavioral unlearning** — "Quên" ở mức *hành vi* (không truy xuất/không trích dẫn), chứ không xoá khỏi trọng số mô hình.
- **Right to be forgotten — Quyền được lãng quên** — Quyền yêu cầu xoá thông tin cá nhân khỏi hệ thống.
- **Audit log / Versioning / Rollback** — Nhật ký thao tác để giải trình; lưu *phiên bản* kho và *khôi phục* khi cập nhật lỗi.
- **Canary set** — Bộ câu hỏi "kim chỉ nam" chạy sau mỗi cập nhật để kiểm hệ còn chạy đúng không. 🔸 Như "chim hoàng yến trong mỏ than" — báo động sớm.

## E. Đánh giá & thực nghiệm
- **Benchmark** — Bộ dữ liệu/bài kiểm chuẩn để so sánh các phương pháp.
- **Baseline** — Phương pháp *mốc* để đối chiếu (vd "RAG thường") xem cải tiến có hơn không.
- **Ablation study** — Tháo bỏ từng thành phần để xem cái nào thực sự đóng góp. 🔸 Như nấu ăn bớt từng gia vị để biết vị nào quan trọng.
- **Metric phân loại / khớp đáp án:**
  - **Precision (độ chính xác)** — trong những cái *lấy ra/đoán*, bao nhiêu **đúng**. 🔸 "nói ít mà trúng".
  - **Recall (độ bao phủ)** — trong những cái *đúng thực sự*, lấy được bao nhiêu. 🔸 "không bỏ sót".
  - **F1** — trung bình điều hoà của Precision & Recall → 1 số *cân bằng* "đúng" vs "đủ".
  - **Accuracy** — tỉ lệ dự đoán đúng trên tất cả (hợp phân loại, vd NLI).
  - **EM (Exact Match)** — câu trả lời khớp *y hệt* đáp án vàng (QA nghiêm).
- **Metric truy xuất / xếp hạng:**
  - **Recall@K** — trong **K** kết quả đầu, có lấy được tài liệu đúng không.
  - **MRR (Mean Reciprocal Rank)** — kết quả đúng **đầu tiên** nằm ở hạng nào; hợp khi mỗi truy vấn ~**1 đáp án đúng** (vd tìm 1 điều luật).
  - **MAP (Mean Average Precision)** — precision trung bình tại các vị trí đúng, qua nhiều truy vấn (nhiều đáp án, nhị phân). *Thường bỏ nếu đã có NDCG (trùng).*
  - **NDCG (Normalized Discounted Cumulative Gain)** — xếp hạng tốt cỡ nào, có tính **mức độ liên quan nhiều bậc** + giảm dần theo vị trí; *tổng quát hơn MAP*.
  - 🔸 *Chọn gọn:* QA 1-đáp-án → **Recall@K + MRR**; nhiều-đáp-án-có-mức-độ → **NDCG (bỏ MAP)**; đừng báo MAP + NDCG cùng lúc.
- **BWT / FWT (Backward / Forward Transfer)** — Đo *chuyển giao* giữa các việc học tuần tự:
  - *BWT*: sau khi học việc mới, điểm trên việc **cũ** thay đổi ra sao → **âm = quên** (tệ đi), dương = việc cũ còn tốt lên. 🔸 Học tới bài 5 rồi kiểm lại bài 1 còn nhớ không.
  - *FWT*: kiến thức **cũ giúp** học việc mới nhanh/tốt hơn (làm được task mới dù học ít).
- **Forgetting Gradient (FG%)** — % năng lực **mất đi** sau khi học mới, tính (điểm trước − điểm sau)/điểm trước cho từng miền/tác vụ. Càng **thấp** càng tốt; **âm** = thậm chí *cải thiện*.
- **USR (Unlearning Success Rate)** — % các mục *cần quên* mà hệ thống **đã quên thành công** (không còn nhả ra). Càng **cao** càng tốt.
- **ROUGE (ROUGE-1 / ROUGE-2 / ROUGE-L)** — Đo độ **trùng khớp** giữa văn bản máy sinh và đáp án mẫu:
  - *ROUGE-1*: trùng **từng từ** (unigram) — "bao nhiêu từ giống".
  - *ROUGE-2*: trùng **cặp 2 từ liền** (bigram) — bắt được cụm từ.
  - *ROUGE-L*: chuỗi con chung **dài nhất** (LCS) — bắt **thứ tự / độ liền mạch**.
  - Trong *sinh văn bản* (tóm tắt/QA): cao = giống đáp án (tốt). Trong *unlearning*: ROUGE **thấp** trên forget set = model không còn nhả thông tin đã quên (tốt).
- **TPR@1%FPR / MIA (Membership Inference Attack)** — Phép thử "moi": kẻ tấn công đoán một mục **có còn được mô hình "biết"** không. *TPR@1%FPR* = tỉ lệ moi đúng tại mức báo-động-giả cố định 1% (điểm vận hành nghiêm). Con số **thấp = khó moi** → quên chắc (tốt).
- **Compliance rate — Tỉ lệ tuân thủ** — % câu trả lời thoả 3 điều: (1) dùng luật **đang hiệu lực**; (2) **không** viện dẫn luật đã bãi bỏ khi hỏi hiện hành; (3) trả lời đúng theo **mốc thời gian** được hỏi.
- **Scaling law / D-CPT Law** — Công thức **dự đoán** hiệu năng/loss theo quy mô mô hình (N), lượng dữ liệu (D) và **tỉ lệ trộn dữ liệu miền (r)**. D-CPT cho biết nên trộn bao nhiêu % dữ liệu chuyên ngành (vd ≈64% cho luật) chỉ với ~1% chi phí thử nghiệm.

## F. Nhiệm vụ & dữ liệu pháp lý
- **QA (Question Answering)** — Hỏi–đáp: cho câu hỏi luật, trả lời.
- **NLI (Natural Language Inference)** — Cho 2 câu, xác định câu này có *suy ra được* từ câu kia không (đúng/sai/không liên quan).
- **Syllogism — Tam đoạn luận** — Lập luận 3 bước: đại tiền đề (luật) + tiểu tiền đề (sự việc) → kết luận. 🔸 Đây là chỗ AI pháp lý còn yếu nhất.
- **VLQA / ViLegalNLI / LegalSLM** — Ba bộ dữ liệu chuẩn về luật *tiếng Việt*: hỏi-đáp / suy luận / đánh giá mô hình nhỏ.
- **Lexical shortcut (PMI)** — "Mẹo từ vựng": mô hình đoán đáp án nhờ từ khoá bề mặt thay vì hiểu thật; ViLegalNLI lọc bỏ để đo *suy luận thật*.

## G. Phương pháp đối chiếu (không phải method chính)
- **ReGrad (Retrievable Gradients) — Gradient có thể truy xuất** — Cách *bán-tham-số* khác: lưu sẵn "bản cập nhật" của từng tài liệu, lúc cần thì nạp tạm vào mô hình rồi gỡ ra. Đề tài dùng để *so sánh*, không làm trục chính (vì chậm + khó "quên").
- **LUFY** — Hệ chatbot RAG dài hạn biết *quên* các đoạn hội thoại ít quan trọng — nguồn cảm hứng cho selective memory (đề tài chuyển sang văn bản luật).
- **Gradient bank** — Kho chứa các "bản cập nhật" (gradient) của ReGrad.
- **Agent memory (Working/Episodic/Semantic/Parametric)** — Phân loại bộ nhớ của tác nhân AI; trong đề tài: **RAG = Semantic** (kho tri thức ngoài), **LoRA = Parametric** (trong trọng số).

---

## H. "Loại tri thức" (7 kiểu — bảng CPT/CIT/CA)
> Khi nói mô hình "học" gì, có 7 *loại* tri thức; quan trọng nhất với đề tài là **Fact** và **Domain**.
- **Fact — Sự kiện/dữ kiện** ⭐ — một *mẩu thông tin cụ thể, đúng/sai rõ ràng*. Trong luật: nội dung một điều luật, một con số (vd "thuế suất 20%"), một quy định cụ thể. 🔸 Như một "ô dữ liệu" tra được. **Đề tài để Fact ở RAG (kho ngoài), không nhồi vào trọng số** — vì fact hay đổi (luật sửa) và cần xoá được.
- **Domain — Tri thức lĩnh vực** — hiểu *bối cảnh ngành*: thuật ngữ pháp lý, văn phong, cấu trúc văn bản luật. (Khác Fact: Domain là "nền hiểu chung", Fact là "dữ kiện lẻ".)
- **Language — Ngôn ngữ** — năng lực tiếng (ở đây: tiếng Việt).
- **Task — Nhiệm vụ** — *loại việc* phải làm: hỏi-đáp, NLI, tóm tắt, lập luận.
- **Skill (Tool) — Kỹ năng/công cụ** — biết *dùng công cụ*: tra cứu, trích dẫn điều luật.
- **Value — Giá trị** — chuẩn mực đạo đức/an toàn (tuân thủ tư pháp).
- **Preference — Sở thích** — cách/văn phong trả lời mong muốn.
→ Quy ước đề tài: **Fact → RAG**, **Task/Skill (hành vi) → LoRA**, base lo Language/Domain nền.

## I. Văn bản & khái niệm pháp lý
- **Statute / Law (luật, bộ luật)**, **Decree (nghị định)**, **Circular (thông tư)**, **Constitution (hiến pháp)** — các loại văn bản quy phạm, theo thứ bậc hiệu lực giảm dần.
- **Precedent / Case law (án lệ)** — bản án mẫu được dùng làm chuẩn tham chiếu.
- **Article / Clause (Điều / Khoản)** — đơn vị nhỏ trong văn bản luật; là "đơn vị tri thức" mô hình truy xuất.
- **In-force / Repealed (còn hiệu lực / hết hiệu lực, bãi bỏ)** — luật đang áp dụng vs đã bị thay/hủy.
- **Sealed (niêm phong)** — hồ sơ/bản án bị hạn chế truy cập vì bảo mật.

## J. Thuật ngữ kỹ thuật chi tiết (trong .tex)
- **W₀ / ΔW / rank r / α (LoRA)** — W₀ = trọng số gốc (đóng băng); ΔW = phần "vá" thêm, được tách thành 2 ma trận nhỏ; **r** = "độ lớn miếng vá" (nhỏ = ít tham số); **α** = hệ số khuếch đại.
- **SVD / Intrinsic rank — Hạng nội tại** — phép đo "cập nhật cần bao nhiêu *chiều* mới đủ". CPT cần ~2000 chiều (LoRA r≤256 không đủ) → LoRA chỉ hợp việc nhẹ (IFT).
- **Orthogonal subspace / projection (O-LoRA)** — ép mỗi việc mới học ở *hướng vuông góc* với việc cũ để **không đè lên nhau**. 🔸 Như mỗi người viết vào một dòng riêng, không chồng chữ.
- **ℓ1 sparsity (N-LoRA)** — ép phần lớn tham số về 0 → mỗi việc dùng "ô" riêng biệt, ít đụng nhau.
- **Parameter collision — Xung đột tham số** — học việc mới *ghi đè* lên trọng số việc cũ → gây quên.
- **MoE (Mixture of Experts) / soft routing** — nhiều "chuyên gia" nhỏ; bộ định tuyến *mềm* gửi câu hỏi tới đúng chuyên gia. 🔸 Tổng đài chuyển cuộc gọi tới đúng phòng ban.
- **Soft prompt / Prefix-tuning / Progressive Prompts** — thêm vài "gợi ý ảo" vào đầu input để định hướng mô hình, *không sửa* trọng số.
- **Gradient / Gradient ascent** — gradient = "hướng chỉnh số để giảm sai"; *ascent* (đi ngược) được dùng để **ép quên** (model-level unlearning).
- **Optimizer / AdamW / Learning rate / Momentum** — thuật toán cập nhật trọng số; *learning rate* = tốc độ học; *momentum* = quán tính; *AdamW* là optimizer phổ biến.
- **Loss / Validation loss / Perplexity** — *loss* = sai số (càng nhỏ càng tốt); *perplexity* = độ "bối rối" của mô hình khi đoán chữ.
- **Distribution shift / OOD / Weak–Strong shift** — dữ liệu mới *khác* dữ liệu cũ; OOD = ngoài phân phối quen; khác *ít* (weak) vs *nhiều* (strong, vd đổi ngôn ngữ).
- **Mixture ratio (r) — Tỉ lệ trộn** — % dữ liệu chuyên ngành so với dữ liệu chung khi huấn luyện (D-CPT tính ra số tối ưu ≈ 0.64 cho luật).
- **Annealing / Infinite LR** — hạ dần tốc độ học về cuối để "chốt" mô hình ổn định.
- **Dense vs Sparse retrieval / BM25 / Hybrid** — *dense* = tìm theo *nghĩa* (vector); *sparse/BM25* = tìm theo *từ khoá*; *hybrid* = kết hợp cả hai cho chắc.
- **Cosine similarity** — cách đo "độ giống nhau" giữa hai vector (câu hỏi vs điều luật).
- **Context / Context window** — lượng chữ mô hình đọc được trong *một lần*; tài liệu tra được sẽ nhét vào đây.
- **Grounding — Neo dẫn chứng** — bắt câu trả lời *dựa trên* tài liệu tra được (giảm bịa). 🔸 "Nói có sách, mách có chứng".
- **Top-k** — lấy *k* kết quả tốt nhất (vd top-5 điều luật giống câu hỏi nhất).
- **Upsert / Supersession** — *upsert* = thêm-hoặc-cập-nhật một mục; *supersession* = văn bản mới *thay thế* văn bản cũ (đánh dấu cũ hết hiệu lực).
- **VRAM** — bộ nhớ của card đồ hoạ (GPU); càng ít tốn càng dễ chạy.

## K. Chi tiết unlearning
- **3 mức quên:** **Temporal gating** (ẩn luật hết hiệu lực, vẫn tra được "tại năm X") · **Access-control gating** (chỉ ai có quyền mới thấy — án niêm phong) · **Index deletion** (xoá hẳn — dữ liệu cá nhân).
- **5 tiêu chí đánh giá unlearning:** *Effectiveness* (quên có thật?) · *Universality* (quên qua mọi cách hỏi?) · *Harmlessness* (không hại câu khác?) · *Robustness* (chống jailbreak?) · *Simplicity* (rẻ, không train lại?).
- **Jailbreak** — mẹo "lách" để dụ mô hình nói ra thứ đáng lẽ bị cấm/đã quên.

---

## L. Bảng tra viết tắt (acronym → dạng đầy đủ)
> **Chỉ tra dạng đầy đủ** — phần *nghĩa* xem ở mục A–K (tránh lặp). Sắp xếp A→Z.

| Viết tắt | Dạng đầy đủ (English) |
|----------|----------------------|
| AA / OP | Average Accuracy / Overall Performance |
| BWT / FWT | Backward / Forward Transfer |
| CA | Continual Alignment |
| CIT | Continual Instruction Tuning |
| CL | Continual Learning |
| CPT | Continual Pre-Training |
| D-CPT | Domain-specific Continual Pre-Training |
| DPO | Direct Preference Optimization |
| EM | Exact Match |
| FG | Forgetting Gradient |
| FPR / TPR | False / True Positive Rate |
| FUAR | Forgotten / (Updated + Acquired) Ratio |
| GPU / VRAM | Graphics Processing Unit / Video RAM |
| GRPO | Group Relative Policy Optimization |
| IFT | Instruction Fine-Tuning |
| KB | Knowledge Base |
| LLM | Large Language Model |
| LoRA | Low-Rank Adaptation |
| LR | Learning Rate |
| MAP | Mean Average Precision |
| MIA | Membership Inference Attack |
| MoE / MoLA | Mixture of Experts / Mixture of LoRA |
| MRR | Mean Reciprocal Rank |
| NDCG | Normalized Discounted Cumulative Gain |
| NLI | Natural Language Inference |
| N-LoRA | Sparse (Norm-constrained) LoRA |
| O-LoRA | Orthogonal LoRA |
| **OOD** | **Out-Of-Distribution** |
| PEFT | Parameter-Efficient Fine-Tuning |
| PMI | Pointwise Mutual Information |
| POMDP | Partially Observable Markov Decision Process |
| QA | Question Answering |
| RAG | Retrieval-Augmented Generation |
| ReGrad | Retrievable Gradients |
| RLHF | Reinforcement Learning from Human Feedback |
| ROUGE (-1/-2/-L) | Recall-Oriented Understudy for Gisting Evaluation |
| SAM | Sharpness-Aware Minimization |
| SFT | Supervised Fine-Tuning |
| SLIM | Soft LoRA and Identity Mixture |
| SVD | Singular Value Decomposition |
| TiC-LM | Time-Continual Language Model |
| USR | Unlearning Success Rate |
| VLQA / ViLegalNLI / LegalSLM | Vietnamese Legal QA / NLI / Small-LM (datasets) |
| WP | Work Package |
