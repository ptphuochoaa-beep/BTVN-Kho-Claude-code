---
name: seo-performance-report
description: Tổng hợp kết quả từ seo-rank-tracking và gsc-traffic-analysis thành 1 báo cáo hiệu suất SEO trực quan hoá bằng biểu đồ (HTML/Chart.js hoặc Excel), đi từ chi tiết URL/từ khoá đến tổng quan toàn site, mỗi biểu đồ kèm nhận xét. Dùng khi người dùng nói "làm báo cáo", "trực quan hoá", "dashboard", "báo cáo sếp", "xuất báo cáo".
when_to_use: |
  Trigger phrases: "làm báo cáo", "trực quan hoá dữ liệu", "dashboard", "báo cáo cho sếp",
  "xuất file báo cáo", "vẽ biểu đồ". Dùng SAU KHI đã có kết quả phân tích từ seo-rank-tracking
  và/hoặc gsc-traffic-analysis trong cùng phiên làm việc — skill này không tự phân tích lại dữ
  liệu thô, chỉ trực quan hoá & tổng hợp kết quả đã có.
argument-hint: "[ket-qua-skill-1] [ket-qua-skill-2] [dinh-dang: html|excel]"
license: MIT
allowed-tools: Bash(python3:*), Read, Write
user-invokable: true
metadata:
  author: SEONGON
  version: "1.0.0"
  category: seo
  role: Nhân viên kỹ thuật SEO
---

# Làm Báo Cáo & Trực Quan Hoá Dữ Liệu SEO

Kế thừa output đã tính toán sẵn của `seo-rank-tracking` (biến động thứ hạng) và
`gsc-traffic-analysis` (biến động traffic) để dựng thành 1 báo cáo hiệu suất dự án SEO hoàn
chỉnh, trực quan bằng biểu đồ, đi từ **chi tiết nhất → tổng quan nhất**.

Nếu người dùng chưa chạy 2 skill trên trong phiên này, hỏi lại hoặc chủ động gợi ý chạy trước —
skill này không tự bịa số liệu để vẽ biểu đồ.

## Bước 0 — Xác định định dạng đầu ra

Hỏi người dùng (nếu chưa nêu) muốn xuất dạng nào, mỗi dạng dùng khi:

| Định dạng | Dùng khi |
|---|---|
| **HTML standalone** (`templates/report-template.html`) | Cần xem nhanh, trình bày trên trình duyệt/trình chiếu, giữ đúng nhận diện thương hiệu SEONGON |
| **Excel/Google Sheet** | Sếp/khách hàng cần chỉnh sửa số liệu, lưu trữ định kỳ, hoặc ghép vào file báo cáo tổng hợp có sẵn |
| **Looker Studio** | Cần dashboard cập nhật tự động theo thời gian — skill chỉ mô tả cấu trúc data source & gợi ý biểu đồ, không tự tạo được report Looker Studio trực tiếp |

## Bước 1 — Cấu trúc báo cáo theo 3 lớp (chi tiết → tổng quan)

Luôn tổ chức theo đúng thứ tự sau, **không đảo ngược** (không mở đầu bằng số tổng quan rồi mới
đi xuống chi tiết — yêu cầu của SEONGON là dẫn dắt người đọc từ bằng chứng cụ thể lên kết luận):

1. **Lớp 1 — Chi tiết URL/từ khoá**: bảng/biểu đồ so sánh biến động của từng URL (traffic) và
   từng từ khoá (thứ hạng) — đây là bằng chứng cụ thể nhất, dùng dữ liệu thô đã tính ở Skill 1 & 2.
2. **Lớp 2 — Theo nhóm**: tổng hợp Lớp 1 theo nhóm chủ đề (traffic) và nhóm từ khoá (rank) —
   cho thấy pattern chung thay vì từng điểm dữ liệu lẻ.
3. **Lớp 3 — Tổng quan toàn site**: KPI tổng (traffic, số từ khoá tụt/tăng, % đạt mục tiêu) —
   đặt ở đầu báo cáo khi trình bày (tóm tắt điều hành) nhưng được XÂY DỰNG SAU CÙNG, dựa trên
   Lớp 1 và 2 đã tổng hợp, để đảm bảo số tổng quan luôn khớp với chi tiết bên dưới.

## Bước 2 — Chọn loại biểu đồ theo dữ liệu

Xem chi tiết & lý do chọn tại `templates/chart-guidelines.md`. Tóm tắt nhanh:

| Dữ liệu | Loại biểu đồ |
|---|---|
| Biến động Clicks/vị trí theo từng URL/từ khoá (Lớp 1) | Bar chart ngang, sắp theo Δ giảm dần, tô màu đỏ/xanh theo chiều tăng/giảm |
| Xu hướng theo ngày (Chart.csv) | Line chart |
| Phân bổ theo nhóm chủ đề/nhóm từ khoá (Lớp 2) | Doughnut/stacked bar chart |
| So sánh 2 kỳ theo KPI tổng (Lớp 3) | KPI card + mũi tên % thay đổi |
| Số lượng từ khoá theo mức độ tụt (nhẹ/sâu/out top 100) | Stacked bar hoặc waterfall |

## Bước 3 — Sinh báo cáo

**Với HTML**: đọc `templates/report-template.html`, giữ nguyên khung màu thương hiệu
(`#004aef` xanh dương chủ đạo, `#07ef9c` xanh mint phụ, font Gilroy — đồng bộ với
`gsc-analysis-tool.html` ở project root) và cấu trúc CDN (Chart.js) đã dùng trong project. Điền
dữ liệu thật từ output Skill 1/2 vào các mảng `chartData` tương ứng, không để placeholder mẫu.

**Với Excel**: dùng `python3` + `openpyxl` để tạo workbook nhiều sheet (Tổng quan, Chi tiết URL,
Chi tiết từ khoá, Theo nhóm), dùng conditional formatting hoặc cột màu để thể hiện tăng/giảm,
và chèn biểu đồ native của Excel (`openpyxl.chart`) tương ứng bảng ở Bước 2.

**Với Looker Studio**: liệt kê cấu trúc data source cần thiết (tên cột, kiểu dữ liệu) và gợi ý
từng loại biểu đồ/bộ lọc nên dùng cho từng lớp — người dùng tự dựng trong giao diện Looker Studio.

## Bước 4 — Bắt buộc: mỗi biểu đồ có 1-2 câu kết luận

Ngay dưới mỗi biểu đồ/bảng, viết 1-2 câu kết luận cụ thể — nêu tên URL/từ khoá/nhóm và con số,
không viết chung chung kiểu "traffic có biến động". Ví dụ đúng:

> "Bài `/blog/seo/mau-bai-viet-chuan-seo.html` giảm 18% Clicks (407 → 334), là mức giảm lớn nhất
> trong nhóm Danh mục/hotkey — nên kiểm tra lại thứ hạng từ khoá chính của bài này qua Skill 1."

## Pre-Delivery Review (bắt buộc)

- [ ] Số liệu Lớp 3 (tổng quan) khớp với tổng cộng từ Lớp 1/2 — không lệch do làm tròn hay lọc sót dữ liệu.
- [ ] Thứ tự trình bày đúng: tóm tắt điều hành (dựa trên số đã tổng hợp) → chi tiết theo nhóm → chi tiết theo URL/từ khoá, hoặc nêu rõ nếu người dùng yêu cầu thứ tự khác.
- [ ] Mọi biểu đồ đều có 1-2 câu kết luận cụ thể kèm theo, không để biểu đồ trơ trọi.
- [ ] Màu sắc/thương hiệu đúng theo `#004aef`/`#07ef9c`/Gilroy nếu xuất HTML.
- [ ] Không tự tạo số liệu mới ngoài những gì Skill 1/2 đã tính — nếu thiếu dữ liệu cho 1 phần, ghi rõ "Chưa có dữ liệu" thay vì bỏ trống hoặc suy diễn.

## Error Handling

| Lỗi | Nguyên nhân | Cách xử lý |
|---|---|---|
| Không có output từ Skill 1/2 trong phiên | Người dùng gọi thẳng Skill 3 | Hỏi lại hoặc đề xuất chạy `seo-rank-tracking`/`gsc-traffic-analysis` trước |
| `openpyxl` chưa cài | Môi trường thiếu thư viện | Báo lỗi cụ thể, đề xuất `pip install openpyxl` hoặc chuyển sang xuất HTML |
| Dữ liệu Lớp 1 quá nhiều dòng (vd >500 URL) cho 1 biểu đồ | Biểu đồ bar chart quá dày, không đọc được | Chỉ vẽ top 20-30 biến động mạnh nhất, còn lại để ở bảng chi tiết kèm theo |
| Thiếu dữ liệu 1 trong 2 nguồn (chỉ có rank hoặc chỉ có traffic) | Người dùng mới chạy 1 skill | Vẫn xuất báo cáo với phần có dữ liệu, ghi chú rõ phần còn thiếu |
