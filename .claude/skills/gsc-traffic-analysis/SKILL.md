---
name: gsc-traffic-analysis
description: Phân tích hiệu suất traffic website từ file export Google Search Console (1 kỳ hoặc so sánh 2 kỳ) — tổng quan traffic, nguồn tăng/giảm theo bài viết, phân nhóm theo chủ đề qua slug. Dùng khi người dùng đưa file GSC (Queries/Pages/Chart .csv hoặc .xlsx multi-sheet) và hỏi "phân tích traffic", "vì sao traffic giảm", "so sánh 2 kỳ GSC".
when_to_use: |
  Trigger phrases: "phân tích traffic", "search console", "GSC", "so sánh 2 kỳ", "traffic giảm",
  "traffic tăng", "hiệu suất website". Tự kích hoạt khi người dùng đính kèm file .csv/.xlsx có
  các cột đặc trưng của GSC (Clicks, Impressions, CTR, Position) hoặc các sheet
  Queries/Pages/Chart/Devices/Countries.
argument-hint: "[file-gsc-1-ky] hoặc [file-so-sanh-2-ky.xlsx]"
license: MIT
allowed-tools: Bash(python3:*), Read, Write, Grep
user-invokable: true
metadata:
  author: SEONGON
  version: "1.0.0"
  category: seo
  role: Nhân viên kỹ thuật SEO
---

# Phân Tích Traffic Website Từ Google Search Console

Trả lời khung câu hỏi đánh giá traffic của SEONGON dựa trên dữ liệu export GSC. Kế thừa và
đồng bộ với framework đã dùng trong `gsc-tool-requirements.md` và `gsc-analysis-tool.html` ở
project root — nếu 2 file đó tồn tại, đọc để giữ nhất quán logic phân tích, KHÔNG viết lại từ đầu.

## Bước 0 — Đọc & nhận dạng dữ liệu đầu vào

File GSC có thể ở 2 dạng:
- **CSV đơn lẻ** theo loại báo cáo (`Queries.csv`, `Pages.csv`, `Chart.csv` theo ngày,
  `Devices.csv`, `Countries.csv`, `Search appearance.csv`).
- **Excel nhiều sheet, so sánh 2 kỳ**: mỗi sheet tương ứng 1 loại báo cáo, thường có cột lặp lại
  cho kỳ hiện tại và kỳ trước (vd `Clicks`, `Clicks (kỳ trước)` hoặc 2 khối cột).

Dùng `python3` (pandas nếu có sẵn, nếu không dùng `csv`/`openpyxl` thuần) để đọc — không đoán số
liệu bằng mắt khi file lớn. Ví dụ đọc nhanh 1 sheet Excel:

```bash
python3 -c "
import openpyxl
wb = openpyxl.load_workbook('so_sanh_2_ky.xlsx', data_only=True)
print(wb.sheetnames)
ws = wb[wb.sheetnames[0]]
for row in ws.iter_rows(min_row=1, max_row=5, values_only=True):
    print(row)
"
```

Xác nhận cột bắt buộc theo từng loại báo cáo:
- Queries: `Top queries, Clicks, Impressions, CTR, Position` (+ cột kỳ trước nếu so sánh)
- Pages: `Top pages, Clicks, Impressions, CTR, Position`
- Chart: `Date, Clicks, Impressions, CTR, Position` — nguồn duy nhất có time-series theo ngày
- Devices / Countries: cùng cấu trúc, khác chiều phân tích

Nếu thiếu cột bắt buộc, dừng lại và báo cụ thể cột nào thiếu — không suy diễn số liệu.

## Bước 1 — Tình trạng tổng quan

- **Traffic hiện tại**: tổng Clicks theo tháng/tuần/ngày gần nhất có trong `Chart`.
- **So với mục tiêu**: đọc mục tiêu traffic trong `rules/muc-tieu-traffic.md`. Nếu người dùng
  chưa điền mục tiêu vào file đó, hỏi trực tiếp thay vì tự bịa con số mục tiêu.
- **Mức thiếu hụt**: `(mục tiêu tháng − traffic thực tế luỹ kế) / số ngày còn lại trong tháng`
  = lượng truy cập trung bình/ngày cần đạt thêm để về đích.
- **Traffic ngày gần nhất**: lấy dòng cuối của `Chart`, so với trung bình 7 ngày trước đó.
- **Xu hướng gần đây**: tính % thay đổi trung bình trượt 7 ngày so với 7 ngày liền trước; nêu rõ
  đang tăng tốc, chững lại, hay giảm tốc.

## Bước 2 — Tình trạng chi tiết

### 2.1 Nguồn gốc tăng/tụt traffic (so khớp `Pages` giữa 2 kỳ theo URL)

- Chuẩn hoá URL (bỏ scheme, `www`, trailing slash) trước khi so khớp.
- **Bài mới**: URL chỉ xuất hiện ở kỳ hiện tại → liệt kê kèm Clicks đóng góp, xếp theo đóng góp
  giảm dần.
- **Bài cũ tăng traffic**: URL có ở cả 2 kỳ, Clicks kỳ này > kỳ trước → nêu cụ thể bài, mức tăng
  tuyệt đối và %, và thời điểm bắt đầu tăng nếu suy ra được từ `Chart` (không có time-series theo
  URL trong export chuẩn của GSC nên chỉ ước lượng bằng mốc so sánh 2 kỳ, nêu rõ giới hạn này).
- **Bài giảm**: tương tự, liệt kê top giảm mạnh nhất — đây là input trực tiếp cho Skill
  `seo-performance-report`.

### 2.2 Phân tích theo nhóm chủ đề (dựa trên slug)

