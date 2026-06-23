# VN-Legal-AI — Continual Pre-Training & Fine-Tuning Analysis

Dự án này tập hợp các nghiên cứu, khảo sát và phân tích về **Học liên tục (Continual Learning)** và **Quên thảm khốc (Catastrophic Forgetting)** cho các Mô hình Ngôn ngữ Lớn (LLMs), làm nền tảng kỹ thuật phục vụ cho việc phát triển mô hình chuyên ngành Pháp luật Việt Nam (`VN-Legal-AI` / `CDNCTQ`).

---

## 📂 Cấu Trúc Thư Mục Tài Liệu

Toàn bộ các bài báo được phân chia theo 4 nhóm chính và lưu trữ tại đường dẫn `4_docs/02_reading_paper/`:

*   **Group 1: Surveys & Overviews (Khảo sát tổng quan)**
    *   Thư mục: [`Suvery/`](file:///e:/DoCode/1%20VN-Legal-AI/03_CDNCTQ/4_docs/02_reading_paper/Suvery/)
    *   Nội dung: Phân tích 4 bài survey cốt lõi (CPT, CIT, CA, Generative CL) và tóm tắt `hightlight.md`.
*   **Group 2: Catastrophic Forgetting & Benchmarks**
    *   Thư mục: [`Gemni_notebooklm/`](file:///e:/DoCode/1%20VN-Legal-AI/03_CDNCTQ/4_docs/02_reading_paper/Gemni_notebooklm/)
    *   Nội dung: Phân tích 6 bài báo về hiện tượng quên và các benchmark (TRACE, ConTinTin) và tóm tắt `hightlight.md`.
*   **Group 3: Continual Pre-Training & Scaling Laws**
    *   Thư mục: [`Group3/`](file:///e:/DoCode/1%20VN-Legal-AI/03_CDNCTQ/4_docs/02_reading_paper/Gemni_notebooklm/Group3/)
    *   Nội dung: Phân tích 10 bài báo về CPT, re-warming, stability gap và scaling laws và tóm tắt `hightlight.md`.
*   **Group 4: PEFT / LoRA & Prompt-Based Continual Learning**
    *   Thư mục: [`Group4/`](file:///e:/DoCode/1%20VN-Legal-AI/03_CDNCTQ/4_docs/02_reading_paper/Gemni_notebooklm/Group4/)
    *   Nội dung: Phân tích 8 bài báo về giải pháp PEFT động (O-LoRA, SLIM, MoLA, N-LoRA) và tóm tắt `hightlight.md`.

---

## ⚙️ Cài Đặt Môi Trường & Thư Viện (Installation & Setup)

Bạn có thể lựa chọn cài đặt thư viện phụ thuộc bằng **`uv`** (Khuyên dùng) hoặc **`pip`** truyền thống thông qua hai file cấu hình `pyproject.toml` và `requirements.txt`:

### Cách 1: Sử dụng `uv` (Cực nhanh và tự động)
Nếu bạn chưa cài `uv`, hãy cài đặt trước (ví dụ trên Windows):
```powershell
powershell -c "irm https://astral.sh/uv/install.ps1 | iex"
```
Sau đó, bạn có hai lựa chọn:
1. **Chạy trực tiếp mà không cần cài đặt trước (Khuyên dùng):**
   Lệnh `uv run` sẽ tự động tạo môi trường ảo tạm thời, cài đặt `pymupdf` theo metadata đặc tả trong file code và thực thi script:
   ```bash
   uv run src/capture_pdf/extract_figures.py
   ```
2. **Khởi tạo và đồng bộ môi trường ảo cục bộ (.venv):**
   ```bash
   # Tạo môi trường ảo .venv cục bộ
   uv venv
   
   # Cài đặt dependencies từ requirements.txt
   uv pip install -r requirements.txt
   
   # Hoặc cài đặt trực tiếp dự án dưới dạng editable từ pyproject.toml
   uv pip install -e .
   ```

### Cách 2: Sử dụng `pip` truyền thống
Nếu bạn muốn cấu hình môi trường Python thông thường:
```bash
# Tạo môi trường ảo
python -m venv .venv

# Kích hoạt môi trường ảo (Windows)
.venv\Scripts\activate

# Cài đặt các thư viện cần thiết từ requirements.txt
pip install -r requirements.txt

# Chạy script bằng python
python src/capture_pdf/extract_figures.py
```

---

## 🛠️ Công Cụ Trích Xuất Sơ Đồ & Bảng Biểu Tự Động (`extract_figures.py`)

Để tự động hóa việc lấy các sơ đồ (Figures) và bảng biểu (Tables) từ các file PDF gốc và nhúng trực tiếp vào các file tổng hợp `hightlight.md`, chúng tôi cung cấp một script Python tự động hỗ trợ **chế độ kép (Dual-Mode)** tại `src/capture_pdf/extract_figures.py`:

1. **AI-Powered Layout Detection (Khuyên dùng - Độ chính xác tối đa)**: Tự động chạy và tích hợp công cụ AI **`marker-pdf`** nếu có sẵn trên máy. Marker sử dụng deep learning để phân tích layout, trích xuất chính xác ảnh Figure chất lượng cao và chuyển đổi Table thành văn bản. Kết quả sẽ được cache lại để tăng tốc cho các lần chạy sau.
2. **Layout-Aware Vector & Text union Cropping (Tự động fallback)**: Nếu không cài đặt Marker hoặc Marker bị lỗi, script sẽ tự động chuyển sang giải pháp PyMuPDF thuần túy. Thuật toán đã được cải tiến để tự động xác định hướng hình/bảng (nằm trên hay dưới caption), định vị ranh giới văn bản xung quanh (body paragraphs) để tránh cắt xén lấn chữ, và thu hẹp vùng cắt theo nội dung thực tế.

### ⚙️ Cài đặt thêm cho chế độ AI (Marker-pdf)

Để sử dụng chế độ AI chất lượng cao nhất, bạn cần cài đặt thư viện `marker-pdf`:

```bash
# Cài đặt bằng pip
pip install marker-pdf

# Hoặc cài đặt bằng uv (nếu sử dụng uv)
uv pip install marker-pdf
```

> [!NOTE]
> `marker-pdf` yêu cầu Python 3.10+ và PyTorch. Nếu bạn chạy lần đầu, Marker sẽ tải một số mô hình deep learning về máy nên có thể mất vài phút.

### Hướng Dẫn Chạy Script

Mở terminal tại thư mục gốc của dự án (`E:\DoCode\1 VN-Legal-AI\03_CDNCTQ`) và chạy các lệnh tương ứng:

#### 1. Chạy trên Group 2 (Mặc định)
```bash
python src/capture_pdf/extract_figures.py
# hoặc sử dụng uv
uv run src/capture_pdf/extract_figures.py
```

#### 2. Chạy trên Group 3
```bash
python src/capture_pdf/extract_figures.py --markdown "4_docs/02_reading_paper/Gemni_notebooklm/Group3/hightlight.md"
```

#### 3. Chạy trên Group 4
```bash
python src/capture_pdf/extract_figures.py --markdown "4_docs/02_reading_paper/Gemni_notebooklm/Group4/hightlight.md"
```

#### 4. Chạy trên Group 1 (Surveys)
```bash
python src/capture_pdf/extract_figures.py --markdown "4_docs/02_reading_paper/Suvery/hightlight.md"
```

### Các Tham Số Tùy Chọn
*   `--markdown`: Đường dẫn tới file `hightlight.md` đích cần cập nhật.
*   `--pdf_dir`: Đường dẫn tới thư mục chứa các file PDF gốc (Mặc định: `4_docs/pdf/02_pdf_reading`).
*   `--output_dir_name`: Tên thư mục lưu ảnh trích xuất (Mặc định: `images` nằm cùng cấp với file markdown).

