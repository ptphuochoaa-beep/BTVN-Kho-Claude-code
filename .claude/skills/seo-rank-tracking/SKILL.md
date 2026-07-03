---
name: seo-rank-tracking
description: Phân tích biến động thứ hạng từ khoá (check top) từ dữ liệu SERPUPDATE — phát hiện tụt/tăng top, phân nhóm từ khoá, mức độ tụt, đối thủ thay thế. Dùng khi người dùng nhắc "check top", "theo dõi thứ hạng", "báo cáo tụt top", "biến động từ khoá", hoặc đưa file export ranking từ SERPUPDATE.
when_to_use: |
  Trigger phrases: "check top", "theo dõi thứ hạng", "tụt top", "tăng top", "báo cáo rank hàng ngày",
  "biến động từ khoá", "so sánh thứ hạng", "serpupdate". Cũng tự kích hoạt khi người dùng đính kèm
  file CSV/Excel export từ SERPUPDATE hoặc hỏi "vì sao từ khoá X tụt top".
argument-hint: "[file-ky-truoc] [file-ky-nay] hoặc [danh-sach-tu-khoa]"
license: MIT
allowed-tools: Bash(curl:*), Read, Write, Grep
user-invokable: true
metadata:
  author: SEONGON
  version: "1.0.0"
  category: seo
  role: Nhân viên kỹ thuật SEO
---

# Check Top & Phân Tích Biến Động Thứ Hạng Từ Khoá

Trả lời trọn bộ khung câu hỏi "tụt top" của SEONGON bằng dữ liệu thứ hạng lấy từ
**SERPUPDATE** — qua file export (CSV/Excel) sẵn có, hoặc gọi trực tiếp **SERPUPDATE Open API**
khi cần check-live một tập từ khoá ngay tại thời điểm phân tích.

## Bước 0 — Xác định nguồn dữ liệu

Hỏi rõ người dùng (nếu chưa nêu) đang có gì trong 2 nguồn sau, có thể dùng cả hai:

1. **File export** (khuyến nghị, nhanh, không tốn API quota): file CSV/Excel xuất từ dashboard
   SERPUPDATE cho kỳ hiện tại và kỳ trước đó. Cột tối thiểu cần có: `keyword, url, position, date`.
2. **Gọi API live** (khi cần số liệu tức thời, không có export sẵn): dùng SERPUPDATE Open API —
   xem chi tiết ở phần "Gọi API SERPUPDATE" bên dưới.

Nếu người dùng chỉ đưa 1 kỳ dữ liệu, vẫn phân tích tổng quan (top hiện tại, phân bổ theo nhóm)
nhưng nêu rõ trong output là **thiếu dữ liệu kỳ trước nên không tính được biến động Δ**.

## Bước 1 — Gọi API SERPUPDATE (khi cần check live)

API yêu cầu API Key trong header `X-API-KEY`. Hỏi người dùng key nếu chưa được cấu hình
trong biến môi trường `SERPUPDATE_API_KEY`. **Không bao giờ in API key ra output hay log.**

Base URL: `https://app.serpupdate.com`

### 1. Tạo task check rank
```bash
curl -s -X POST "https://app.serpupdate.com/api/v1/open-api/create-task" \
  -H "X-API-KEY: $SERPUPDATE_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "device": "desktop",
    "locationCode": "<location_code>",
    "languageCode": "<language_code>",
    "searchEngine": "GOOGLE",
    "keywords": [
      {"id": "kw-1", "keyword": "seongon"},
      {"id": "kw-2", "keyword": "seo là gì"}
    ]
  }'
```
Lấy `locationCode`/`languageCode` hợp lệ qua `POST /api/v1/open-api/locations` và
`POST /api/v1/open-api/languages` nếu chưa biết mã của Việt Nam / tiếng Việt.

### 2. Lấy kết quả task
```bash
curl -s -X POST "https://app.serpupdate.com/api/v1/open-api/task-result" \
  -H "X-API-KEY: $SERPUPDATE_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"taskId": "<task_id_tra_ve_o_buoc_1>"}'
```
Task chạy bất đồng bộ — nếu `content` rỗng hoặc `isSuccess` báo đang xử lý, đợi ~5-10s rồi gọi
lại `task-result`, tối đa 6 lần thử. Nếu vẫn chưa xong, báo cho người dùng thay vì bịa kết quả.

