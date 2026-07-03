# Schema dữ liệu trung gian giữa các skill trong pipeline

Khi `seo-report-pipeline` điều phối, kết quả của Bước 1 (`seo-rank-tracking`) và Bước 2
(`gsc-traffic-analysis`) phải được tổng hợp lại theo đúng field bên dưới trước khi đưa vào Bước 3
(`seo-performance-report`) — field này khớp 1-1 với `REPORT_DATA` trong
`seo-performance-report/templates/report-template.html`, để Bước 3 không phải đoán lại cấu trúc.

Đây là **quy ước nội bộ giữa các bước trong pipeline**, không phải file cấu hình cần người dùng
điền tay — Claude tự tổng hợp output của Bước 1/2 vào đúng hình dạng này trong lúc chạy.

```jsonc
{
  "kpi": {
    "traffic": { "value": 0, "deltaPct": 0 },      // tổng Clicks kỳ này + % so kỳ trước
    "rankDown": { "value": 0, "deltaPct": 0 },      // số từ khoá tụt top + % trên tổng bộ từ khoá
    "rankUp": { "value": 0, "deltaPct": 0 },
    "targetPct": { "value": 0, "deltaPct": 0 }      // % đạt mục tiêu traffic (từ muc-tieu-traffic.md)
  },
  "trend": { "labels": ["2026-06-01", "..."], "clicks": [0] },   // từ Chart.csv, Skill 2
  "topicGroups": { "labels": ["Thương hiệu","..."], "values": [0] },   // Skill 2, Bước 2.2
  "keywordGroups": { "labels": ["hotkey","..."], "down": [0], "up": [0] },  // Skill 1, Bước 3
  "urls": [
    { "url": "...", "prevClicks": 0, "currClicks": 0, "deltaPct": 0, "group": "..." }
  ],
  "keywords": [
    { "keyword": "...", "prevPos": 0, "currPos": 0, "delta": 0, "group": "...", "severity": "nhẹ|sâu|out-top100" }
  ]
}
```

Nếu Bước 1 hoặc Bước 2 bị bỏ qua/lỗi (xem Error Handling trong SKILL.md của pipeline), để trống
đúng field tương ứng (mảng rỗng hoặc `null`) — không bịa số 0 gây hiểu nhầm là "không biến động".
