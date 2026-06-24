# 1. Định vị Novelty & Research Gap

> Mục đích: chỉ chính xác **ai đã làm gì** trong 36 paper, **chỗ trống** đề tài lấp, và phân tách **đóng góp khoa học vs kỹ thuật**. Đây là "bộ giáp" cho câu hỏi *"đề tài có gì mới?"*.

---

## 1.1 Bản đồ lĩnh vực (rút từ global synthesis)

Cả lĩnh vực CL-for-LLM chia theo **4 paradigm chống quên** (Group 1): rehearsal/replay · regularization · architecture/PEFT · **external memory (RAG)**. Đề tài nằm hẳn ở nhánh **external memory** — nhánh ít bị "stability–plasticity dilemma" nhất (xem `2_Theoretical_Foundation.md`).

Bốn dòng nghiên cứu liên quan trực tiếp:
- **Parametric CL** (Group 2,3,4): CPT/CFT, scaling laws, PEFT/LoRA, O-LoRA → mạnh về tích hợp tri thức nhưng **quên + weight drift + khó unlearn**.
- **Semi-parametric tạm thời** (Group 5): ReGrad (`Su2026_ReGrad`) — gradient bank, tránh drift nhưng **độ trễ inference cao**.
- **Non-parametric / RAG memory** (Group 5): LUFY (`Sumida2025_LUFY`) — selective forgetting **cho hội thoại**; RAG-Unlearning (`Wang2026_RAGUnlearning`) — behavioral unlearning **generic**.
- **VN legal NLP** (Group 5): VLQA, ViLegalNLI, LegalSLM — **dataset/benchmark**, chưa có *framework* CL/memory/unlearning xây trên đó.

## 1.2 Ma trận định vị (cái này nên vẽ vào slide bảo vệ)

Ký hiệu: ✓ có · ◐ một phần · ✗ không.

| Năng lực | LUFY `Sumida2025` | RAG-Unlearn `Wang2026` | ReGrad `Su2026` | CPT/D-CPT `Que2024` | VN datasets `Nguyen/Duong/Le` | **Đề tài** |
|----------|:---:|:---:|:---:|:---:|:---:|:---:|
| Base LLM đóng băng (không weight update) | ✓ | ✓ | ✗ (gradient tạm) | ✗ | – | **✓** |
| Cập nhật liên tục không quên năng lực chung | ✓ | ✓ | ◐ | ✗ | – | **✓** |
| Selective memory theo importance | ✓ (hội thoại) | ✗ | ✗ | ✗ | ✗ | **✓ (pháp lý)** |
| Importance **đặc thù luật** (citation+hiệu lực+rủi ro) | ✗ | ✗ | ✗ | ✗ | ✗ | **✓** |
| **Compliance hard-gate** (giữ luật còn hiệu lực) | ✗ | ✗ | ✗ | ✗ | ✗ | **✓** |
| Unlearning có kiểm soát (quên chủ đích) | ✗ | ✓ (generic) | ✗ | ✗ | ✗ | **✓ (legal)** |
| **Regime theo thời gian** (validity interval) | ✗ | ✗ | ✗ | ✗ | ✗ | **✓** |
| Miền **luật tiếng Việt** (low-resource) | ✗ | ✗ | ✗ | ◐ (law CPT EN/ZH) | ✓ (eval) | **✓** |

→ **Hàng đầy đủ duy nhất là cột "Đề tài".** Gap = **giao điểm** chưa ai phủ: *selective memory đặc thù luật + unlearning có kiểm soát + regime thời gian, trên base đóng băng, cho luật tiếng Việt.*

## 1.3 Phát biểu research gap (1 câu chuẩn để đọc khi bị hỏi)

> "Selective/cognitive forgetting (LUFY) mới làm cho **hội thoại** với importance generic; RAG-unlearning (Wang2026) mới làm **behavioral unlearning generic**; chưa có công trình nào **hợp nhất** importance-memory *đặc thù pháp lý* (citation graph + hiệu lực + rủi ro, kèm **compliance hard-gate**) với **controllable unlearning** và **xử lý regime theo thời gian**, trên **base đóng băng** cho **luật tiếng Việt low-resource**."

## 1.4 Đóng góp KHOA HỌC vs KỸ THUẬT (phân tách rõ để không bị quy "chỉ là kỹ thuật")

**Đóng góp khoa học (cái hội đồng quan tâm):**
- (KH1) **Tái khung** bài toán "duy trì tri thức pháp lý đang biến động" thành bài toán continual learning, và **lập luận chọn external-memory paradigm** thay vì parametric, dựa trên stability–plasticity + inverse linear law (`Kalajdzievski2024_ScalingForgetting`).
- (KH2) **Định nghĩa hai construct mới làm first-class**: *legal importance* (đo bằng citation/hiệu lực/rủi ro) và *legal temporal regime* (validity interval) → biến "quên" từ lỗi (catastrophic) thành **tính năng có kiểm soát**.
- (KH3) **Bằng chứng thực nghiệm** cho 3 câu hỏi mở chưa ai trả lời trong miền luật VN: selective RAG có giảm nhiễu mà giữ compliance? frozen-RAG có quên ít hơn CPT/CFT? retrieval-level unlearning có đạt tiêu chí compliance?

**Đóng góp kỹ thuật (phương tiện, không phải mục tiêu khoa học):**
- (KT1) Pipeline RAG bán-tham-số + 2 module (Selective Memory, Unlearning).
- (KT2) Bộ metric + benchmark nhỏ "legal-CL tiếng Việt".

> **Câu chốt:** "Đề tài KHÔNG claim thuật toán RAG mới; novelty là **synthesis + domain reframing + evidence** — đúng vai trò của nghiên cứu định hướng."

## 1.5 Phản bác trước các đòn "đây là incremental"
- *"Chỉ ghép LUFY + Wang2026?"* → Không phải ghép cơ học: importance pháp lý + compliance hard-gate + temporal regime là **construct mới**, đòi hỏi mô hình hoá khác (validity là ràng buộc cứng, không phải feature mềm).
- *"Datasets đã có rồi?"* → Đúng, nhưng chúng là **eval**; đề tài là **framework + protocol đánh giá CL/unlearning** chưa tồn tại trên chúng.
- *"D-CPT đã làm law domain?"* → Đó là **parametric CPT** (quên + drift); đề tài đối lập về paradigm và mục tiêu (unlearn được).
