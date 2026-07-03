# Hướng dẫn chọn biểu đồ theo loại dữ liệu

Áp dụng ở Bước 2 của `seo-performance-report`. Nguyên tắc chung: **biểu đồ phải trả lời được
1 câu hỏi cụ thể trong khung phân tích của Skill 1/2** — không chọn biểu đồ vì "cho đẹp".

## Lớp 1 — Chi tiết URL / từ khoá

| Câu hỏi cần trả lời | Biểu đồ | Lý do chọn |
|---|---|---|
| URL/từ khoá nào biến động mạnh nhất? | Bar chart ngang, sắp giảm dần theo \|Δ\|, màu đỏ = giảm/tụt, xanh = tăng | Dễ so sánh nhiều hạng mục, đọc tên URL/từ khoá dài không bị cắt |
| Vị trí từ khoá thay đổi thế nào theo thời gian? | Line chart, 1 đường/từ khoá (giới hạn ≤ 8 đường để không rối) | Thể hiện được "tụt liên tục" vs "tụt 1 lần rồi dừng" |

## Lớp 2 — Theo nhóm

| Câu hỏi cần trả lời | Biểu đồ | Lý do chọn |
|---|---|---|
| Nhóm nào chiếm tỷ trọng traffic/từ khoá lớn nhất? | Doughnut chart | Trực quan tỷ trọng, tối đa 5-6 lát cắt (khớp đúng 4-5 nhóm của SEONGON) |
| Nhóm nào tăng/giảm mạnh nhất so kỳ trước? | Stacked hoặc grouped bar chart (2 cột/nhóm: kỳ trước vs kỳ này) | So sánh trực tiếp 2 kỳ trên cùng trục, dễ thấy nhóm nào đảo chiều |
| Phân bổ mức độ tụt (nhẹ/sâu/out top 100) | Stacked bar theo nhóm từ khoá | Thấy được nhóm nào tụt sâu nhiều hơn nhóm khác |

## Lớp 3 — Tổng quan toàn site

| Câu hỏi cần trả lời | Hiển thị | Lý do chọn |
|---|---|---|
| KPI tổng thay đổi bao nhiêu % so kỳ trước? | KPI card + mũi tên (↑/↓) + % | Đọc nhanh trong 3 giây, đúng chuẩn dashboard điều hành |
| Xu hướng traffic tổng theo ngày | Line chart toàn site (đã có sẵn từ `Chart.csv`) | Cho thấy pattern chung trước khi đi vào chi tiết |

## Quy tắc màu bắt buộc (đồng bộ thương hiệu SEONGON)

- Xanh dương `#004aef` — chỉ số chính/trung tính, đường xu hướng chính
- Xanh mint `#07ef9c` — tăng trưởng, cải thiện
- Đỏ (`#ef4444` hoặc tương đương) — giảm/tụt, dùng nhất quán trong toàn báo cáo
- Không dùng quá 5-6 màu trong 1 biểu đồ — nếu nhóm/hạng mục nhiều hơn, gộp phần nhỏ vào "Khác"

## Giới hạn số lượng hạng mục mỗi biểu đồ

- Bar chart chi tiết URL/từ khoá: tối đa 20-30 hạng mục/biểu đồ, phần còn lại đưa xuống bảng dữ liệu đầy đủ bên dưới.
- Line chart nhiều đường: tối đa 8 đường, quá số này tách thành nhiều biểu đồ nhỏ theo nhóm.
- Doughnut/pie: tối đa 6 lát cắt, gộp phần dư vào "Khác".
