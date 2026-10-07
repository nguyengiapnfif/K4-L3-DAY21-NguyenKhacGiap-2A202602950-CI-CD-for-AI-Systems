# Báo Cáo Lab Day 21 - CI/CD cho AI Systems

<!--
HƯỚNG DẪN - đọc rồi XÓA TOÀN BỘ các khối chú thích này sau khi điền xong:

  - Giới hạn: KHÔNG QUÁ 1 TRANG A4, tương đương khoảng 450 - 550 từ nội dung.
  - Chỉ điền vào các chỗ ___ và các ô trong bảng. Không thêm mục mới.
  - Viết bằng câu hoàn chỉnh, không gạch đầu dòng cụt lủn.
  - Kiểm tra độ dài sau khi đã xóa hết chú thích:
        wc -w nop-bai/bao-cao.md
    và xem trước bản in bằng cách mở file trên GitHub rồi Ctrl+P / Cmd+P.
-->

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

**Lý do:** Lần 3 có f1_score cao nhất (0.7149) và vượt ngưỡng 0.65 của Quality Gate. Lần chạy có accuracy cao nhất lại là lần 1 (0.8780), không trùng với lần có F1 cao nhất; accuracy chỉ dao động 0.846 - 0.878 trong khi F1 chênh gần 0.11, nên accuracy không phân biệt được chất lượng trên lớp thu nhập cao. Lần 2 dùng learning_rate nhỏ nhưng chỉ 50 cây nông nên mô hình chưa học đủ (F1 0.6051, dưới ngưỡng): learning_rate nhỏ phải đi kèm n_estimators lớn hơn để bù. Lần 3 chỉ nhỉnh hơn lần 1 khoảng 0.004 F1 nhưng huấn luyện lâu hơn (6.5s so với 4.1s), nên mức lợi từ mô hình lớn hơn là nhỏ.

---

## 2. Vì Sao Ngưỡng Chất Lượng Đặt Trên F1 Chứ Không Phải Accuracy

Tập Adult chỉ có 24,8% mẫu thuộc lớp thu nhập > 50K, còn 75,2% thuộc lớp thu nhập thấp. Một mô hình vô dụng luôn trả lời "thu nhập thấp" vẫn đạt accuracy 0,752, tức chỉ thấp hơn các mô hình thật của lab (0,846 - 0,878) khoảng 0,09 - 0,13 dù không học được gì, nên accuracy che giấu việc mô hình có nhận ra được ai thu nhập cao hay không. F1 của lớp dương là trung bình điều hòa của precision và recall trên đúng lớp thu nhập cao; mô hình luôn đoán lớp 0 sẽ có F1 bằng 0. Vì vậy ngưỡng 0.65 trên F1 mới thật sự chặn được mô hình kém. Khi gọi `f1_score` không dùng `average="macro"` hay `"weighted"`, vì trung bình có trọng số theo số mẫu sẽ bị lớp đa số kéo điểm lên và che mất hiệu quả thực trên lớp thiểu số.

---

## 3. Khó Khăn Gặp Phải và Cách Giải Quyết

<!-- Nêu 2 - 3 khó khăn thật, mỗi ô một câu ngắn. -->

| Khó khăn | Nguyên nhân | Cách giải quyết |
|---|---|---|
| ___ | ___ | ___ |
| ___ | ___ | ___ |
| ___ | ___ | ___ |

---

## 4. So Sánh Bước 2 và Bước 3 (bắt buộc, 2 - 3 câu)

<!-- Lấy số liệu từ bảng ở mục 3.6 của tasks/buoc-3.md. -->

| | f1_score | accuracy |
|---|---|---|
| Bước 2 (chỉ `train_batch1`) | ___ | ___ |
| Bước 3 (thêm `train_batch2`) | ___ | ___ |

**Nhận xét:** ___

<!--
Một câu trả lời trung thực kiểu "f1 giảm 0,01 vì dữ liệu mới cùng phân phối, không mang
thêm thông tin mới" được đánh giá cao hơn kết luận sai rằng thêm dữ liệu luôn tốt hơn.
-->

---

## 5. Phần Bonus Đã Thực Hiện (nếu có)

<!-- Xóa cả mục 5 nếu không làm bonus. Mỗi bonus tối đa 1 dòng. -->

- [ ] Bonus 1 - Tracking MLflow từ xa với DagsHub: ___
- [ ] Bonus 2 - Điều chỉnh ngưỡng quyết định: ___
- [ ] Bonus 3 - Báo cáo precision / recall tự động: ___
- [ ] Bonus 4 - Hoàn trả về phiên bản trước: ___
- [ ] Bonus 5 - Cảnh báo lệch lạc dữ liệu: ___