Từ `content` trả về, với mỗi từ khoá lấy: vị trí (rank), URL xếp hạng, domain, và danh sách
domain khác đang chiếm top 10 tại thời điểm check — dữ liệu này dùng để trả lời câu hỏi
"đối thủ nào đang lên thay vị trí của mình".

## Bước 2 — Chuẩn hoá & gắn nhóm từ khoá

Mỗi từ khoá phải được gắn 1 trong 4 nhóm theo `keyword-groups/phan-nhom-tu-khoa.md`:
**hotkey, volume cao, info, brand**. Đọc file đó để lấy quy tắc/danh sách phân nhóm hiện có
của người dùng. Nếu một từ khoá không khớp quy tắc nào, xếp vào "Chưa phân nhóm" và liệt kê
riêng — không tự suy đoán nhóm.

Đọc ngưỡng đánh giá mức độ tụt (nhẹ/sâu/out top 100) tại `keyword-groups/nguong-danh-gia.md`.

## Bước 3 — Tính toán các chỉ số bắt buộc

Với mỗi từ khoá: `Δ vị trí = vị trí kỳ này − vị trí kỳ trước` (dương = tụt, âm = tăng).

- **Tụt top bao nhiêu từ khoá** = đếm số từ khoá có Δ > 0 (hoặc chuyển từ "trong top 100" sang
  "không xếp hạng").
- **Tụt ở nhóm nào** = group theo nhóm từ khoá, tính số lượng & % tụt trên tổng nhóm đó.
- **% tụt theo URL** = group theo URL đích, kiểm tra trong các từ khoá cùng trỏ về 1 URL có phải
  tất cả đều tụt hay chỉ một phần — nêu rõ tỷ lệ.
- **Tụt theo ngày hay 1 lần rồi dừng**: nếu có time-series nhiều điểm ngày (từ Chart export hoặc
  nhiều lần gọi API), vẽ chuỗi vị trí theo ngày cho từng từ khoá tụt và phân loại: "tụt liên tục
  nhiều ngày", "tụt 1 lần rồi giữ ổn định ở vị trí mới", hoặc "dao động lên xuống".
- **Nhận sai trang đích**: so sánh URL xếp hạng kỳ này với kỳ trước cho cùng 1 từ khoá — nếu khác
  nhau, đánh dấu "đổi trang đích" (có thể là dấu hiệu cannibalization giữa các URL nội bộ).
- **Phân loại mức độ tụt** theo `nguong-danh-gia.md`: tụt sâu (rời khỏi top 10), tụt nhẹ (tụt
  trong top 3/5/10), out top 100 — với mỗi loại tính % và tỷ lệ còn giữ đúng URL đích.
- **Đánh giá biến động toàn top 10**: với từ khoá tụt, đối chiếu danh sách domain top 10 kỳ trước
  và kỳ này (lấy từ API hoặc dữ liệu SERP nếu có) — nếu nhiều domain trong top 10 đổi chỗ (không
  riêng domain của mình) → nhiều khả năng là update thuật toán diện rộng; nếu chỉ domain của mình
  tụt còn lại giữ nguyên → vấn đề riêng của trang/site.
- **1 web hay nhiều web thay thế**: liệt kê domain đã "chiếm chỗ" các vị trí bị tụt — nếu lặp lại
  cùng 1-2 domain ở nhiều từ khoá, gọi tên cụ thể domain đó là đối thủ đang lên.

## Bước 4 — Định dạng Output (bắt buộc theo đúng khung câu hỏi gốc)

```
## 1. Tình trạng tổng quan
- Tụt top: XX/YY từ khoá (ZZ%)
- Nhóm tụt nhiều nhất: <nhóm> (XX%)
- Phạm vi: toàn bộ nhóm hay chỉ một số URL/từ khoá cụ thể — nêu rõ
- % tụt/nhóm | % tụt/URL (bảng)
- Xu hướng: tụt liên tục / tụt 1 lần rồi dừng / dao động
- Sai trang đích: có/không, số lượng từ khoá bị

## 2. Tình trạng chi tiết
### Theo nhóm từ khoá (bảng: nhóm | số lượng tụt | tụt sâu/nhẹ | % giữ đúng URL)
### Theo mức độ tụt (bảng: mức độ | số lượng | % | % giữ đúng URL)
### Đánh giá top 10 (biến động toàn top hay riêng site mình, 1 hay nhiều đối thủ thay thế + tên domain)

## 3. Đề xuất phân tích bổ sung
(xem "Gợi ý mở rộng phân tích" bên dưới, chỉ nêu các gợi ý áp dụng được với dữ liệu đang có)
```

