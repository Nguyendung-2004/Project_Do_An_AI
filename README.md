# Hệ Thống Nhận Diện Và Phân Loại Rác Thải Nhựa Bằng AI

Đây là đồ án xây dựng ứng dụng web dùng trí tuệ nhân tạo để nhận diện và phân loại rác thải nhựa, đặc biệt là chai nhựa, thành hai nhóm chính: có thể tái chế và không thể tái chế. Hệ thống sử dụng mô hình YOLO để phát hiện đối tượng trong ảnh hoặc camera realtime, sau đó hiển thị kết quả phân loại, độ tin cậy và lưu lại lịch sử thao tác của từng người dùng.

## Mục Tiêu Đề Tài

- Ứng dụng AI vào bài toán phân loại rác thải nhựa.
- Hỗ trợ người dùng nhận biết rác có thể tái chế hoặc không thể tái chế.
- Xây dựng giao diện web dễ sử dụng cho upload ảnh và nhận diện qua camera.
- Lưu lịch sử phân loại để người dùng xem lại kết quả đã thực hiện.
- Góp phần nâng cao ý thức phân loại rác và bảo vệ môi trường.

## Chức Năng Chính

- Đăng ký và đăng nhập tài khoản người dùng.
- Upload ảnh rác thải/chai nhựa để AI phân loại.
- Hiển thị ảnh kết quả đã được vẽ bounding box.
- Hiển thị nhãn phân loại, độ tin cậy và thời gian xử lý.
- Nhận diện realtime qua webcam.
- Lưu và xem lại lịch sử phân loại theo từng tài khoản.
- Thống kê số lần phân loại, số rác có thể tái chế và không thể tái chế.

## Công Nghệ Sử Dụng

- Python
- Flask
- Ultralytics YOLO
- OpenCV
- PyTorch
- HTML, CSS, JavaScript
- JSON để lưu tài khoản và lịch sử
- PyInstaller để đóng gói ứng dụng thành file `.exe`

## Cấu Trúc Dự Án

```text
Do_An_AI/
├── app.py                         # Backend Flask chính
├── requirements.txt               # Danh sách thư viện Python
├── data.yaml                      # Cấu hình dataset YOLO
├── users.json                     # Dữ liệu tài khoản người dùng
├── classification_history.json    # Dữ liệu lịch sử phân loại
├── app.log                        # Log hoạt động của hệ thống
├── templates/                     # Giao diện HTML
│   ├── login.html
│   ├── register.html
│   ├── index.html
│   └── history.html
├── static/
│   └── uploads/                   # Ảnh kết quả sau khi phân loại
├── datasets_all/                  # Dataset train/test YOLO
├── runs/detect/train/weights/
│   └── best.pt                    # Model YOLO đang được sử dụng
├── app.spec                       # Cấu hình build PyInstaller
└── NhanDienChaiNhua.spec          # Cấu hình build file exe
```

## Backend

File backend chính là `app.py`. File này đảm nhiệm:

- Khởi tạo Flask app.
- Load model YOLO từ `runs/detect/train/weights/best.pt`.
- Xử lý đăng nhập, đăng ký và đăng xuất.
- Nhận ảnh upload từ người dùng.
- Gọi model YOLO để dự đoán ảnh.
- Vẽ bounding box lên ảnh bằng OpenCV.
- Trả kết quả phân loại về giao diện.
- Lưu lịch sử phân loại vào file JSON.
- Stream webcam realtime qua route `/video_feed`.

Các route chính:

| Route | Chức năng |
| --- | --- |
| `/` | Điều hướng về đăng nhập hoặc dashboard |
| `/login` | Đăng nhập |
| `/register` | Đăng ký |
| `/logout` | Đăng xuất |
| `/dashboard` | Trang phân loại ảnh và camera |
| `/classify` | API phân loại ảnh upload |
| `/history` | Trang lịch sử phân loại |
| `/video_feed` | Stream camera realtime |

## Nơi Lưu Dữ Liệu

| Dữ liệu | Vị trí lưu |
| --- | --- |
| Tài khoản người dùng | `users.json` |
| Lịch sử phân loại | `classification_history.json` |
| Ảnh kết quả sau phân loại | `static/uploads/` |
| Model YOLO đã train | `runs/detect/train/weights/best.pt` |
| Dataset train/test | `datasets_all/train`, `datasets_all/test` |
| Log ứng dụng | `app.log` |

Lưu ý: ảnh gốc người dùng upload sẽ được xử lý xong rồi xóa, hệ thống chỉ giữ lại ảnh kết quả đã được vẽ bounding box.

## Dataset Và Model

Dataset được cấu hình trong file `data.yaml` với 2 lớp:

```yaml
names: ["rac_khong_tai_che_duoc", "rac_tai_che_duoc"]
```

Ý nghĩa:

- `rac_tai_che_duoc`: chai nhựa có thể tái chế.
- `rac_khong_tai_che_duoc`: chai nhựa không thể tái chế.

Model chính đang chạy là:

```text
runs/detect/train/weights/best.pt
```

Model được huấn luyện bằng YOLOv8 với kết quả train được lưu trong thư mục `runs/detect/train/`.

## Cài Đặt Và Chạy Dự Án

1. Tạo môi trường ảo:

```bash
python -m venv venv
```

2. Kích hoạt môi trường ảo trên Windows:

```bash
venv\Scripts\activate
```

3. Cài đặt thư viện:

```bash
pip install -r requirements.txt
```

4. Chạy ứng dụng:

```bash
python app.py
```

5. Mở trình duyệt tại:

```text
http://127.0.0.1:5000
```

## Cách Sử Dụng

1. Đăng ký tài khoản mới hoặc đăng nhập tài khoản đã có.
2. Vào trang phân loại.
3. Chọn ảnh rác thải/chai nhựa từ máy tính.
4. Nhấn nút "Phân loại".
5. Xem kết quả gồm ảnh đã nhận diện, nhãn phân loại và độ tin cậy.
6. Có thể bật camera để nhận diện realtime.
7. Vào trang "Lịch sử" để xem lại các lần phân loại trước đó.


