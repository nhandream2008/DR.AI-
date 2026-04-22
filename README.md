# DR.AI+ — Hệ Thống Hỗ Trợ Chẩn Đoán Ung Thư Bằng AI

Ứng dụng desktop hỗ trợ bác sĩ chẩn đoán hình ảnh y tế, sử dụng mô hình YOLOv8 để phát hiện khối u vú, phổi và não từ ảnh X-quang / MRI / CT.

---

## Tính Năng Chính

- **Chẩn đoán AI**: Phân tích ảnh y tế bằng 3 mô hình YOLO chuyên biệt (u vú, u phổi, u não)
- **Hỗ trợ GPU/CPU**: Tự động phát hiện và sử dụng GPU nếu có, fallback về CPU
- **Đa nguồn đầu vào**: Tải ảnh từ file (JPG/PNG/BMP) hoặc chụp trực tiếp từ webcam
- **Xử lý hàng loạt**: Phân tích nhiều ảnh cùng lúc, duyệt kết quả qua lại từng ảnh
- **Tiền xử lý CLAHE**: Tăng cường độ tương phản ảnh y tế trước khi đưa vào AI
- **Xuất PDF hồ sơ**: Tạo phiếu kết quả bệnh nhân có tên bác sĩ, kết quả AI, lời khuyên
- **Đọc kết quả TTS**: Thông báo kết quả bằng giọng nói tiếng Việt (gTTS + VLC)
- **Giao diện sáng/tối**: Hỗ trợ Dark Mode, zoom & kéo ảnh trực tiếp trên màn hình

---

## Yêu Cầu Hệ Thống

- Python **3.9+**
- Windows (để xuất PDF với font Arial; Linux/macOS dùng font Helvetica tự động)
- GPU NVIDIA (khuyến nghị, không bắt buộc)

---

## Cài Đặt

```bash
pip install customtkinter pillow opencv-python torch torchvision
pip install ultralytics gtts python-vlc reportlab
```

Nếu dùng GPU:
```bash
pip install torch torchvision --index-url https://download.pytorch.org/whl/cu118
```

---

## Cấu Trúc Thư Mục

```
project/
├── main.py            # Phiên bản gốc
├── ai.py              # Phiên bản mở rộng (hỗ trợ ảnh kiểm chứng)
├── taikhoang.txt      # File tài khoản bác sĩ (username:password)
├── UT_VU.pt           # Model YOLO — Ung thư vú
├── UT_PHOI.pt         # Model YOLO — Ung thư phổi
└── NAO.pt             # Model YOLO — Khối u não
```

---

## File Tài Khoản (`taikhoang.txt`)

Tạo file `taikhoang.txt` trong cùng thư mục, mỗi dòng là một tài khoản theo định dạng:

```
bacsi1:matkhau1
bacsi2:matkhau2
```

---

## Hướng Dẫn Sử Dụng

1. Chạy ứng dụng: `python main.py` hoặc `python ai.py`
2. Đăng nhập bằng tài khoản bác sĩ
3. Nhập tên/mã bệnh nhân ở thanh bên trái
4. Chọn mô hình chẩn đoán: **U vú**, **U phổi** hoặc **U não**
5. Tải ảnh từ máy tính hoặc bật webcam để chụp
6. Nhấn **"CHẨN ĐOÁN TẤT CẢ ẢNH"** để chạy AI
7. Duyệt kết quả từng ảnh bằng nút điều hướng
8. Nhấn **"LẬP HỒ SƠ BỆNH ÁN"** để xuất PDF

---

## Mô Hình AI

| Model | File | Loại bệnh |
|-------|------|-----------|
| Model2 | `UT_VU.pt` | Khối u vú (cancer / normal) |
| Model3 | `UT_PHOI.pt` | Khối u phổi |
| Model4 | `NAO.pt` | Khối u não (tumor / normal) |

> Các file `.pt` là trọng số tự train trên kiến trúc **YOLOv26 Medium**.

Ngưỡng tin cậy mặc định: **conf = 0.45**, **IoU = 0.45**

Kết quả phân loại:
- ≥ 50% → **PHÁT HIỆN** (cảnh báo đỏ)
- 20–49% → **CÓ DẤU HIỆU** (cảnh báo vàng)
- < 20% → **KHÔNG PHÁT HIỆN BẤT THƯỜNG** (xanh lá)

---

## Sự Khác Biệt Giữa `main.py` và `ai.py`

`ai.py` là phiên bản mở rộng của `main.py`, bổ sung logic xử lý **ảnh kiểm chứng đặc biệt**: với một file ảnh được chỉ định (`anhkiemchungduocphepsudung1.jpg`), hệ thống bỏ qua AI và tự vẽ bounding box thủ công tại tọa độ định sẵn, dùng cho mục đích demo/kiểm thử.

---

## Lưu Ý

> ⚠️ Kết quả từ AI **chỉ mang tính tham khảo hỗ trợ**. Không thay thế chẩn đoán của bác sĩ chuyên khoa.

---

## Công Nghệ Sử Dụng

- [CustomTkinter](https://github.com/TomSchimansky/CustomTkinter) — Giao diện đồ họa
- [YOLOv26 Medium](https://github.com/ultralytics/ultralytics) — Mô hình phát hiện đối tượng
- [OpenCV](https://opencv.org/) — Xử lý ảnh
- [gTTS](https://pypi.org/project/gTTS/) + [python-vlc](https://pypi.org/project/python-vlc/) — Đọc kết quả bằng giọng nói
- [ReportLab](https://www.reportlab.com/) — Xuất PDF
- [PyTorch](https://pytorch.org/) — Backend deep learning
