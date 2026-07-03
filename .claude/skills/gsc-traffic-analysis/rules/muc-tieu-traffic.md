# Mục tiêu traffic theo dự án

Dùng ở Bước 1 của `gsc-traffic-analysis` để tính mức thiếu hụt so với mục tiêu. Điền số liệu
thật của từng dự án vào bảng dưới — nếu để trống, skill sẽ hỏi trực tiếp thay vì tự đặt số.

| Dự án / domain | Mục tiêu traffic/tháng (Clicks) | Ghi chú |
|---|---|---|
| seongon.com | 40.000 / 30 ngày (≈ 1.333/ngày) | Xác nhận bởi người dùng ngày 03/07/2026 |

## Cách tính mức thiếu hụt

```
Traffic luỹ kế tháng hiện tại = tổng Clicks từ đầu tháng đến ngày gần nhất (từ Chart.csv)
Số ngày còn lại trong tháng   = tổng số ngày trong tháng − ngày hiện tại
Mức thiếu hụt/ngày            = (Mục tiêu tháng − Traffic luỹ kế) / Số ngày còn lại
```

Nếu `Mức thiếu hụt/ngày` âm → đã vượt tiến độ so với mục tiêu, nêu rõ đang vượt bao nhiêu %.
