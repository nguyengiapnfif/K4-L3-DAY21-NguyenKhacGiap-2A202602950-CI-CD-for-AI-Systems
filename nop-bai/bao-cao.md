# Báo Cáo Lab Day 21 - CI/CD cho AI Systems

| | |
|---|---|
| Họ và tên | Nguyễn Khắc Giáp |
| MSSV | 2A202602950 |
| Lớp / Khóa | K4 |
| Repo GitHub | https://github.com/nguyengiapnfif/K4-L3-DAY21-NguyenKhacGiap-2A202602950-CI-CD-for-AI-Systems |
| Ngày nộp | 07/10/2026 |

---

## 1. Bộ Siêu Tham Số Đã Chọn và Lý Do

| Lần chạy | n_estimators | learning_rate | max_depth | f1_score | accuracy |
|---|---|---|---|---|---|
| 1 | 100 | 0.1 | 3 | 0.7109 | 0.8780 |
| 2 | 50 | 0.05 | 2 | 0.6051 | 0.8460 |
| 3 | 200 | 0.1 | 5 | 0.7149 | 0.8740 |

**Bộ siêu tham số đã chọn:** `n_estimators=200`, `learning_rate=0.1`, `max_depth=5`.

**Lý do:** Lần 3 có F1 cao nhất (0.7149), vượt ngưỡng 0.65. Lần có accuracy cao nhất là lần 1 (0.8780), không trùng lần có F1 cao nhất: accuracy chỉ dao động 0.846 - 0.878, còn F1 chênh gần 0.11. Lần 2 dùng learning_rate nhỏ nhưng chỉ 50 cây nông nên chưa học đủ (F1 0.6051); learning_rate nhỏ phải đi kèm n_estimators lớn hơn.

---

## 2. Vì Sao Ngưỡng Chất Lượng Đặt Trên F1 Chứ Không Phải Accuracy

Chỉ 24,8% mẫu thuộc lớp thu nhập > 50K. Mô hình luôn trả lời "thu nhập thấp" vẫn đạt accuracy 0,752, chỉ kém các mô hình thật (0,846 - 0,878) khoảng 0,1 dù không học được gì, nên accuracy che giấu việc mô hình có nhận ra lớp thu nhập cao hay không. F1 của lớp dương là trung bình điều hòa của precision và recall trên đúng lớp này; mô hình luôn đoán lớp 0 có F1 bằng 0. Không dùng `average="macro"` hay `"weighted"` vì trung bình sẽ bị lớp đa số kéo điểm lên.

---

## 3. Khó Khăn Gặp Phải và Cách Giải Quyết

| Khó khăn | Nguyên nhân | Cách giải quyết |
|---|---|---|
| MLflow 2.13 lỗi `pkg_resources` và SQLAlchemy | Môi trường mới thiếu setuptools, SQLAlchemy 2.1 không tương thích | Ghim `setuptools<81`, `sqlalchemy<2.1` |
| Không tạo được bucket S3 (AccessDenied) | Nhóm IAM chưa có quyền S3 | Gắn `AmazonS3FullAccess`; VM dùng IAM role chỉ đọc, CI dùng IAM user giới hạn một bucket |
| Push lên `main` không kích hoạt workflow | Repo là bản fork; đã thử đổi `paths` và bật lại workflow nhưng vẫn không có run | Chạy `workflow_dispatch` sau commit dữ liệu (xem mục 4) |

---

## 4. So Sánh Bước 2 và Bước 3

| | f1_score | accuracy |
|---|---|---|
| Bước 2 (chỉ `train_batch1`) | 0.7149 | 0.8740 |
| Bước 3 (thêm `train_batch2`) | 0.7354 | 0.8820 |

**Nhận xét:** F1 tăng khoảng 0,02 khi dữ liệu gấp đôi, nhưng holdout chỉ 500 mẫu nên mức chênh nằm trong dao động ngẫu nhiên. Lưu ý: commit dữ liệu mới không tự kích hoạt được workflow trên repo fork này, nên lần chạy Bước 3 được kích hoạt thủ công bằng `workflow_dispatch`; cả 4 job vẫn xanh và mô hình mới đã lên VM.
