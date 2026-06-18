# Khảo sát toàn diện về Catastrophic Forgetting trong LLMs

Catastrophic forgetting — hiện tượng mạng nơ-ron đánh mất tri thức cũ khi học tác vụ mới — vẫn là rào cản trung tâm của continual learning (CL) cho các Large Language Models hiện đại, bất chấp kích thước mô hình đã đạt hàng trăm tỷ tham số. Nghiên cứu gần đây (2023–2026) đã chuyển trọng tâm từ các phương pháp regularization cổ điển (EWC, SI, MAS) sang một bộ công cụ hỗn hợp mới: LoRA trực giao/null-space (O-LoRA, InfLoRA, AlphaEdit), Mixture-of-Experts LoRA (LoRAMoE, MoLA), model merging task-vector (TIES, DARE, EMR-Merging), và synthetic rehearsal tự sinh (SSR, SEEKR, Magpie). 

Các công trình thực nghiệm then chốt (Ibrahim et al. 2024, Biderman et al. 2024, Luo et al. 2023, Kalajdzievski 2024) đã làm rõ các quy luật scaling và các best-practice công nghiệp: re-warming learning rate, replay 1–25% dữ liệu cũ, block expansion, và weight averaging đã trở thành công thức de-facto cho continual pre-training ở các phòng lab lớn (Meta Llama 3, DeepSeek V3, Google Gemma 2, Cohere Aya). Bản khảo sát này tổng hợp hơn 100 phương pháp với các công thức, benchmark, và kết quả thực nghiệm, phục vụ cho nghiên cứu học thuật và kỹ sư LLM chuyên sâu.

---

## 1. Định nghĩa vấn đề và bối cảnh

Catastrophic forgetting (hay catastrophic interference) được McCloskey & Cohen (1989, *Psychology of Learning and Motivation 24*) và Ratcliff (1990, *Psychological Review 97*) phát hiện độc lập: khi mạng connectionist được huấn luyện tuần tự trên tác vụ A rồi B, các gradient update lên tham số dùng chung sẽ ghi đè biểu diễn thiết yếu của A. French (1999, *Trends Cogn. Sci.*) lập luận đây là hệ quả nội tại của distributed representation — cùng cơ chế cho phép generalization cũng phá hủy ký ức cũ.

