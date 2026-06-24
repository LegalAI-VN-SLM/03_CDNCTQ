# 2. Nền tảng lý thuyết (Continual Learning Theory)

> Mục đích: cho đề tài một **chỗ đứng lý thuyết** trong CL, không chỉ là "một hệ thống". Trả lời được: *"Về mặt lý thuyết, vì sao cách làm này hợp lý?"*

---

## 2.1 Thế lưỡng nan Stability–Plasticity (trục lý thuyết trung tâm)

CL bị chi phối bởi **stability–plasticity dilemma**: học cái mới (plasticity) ↔ giữ cái cũ (stability). Mọi phương pháp **parametric** đều phải *đánh đổi* hai cực này trên cùng tập trọng số (Group 1: `Wu2024_CL_Survey`, `Shi2024_CL_Survey`).

**Lập luận lõi của đề tài:** thay vì *cân bằng* đánh đổi trên trọng số, ta **tách rời lưu trữ** — externalize tri thức pháp lý ra **bộ nhớ phi tham số (KB)**:
- *Stability* được **bảo đảm tuyệt đối** vì base LLM đóng băng (không có cập nhật phá tri thức cũ).
- *Plasticity* = **sửa KB** (thêm/xoá/re-rank entry), không tốn gradient.

→ Đề tài **không giải** dilemma theo cách cũ; nó **né** dilemma bằng cách đổi nơi chứa tri thức. Đây là luận điểm lý thuyết bán-tham-số.

## 2.2 Đòn bẩy: Inverse Linear Law của sự quên

`Kalajdzievski2024_ScalingForgetting`: mức quên L_f tỉ lệ tuyến tính với fine-tuning loss L_ft — **học mới càng sâu, quên cũ càng nhiều**; giảm rank LoRA hay dừng sớm không cứu được (chỉ làm học kém đi). `Biderman2024_LoRALearnsLess` củng cố: "learns less ↔ forgets less".

**Hệ quả lý thuyết cho đề tài:** mọi tích hợp tri thức **vào trọng số** đều kéo theo quên *cân xứng*. Đóng băng base ⇒ L_ft = 0 trên base ⇒ **không quên parametric**. Cái giá phải trả (và phải thừa nhận): tri thức mới **không** được "nội tại hoá" vào năng lực suy luận của model — nó sống ngoài, ở context. Đây chính là đánh đổi "learns less" mà đề tài *chấp nhận có chủ đích* (xem giới hạn reasoning ở 3.1).

## 2.3 Lý thuyết bộ nhớ bán-tham-số

Theo taxonomy agent lifelong (`Zheng2026_AgentLifelongSurvey`): Working / Episodic / **Semantic** / Parametric memory.
- Tri thức trong trọng số = **parametric memory** (ngầm, khó sửa/quên).
- KB pháp lý của đề tài = **semantic memory** (tường minh, truy xuất được, **sửa/quên dễ**).

→ Việc chọn semantic memory làm nơi chứa tri thức biến-động chính là lý do **unlearning trở thành thao tác bậc nhất** (gỡ entry), thay vì bài toán tối ưu ngược đắt đỏ trên parametric memory.

## 2.4 Lý thuyết về "quên": từ BUG → FEATURE

Phân biệt hai loại quên (đóng góp khái niệm KH2):
| | Catastrophic forgetting | **Selective / intentional forgetting** |
|---|---|---|
| Bản chất | ngoài ý muốn, do gradient | **có chủ đích, có kiểm soát** |
| Cơ chế | weight drift | decay importance (3.2) + unlearning P+Q (3.3) |
| Mong muốn | tránh | **tận dụng** |
| Cảm hứng | – | đường cong quên nhận thức (`Sumida2025_LUFY`, Ebbinghaus) |

**Luận điểm:** đề tài lật ngược vai trò của "quên": catastrophic forgetting là kẻ thù (né bằng frozen base), còn *selective forgetting* là **công cụ thiết kế** (giảm nhiễu memory, tuân thủ pháp lý). Đây là khung lý thuyết gắn kết 3.2 và 3.3 dưới một mái.

## 2.5 Tri thức pháp lý là NON-STATIONARY có cấu trúc thời gian

Khác dữ liệu thường, tri thức luật có **validity interval [t_start, t_end]** tường minh và **quan hệ thay thế** (văn bản mới bãi bỏ cũ). Liên hệ ý tưởng time-continual (`Li2025_TiCLM`).

**Hệ quả thiết kế-lý thuyết:**
- Truy vấn pháp lý ngầm gắn **mốc thời gian** t_q ("hiện hành" hoặc "tại 20XX").
- RAG có metadata thời gian cho phép **time-travel query** (trả lời theo regime) — điều parametric model gần như không làm được vì tri thức bị "nén" mất chiều thời gian.

## 2.6 Compliance như một RÀNG BUỘC CỨNG (khác RAG generic)

RAG thông thường tối ưu **relevance** (sim). Nhưng QA pháp lý có **ràng buộc đúng-sai cứng**: phải dùng luật *đang hiệu lực*, không viện dẫn luật đã bãi bỏ.

→ Lý thuyết hoá: bài toán không phải "xếp hạng theo độ liên quan" mà là **xếp hạng có ràng buộc** (constrained ranking) — relevance tối ưu *trong miền hợp lệ về hiệu lực*. Đây là cơ sở lý thuyết cho **compliance hard-gate** (3.2.5), phân biệt đề tài với selective RAG generic.

---

## Tóm tắt: 5 trụ lý thuyết để đọc khi bị hỏi
1. Né stability–plasticity bằng externalize (2.1).
2. Frozen base ⇒ không quên parametric (inverse linear law, 2.2) — chấp nhận đánh đổi "learns less".
3. Semantic memory ⇒ unlearning là thao tác bậc nhất (2.3).
4. Quên = feature có kiểm soát, không phải bug (2.4).
5. Tri thức luật non-stationary + ràng buộc compliance ⇒ constrained, time-aware retrieval (2.5–2.6).
