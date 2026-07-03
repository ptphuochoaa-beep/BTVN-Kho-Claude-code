# Nguồn tham khảo thương hiệu (dùng khi dựng báo cáo HTML)

Trước khi tuỳ biến `templates/report-template.html`, đối chiếu với các tài sản thương hiệu đã
có sẵn ở project root để giữ nhất quán:

- `gsc-analysis-tool.html` — công cụ đã build sẵn cho GSC, cùng bảng màu (`#004aef`, `#07ef9c`)
  và cấu trúc CDN (Chart.js, PapaParse, xlsx.js). Nếu cần thêm tính năng đọc file trực tiếp trên
  trình duyệt (thay vì qua Claude Code), tham khảo cách tool này parse CSV/Excel bằng JS thuần.
- `logo-seongon-ai-first.png` — logo chính thức, nhúng base64 khi cần logo trong báo cáo HTML
  xuất ra để chia sẻ độc lập (không phụ thuộc đường dẫn file ngoài).
- `Mẫu tham khảo giao diện/` — ảnh chụp màn hình UI mẫu, tham khảo bố cục/spacing khi cần nâng
  cấp giao diện báo cáo vượt ngoài khung có sẵn trong `report-template.html`.
- `gsc-tool-requirements.md` — mô tả đầy đủ yêu cầu UX/UI và framework phân tích gốc mà
  `gsc-traffic-analysis` và skill này phải giữ nhất quán khi mở rộng.