Mỗi bảng số liệu bắt buộc có 1-2 câu nhận xét ngay bên dưới, không để bảng số trần trụi.

## Gợi ý mở rộng phân tích (đề xuất thêm ngoài yêu cầu gốc)

Chỉ đề xuất — không tự động chạy trừ khi người dùng đồng ý:

- **Đối chiếu lịch Google Core/Spam Update**: nếu ngày bắt đầu tụt trùng khoảng thời gian có
  core update đã công bố, ghi chú khả năng liên quan thay vì kết luận do lỗi on-page/off-page.
- **Rank theo thiết bị**: SERPUPDATE hỗ trợ check riêng desktop/mobile — tách để phát hiện tụt
  chỉ xảy ra trên 1 thiết bị (dấu hiệu vấn đề mobile-friendliness/Core Web Vitals riêng mobile).
- **Cannibalization nội bộ**: phát hiện nhiều URL của cùng site đang cạnh tranh nhau cho cùng
  1 từ khoá (thứ hạng đổi qua lại giữa 2 URL nội bộ theo thời gian).
- **SERP feature**: nếu API trả kèm dữ liệu Featured Snippet/People Also Ask/AI Overview, kiểm
  tra xem việc "tụt" có thực chất do SERP feature chiếm chỗ (organic vẫn giữ vị trí tương đối)
  chứ không phải do site yếu đi.
- **Cảnh báo sớm**: nếu có time-series đủ dài, tính từ khoá nào đang "trượt dần" nhiều ngày liên
  tiếp dù chưa rời top 10 — để xử lý trước khi thực sự rớt hạng.

## Pre-Delivery Review (bắt buộc, tự kiểm tra trước khi trả kết quả)

- [ ] Mọi % đều nêu rõ mẫu số tính trên (tổng từ khoá / tổng nhóm / tổng URL) — không để % mập mờ.
- [ ] Từ khoá "không xếp hạng"/"ngoài top 100" được phân biệt rõ với "rớt hạng nhẹ trong top 100".
- [ ] Từ khoá mới xuất hiện (không có ở kỳ trước) không bị tính nhầm là "tụt".
- [ ] Nhóm từ khoá lấy đúng từ `keyword-groups/phan-nhom-tu-khoa.md`, không tự suy đoán khi không khớp.
- [ ] Domain đối thủ nêu tên cụ thể chỉ khi có dữ liệu SERP thật, không suy diễn.
- [ ] Nếu thiếu dữ liệu kỳ trước hoặc API chưa trả kết quả, output nói rõ "INSUFFICIENT DATA" ở
      phần liên quan thay vì bỏ qua im lặng.

## Error Handling

| Lỗi | Nguyên nhân | Cách xử lý |
|---|---|---|
| `401/403` khi gọi API | Thiếu/sai `X-API-KEY` | Hỏi lại người dùng key, không đoán hoặc dùng key cũ trong lịch sử chat |
| `429 Too Many Requests` | Vượt rate limit | Chờ và thử lại theo cơ chế backoff, báo người dùng nếu lặp lại nhiều lần |
| `task-result` trả `content` rỗng | Task chưa xử lý xong | Poll lại tối đa 6 lần, cách nhau 5-10s, không trả kết quả rỗng là "tụt top" |
| Từ khoá không có trong kết quả kỳ trước | Từ khoá mới thêm vào bộ theo dõi | Xếp vào nhóm "Từ khoá mới", không tính vào % tụt |
| Cột trong file export không khớp (`keyword/url/position/date`) | Người dùng export sai loại báo cáo | Yêu cầu export lại đúng định dạng, liệt kê cột đang thiếu |
| URL lệch `http` vs `https` hoặc có/không `www` | Không chuẩn hoá URL trước khi so sánh | Chuẩn hoá (bỏ scheme + www) trước khi group theo URL |
