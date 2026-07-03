# Mapping nhóm chủ đề theo slug

Dùng ở Bước 2.2 của `gsc-traffic-analysis` để gán mỗi URL vào 1 trong 5 nhóm chủ đề. Khớp theo
**thứ tự từ trên xuống**, dừng lại ở pattern đầu tiên khớp (pattern cụ thể hơn đặt trước pattern
rộng hơn để tránh gán nhầm).

> File này khởi tạo dựa trên cấu trúc slug thực tế của seongon.com — **chỉnh lại theo domain của
> dự án đang phân tích** nếu khác cấu trúc.

## Bảng mapping (thứ tự ưu tiên từ trên xuống)

| Thứ tự | Pattern slug (chứa đoạn) | Nhóm | Ghi chú |
|---|---|---|---|
| 1 | `/tuyen-dung`, `/ve-seongon`, `/tac-gia`, `/lien-he`, `/ho-so-nang-luc` | Nhóm 1 — Thương hiệu | Nội dung về công ty, con người, tuyển dụng |
| 2 | `/case-study`, `/dao-tao`, `/su-kien`, `/tai-lieu-ebook` | Nhóm 2 — Điểm mạnh website | Bằng chứng năng lực: case study, đào tạo, tài liệu, sự kiện |
| 3 | `/dich-vu`, `/blog/seo` | Nhóm 5 — Danh mục/hotkey | Nội dung gắn trực tiếp dịch vụ cốt lõi (SEO) |
| 4 | (bài match theo danh sách từ khoá xu hướng/thời vụ — xem mục "Nhận diện chủ đề xu hướng") | Nhóm 4 — Xu hướng | Ngày lễ, sự kiện thời sự, theo mùa |
| 5 | `/blog/digital-marketing`, `/blog/ai`, `/blog/facebook`, `/blog/tiktok`, `/blog/google-ads`, `/blog/website` | Nhóm 3 — Chủ đề ngang | Kiến thức digital marketing tầm rộng, không riêng SEO |
| — | Không khớp pattern nào | Chưa phân nhóm | Báo cáo riêng, đề xuất bổ sung pattern |

## Nhận diện "chủ đề xu hướng" (Nhóm 4)

Không nhận diện được qua slug đơn thuần — cần khớp thêm theo **tiêu đề bài viết** (nếu export
GSC có kèm tiêu đề) hoặc danh sách từ khoá thời vụ tự cập nhật dưới đây:

| Từ khoá/cụm nhận diện trong tiêu đề hoặc slug |
|---|
| tết, giáng sinh, black friday, 11-11, 12-12, sale |
| xu hướng 2026, dự đoán 2026, trend |
| (bổ sung thêm theo lịch content thời vụ của dự án) |

Nếu bài vừa khớp Nhóm 4 vừa khớp 1 nhóm khác theo slug (vd `/blog/digital-marketing/xu-huong-...`),
**ưu tiên Nhóm 4** vì mục tiêu là tách riêng hiệu ứng thời vụ khỏi tăng trưởng nền tảng.

## Khi > 15% URL rơi vào "Chưa phân nhóm"

Liệt kê 10-15 slug phổ biến nhất trong nhóm "Chưa phân nhóm" cho người dùng, đề xuất pattern mới
để bổ sung vào bảng trên — không tự ý thêm pattern mà không xác nhận.
