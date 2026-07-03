---
name: seo-report-pipeline
description: Điều phối chạy tuần tự seo-rank-tracking, gsc-traffic-analysis rồi seo-performance-report để tạo 1 bản báo cáo hiệu suất SEO định kỳ hoàn chỉnh (top từ khoá + traffic + dashboard trực quan). Dùng khi người dùng muốn "chạy full báo cáo", "báo cáo tuần/tháng", "báo cáo định kỳ" trong 1 lần thay vì gọi từng skill riêng lẻ.
when_to_use: |
  Trigger phrases: "chạy full pipeline", "làm báo cáo tuần/tháng", "báo cáo định kỳ", "báo cáo
  tổng hợp SEO", "check top và traffic rồi làm báo cáo giúp tôi". Dùng khi người dùng muốn kết
  quả cuối cùng là 1 bản báo cáo hoàn chỉnh, không cần tự gọi lần lượt 3 skill con.
argument-hint: "[du-an] [ky-bao-cao] [file-rank] [file-gsc]"
license: MIT
allowed-tools: Bash(curl:*), Bash(python3:*), Read, Write, Grep
user-invokable: true
metadata:
  author: SEONGON
  version: "1.0.0"
  category: seo
  role: Nhân viên kỹ thuật SEO
---

# Pipeline Báo Cáo Hiệu Suất SEO Định Kỳ

Gọi tuần tự 3 skill đã có, đúng thứ tự phụ thuộc dữ liệu, để tạo ra **1 bản báo cáo hiệu suất dự
án SEO hoàn chỉnh**: kết quả TOP bộ từ khoá + kết quả phân tích traffic từ GSC + báo cáo trực
quan hoá — đúng như yêu cầu đầu ra gộp của 3 skill gốc.

Đây là skill **điều phối** — không viết lại logic phân tích. Khi chạy, đọc và làm theo đúng nội
dung của từng SKILL.md con tại các bước tương ứng, không tự suy diễn tắt bước.

## Bước 0 — Thu thập thông tin đầu vào cần thiết

Hỏi gộp 1 lần (thay vì hỏi rải rác giữa chừng) những gì còn thiếu:

1. Dự án/domain đang chạy báo cáo, kỳ so sánh (vd "tuần này vs tuần trước", hoặc ngày cụ thể).
2. Nguồn dữ liệu rank: file export SERPUPDATE 2 kỳ, hay cần gọi API live (cần `SERPUPDATE_API_KEY`).
3. File GSC (CSV/Excel 1 kỳ hoặc so sánh 2 kỳ) cho phần traffic.
4. Định dạng báo cáo cuối muốn xuất: HTML / Excel / mô tả cấu trúc Looker Studio (theo Bước 0 của
   `seo-performance-report`).

Nếu người dùng chỉ có 1 trong 2 nguồn dữ liệu (chỉ rank hoặc chỉ traffic), vẫn tiếp tục pipeline
với phần có dữ liệu và nêu rõ phần thiếu ở báo cáo cuối — không dừng toàn bộ pipeline.

## Bước 1 — Chạy `seo-rank-tracking`

Đọc `../seo-rank-tracking/SKILL.md` và thực hiện đầy đủ Bước 0-4 của skill đó với dữ liệu rank
đã thu thập ở Bước 0. Giữ lại kết quả có cấu trúc (số liệu tổng quan + bảng chi tiết theo nhóm/
mức độ/URL) để dùng ở Bước 3 — không chỉ in ra text rồi bỏ qua.

## Bước 2 — Chạy `gsc-traffic-analysis`

Đọc `../gsc-traffic-analysis/SKILL.md` và thực hiện đầy đủ Bước 0-3 của skill đó với file GSC đã
thu thập ở Bước 0. Giữ lại kết quả có cấu trúc tương tự Bước 1.

Bước 1 và Bước 2 độc lập dữ liệu đầu vào — có thể thực hiện không theo thứ tự nghiêm ngặt, nhưng
**phải hoàn tất cả hai** (hoặc ghi nhận rõ lý do thiếu) trước khi sang Bước 3.

## Bước 3 — Tổng hợp dữ liệu trung gian

Gộp kết quả Bước 1 + Bước 2 theo đúng schema tại `contracts/du-lieu-trung-gian.md`. Đây là bước
riêng của pipeline (không có trong skill con) để đảm bảo Bước 4 nhận đúng hình dạng dữ liệu mà
`report-template.html` cần, tránh phải đoán lại field khi dựng biểu đồ.

## Bước 4 — Chạy `seo-performance-report`

Đọc `../seo-performance-report/SKILL.md` và thực hiện Bước 0-4 của skill đó, dùng dữ liệu đã
tổng hợp ở Bước 3 làm input — không phân tích lại dữ liệu thô.

## Bước 5 — Bàn giao

Trả về cho người dùng:
- Bản tóm tắt điều hành ngắn (3-5 câu, dựa trên KPI Lớp 3 của báo cáo).
- File báo cáo đã tạo (đường dẫn HTML/Excel) hoặc nội dung mô tả cấu trúc Looker Studio.
- Danh sách phần nào bị thiếu dữ liệu (nếu có) và cần bổ sung gì cho lần chạy sau.

## Pre-Delivery Review (bắt buộc)

- [ ] Cả 3 skill con đều được thực hiện đúng trình tự phụ thuộc (1, 2 trước Bước 3-4) — không nhảy thẳng vào Bước 4 khi thiếu dữ liệu tổng hợp.
- [ ] Dữ liệu trung gian ở Bước 3 khớp schema `contracts/du-lieu-trung-gian.md`, field thiếu để trống thay vì mặc định 0.
- [ ] Checklist Pre-Delivery Review riêng của từng skill con (1, 2, 3) đã được áp dụng trong bước tương ứng, không bỏ qua vì đang chạy pipeline.
- [ ] Báo cáo cuối nêu rõ phần nào thiếu dữ liệu nếu Bước 1 hoặc Bước 2 không đủ input.

## Error Handling

| Lỗi | Nguyên nhân | Cách xử lý |
|---|---|---|
| Thiếu dữ liệu rank hoặc traffic | Người dùng chưa cung cấp 1 trong 2 nguồn | Tiếp tục pipeline với phần có sẵn, ghi rõ phần thiếu trong báo cáo cuối, không dừng toàn bộ |
| Lỗi ở Bước 1 (API SERPUPDATE) | Sai key, rate limit, task chưa xong (xem Error Handling của `seo-rank-tracking`) | Xử lý theo đúng bảng lỗi của skill con đó; nếu không khắc phục được, bỏ qua phần rank, vẫn chạy tiếp Bước 2 và 4 |
| Lỗi ở Bước 2 (file GSC sai định dạng) | Xem Error Handling của `gsc-traffic-analysis` | Tương tự — báo lỗi cụ thể, không chặn toàn bộ pipeline nếu Bước 1 vẫn có dữ liệu |
| Dữ liệu tổng hợp Bước 3 thiếu field bắt buộc cho biểu đồ nào đó | Bước 1/2 trả về không đủ chi tiết | Bỏ qua đúng biểu đồ đó ở Bước 4, ghi chú "Chưa có dữ liệu", không làm hỏng cả báo cáo |