**Stability–plasticity dilemma** (Grossberg 1982; Carpenter & Grossberg 1987) đóng khung mọi phương pháp CL như một điểm đánh đổi trên đường cong $\text{stability} \leftrightarrow \text{plasticity}$. Hệ sinh học giải quyết qua complementary learning systems (hippocampus + neocortex; McClelland, McNaughton & O'Reilly 1995) và metaplasticity synapse (Fusi et al. 2005). Chaudhry et al. (2018, RWalk) hình thức hóa *intransigence* — năng lực học tác vụ mới — như metric bổ trợ cho BWT/FWT.

Forgetting biểu hiện đa dạng trong LLM, vượt xa classical task-accuracy drop:
1. **Capability forgetting** trên MMLU/HumanEval/GSM8K sau fine-tune hẹp (Luo et al. 2023, arXiv:2308.08747).
2. **Instruction-following degradation** (Wang et al. 2023 TRACE).
3. **Alignment/safety regression** — Qi et al. 2023 cho thấy chỉ **10 ví dụ adversarial** đủ phá hỏng safety alignment của GPT-3.5.
4. **Factual drift** trong parametric memory (Jang et al. ICLR 2022).
5. **Representation drift** ngay cả khi task-level performance giữ nguyên (Luo et al. 2305.05968). 

Ramasesh, Lewkowycz & Dyer (ICLR 2022) chứng minh scale làm giảm forgetting trong classical CL, nhưng Luo et al. 2023 và Kalajdzievski 2024 cho thấy ở continual instruction tuning, **mô hình lớn hơn có thể forgetting nhiều hơn về trị tuyệt đối** do baseline cao hơn — một mâu thuẫn quan trọng cần lưu ý.

### Các thiết lập CL cho LLM
(Theo taxonomy Wu et al. 2024 "Continual Learning for Large Language Models: A Survey" arXiv:2402.01364; Shi et al. ACM CSUR 2025, arXiv:2404.16789):
*   **Continual Pre-Training (CPT):** Cập nhật foundation model trên corpus mới (ELLE, Jin et al. 2022, Llama-Pro).
*   **Continual Instruction Tuning (CIT):** Fine-tune tuần tự trên luồng tác vụ có instruction (Continual-T0/CT0, TRACE, InsCL).
*   **Continual Alignment (CA):** RLHF/DPO tuần tự với preference data mới (Rewarded Soups, WARP).

Các scenario cổ điển của van de Ven & Tolias (2019): **Task-IL** (biết task ID), **Domain-IL** (phân phối đầu vào thay đổi, cùng label space), **Class-IL** (lớp mới đến dần; khó nhất).

### Benchmarks cho LLM
*   **TRACE** (Wang et al. arXiv:2310.06762) — 8 dataset (C-STANCE, FOMC, MeetingBank, Py150, ScienceQA, NumGLUE-cm/ds, 20Minuten), 5K train/2K test, với 3 metric Delta ($\Delta R^G$ General, $\Delta R^I$ Instruction, $\Delta R^S$ Safety). Kết quả nổi bật: Llama-2-chat-13B rớt GSM8K từ **28.8% $\rightarrow$ 2.0%** sau naïve sequential fine-tuning.
*   **CITB** (Zhang et al. Findings EMNLP 2023) với InstrDialog/InstrDialog++.
*   **ConTinTin** (Yin et al. ACL 2022) — 61 tác vụ từ Natural Instructions.
*   **CoIN** (Chen et al. NeurIPS 2024 D&B) cho multimodal LLM.
*   **CLiF-26/CLIF** (Jin et al. Findings EMNLP 2021).
*   *Benchmarks cổ điển:* Split-MNIST, Permuted-MNIST, Split-CIFAR-100, CORe50, 5-Datasets, MNIST-360 (Buzzega 2020).
*   *Probes suy giảm năng lực LLM:* MMLU, HumanEval, BIG-Bench Hard, GSM8K, TruthfulQA.

### Metric chuẩn
(Lopez-Paz & Ranzato 2017; Chaudhry et al. 2018): Gọi $R_{i,j}$ là độ chính xác trên task $j$ sau khi học task $i$:

*   **Average Accuracy:** 
    $$ACC = \frac{1}{T} \sum_{i=1}^{T} R_{T,i}$$
*   **Backward Transfer:** 
    $$BWT = \frac{1}{T-1} \sum_{i=1}^{T-1} (R_{T,i} - R_{i,i})$$
    *(BWT âm tương đương với forgetting)*
*   **Forward Transfer:** 
    $$FWT = \frac{1}{T-1} \sum_{i=2}^{T} (R_{i-1,i} - \bar{b}_i)$$
*   **Forgetting Measure** (RWalk): 
    $$F_k = \frac{1}{T-1} \sum_{j} \max_{l < k} R_{l,j} - R_{k,j}$$

---

## 2. Các phương pháp cổ điển/nền tảng

### 2.1 Regularization-based

*   **Elastic Weight Consolidation (EWC)** — Kirkpatrick et al. *PNAS* 2017 (arXiv:1612.00796): Xấp xỉ posterior $p(\theta \mid D_A)$ bằng Gaussian tại MAP $\theta_A^*$ với precision là diagonal Fisher Information Matrix $F_i$:
    $$\mathcal{L}(\theta) = \mathcal{L}_B(\theta) + \frac{\lambda}{2} \sum_{i} F_i (\theta_i - \theta_{A,i}^*)^2, \quad F_i = \mathbb{E} \left[ \left( \frac{\partial \log p(y \mid x; \theta)}{\partial \theta_i} \right)^2 \right]$$
*   **Online EWC** (Schwarz et al. ICML 2018 *Progress & Compress*): Thay tổng penalty bằng một quadratic đơn tại $\theta^*$ gần nhất với Fisher tích lũy:
    $$\tilde{F}_t = \gamma \tilde{F}_{t-1} + F_t$$
    Sửa chỉ trích của Huszár (2018, *PNAS*) về diagonal-Fisher. Độ phức tạp $O(1)$ theo số task.
*   **Synaptic Intelligence (SI)** — Zenke, Poole & Ganguli ICML 2017: Tích path-integral trực tuyến $\omega_i^\mu = - \sum_t g_i(t) \cdot \Delta \theta_i(t)$, chuẩn hóa bởi tổng thay đổi tham số bình phương:
    $$\Omega_i^\mu = \frac{\sum_{\nu \le \mu} \omega_i^\nu}{(\Delta \theta_i^\nu)^2 + \xi}$$
    SI mang tính "online" hơn EWC vì tính toán trực tiếp trong quá trình huấn luyện thay vì tính Hessian tại endpoint.
*   **Memory Aware Synapses (MAS)** — Aljundi et al. ECCV 2018: Đo tầm quan trọng (importance) bằng độ nhạy của $\|F(x; \theta)\|_2^2$ với parameter change. Không cần label (có thể ước lượng trên unlabeled data).
*   **Learning without Forgetting (LwF)** — Li & Hoiem TPAMI 2017: Sử dụng kiến trúc knowledge distillation để ghi lại $\hat{y}^{\text{old}}$ của old model trên new-task inputs, kết hợp thêm KL-loss với temperature $T$.
*   **RWalk** (Chaudhry et al. ECCV 2018): Hợp nhất EWC++ và SI dưới góc nhìn KL-Riemannian, giới thiệu Forgetting Measure và Intransigence.

### 2.2 Replay/rehearsal

*   **Experience Replay (ER)** — Rolnick et al. NeurIPS 2019 (CLEAR): Lưu trữ buffer $M$ với **reservoir sampling** (Vitter 1985), xen kẽ (interleave) batch từ $M$ với batch hiện tại:
    $$\mathcal{L} = \mathcal{L}_{\text{stream}} + \alpha \mathbb{E}_M [\ell(f_\theta(x), y)]$$
*   **Deep Generative Replay (DGR)** — Shin et al. NeurIPS 2017: Sử dụng bộ sinh GAN scholar sinh pseudo-sample, tránh lưu dữ liệu thô (bảo vệ quyền riêng tư) nhưng bất ổn ở CIFAR-10+.
*   **Dark Experience Replay (DER/DER++)** — Buzzega et al. NeurIPS 2020: Lưu thêm **logits dọc quỹ đạo** $z = h_\theta(x)$ và thực hiện chưng cất:
    $$\mathcal{L}_{\text{DER++}} = \mathcal{L}_{\text{task}} + \alpha \mathbb{E}_M \|h_\theta(x) - z\|_2^2 + \beta \mathbb{E}_M \ell_{CE}(h_\theta(x), y)$$
*   **iCaRL** (Rebuffi et al. CVPR 2017) cho Class-IL: herding exemplar selection + Nearest-Mean-of-Exemplars classifier + distillation BCE.
*   **MIR** (Aljundi et al. NeurIPS 2019): Truy hồi các sample mà loss sẽ tăng nhiều nhất sau một bước virtual SGD.
*   **GSS** (Aljundi et al. NeurIPS 2019): Chọn buffer dựa trên sự đa dạng của gradient (gradient diversity greedy).

### 2.3 Architecture-based

*   **Progressive Networks** (Rusu et al. arXiv:1606.04671): Cho mỗi task $k$, thêm column mới với lateral connection đến các column cũ đã bị đóng băng (frozen):
    $$h_i^{(k)} = f \left( W_i^{(k)} h_{i-1}^{(k)} + \sum_{j < k} U_i^{(k:j)} h_{i-1}^{(j)} \right)$$
    Phương pháp này đạt **zero forgetting by construction** nhưng tham số tăng tuyến tính.
*   **PackNet** (Mallya & Lazebnik CVPR 2018): Tiến hành cắt tỉa lặp (iterative-prune) từ 50–75%, huấn luyện lại rồi đóng băng.
*   **Piggyback** (Mallya et al. ECCV 2018): Học binary mask $m = 1[m^r > \tau]$ trên backbone frozen — đạt mức **1 bit/parameter**.
*   **HAT** (Serrà et al. ICML 2018): Gate mask sigmoid $a_l^t = \sigma(s \cdot e_l^t)$ với $s$ được nâng dần (anneal) từ $1/s_{\text{max}} \rightarrow s_{\text{max}}$; giảm forgetting hiệu quả từ 45–80%.
*   **DEN** (Yoon et al. ICLR 2018): Học kết hợp chọn lọc retraining + dynamic expansion + split/duplicate.

### 2.4 Gradient-based (constrained optimization)

*   **GEM** (Lopez-Paz & Ranzato NeurIPS 2017): Giải bài toán tối ưu Quadratic Programming (QP):
    $$\min_{\tilde{g}} \frac{1}{2} \|\tilde{g} - g\|^2 \quad \text{s.t.} \quad \langle \tilde{g}, g_k \rangle \ge 0 \quad \forall k < t$$
*   **A-GEM** (Chaudhry et al. ICLR 2019): Thay thế bằng ràng buộc trung bình đơn, giải công thức closed-form:
    $$\tilde{g} = g - \frac{g^T g_{\text{ref}}}{g_{\text{ref}}^T g_{\text{ref}}} g_{\text{ref}} \quad \text{nếu} \quad g^T g_{\text{ref}} < 0$$
*   **OGD** (Farajtabar et al. AISTATS 2020): Thực hiện chiếu gradient (project gradient) trực giao với tập hợp $\{\nabla_\theta f_\theta(x) : x \in M_{\text{past}}\}$.
*   **OWM** (Zeng et al. *Nat. Mach. Intell.* 2019): Chiếu cập nhật trọng số vào null-space của các input từ tác vụ cũ:
    $$\Delta W^l \leftarrow P^l \Delta W^l \quad \text{với} \quad P^l = I - X^l \left( (X^l)^\top X^l + \alpha I \right)^{-1} (X^l)^\top$$

### 2.5 Distillation đặc thù Class-IL

*   **PODNet** (Douillard et al. ECCV 2020): Chưng cất pooled-output tại mọi tầng trung gian.
*   **LUCIR** (Hou et al. CVPR 2019): Kết hợp cosine normalization + less-forget feature distillation + margin ranking để khắc phục mất cân bằng dữ liệu giữa lớp cũ và lớp mới.

---

## 3. Phương pháp đặc thù LLM: PEFT và LoRA-based CL

### 3.1 Nền PEFT

LoRA (Hu et al. ICLR 2022, arXiv:2106.09685) phân rã cập nhật trọng số:
$$\Delta W = BA, \quad B \in \mathbb{R}^{d \times r}, \quad A \in \mathbb{R}^{r \times d}, \quad r \ll d$$
với $B$ được khởi tạo bằng 0.

**Tại sao LoRA vốn đã giảm forgetting:** $W_{\text{base}}$ đóng băng giúp bảo tồn tri thức tiền huấn luyện (pretraining knowledge), trong khi cập nhật chỉ giới hạn trong subspace chiều thấp. Biderman et al. 2024 (TMLR, arXiv:2405.09673) trong nghiên cứu "LoRA Learns Less and Forgets Less" chỉ ra: full FT đạt kết quả tốt hơn trên miền đích (target domain) nhưng có nhiễu loạn (perturbation rank) cao hơn **10–100 $\times$** so với LoRA; LoRA giúp giữ năng lực nền tảng (base capability) tốt hơn đáng kể ngoài miền đích.

**Các PEFT khác:**
*   **AdapterFusion** (Pfeiffer et al. EACL 2021): Hợp nhất các adapter đã huấn luyện riêng bằng attention-fusion.
*   **Prefix Tuning** (Li & Liang ACL 2021).
*   **Prompt Tuning** (Lester et al. EMNLP 2021).
*   **P-tuning v2** (Liu et al. ACL 2022): Nhúng deep prompt ở mọi layer.
*   **(IA)³** (Liu et al. NeurIPS 2022): Nhân element-wise 3 vector scale với K, V, FF (chỉ chiếm ~0.01% tham số).

### 3.2 LoRA-based Continual Learning (trọng tâm 2023–2026)

*   **O-LoRA (Orthogonal LoRA)** — Wang et al. Findings EMNLP 2023 (arXiv:2310.14152): Với mỗi task $t$, học cặp $(A_t, B_t)$ mới với ràng buộc trực giao giữa column span của $A_t$ và các $A_i$ trước đó:
    $$\mathcal{L}_{\text{orth}} = \sum_{i < t} \|A_i^\top A_t\|_F^2, \quad \mathcal{L}_{\text{total}} = \mathcal{L}_{\text{task}} + \lambda \mathcal{L}_{\text{orth}}$$
    Vượt qua LFPT5, EWC, Replay trên các benchmark 5-task và 15-task long-sequence với T5-large.
*   **N-LoRA (Null-space/Non-collision)** — Yang et al. arXiv:2410.10179: Lập luận rằng trực giao là chưa đủ, vấn đề thực tế nằm ở sự va chạm tham số (parameter collision/chồng chéo support). Áp dụng $\ell_1$ sparsity giúp đạt trực giao tốt hơn **4.1 $\times$**, ít va chạm hơn **58.1 $\times$**, và tăng $+2.9\%$ độ chính xác so với O-LoRA.
*   **InfLoRA** — Liang & Li CVPR 2024 (arXiv:2404.00228): Tái tham số hóa $W = W_{t-1} + A_t B_t$, thiết kế $B_t$ sao cho subspace của nó trực giao với past-task input features (null-space projection). Chỉ huấn luyện $A_t$. Trên ImageNet-R 20-task: ACC đạt 71.01% so với CODA-P (64.57%), DualPrompt, và L2P.
*   **LoRAMoE** — Dou et al. ACL 2024 (arXiv:2312.09979): Thiết lập nhiều LoRA expert ở mỗi layer FFN cùng ràng buộc cân bằng cục bộ (localized balancing constraint). Experts được phân thành 2 nhóm: một để giữ kiến thức thế giới (world knowledge), một cho các tác vụ downstream $\rightarrow$ ngăn cản quá trình SFT xóa bỏ tri thức pretraining. Giữ vững hiệu năng trên NQ, TriviaQA trong khi cải thiện downstream.
*   **MoLA** — Gao et al. Findings NAACL 2025 "Higher Layers Need More LoRA Experts": Phân bổ số lượng expert tăng dần theo chiều sâu của lớp (ví dụ kiến trúc MoLA-$\nabla$ 2,4,6,8) do các tầng trên hưởng lợi nhiều hơn từ sự đa dạng của expert. Mô hình đạt hiệu quả tương đương MoLA-8888 nhưng giảm ~40% tham số.
*   **I-LoRA** — Ren et al. arXiv:2402.18865: Thiết kế bộ nhớ kép (dual-memory) với fast-learner và slow-learner (EMA), khai thác tính liên thông chế độ (mode connectivity) giữa các điểm tối ưu của các tác vụ liên tiếp. Tăng $+11\%$ trên 8 tập con của TRACE với Llama-2-7B.
*   **SAPT** — Zhao et al. ACL 2024 (arXiv:2401.08295): Shared Attentive Learning & Selection giúp đồng bộ PET-learning và PET-selection bằng attention weights; kết hợp Attentive Reflection Module sử dụng pseudo-samples. Vượt trội O-LoRA, L2P, LFPT5 trên SuperNI 15-task.
*   **CURLoRA** — Fawi arXiv:2408.14572: Dùng phân rã ma trận CUR ($W = C U R$) thay cho khởi tạo ngẫu nhiên, chỉ huấn luyện ma trận $U$ (khởi tạo bằng 0). Giúp Mistral-7B/Llama-2 giữ nguyên perplexity cơ bản khi fine-tune tuần tự.
*   **CL-LoRA** (He et al. CVPR 2025): Kiến trúc adapter kép (dual-adapter) gồm phần chia sẻ tác vụ (task-shared) khởi tạo trực giao ngẫu nhiên và phần đặc thù tác vụ (task-specific) với trọng số khối (block-wise weights).
*   **LoRAHub** (Huang et al. COLM 2024, arXiv:2307.13269): Tổ hợp các adapter theo công thức $\sum_i w_i(B_i A_i)$ thông qua tối ưu không gradient (Nelder-Mead/CMA-ES).
*   **LoraRetriever** (Zhao et al. Findings ACL 2024): Dùng sentence embedding đã được instruction-tuned để truy hồi top-$k$ LoRA và kết hợp bằng parameter-fusion hoặc output-mixture.
*   **MoLE** (Wu et al. ICLR 2024): Thiết kế phân cấp gating layer-wise trên một pool các LoRA.
*   **MOELoRA** (Liu et al. SIGIR 2024): Gating kích hoạt theo tác vụ (task-motivated gate) hướng tới các tác vụ y học đa nhiệm (PromptCBLUE).
*   *Các biến thể khác:* 
    *   **CLoRA** (Lu et al. ACL 2025, arXiv:2410.16801): Regularization trực giao null-space một giai đoạn không cần dữ liệu tác vụ cũ.
    *   **LoRA-Null** (arXiv:2503.02659): Khởi tạo từ các vector null-space.
    *   **MELoRA** (Ren et al. ACL 2024): Mini-ensemble khối chéo (block-diagonal).
    *   **GS-LoRA** (CVPR 2024): Cắt giảm forgetting thông qua tối ưu hóa group-sparse.
    *   **Online-LoRA** (WACV 2025): Hỗ trợ streaming không phụ thuộc tác vụ (task-free).
    *   **SD-LoRA** (ICLR 2025): Khả năng mở rộng decoupled có điều chỉnh độ lớn (magnitude scaling).
    *   **OPLoRA** (arXiv:2510.13003): Chiếu hai phía dựa trên phân rã SVD.
    *   **OLieRA** (arXiv:2509.06100): Tham số hóa dựa trên nhóm Lie (Lie group).
    *   **PEARL** (arXiv:2505.11998): Phân bổ rank động.
    *   **LoRAC-IPC** (Pattern Recognition 2025): Tổ hợp trực giao kèm ràng buộc tham số quan trọng ($+6.35\%$ trên Split CIFAR-100).

### 3.3 Prompt-based CL

*   **L2P** (Wang et al. CVPR 2022): Thiết lập pool các prompt đi kèm cơ chế truy hồi key-query.
*   **DualPrompt** (Wang et al. ECCV 2022): Phân chia thành G-prompts (task-invariant) ở các tầng nông và E-prompts (task-specific) ở các tầng sâu.
*   **CODA-Prompt** (Smith et al. CVPR 2023): Thay thế cơ chế truy hồi rời rạc bằng tổ hợp tuyến tính dựa trên attention:
    $$P = \sum_i \alpha_i(x) P_i$$
    và thực hiện huấn luyện end-to-end.
*   **S-Prompts** (Wang et al. NeurIPS 2022): Áp dụng cho Domain-IL với phân vùng miền bằng K-means + K-NN.
*   **ProgPrompt** (Razdaibiedina et al. ICLR 2023): Nối tuần tự (sequentially concatenate) các soft prompt; đạt $+20\%$ trên luồng tác vụ dài 15-task.

### 3.4 MoE cho CL

*   **LLaMA-MoE** (Zhu et al. EMNLP 2024, arXiv:2406.16554): Chia nhỏ FFN của LLaMA-2-7B thành $N$ expert, thực hiện continual pre-train trên 200 tỷ token.
*   **Lifelong-MoE** (Chen et al. ICML 2023): Mở rộng số lượng expert kết hợp các phương pháp regularization.
*   **DES-MoE**: Đạt **89% giảm thiểu forgetting** khi dịch chuyển miền từ $2 \rightarrow 6$.
*   **MoE-LPR** (AAAI 2025): Thiết kế cho việc mở rộng ngôn ngữ qua hai giai đoạn.
*   **Mixtral-based** & **MixLoRA** (arXiv:2404.15159): Nhúng kiến trúc sparse MoE bên trong các cấu trúc LoRA.

---

## 4. Model merging, knowledge editing, và synthetic replay

### 4.1 Model merging (dominant 2023–2026)

Đơn vị nền tảng là **task vector**:
$$\tau = \theta_{\text{ft}} - \theta_{\text{pre}}$$
(Ilharco et al. ICLR 2023, arXiv:2212.04089), cho phép thực hiện cộng tác vụ $\theta_{\text{new}} = \theta_{\text{pre}} + \lambda \sum_t \tau_t$, loại bỏ tri thức (negation) cho unlearning, và giải bài toán tương tự (analogy).

*   **TIES-Merging** (Yadav et al. NeurIPS 2023, arXiv:2306.01708) gồm ba bước (Trim – Elect sign – Sparse merge):
    1.  *Trim:* Chỉ giữ lại top-$k\%$ tham số có độ lớn (magnitude) cao nhất.
    2.  *Elect sign:* Chọn dấu thống trị dựa trên đa số:
        $$\gamma_p = \text{sgn}\left(\sum_t \tau_t^p\right)$$
    3.  *Sparse merge:* Hợp nhất các tham số không giao nhau có cùng dấu thống trị.
*   **DARE (Drop And REscale)** (Yu et al. ICML 2024, arXiv:2311.03099):
    $$\tilde{\tau} = \frac{m \odot \tau}{1 - p}, \quad m \sim \text{Bernoulli}(1 - p)$$
    Công thức cho phép loại bỏ (drop) từ 90–99% delta parameters ở các mô hình $\ge 70\text{B}$. Việc hợp nhất WizardLM + WizardMath giúp nâng GSM8K từ $2.2\% \rightarrow 66.3\%$ nhờ DARE và weight averaging.
*   **Model Soups** (Wortsman et al. ICML 2022): Thuật toán greedy soup giúp ViT-G đạt 90.94% top-1 trên ImageNet.
*   **Fisher-Weighted Merging** (Matena & Raffel NeurIPS 2022):
    $$\theta_m^j = \frac{\sum_i \alpha_i F_i^j \theta_i^j}{\sum_i \alpha_i F_i^j}$$
*   **RegMean** (Jin et al. ICLR 2023) — Tính trung bình bình phương tối thiểu dạng closed-form cho mỗi layer:
    $$W_m = \left( \sum_i X_i^\top X_i \right)^{-1} \left( \sum_i X_i^\top X_i W_i \right)$$
    chỉ yêu cầu ma trận Gram, hoàn toàn không cần dữ liệu thô.
*   **Model Breadcrumbs** (Davari & Belilovsky ECCV 2024): Đặt về 0 cả các outlier lớn và các dao động (perturbation) nhỏ (đạt mức độ thưa ~85%).
*   **AdaMerging** (Yang et al. ICLR 2024, arXiv:2310.02575): Học trọng số gộp $\{\lambda_t\}$ hoặc $\{\lambda_{t,l}\}$ bằng cách tối thiểu hóa entropy trên dữ liệu test không nhãn (unlabeled test data) — mang tính transductive nhưng tăng +3–5% trên 8-task CLIP-ViT.
*   **EMR-Merging** (Huang et al. NeurIPS 2024 Spotlight, arXiv:2405.17461): Phương pháp gộp không cần huấn luyện (tuning-free), giúp ViT-L/14 trên 8 tác vụ đạt 93.7% hiệu năng (so với 94.2% huấn luyện riêng lẻ và 93.5% của chặn trên đa nhiệm MTL).
*   **Consensus TA/TALL-Masks** (Wang et al. ICML 2024): Loại bỏ các trọng số mang tính cục bộ ích kỷ (selfish weights).
*   **DELLA** (arXiv:2406.11617): Ứng dụng MAGPRUNE với xác suất drop dựa trên thứ hạng độ lớn (magnitude-rank drop probability).
*   **PCB-Merging** (Du et al. NeurIPS 2024): Cân bằng sự cạnh tranh tham số (parameter competition balancing).
*   **Evolutionary Model Merging** (Akiba et al. *Nature MI* 2024): Sử dụng thuật toán tiến hóa CMA-ES trên không gian tham số (Parameter Space) và không gian luồng dữ liệu (Data Flow Space); hỗ trợ gộp xuyên miền như kết hợp LLM tiếng Nhật và mô hình toán tiếng Anh.
*   **MergeKit** (Goddard et al. EMNLP Industry 2024): Bộ công cụ (toolkit) gộp mô hình mã nguồn mở tiêu chuẩn cho cộng đồng.

### 4.2 Model/knowledge editing

*   **ROME** (Meng et al. NeurIPS 2022): Phương pháp truy vết nhân quả (causal tracing) chỉ ra rằng tầng MLP giữa tại subject token cuối cùng đóng vai trò quyết định; cập nhật dạng hạng một (rank-one update):
    $$\hat{W} = W + \Lambda(C^{-1}k^*)^\top, \quad C = K K^\top$$
    với $v^*$ được tối ưu bằng Gradient Descent.
*   **MEMIT** (Meng et al. ICLR 2023, arXiv:2210.07229): Cho phép chỉnh sửa hàng loạt (mass-edit) hơn 10,000+ sự thật vào các mô hình GPT-J/GPT-NeoX-20B, phân phối lượng hiệu chỉnh $\Delta$ qua $R$ layer.
*   **MEND** (Mitchell et al. ICLR 2022): Sử dụng hypernetwork để biến đổi gradient dạng rank-1.
*   **SERAC** (Mitchell et al. ICML 2022): Cấu trúc bán tham số (semi-parametric) — giữ nguyên mô hình gốc, kết hợp bộ nhớ chỉnh sửa tường minh (explicit edit memory) và mô hình phản thực tế (counterfactual model).
*   **GRACE** (Hartvigsen et al. NeurIPS 2023, arXiv:2211.11031): Thêm adapter key-value rời rạc giữa các lớp với cơ chế truy hồi quả cầu $\epsilon$ ( $\epsilon$-ball retrieval) — cho phép thực hiện hàng nghìn chỉnh sửa tuần tự.
*   **T-Patcher** (Huang et al. ICLR 2023): Bổ sung thêm 1 neuron lỗi/sửa vào tầng FFN cuối.
*   **WilKE** (Hu et al. Findings ACL 2024): Chọn tầng cập nhật động, tăng hiệu quả $+46.2\%$ trên GPT2-XL và $+67.8\%$ trên GPT-J qua 1,024 lượt chỉnh sửa trọn đời.
*   **AlphaEdit** (Fang et al. ICLR 2025, arXiv:2410.02355): Theo triết lý định vị rồi sửa (locate-then-edit) nhưng chiếu $\Delta$ lên không gian vô hiệu (null-space) của ma trận hiệp biến tri thức cần bảo tồn:
    $$\hat{\Delta} = P \Delta \quad \text{với} \quad P = I - U U^\top$$
    Bảo đảm điều kiện $W \hat{\Delta}^\top K_0 = 0$, giúp không gây nhiễu lên tri thức cũ. Đạt hiệu năng tăng trung bình **$+36.7\%$** khi tích hợp vào MEMIT/RECT/PRUNE trên Llama-3-8B.
*   **WISE** (NeurIPS 2024): Phân tách rõ ràng giữa bộ nhớ phụ (side memory) và bộ nhớ chính thông qua bộ định tuyến (router).
*   **Hu et al.** (arXiv:2408.07413): Chứng minh mặt lý thuyết rằng việc chỉnh sửa trọn đời không hao tổn chỉ khả thi khi các biểu diễn kiến thức gần như trực giao; dưới hiện tượng chồng chập (superposition), nhiễu loạn (interference) sẽ tích lũy tuyến tính.
*   **EasyEdit** (Wang et al. ACL 2024 Demo): Thư viện chuẩn hóa việc triển khai các phương pháp chỉnh sửa tri thức.

### 4.3 Synthetic/generative replay cho LLM

*   **LAMOL** (Sun, Ho & Lee ICLR 2020, arXiv:1909.03329): Mô hình ngôn ngữ dạng GPT học đồng thời mục tiêu QA và mục tiêu sinh mẫu (sample-generation) với các token đặc thù cho từng tác vụ; khi học tác vụ $T_k$, mô hình tự sinh pseudo-sample của các tác vụ trước đó. Thu hẹp khoảng cách hiệu năng xuống chỉ còn 2–3% so với chặn trên đa nhiệm (multitask upper bound) trên decaNLP.
*   **Self-Synthesized Rehearsal (SSR)** — Huang et al. ACL 2024 (arXiv:2403.01244): Sử dụng base LLM để tự sinh các cặp Input-Output nhân tạo thông qua In-Context Learning với vài mẫu ví dụ (few-shot demo), sau đó dùng mô hình mới nhất để tinh chỉnh kết quả và lọc lại dựa trên độ đa dạng. **Không cần dữ liệu gốc** — cực kỳ hữu ích cho các public checkpoint. Giúp duy trì năng lực trên AlpacaEval/MMLU trong suốt quá trình học tuần tự trên SuperNI.
*   **SEEKR** (He et al. EMNLP 2024, arXiv:2411.06171): Chưng cất attention (attention distillation) trên các head được chọn lọc theo độ dễ quên $F_{l,h}$ và độ nhạy tác vụ $S_{l,h}$. SEEKR với chỉ **1% replay data** đạt chỉ số OP = 54.99 trên benchmark TRACE, vượt qua DER++ sử dụng cùng 1% replay (OP = 49.22) trong khi tối ưu tốc độ gấp 10 lần.
*   **Magpie** (Xu et al. ICLR 2025, arXiv:2406.08464): Gợi ý mô hình chỉ bằng các template câu hỏi trống (pre-query template) để mô hình tự "ảo tưởng" (hallucinate) ra cả câu hỏi và câu trả lời — tạo ra dữ liệu instruction tự sinh hoàn toàn không cần seed data. Huấn luyện Llama-3-8B trên Magpie cho ra kết quả tương đương bản Llama-3-8B-Instruct chính thức.

### 4.4 Function-preserving growth

*   **Net2Net** (Chen, Goodfellow, Shlens ICLR 2016): Net2WiderNet giúp sao chép cột và điều chỉnh scale, Net2DeeperNet chèn thêm tầng đồng nhất (identity layer).
*   **bert2BERT** (Chen et al. ACL 2022): Khởi tạo tham số bảo toàn chức năng (Function-Preserving Initialization - FPI) cho cấu trúc Transformer + AKI; giúp tiết kiệm **~45% chi phí tính toán FLOPs** so với huấn luyện từ đầu.
*   **LiGO** (Wang et al. ICLR 2023, arXiv:2303.00980): Học toán tử tăng trưởng tuyến tính thưa (sparse linear growth operator) $M$ thỏa mãn:
    $$\theta_{\text{large}} = M \cdot \text{vec}(\theta_{\text{small}})$$
    tiết kiệm 44.7% chi phí huấn luyện BERT-BASE.
*   **MSG** (ICLR 2024): Đưa ra 6 phép biến đổi bảo toàn chức năng nghiêm ngặt.
*   **LLaMA-Pro block expansion** (Wu et al. ACL 2024, arXiv:2401.02415): Chèn thêm các khối Transformer được khởi tạo bằng 0 (mang tính chất identity khi bắt đầu), và chỉ huấn luyện trên các khối mới này. LLaMA Pro-8.3B được phát triển từ LLaMA2-7B với 80 tỷ token về code và toán vẫn giữ nguyên vẹn năng lực MMLU tổng quát.

---

## 5. Các tiến bộ 2024–2026: continual pre-training và scaling

### 5.1 Công thức continual pre-training (Ibrahim et al. 2024 — bài then chốt)

Ibrahim et al. 2024 trong nghiên cứu "Simple and Scalable Strategies to Continually Pre-train LLMs" (TMLR 2024, arXiv:2403.08763, Mila) xác lập công thức chuẩn thông qua thực nghiệm trên các quy mô 405M và 10B tham số:
1.  **Re-warm Learning Rate (LR):** Đưa LR về gần mức đỉnh (peak) ban đầu.
2.  **Re-decay Cosine:** Thực hiện giảm LR theo đường cosine về cùng mức tối thiểu (min LR).
3.  **Replay dữ liệu cũ:** Tỷ lệ **5% là đủ cho dịch chuyển nhẹ** (weak shift, ví dụ Pile $\rightarrow$ SlimPajama), nhưng cần đến **25% cho dịch chuyển mạnh** (strong shift, ví dụ tiếng Anh $\rightarrow$ tiếng Đức). Tỷ lệ 10% có cải thiện hiệu năng nhưng vẫn gặp hiện tượng forgetting nhiều hơn.

*Lưu ý:* Việc giữ nguyên LR cuối quá thấp dẫn đến hiện tượng thích ứng kém (under-adaptation); trong khi re-warm trực tiếp từ minimum LR gây ra hiện tượng spike về loss và làm nghiêm trọng hóa forgetting. Nghiên cứu đề xuất sử dụng **infinite/WSD schedules** (chu kỳ hằng số hoặc cosine với sàn LR khác 0). 

Các nghiên cứu bổ trợ:
*   **Gupta et al. 2023** (ICML ES-FoMo Workshop) là nền tảng ban đầu của ý tưởng này.
*   **Parmar et al. 2024 NVIDIA** (arXiv:2407.07263) trong "Reuse, Don't Retrain" xác nhận việc duy trì $\eta_{\text{min}} > 0$ ($\approx 1.5\text{e}-5$) giúp giữ nguyên giá trị sử dụng (utility); tăng **$+9\%$ độ chính xác trung bình** so với lịch trình LR cơ sở nhờ cơ chế trộn dữ liệu hai giai đoạn kết hợp QA ở giai đoạn 2.
*   **Yıldız/Shi et al. 2025** (arXiv:2503.02844) đề xuất lịch trình infinite LR vượt trội cosine lặp lại cho các luồng dữ liệu streaming.
*   **Guo et al. 2024** (arXiv:2406.14833) định nghĩa hiện tượng **stability gap** — mức sụt giảm loss tạm thời ở đầu mỗi phase học tập ngay cả khi có sử dụng replay; đề xuất huấn luyện lặp (multiple epochs) trên các tập con chất lượng cao.
*   **Bethune et al. 2025 Apple** (arXiv:2502.06042) tìm ra quy luật scaling với tỷ lệ tiêm dữ liệu pretraining (injection fraction) $p$: chỉ cần **1% dữ liệu tiêm vào** đã đủ ngăn chặn forgetting trên phân phối pretraining mà không ảnh hưởng tiêu cực đến loss trên miền đích.
*   **D-CPT Law** (Que et al. 2024): Xây dựng quy luật scaling tích hợp (gồm tokens, kích thước mô hình, tỷ lệ domain) cho quá trình thích ứng tên miền liên tục (continual domain adaptation).
*   **CMR scaling law** và **ALMR/LR law** (Gu et al. 2409.06624): Nghiên cứu trên 1.7T tokens CPT trên kiến trúc Llama-3-8B.
*   **Xie et al. 2024** (Findings ACL 2024): Thử nghiệm FinPythia-6.9B với thuật toán ETS-DACP/ETA-DACP đạt hiệu quả tương đương DACP đầy đủ nhưng chỉ dùng **10% kích thước corpus**.
*   **Li & Lee 2024** (arXiv:2401.03129): Thực hiện continual pre-train LLaMA-2-7b-chat trên tiếng Trung phồn thể; chỉ ra rằng không có kỹ thuật đóng băng (freezing) nào giải quyết triệt để forgetting trên các mô hình đã căn chỉnh (aligned model); trong đó độ tin cậy về an toàn (safety reliability) bị suy giảm nặng nề nhất.

### 5.2 Scaling laws của forgetting

*   **Luo et al. 2023** (arXiv:2308.08747): Đánh giá các mô hình BLOOMZ/mT0/LLaMA/Alpaca từ 1B–7B chỉ ra **forgetting xảy ra nghiêm trọng hơn ở các mô hình lớn hơn về mặt trị tuyệt đối** (ví dụ Llama-7B giảm năng lực MMLU-human từ $34.72\% \rightarrow 26.8\%$ khi học đơn tác vụ, và giữ ở mức $30.0\%$ nếu có 10K mẫu replay). Dạng kiến trúc Decoder-only (BLOOMZ) ít bị forgetting hơn dạng Encoder-Decoder (mT0). Việc kết hợp một lượng nhỏ dữ liệu instruction tuning tổng quát luôn giúp giảm forgetting — tạo ra hiệu ứng neo giữ tri thức ("general-knowledge anchor effect").
*   **Kalajdzievski 2024 Tenyx** (arXiv:2401.05605): Fine-tune mô hình Llama-2-7B-chat LoRA với các mức rank và tài nguyên token khác nhau; chỉ ra **quan hệ tuyến tính nghịch đảo (inverse-linear) mạnh mẽ** giữa mức độ cải thiện loss khi fine-tune và mức độ forgetting (đo qua khoảng cách cross-entropy). Sự quên lãng tuân theo quy luật lũy thừa dịch chuyển (shifted power law) phụ thuộc vào số lượng tham số được tune (rank) và số bước cập nhật gradient. Việc dừng sớm (early stopping) hoặc giảm rank không giúp triệt tiêu forgetting mà chỉ dịch chuyển điểm vận hành dọc theo đường cong Pareto tối ưu.
*   **Biderman et al. 2024** (arXiv:2405.09673): LoRA học kém hơn trên miền đích so với full FT nhưng tạo ra perturbation rank thấp hơn từ **10–100 $\times$**, giúp bảo toàn tốt năng lực cơ sở vượt trội mọi kỹ thuật regularization khác. Bản chất là sự đánh đổi cơ bản giữa plasticity và stability thông qua việc căn chỉnh siêu tham số.

### 5.3 Continual instruction tuning và alignment

*   **TRACE** (Wang et al. 2023): Đề xuất phương pháp RCL (Reasoning-augmented CL) giúp bổ sung các lập luận Chain-of-Thought (CoT rationales).
*   **InsCL** (Wang et al. NAACL 2024, arXiv:2403.11435): Kết hợp replay động dựa trên khoảng cách Wasserstein (Wasserstein-distance dynamic replay) và độ phức tạp thông tin InsInfo; đạt $+3.0$ Relative Gain so với Random Replay, và $+27.96$ so với thiết lập không Replay trên LLaMA-7B 16-task.
*   **CoIN** (Chen et al. NeurIPS 2024): Nghiên cứu trên mô hình LLaVA-1.5-7B với 10 bộ dữ liệu/8 tác vụ cho thấy forgetting xảy ra chủ yếu do suy giảm khả năng tuân thủ chỉ dẫn (instruction-following loss) chứ không phải do sự suy rã tri thức (knowledge decay).

Các phương pháp nổi bật giai đoạn 2024–2025:
*   **MIGU** (Du et al. Findings EMNLP 2024, arXiv:2406.17245) — *Magnitude-based Gradient Updating:* Chỉ cập nhật top-$k$ tham số có độ lớn gradient L1 cao nhất; phương pháp không cần replay (rehearsal-free), không cần nhãn (label-free) và dễ dàng tích hợp (plug-and-play).
*   **CorDA** (Yang et al. NeurIPS 2024, arXiv:2406.05223) — *Context-oriented Decomposition Adaptation:* Phân tích SVD trên tích ma trận trọng số $\times$ hiệp biến kích hoạt (activation covariance). Chế độ **KPA mode** huấn luyện các thành phần có trị riêng nhỏ nhất nhằm giảm thiểu forgetting; chế độ **IPA mode** huấn luyện các thành phần lớn nhất cho hiệu năng vượt trội full FT. Đã được tích hợp vào thư viện HuggingFace PEFT.
*   **DAM** (Cheng et al. ICLR 2024, arXiv:2403.08755) — *Dynamic Adapter Merging:* Sử dụng bộ định tuyến không tham số tính điểm cho từng đầu vào, thực hiện gộp trọng số trực tiếp trong quá trình chạy (on-the-fly). Tăng $+9.1\%$ hiệu năng và giảm $1.9\%$ forgetting trên các tác vụ video-QA liên tục.
*   **LiNeS** (Wang et al. ICLR 2025, arXiv:2410.17146) — *Layer-increasing Network Scaling:* Tỷ lệ hóa tuyến tính lượng hiệu chỉnh $\Delta \theta$ theo độ sâu, giữ các tầng nông gần với trạng thái pretrained. Đạt $+4.5\%$ trên 8 tác vụ tính toán; khi kết hợp với Rewarded Soups cho kết quả vượt trội đường baseline Pareto.
*   **Spurious Forgetting** (ICLR 2025): Phát hiện ra rằng sự sụt giảm hiệu năng thường do mất căn chỉnh tác vụ (task alignment loss) hơn là mất mát tri thức thực tế; việc đóng băng các tầng đáy (bottom-layer freezing) giúp giảm thiểu đáng kể hiện tượng này.
*   **FGGM 2026** (*Fisher-Guided Gradient Masking*): Đạt hiệu quả $+9.6\%$ so với SFT thông thường và $+4.4\%$ so với MIGU trên benchmark TRACE.
*   **Branch-and-Merge** (Alexandrov et al. 2024): Được áp dụng thực tế trong dự án BgGPT-Gemma-2 để phát triển mô hình tiếng Bulgaria (Bulgarian CPT).
*   **TiC-LM** (arXiv:2504.02107): Tiền huấn luyện liên tục theo thời gian thực (time-continual pretraining) sử dụng các bản dump dữ liệu tuần tự từ CommonCrawl, việc áp dụng replay giúp giảm thiểu khoảng 60% mức hối tiếc loss (held-out loss regret).

### 5.4 Continual alignment: weight averaging dominant

*   **Rewarded Soups** (Ramé et al. NeurIPS 2023, arXiv:2306.04488): Fine-tune $N$ policy độc lập trên $N$ phần thưởng đại diện (reward proxy) từ cùng một điểm khởi tạo SFT, sau đó nội suy tuyến tính trọng số tại bước suy luận; dựa trên lý thuyết kết nối chế độ tuyến tính (Linear Mode Connectivity).
*   **WARM** (Ramé et al. ICML 2024, arXiv:2401.12187): Tính trung bình trọng số của nhiều mô hình phần thưởng (multiple Reward Models - RM); chính sách huấn luyện với WARM đạt **79.4% win rate** so với việc dùng RM đơn lẻ.
*   **WARP** (Ramé et al. arXiv:2406.16768): Quy trình merging ba giai đoạn: EMA policy với KL anchor $\rightarrow$ SLERP $\rightarrow$ LERP hướng về điểm khởi đầu SFT. Thuật toán này được ứng dụng chính thức trong dòng mô hình Gemma 2.
*   **Alignment tax** (Ouyang et al. 2022 InstructGPT): Khái niệm mô tả việc học RLHF làm sụt giảm năng lực tổng quát trên MMLU/HellaSwag.
*   **Lin et al. EMNLP 2024** (arXiv:2309.06256): Đưa ra phương pháp hệ thống *Heterogeneous Model Averaging (HMA)* với các tỷ lệ gộp khác nhau theo từng tầng; việc gộp ở các tầng thấp (low-level layers) mang lại hiệu quả cao nhất — vượt trội hơn các phương pháp replay, EWC, LoRA, hay KL-penalty đơn thuần.
*   **Online Merging Optimizers** (Lu et al. arXiv:2405.17461): Tích hợp kỹ thuật gộp số học tác vụ (task-arithmetic merging) trực tiếp vào từng bước cập nhật RLHF.
*   **LIMA** (Zhou et al. NeurIPS 2023): Giả thuyết căn chỉnh bề mặt (*Superficial Alignment Hypothesis*) chứng minh chỉ cần 1,000 prompt chất lượng cao huấn luyện SFT là đủ căn chỉnh mô hình, tránh được hiện tượng forgetting do RLHF.
*   **Kumar et al. ICLR 2022** trong "Fine-Tuning can Distort Pretrained Features" (arXiv:2202.10054): Chỉ ra full FT đạt kết quả tốt hơn linear probe 2% trên dữ liệu cùng phân phối (In-Distribution - ID) nhưng kém hơn tới **7% trên dữ liệu ngoài phân phối** (Out-of-Distribution - OOD); phương pháp LP-FT (huấn luyện linear probe trước, sau đó fine-tune toàn bộ) giúp tăng $+1\%$ ID và **$+10\%$ OOD**.
*   **$\beta$-DPO** (Wu et al. NeurIPS 2024, arXiv:2407.08639): Sử dụng hệ số $\beta$ động ở cấp độ batch (dải giá trị 0.1–0.5, mặc định là 0.1).
*   **DPH-RL** (arXiv:2509.07430): Chỉ ra hàm phạt reverse-KL hướng tới tìm kiếm chế độ dễ dẫn đến sụp đổ độ đa dạng (diversity collapse); đề xuất dùng forward-KL hoặc khoảng cách Jensen-Shannon (JS) giúp bao phủ khối lượng phân phối cũ, bảo tồn tri thức pretraining và cải thiện đồng thời cả Pass@1 và Pass@k.

### 5.5 Các survey tổng hợp

| Survey | Taxonomy chính |
| :--- | :--- |
| **Wu et al. 2024** "Continual Learning for LLMs: A Survey" (arXiv:2402.01364) | Quy trình 3 giai đoạn: CPT $\rightarrow$ CIT $\rightarrow$ CA |
| **Shi et al. 2025** ACM CSUR (arXiv:2404.16789) | Cắt dọc (General $\rightarrow$ Specific) $\times$ Chiều ngang (Thời gian/Tên miền) $\times$ Quy trình 3 giai đoạn |
| **Wang et al. 2024** IEEE TPAMI | Khung lý thuyết thống nhất cổ điển: Regularization / Replay / Architecture / Optimization / Representation |
| **Yang et al. ACM CSUR 2025** (10.1145/3705725) | Tập trung vào Foundation-LM: phân tích sâu CPT, PEFT, IT-CL |
| **Zheng et al. 2024** "Towards Lifelong Learning of LLMs" | Phạm vi tiếp cận rộng (Broad scope) |

---

## 6. Các bài toán con và giải pháp

### 6.1 Thêm ngôn ngữ mới không quên tiếng Anh

*   **Chinese-LLaMA** (Cui et al. arXiv:2304.08177): Mở rộng từ điển thêm $+20,000$ token (tổng đạt 49,953), áp dụng quy trình LoRA hai giai đoạn: Giai đoạn 1 chỉ huấn luyện các tầng nhúng mới (embeddings) giữ nguyên backbone; Giai đoạn 2 áp dụng LoRA trên các ma trận QKVO+MLP với corpus kích thước 20GB (bản base) hoặc 120GB (bản Plus). Thiết lập LR đỉnh đạt 2e-4, giảm theo cosine với 5% số bước khởi động (warmup).
*   **Aya** (Cohere, Üstün et al. 2024, arXiv:2402.07827): Huấn luyện mô hình mT5-13B trên 101 ngôn ngữ với 203 triệu cặp instruction từ tập Aya Collection (18.9 triệu mẫu template), Aya Dataset (204K mẫu do người viết), Aya Collection translated (7.53 triệu mẫu dịch qua NLLB), và dữ liệu tổng hợp ShareGPT. Phiên bản **Aya-23** (quy mô 8B/35B) phát triển trên nền Cohere Command hỗ trợ 23 ngôn ngữ đạt hiệu năng vượt trội Gemma-1.1-7B-it.
*   **SeaLLM-v2** (DAMO-NLP): Thực hiện continued pretrain trên Mistral-7B — đạt điểm số GSM8K zero-shot CoT là 78.2% (dẫn đầu trong nhóm 7B), vượt trội GPT-3.5 trên các tác vụ dịch thuật và giải toán GSM8K tiếng Trung/Việt/Indo/Thái.
*   **Sailor** (EMNLP 2024 Demo): Phát triển các phiên bản từ 0.5B–14B trên nền Qwen 1.5 với 200–400 tỷ token khu vực Đông Nam Á, sử dụng dữ liệu tiếng Anh SkyPile làm phần replay, áp dụng kỹ thuật trộn mã ở cấp độ tài liệu (document-level code-switching), và hồi quy tuyến tính (linear regression) trên 64 proxy trộn để dự đoán loss.
*   **Typhoon** (SCB 10X): Dòng mô hình tối ưu riêng cho tiếng Thái.
*   **BLOOM+1** (Yong et al. ACL 2023): Thử nghiệm trên 15+ chiến lược; kết luận việc dùng adapter ngôn ngữ MAD-X kết hợp invertible adapters trên nền BLOOMZ là phương án tối ưu.
*   **Gogoulou et al. TSD 2024** "Continual Learning Under Language Shift": Chỉ ra rằng khả năng truyền tri thức ngược (backward transfer) có thể đạt giá trị dương hoặc âm tùy thuộc vào thứ tự học ngôn ngữ, độ tương đồng cú pháp (syntactic similarity) và mức độ nhiễm chéo dữ liệu ngôn ngữ (language contamination).
*   **Huang et al. 2024** "LLaMA Beyond English": Chứng minh việc fine-tune quy mô nhỏ mang lại hiệu quả cao hơn so với việc pre-train thô bạo (brute-force) bằng luồng dữ liệu 30 tỷ token CPT.
*   **Yamaguchi et al. 2024** (arXiv:2406.11477): Chỉ ra rằng với chỉ **0.01GB dữ liệu ngôn ngữ đích**, việc mở rộng vocab kết hợp LoRA rank = 8 vẫn mang lại hiệu năng tốt. Đưa ra heuristic: nên mở rộng từ điển khi hệ chữ viết bị phân mảnh (như tiếng Trung, Thái, Lào); và giữ nguyên từ điển gốc nếu ngôn ngữ đích dùng hệ chữ Latin.

### 6.2 Domain adaptation (medical, legal, code)

*   **Medical (Y học):**
    *   **Meditron** (Chen et al. arXiv:2311.16079): Phát triển trên Llama-2-7B/70B kết hợp GAP-Replay trên 48.1 tỷ token (gồm tài liệu Guidelines 46K + Abstracts 16.1M + Papers 5M + 400M token RedPajama tổng hợp $\rightarrow$ tỷ lệ replay đạt **0.83%**). Meditron-70B đạt kết quả **70.2% trên MedQA**, vượt qua GPT-3.5 và Med-PaLM, tiếp cận sát nút trong khoảng 5% so với GPT-4.
    *   **Me-LLaMA** (Xie et al. *Nature Digital Medicine* 2025): CPT với 129 tỷ token; giữ vững năng lực tổng quát MMLU trong khi cải thiện mạnh mẽ MedQA; ngược lại PMC-LLaMA bị giảm sút ở cả hai khía cạnh, và Meditron bị mất năng lực MMLU.
    *   **BioMedLM** (Stanford+MosaicML, arXiv:2403.18421): Huấn luyện hoàn toàn từ đầu (from scratch) mô hình quy mô 2.7B trên 300 tỷ token y học; đạt điểm số MedQA là 50.3% (tốt nhất trong phân khúc 2.7B).
    *   **HuatuoGPT-II**: Áp dụng quy trình huấn luyện một giai đoạn (one-stage training) bằng cách trộn chung dữ liệu domain chuyên sâu và dữ liệu instruction.
*   **Legal (Pháp luật):**
    *   **SaulLM-7B** (Colombo et al. arXiv:2403.03883, Equall): Mistral-7B + 30 tỷ token pháp lý kết hợp **~2% replay** từ nguồn Wiki/StackExchange/GitHub. Phiên bản Instruct vượt phiên bản gốc Mistral-Instruct +6 điểm trên benchmark LegalBench-Instruct.
    *   **SaulLM-141B** (NeurIPS 2024): Phát triển trên Mixtral + 540 tỷ token pháp luật kết hợp căn chỉnh DPO/DSO vượt trội GPT-4 trung bình trên LegalBench-Instruct.
    *   **ChatLaw-4x7B**: Kiến trúc dạng MoE chuyên biệt chống ảo giác (anti-hallucination) trong lĩnh vực luật.
*   **Code (Lập trình):**
    *   **Code Llama** (Rozière et al. 2023): Trộn dữ liệu theo tỷ lệ 85% code / 8% dữ liệu liên quan đến ngôn ngữ tự nhiên / 7% dữ liệu ngôn ngữ tự nhiên thuần túy (với phần Instruct sử dụng thêm 6% code + 2% NL replay) — đây là công trình DAP (Domain Adaptive Pretraining) hiếm hoi công bố chi tiết tỷ lệ replay.
    *   **DeepSeek-Coder V2** (Zhu et al. arXiv:2406.11931): Tiếp tục pre-train từ mô hình trung gian DeepSeek-V2 với $+6\text{T}$ tokens, hỗ trợ 338 ngôn ngữ, cửa sổ ngữ cảnh 128K, giảm LR 10% theo đồ thị cosine decay; đạt hiệu năng tương đương GPT-4-Turbo.
    *   **StarCoder2** (Lozhkov et al. 2024): Huấn luyện trên tập dữ liệu The Stack v2 đạt quy mô 3–4T tokens, tích hợp cơ chế điền vào giữa (fill-in-the-middle) và trộn dữ liệu ngôn ngữ tự nhiên.
    *   **Qwen2.5-Coder** (arXiv:2409.12186): Quy trình ba giai đoạn: huấn luyện cấp độ file trên 5.2T tokens $\rightarrow$ huấn luyện cấp độ kho mã nguồn (repo-level) trên 300B tokens $\rightarrow$ giai đoạn instruction tuning; nâng nền RoPE từ 10K lên 1M để hỗ trợ ngữ cảnh 32K.
    *   **LLaMA-Pro**: Ứng dụng kỹ thuật mở rộng khối (block expansion) giúp giữ nguyên vẹn năng lực tổng quát.

#### Tổng hợp tỷ lệ dữ liệu Replay (Replay Ratio) trong thực tế:
*   **Meditron:** 0.83%
*   **SaulLM:** ~2%
*   **Code Llama:** 7–8%
*   **DeepSeek-Coder V1:** 13%
*   **Llemma:** 5%
*   **GeoGalactica:** 20%
*   *Tỷ lệ trung vị (Median DAP):* **1–15%**.
*   *Heuristic:* Sử dụng LoRA với rank $r \in [8, 64]$ mang lại khả năng giữ tri thức tốt nhất khi lượng dữ liệu mới $< 1\text{B}$ tokens; ngược lại nên áp dụng block-expansion hoặc full-FT kèm replay khi lượng dữ liệu $\ge 10\text{B}$ tokens.

### 6.3 Forgetting trong alignment/RLHF

*   Ouyang et al. (2022) lần đầu tiên đưa ra thuật ngữ **alignment tax** và đề xuất áp dụng experience replay để khắc phục.
*   Qi et al. (2023) chứng minh độ giòn của hệ thống: chỉ cần **10 ví dụ độc hại (adversarial examples)** là đủ phá vỡ hoàn toàn hàng rào an toàn (safety alignment) của GPT-3.5.
*   Kotha, Springer & Raghunathan (ICLR 2024) trong nghiên cứu "Understanding Catastrophic Forgetting in LMs via Implicit Inference" chỉ ra hiện tượng fine-tune bị lệch do các tiền giả định ngầm của tác vụ (implicit task-prior); việc dịch prompt sang các ngôn ngữ khác tiếng Anh giúp khôi phục cả khả năng học trong ngữ cảnh (ICL) lẫn ngăn chặn việc sinh nội dung độc hại.
*   Nghiên cứu phân tích vector chức năng (function-vector analysis) tại ICLR 2025 chỉ ra: hiện tượng forgetting bắt nguồn từ việc kích hoạt các vector chức năng bị lệch (biased function vectors) trong các attention head, chứ không phải do việc ghi đè (overwrite) trực tiếp lên các hàm toán học đã học trước đó.
*   *Giải pháp tối ưu Pareto (Mitigation Pareto-optimal):* Áp dụng trung bình trọng số (Rewarded Soups/WARM/WARP/HMA) kết hợp replay dữ liệu SFT + giữ hệ số phạt KL anchor ở mức cao.

### 6.4 Các vấn đề khác

*   **Continual RLHF/DPO:** Thuật toán **CPPO** (ICLR 2024) thực hiện chưng cất để giữ lại các chính sách cũ (past policies distillation) kết hợp cơ chế memory replay. Các quy trình DPO lặp (Iterative DPO) luôn yêu cầu một neo KL (KL anchor) hướng về policy ở bước trước đó.
*   **LaLoRA** (arXiv:2512.17720): Áp dụng xấp xỉ Laplace (Laplace-approximation) để thực hiện regularization không gian trọng số riêng cho các cấu trúc LoRA.
*   **FAPM** (EMNLP 2025): Tiến hành cắt tỉa các task-vector có độ lớn nhỏ để đạt độ thưa $\ge 90\%$.

---

## 7. So sánh thực tiễn và best practices công nghiệp

### 7.1 Bảng trade-off các họ phương pháp

| Họ phương pháp | Bộ nhớ thêm | Compute | $\Delta G$ điển hình trên TRACE | Khi nào dùng |
| :--- | :--- | :--- | :--- | :--- |
| **Full FT** | 0 | Rất cao | Âm lớn ($-13$ pts @13B) | Khi lượng dữ liệu $\ge 10\text{B}$ tokens, bắt buộc phải phối hợp replay dữ liệu cũ. |
| **Full FT + replay (1–15%)** | +buffer | $+5\text{–}15\%$ | Âm nhỏ | Khi thực hiện thích ứng miền (Domain CPT) quy mô lớn (ví dụ Code Llama, Meditron, SaulLM). |
| **LoRA ($r=8\text{–}64$)** | ~0.1–1% params | 10–20% full FT | Âm nhỏ | Triển khai PEFT, khi lượng dữ liệu hạn chế hoặc trong các hệ thống đa người dùng (multi-tenant). |
| **LoRA + replay** | LoRA + buffer | ~LoRA | Gần 0 | **Khuyến nghị mặc định** cho các bài toán có lượng dữ liệu trung bình (medium data). |
| **Block expansion (LLaMA-Pro)** | $+20\%$ layers | Chỉ FT trên các block mới | Gần 0 | Khi cần mở rộng thêm domain chuyên sâu hoặc modality mới mà không muốn ảnh hưởng đến mô hình nền (base model). |
| **Model merging (TA/TIES/DARE/WARP)** | 0 tại bước suy luận | 0 (tính toán post-hoc) | Gần 0 nếu mô hình nền mạnh | Khi cần kết hợp năng lực từ $N$ chuyên gia tên miền độc lập, hoặc giảm thiểu alignment tax. |
| **Distillation từ teacher** | Cần mô hình teacher lớn | ~pretrain | Dương (thường vượt trội so với train từ đầu) | Khi huấn luyện các mô hình nhỏ, tận dụng dữ liệu tổng hợp (synthetic data) (ví dụ Gemma 2, DeepSeek V3). |
| **SSR/SEEKR** | 0 (tự sinh dữ liệu) | Tốn compute cho bước sinh | Âm nhỏ | Khi hoàn toàn không có quyền truy cập vào dữ liệu pretrain gốc. |
| **EWC/L2-SP** | +Fisher | +FI (Fisher Information) | Thường gây hại cho miền đích | **Không khuyến nghị** áp dụng cho các mô hình ngôn ngữ lớn (LLM). |
| **Prompt/prefix tuning** | $<0.01\%$ | Rất thấp | 0 (do đóng băng base model) | Căn chỉnh thích ứng tác vụ (Task adaptation), không phù hợp cho CPT. |
| **Train từ đầu (BioMedLM)** | Cập nhật toàn bộ | Toàn bộ chi phí pretrain | Không áp dụng (N/A) | Khi lượng dữ liệu $\ge 50\text{B}$ tokens và không có nhu cầu giữ năng lực tổng quát toàn miền. |

### 7.2 Thực tiễn từ các phòng lab lớn (Meta, Google, DeepSeek, Alibaba, Cohere)

*   **Meta Llama 3/3.1** (Grattafiori et al. 2024, 15.6T tokens, ngữ cảnh 128K): Tỷ lệ trộn dữ liệu cuối cùng (final data mix) gồm: **50% general / 25% math/reasoning / 17% code / 8% multilingual**. Quy trình tiền huấn luyện gồm 3 giai đoạn: Giai đoạn ban đầu $\rightarrow$ Giai đoạn mở rộng ngữ cảnh 8K $\rightarrow$ 128K qua 6 bước trên 800 tỷ token $\rightarrow$ Giai đoạn tinh luyện (annealing) cuối kỳ (sử dụng 40 triệu token cuối để giảm LR dần về 0, tăng tỷ lệ dữ liệu chất lượng cao, áp dụng Polyak averaging trên các checkpoint). Quá trình annealing giúp tăng **$+24\%$ trên GSM8K và $+6.4\%$ trên MATH** đối với phiên bản 8B, tuy nhiên hiệu quả này không thể hiện rõ ở phiên bản lớn 405B. Giai đoạn annealing cũng đóng vai trò như một phép thử (probe) đánh giá chất lượng của tập dữ liệu. Việc mở rộng từ điển lên 128K tiktoken + 28K token phi tiếng Anh giúp cải thiện hiệu suất nén từ $3.17 \rightarrow 3.94$ ký tự/token.
*   **Google Gemma 2** — Phiên bản 27B được huấn luyện từ đầu trên 13T tokens; hai phiên bản nhỏ hơn là 2B & 9B được huấn luyện bằng kỹ thuật chưng cất tri thức (knowledge distillation) từ một mô hình teacher lớn hơn thay vì dùng hàm loss next-token thông thường — giúp đạt mức tăng trưởng hiệu năng vượt trội ở cùng một lượng token tiêu thụ. Giai đoạn post-training tuân thủ quy trình: SFT $\rightarrow$ RLHF $\rightarrow$ áp dụng **WARP merging**. Một số kỹ thuật đi kèm bao gồm logit soft-capping, cơ chế attention kết hợp local-global 4K/8K, GQA, và áp dụng RMSNorm ở cả trước và sau các khối.
*   **DeepSeek V3** (14.8T tokens): Áp dụng cơ chế ngữ cảnh hai giai đoạn 4K $\rightarrow$ 32K $\rightarrow$ 128K thông qua YaRN; sử dụng kiến trúc MoE với thuật toán cân bằng tải không phụ thuộc loss phụ (auxiliary-loss-free load balancing MoE); áp dụng dự đoán đa token (multi-token prediction) và định dạng dấu phẩy động FP8. Giai đoạn post-training kết hợp SFT + RL với chưng cất tri thức trực tiếp từ các mô hình R1 CoT sang V3. Phiên bản tiếp nối **V3.2** tiếp tục huấn luyện thêm $+3.5\text{T}$ tokens trên nền V3.1 tại mức **LR cực thấp 5e-6** (~1/6 mức LR đỉnh của V3) để bảo đảm tính ổn định — đây là một ví dụ điển hình về việc áp dụng chiến lược giảm LR mạnh mẽ trong công nghiệp. Tỷ lệ trộn dữ liệu cho phiên bản DeepSeek-Coder V2 được thiết lập theo công thức 87/10/3 (tương ứng code / code-NL / NL).
*   **Alibaba Qwen 2.5:** Huấn luyện trên quy mô từ 7T $\rightarrow$ 18T tokens, kết hợp trên 1 triệu dữ liệu SFT và quy trình học tăng cường đa giai đoạn (multistage RL). Bản chuyên toán Qwen 2.5-Math sử dụng tập Qwen Math Corpus v1 (700B tokens) khởi tạo từ bản Qwen2 intermediate, phiên bản v2 mở rộng trên 1T tokens khởi tạo từ bản Qwen 2.5 base. Bản Qwen2.5-Coder được huấn luyện trên 5.5T tokens chuyên biệt về code qua ba giai đoạn.
*   **Anthropic Constitutional AI** (Bai et al. 2022): Áp dụng SFT dựa trên cơ chế tự phê bình (self-critique) tuân thủ theo một hiến pháp định sẵn (constitution), giúp giảm sự phụ thuộc vào các chiến dịch red-teaming thủ công và hạn chế tối đa hiện tượng forgetting do SFT gây ra. Các chi tiết huấn luyện sâu hơn của dòng mô hình Claude được giữ kín.
*   **Mistral/Mixtral:** Ít công bố chi tiết kỹ thuật; phần lớn các dự án học liên tục trong cộng đồng được thực hiện trên nền Mistral (ví dụ Typhoon v1, SaulLM-7B).

#### Nhịp độ cập nhật mô hình trong công nghiệp (2024–2026):
*   **Meta:** Cập nhật mỗi 6–12 tháng.
*   **DeepSeek:** Cập nhật mỗi 2–4 tháng.
*   **Google:** Cập nhật định kỳ theo quý đối với dòng Gemini/Gemma.
*   **Anthropic/OpenAI:** Phát hành các phiên bản nâng cấp nhỏ (point releases) mỗi 1–3 tháng.

### 7.3 Các bài toán chưa có lời giải (Open problems)

1.  **Thiếu benchmark chuẩn cho streaming-corpus** ở quy mô lớn $> 10\text{B}$ tokens.
2.  **Thiếu một hệ thống lý thuyết thống nhất** giải thích sự đánh đổi stability-plasticity riêng cho kiến trúc deep transformer.
3.  **Khả năng giải thích (Interpretability) của hiện tượng forgetting:** Mặc dù phương pháp HMA (Lin et al.) chứng tỏ việc gộp ở các tầng thấp giúp giữ tri thức tốt nhất, các hướng tiếp cận khác như SparcL, phân tích vector chức năng (function vectors), hay nghiên cứu không gian con (subspace-aware) vẫn đang ở dạng tiềm năng.
4.  **Sự kết hợp các phương pháp neurosymbolic** cho LLM quy mô lớn còn sơ khai; RAG hiện tại chủ yếu đóng vai trò thay thế tạm thời chứ không giải quyết được bài toán cập nhật trọng số (weight-update learning).
5.  **Nested Learning** (Google 2024): Ý tưởng lấy cảm hứng từ sinh học này hiện có rất ít thực nghiệm kiểm chứng trên LLM.
6.  **Streaming continual pretraining hoàn toàn:** Việc chuyển dịch từ DeepSeek V3 $\rightarrow$ V3.2 là tiếp cận gần nhất, nhưng học thuật vẫn thiếu các khung đánh giá chuẩn.
7.  **Lý thuyết model merging còn bỏ ngỏ:** Tại sao việc nội suy trọng số trực tiếp (weight interpolation) lại cho ra kết quả Pareto vượt trội so với các phương pháp replay vẫn là một câu hỏi chưa có lời giải đáp thỏa đáng.
8.  **Tính giòn của việc bảo tồn an toàn (Safety preservation):** Chưa có bất kỳ giao thức căn chỉnh liên tục (continual-alignment protocol) nào đạt độ tin cậy tuyệt đối để chống lại các tác nhân jailbreak hoặc trôi lệch hành vi.

---

## 8. Kết luận — chuyển dịch paradigm và hướng đi

Trường nghiên cứu về học liên tục hiện nay không còn tập trung vào các phương pháp regularization dựa trên hàm phạt quadratic cổ điển (như EWC, SI, MAS) — các phương pháp này tuy có ý nghĩa về mặt khái niệm nhưng không thể mở rộng hiệu quả lên quy mô hàng tỷ tham số và thường xung đột trực tiếp với mục tiêu tối ưu của giai đoạn fine-tune.

Thực tiễn phát triển LLM hiện đại đã hội tụ vào bốn trụ cột chính:
1.  **PEFT cấu trúc:** Đặc biệt là các biến thể LoRA trực giao hoặc chiếu null-space (O-LoRA, InfLoRA, AlphaEdit) và các cấu trúc Mixture-of-Experts LoRA (LoRAMoE, MoLA, MoLE).
2.  **Model merging dựa trên task-vector:** Các công cụ hậu kỳ như TIES, DARE, EMR-Merging, hay WARP giúp hợp nhất các chuyên gia tên miền một cách hiệu quả mà không tiêu tốn thêm tài nguyên tính toán.
3.  **Tự sinh dữ liệu diễn tập (Synthetic/self-generated rehearsal):** Các giải pháp như SSR, SEEKR, hay Magpie giúp giải quyết triệt để bài toán thiếu hụt dữ liệu pretrain gốc khi làm việc với các checkpoint công cộng.
4.  **Chuẩn hóa quy trình Continual Pre-training:** Áp dụng công thức chuẩn của Ibrahim et al. 2024 bao gồm việc thiết lập lại nhiệt độ học tập (LR re-warming), kết hợp tỷ lệ replay từ 5–25%, chạy annealing cuối kỳ và áp dụng Polyak averaging checkpoint — công thức đã được kiểm chứng thành công trên các dòng mô hình Llama 3, Gemma 2, và DeepSeek V3.

#### Sự thay đổi trong thế giới quan khoa học:
*   LoRA hoàn toàn không "miễn dịch" với hiện tượng forgetting; nghiên cứu chỉ ra sự quên lãng tuân theo quy luật lũy thừa dịch chuyển (shifted power law) trên rank và số bước cập nhật, LoRA chỉ giúp dịch chuyển điểm vận hành dọc theo đường cong tối ưu Pareto.
*   Yêu cầu trực giao (orthogonality) là chưa đủ, sự va chạm tham số mới là nguồn cơn gây suy giảm hiệu năng; N-LoRA đã chứng minh điều này.
*   Tăng quy mô mô hình (scaling) giúp giảm forgetting trong các bài toán CL cổ điển, nhưng đối với CIT, mô hình lớn hơn có thể forgetting nhiều hơn về trị tuyệt đối do mức baseline ban đầu cao hơn.
*   Chỉ cần tiêm vào **1% dữ liệu pretraining** đã đủ để duy trì phân phối tri thức cũ, thấp hơn nhiều so với các khuyến nghị 5–25% trước đây.
*   Một lượng lớn hiện tượng "forgetting" được xác định là **spurious forgetting** (do lệch căn chỉnh phong cách tác vụ) chứ không phải do mất mát tri thức thực tế trong trọng số, gợi ý rằng các công cụ đo lường hiện tại có thể đang đánh giá quá cao mức độ nghiêm trọng của Catastrophic Forgetting.

**Hướng nghiên cứu giai đoạn 2026+** sẽ tập trung vào việc xây dựng lý thuyết scaling law tích hợp, chuẩn hóa các benchmark streaming, hoàn thiện khung toán học cho model merging, thiết lập các giao thức bảo tồn an toàn khi căn chỉnh liên tục, tách biệt rõ ràng việc đánh giá spurious vs essential forgetting, và tích hợp các kỹ thuật tăng trưởng bảo toàn chức năng vào một đường ống (pipeline) continual end-to-end hoàn chỉnh. 

Câu hỏi trung tâm của kỷ nguyên mới không còn là *"Làm sao để chống forgetting?"* mà dịch chuyển thành:
> **"Làm sao để phân bổ tối ưu tài nguyên tính toán (compute) và dung lượng tham số (parameter budget) giữa việc tiếp thu tri thức mới và duy trì tri thức cũ, hướng tới một mô hình sản xuất (production LLM) được cập nhật định kỳ hàng quý?"**

---

## Danh sách liên kết trích xuất từ tài liệu (Extracted Hyperlinks)

Dưới đây là toàn bộ các liên kết được trích xuất từ các nút (badges/buttons) và văn bản trong tệp PDF gốc:

*   [LAMOL (NeurIPS 2019 / Deep Generative Replay)](http://papers.neurips.cc/paper/6892-continual-learning-with-deep-generative-replay.pdf)
*   [HAT supplementary (Serrà et al. ICML 2018)](http://proceedings.mlr.press/v80/serra18a/serra18a-supp.pdf)
*   [Orthogonal Subspace Learning (Findings of EMNLP 2023)](https://aclanthology.org/2023.findings-emnlp.633/)
*   [SAPT (ACL 2024)](https://aclanthology.org/2024.acl-long.625/)
*   [TRACE html (arXiv:2310.06762)](https://ar5iv.labs.arxiv.org/html/2310.06762v1)
*   [Elastic Weight Consolidation (arXiv:1711.09601)](https://arxiv.org/abs/1711.09601)
*   [LoRA: Low-Rank Adaptation (arXiv:2203.08512)](https://arxiv.org/abs/2203.08512)
*   [TRACE Benchmark (arXiv:2310.06762)](https://arxiv.org/abs/2310.06762)
*   [Awesome Lifelong Learning Methods (arXiv:2310.18339)](https://arxiv.org/abs/2310.18339)
*   [SAPT: Shared Attentive Learning (arXiv:2401.08295)](https://arxiv.org/abs/2401.08295)
*   [Survey Wu et al. 2024 (arXiv:2402.01364)](https://arxiv.org/abs/2402.01364)
*   [MoLA (arXiv:2404.13628)](https://arxiv.org/abs/2404.13628)
*   [Survey Shi et al. 2025 (arXiv:2404.16789)](https://arxiv.org/abs/2404.16789)
*   [PEARL (arXiv:2505.24816)](https://arxiv.org/abs/2505.24816)
*   [OPLoRA (arXiv:2510.13003)](https://arxiv.org/abs/2510.13003)
*   [LoRAHub html (arXiv:2307.13269)](https://arxiv.org/html/2307.13269v3)
*   [Catastrophic Forgetting in LLMs html (arXiv:2308.08747)](https://arxiv.org/html/2308.08747v3)
*   [SAPT html (arXiv:2401.08295)](https://arxiv.org/html/2401.08295)
*   [InfLoRA html (arXiv:2402.17263)](https://arxiv.org/html/2402.17263)
*   [InfLoRA v1 html (arXiv:2402.17263v1)](https://arxiv.org/html/2402.17263v1)
*   [I-LoRA html (arXiv:2403.08350v1)](https://arxiv.org/html/2403.08350v1)
*   [LoRA Learns Less and Forgets Less html (arXiv:2405.09673v2)](https://arxiv.org/html/2405.09673v2)
*   [WARP merging html (arXiv:2502.17510)](https://arxiv.org/html/2502.17510)
*   [LoRA-Null html (arXiv:2503.02659v1)](https://arxiv.org/html/2503.02659v1)
*   [EWC paper pdf (arXiv:1703.04200)](https://arxiv.org/pdf/1703.04200)
*   [TRACE pdf (arXiv:2310.06762)](https://arxiv.org/pdf/2310.06762)
*   [Wu et al. 2024 pdf (arXiv:2402.01364)](https://arxiv.org/pdf/2402.01364)
*   [N-LoRA pdf (arXiv:2410.10179)](https://arxiv.org/pdf/2410.10179)
*   [PEARL pdf (arXiv:2505.24816)](https://arxiv.org/pdf/2505.24816)
*   [Riemannian Walk - Researcher Life](https://discovery.researcher.life/article/riemannian-walk-for-incremental-learning-understanding-forgetting-and-intransigence/fb96ef992e373fb8b19ac84408b9db51)
*   [L2P Google Research GitHub](https://github.com/google-research/l2p)
*   [Piggyback (Springer Chapter)](https://link.springer.com/chapter/10.1007/978-3-030-01225-0_5)
*   [HAT (Springer Chapter)](https://link.springer.com/chapter/10.1007/978-3-030-58565-5_6)
*   [CODA-Prompt CVPR 2023 paper](https://openaccess.thecvf.com/content/CVPR2023/html/Smith_CODA-Prompt_COntinual_Decomposed_Attention-Based_Prompting_for_Rehearsal-Free_Continual_Learning_CVPR_2023_paper.html)
*   [CODA-Prompt CVPR 2023 pdf](https://openaccess.thecvf.com/content/CVPR2023/papers/Smith_CODA-Prompt_COntinual_Decomposed_Attention-Based_Prompting_for_Rehearsal-Free_Continual_Learning_CVPR_2023_paper.pdf)
*   [PackNet CVPR 2018 pdf](https://openaccess.thecvf.com/content_cvpr_2018/papers/Mallya_PackNet_Adding_Multiple_CVPR_2018_paper.pdf)
*   [LUCIR CVPR 2019 html](https://openaccess.thecvf.com/content_CVPR_2019/html/Hou_Learning_a_Unified_Classifier_Incrementally_via_Rebalancing_CVPR_2019_paper.html)
*   [Piggyback ECCV 2018 html](https://openaccess.thecvf.com/content_ECCV_2018/html/Arun_Mallya_Piggyback_Adapting_a_ECCV_2018_paper.html)
*   [OpenReview: TRACE Forum](https://openreview.net/forum?id=Hkf2_sC5FX)
*   [OpenReview: ConTinTin Forum](https://openreview.net/forum?id=QA1jlb1VG7)
*   [OpenReview: SAPT Forum](https://openreview.net/forum?id=uWvKBCYh4S)
*   [OpenReview: WARM Forum](https://openreview.net/forum?id=xelrLobW0n)
*   [OpenReview: Replay Forum](https://openreview.net/pdf?id=whNntrHtB8D)
*   [OpenReview: WARM pdf](https://openreview.net/pdf?id=xelrLobW0n)
*   [Experience Replay NeurIPS 2019 Abstract](https://proceedings.neurips.cc/paper/2019/hash/15825aee15eb335cc13f9b559f166ee8-Abstract.html)
*   [MIR NeurIPS 2019 Abstract](https://proceedings.neurips.cc/paper/2019/hash/e562cd9c0768d5464b64cf61da7fc6bb-Abstract.html)
*   [Learning to Prompt (L2P) Blog Post](https://research.google/blog/learning-to-prompt-for-continual-learning/)
*   [iCaRL - ISTA Research Explorer](https://research-explorer.ista.ac.at/record/998)
*   [SciSpace: Catastrophic Forgetting Study](https://scispace.com/papers/an-empirical-study-of-catastrophic-forgetting-in-large-3vza0erhh8)
*   [SciSpace: DEN Study pdf](https://scispace.com/pdf/lifelong-learning-with-dynamically-expandable-networks-4pw1n4z28f.pdf)
*   [Databricks: LLM Fine-Tuning Blog](https://www.databricks.com/blog/llm-fine-tuning)
*   [Emergent Mind: Progressive Neural Networks](https://www.emergentmind.com/topics/progressive-neural-networks)
*   [Emergent Mind: TRACE Benchmark](https://www.emergentmind.com/topics/trace-benchmark)
*   [Emergent Mind: TRACE Benchmark Datasets](https://www.emergentmind.com/topics/trace-benchmark-datasets)
*   [ResearchGate: GEM Publication](https://www.researchgate.net/publication/317955052_Gradient_Episodic_Memory_for_Continuum_Learning)
*   [ResearchGate: iCaRL Publication](https://www.researchgate.net/publication/320971202_iCaRL_Incremental_Classifier_and_Representation_Learning)
*   [ResearchGate: HAT Library Publication](https://www.researchgate.net/publication/372468613_HAT-CL_A_Hard-Attention-to-the-Task_PyTorch_Library_for_Continual_Learning)
*   [Semantic Scholar: Survey Wu et al.](https://www.semanticscholar.org/paper/Continual-Learning-for-Large-Language-Models:-A-Wu-Luo/bd0cd89337cc40d39d3a4cbe9c8709e06e877f3e)
*   [Semantic Scholar: Learning without Forgetting](https://www.semanticscholar.org/paper/Learning-without-Forgetting-Li-Hoiem/8f3b80ddc0dd62e6c3369fabb1715990c29e9b9a)
*   [Studocu: iCaRL Document](https://www.studocu.com/en-ca/document/york-university/grade-12-english/rebuffi-i-ca-rl-incremental-classifier-cvpr-2017-paper/44783038)