Đọc bảng mapping slug → nhóm tại `rules/nhom-chu-de.md` (5 nhóm: thương hiệu, điểm mạnh website,
chủ đề ngang, chủ đề xu hướng, danh mục/hotkey). Với mỗi URL trong `Pages`, gán nhóm theo pattern
khớp trước tiên (ưu tiên pattern cụ thể hơn pattern rộng), tính tăng/giảm Clicks & Impressions
theo từng nhóm, và nêu nhóm nào đang kéo tăng/kéo giảm traffic tổng thể.

Nếu > 15% số URL rơi vào "Chưa phân nhóm", báo cho người dùng để bổ sung pattern vào
`rules/nhom-chu-de.md` thay vì âm thầm bỏ qua nhóm lớn dữ liệu.

## Bước 3 — Định dạng Output

```
## 1. Tình trạng tổng quan
- Traffic hiện tại: ... (theo ngày/tuần/tháng)
- So với mục tiêu: ... (thiếu/vượt bao nhiêu, cần thêm X lượt/ngày)
- Traffic ngày gần nhất: ... so với trung bình 7 ngày trước
- Xu hướng: tăng tốc / chững / giảm tốc + % cụ thể

## 2. Tình trạng chi tiết
### Nguồn tăng/giảm (bảng: URL | Clicks kỳ trước | Clicks kỳ này | Δ | Δ% | phân loại mới/cũ)
### Theo nhóm chủ đề (bảng: nhóm | Clicks kỳ trước | Clicks kỳ này | Δ% | Impressions Δ%)

## 3. Đề xuất phân tích bổ sung
(chỉ nêu các gợi ý áp dụng được — xem "Gợi ý mở rộng phân tích")
```

Mỗi bảng đi kèm 1-2 câu nhận xét cụ thể (gọi tên URL/nhóm, không nhận xét chung chung).

## Gợi ý mở rộng phân tích (đề xuất thêm ngoài yêu cầu gốc)

- **CTR vs Position**: lọc các trang có Impressions cao nhưng CTR thấp hơn kỳ vọng theo vị trí
  (vd top 5 mà CTR < 3%) → cơ hội tối ưu title/meta description mà không cần tăng thứ hạng.
- **Cannibalization**: nhiều URL cùng lên top cho 1 query trong `Queries` — dấu hiệu cạnh tranh
  nội bộ làm loãng traffic, nên gộp hoặc điều hướng canonical.
- **Thiết bị**: đối chiếu `Devices` 2 kỳ — nếu traffic giảm tập trung ở Mobile, ưu tiên kiểm tra
  Core Web Vitals/mobile usability trước khi kết luận do nội dung.
- **Địa lý bất thường**: tăng đột biến traffic từ quốc gia ngoài thị trường mục tiêu trong
  `Countries` có thể là dấu hiệu bot/click không hợp lệ, cần loại trừ trước khi báo cáo là tăng
  trưởng thật.
- **So sánh cùng kỳ năm trước (YoY)**: nếu người dùng cung cấp thêm dữ liệu cùng kỳ năm trước,
  tách biệt biến động theo mùa vụ khỏi biến động do thay đổi thực sự trên site.
- **Tương quan với Skill 1**: nếu cùng thời điểm có dữ liệu tụt top từ `seo-rank-tracking`, đối
  chiếu để xác nhận traffic giảm có khớp với từ khoá/URL bị tụt hạng hay không.

## Pre-Delivery Review (bắt buộc)

- [ ] Đã đọc `gsc-tool-requirements.md`/`gsc-analysis-tool.html` (nếu có) để không mâu thuẫn logic đã thống nhất trước đó.
- [ ] URL đã chuẩn hoá (scheme, www, trailing slash) trước khi so khớp 2 kỳ.
- [ ] Mục tiêu traffic lấy từ `rules/muc-tieu-traffic.md` hoặc hỏi người dùng — không tự đặt số.
- [ ] Mọi Δ% đều nêu rõ số tuyệt đối đi kèm, tránh % gây hiểu nhầm trên nền số nhỏ.
- [ ] URL "Chưa phân nhóm" được báo cáo riêng nếu chiếm tỷ trọng đáng kể, không im lặng bỏ qua.
- [ ] Không suy diễn nguyên nhân giảm traffic (thuật toán, kỹ thuật...) nếu không có dữ liệu hỗ trợ — chỉ nêu tương quan và đề xuất kiểm tra thêm.

## Error Handling

| Lỗi | Nguyên nhân | Cách xử lý |
|---|---|---|
| Thiếu cột `Clicks/Impressions/CTR/Position` | Export sai loại báo cáo GSC | Yêu cầu export lại đúng tab (Queries/Pages/...) |
| Sheet Excel không đúng tên/thứ tự | File so sánh 2 kỳ có cấu trúc khác | Dò từng sheet bằng `wb.sheetnames`, không giả định thứ tự cố định |
| Dòng `(other)` trong Queries/Pages | GSC gộp các query/URL dưới ngưỡng hiển thị | Tách riêng, không cộng gộp vào phân tích theo nhóm/URL cụ thể |
| Ngày bị thiếu (gap) trong `Chart` | Property mới verify hoặc lỗi thu thập dữ liệu | Nêu rõ khoảng ngày thiếu, không nội suy số liệu |
| URL trùng nội dung nhưng khác dạng (`http` vs `https`, có/không `www`) | Redirect hoặc thay đổi domain | Chuẩn hoá URL trước khi so khớp 2 kỳ |
| CTR dạng chuỗi có `%` khi đọc bằng pandas | Không parse kiểu số | Strip `%` và chia 100 trước khi tính toán |
