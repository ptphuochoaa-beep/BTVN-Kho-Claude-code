# Công cụ phân tích hiệu suất website từ Google Search Console

> **Trạng thái:** Đang phát triển — file `gsc-analysis-tool.html` đã có bản đầu, chưa hoàn thiện.  
> **Repo GitHub:** https://github.com/ptphuochoaa-beep/BTVN-Kho-Claude-code

---

## 1. Mục tiêu & lý do xây dựng

- Website SEONGON chuyên cung cấp kiến thức SEO; muốn thêm **công cụ free** để tăng thời gian trên trang, tăng traffic và tạo giá trị thực cho người dùng.
- Tham khảo hướng đi: https://aiktp.com/vi/cong-cu-nghien-cuu-tu-khoa/
- Đối tượng: người mới học SEO đến người làm SEO lâu năm nhưng gặp khó khăn khi đọc & trực quan hoá dữ liệu GSC (thay thế Looker Studio cho người không thạo công nghệ).

---

## 2. Yêu cầu UX / UI

| Thành phần | Chi tiết |
|---|---|
| **Logo** | `logo-seongon-ai-first.png` (đã nhúng base64 vào HTML) |
| **Màu chủ đạo** | `#004aef` (xanh dương) |
| **Màu phụ** | `#07ef9c` (xanh lá mint) |
| **Font chữ** | Gilroy |
| **Phong cách** | Hiện đại, tối giản, high-tech |
| **Tham khảo giao diện** | Thư mục `Mẫu tham khảo giao diện/` trong project |

---

## 3. Cấu trúc landing page

### Section 1 — Hero Banner
- Ảnh nền + nội dung chữ
- **Tên công cụ:** Công cụ phân tích hiệu suất website
- **Mô tả:** Đoạn ngắn 80–100 từ giới thiệu tool (là gì, để làm gì)

### Section 2 — Hướng dẫn sử dụng (4 bước)
| Bước | Hành động |
|---|---|
| 1 | Tải báo cáo cần phân tích từ Google Search Console (hiệu suất, backlink, index…) |
| 2 | Upload file Excel hoặc CSV lên công cụ |
| 3 | Công cụ xử lý và trả kết quả theo framework phân tích của chuyên gia SEONGON |
| 4 | Người dùng đọc báo cáo, copy hoặc in |

---

## 4. Yêu cầu chức năng công cụ

### 4.1 Đầu vào
- Upload **1 kỳ** hoặc **so sánh 2 kỳ** (kéo thả hoặc click)
- Định dạng: CSV hoặc Excel
- Tự nhận dạng loại báo cáo GSC: Từ khoá / Trang / Ngày / Quốc gia / Thiết bị
- Hỗ trợ **tất cả loại báo cáo GSC**

### 4.2 Đầu ra — Dashboard
- KPI cards: 4 chỉ số chính + mũi tên thay đổi % so kỳ trước
- Biểu đồ xu hướng (line chart nếu có dữ liệu ngày, bar chart top 10 nếu không)
- Biểu đồ phân bổ vị trí (doughnut chart)
- Bảng dữ liệu: sort, filter, phân trang, cột Δ khi so sánh 2 kỳ
- Tab **"Biến động"**: top 30 tụt top + top 20 cải thiện

### 4.3 Tích hợp
- Nhúng vào **WordPress** qua `<iframe>`
- Toàn bộ xử lý **client-side** (không cần backend)

---

## 5. Framework phân tích SEONGON (logic báo cáo auto-generate)

### Trường hợp 1 — Website tụt top

#### 1.1 Tình trạng tổng quan
- Tụt top bao nhiêu từ khóa?
- Tụt ở nhóm nào?
- Tất cả từ khóa của nhóm đó có tụt không hay chỉ 1 số URL?
- % tụt/nhóm và % tụt/URL là bao nhiêu?
- Tụt theo ngày hay tụt 1 lần rồi dừng hẳn, có tiếp tục tụt không?
- Có nhận sai trang đích so với trước đó không?

#### 1.2 Tình trạng chi tiết

**Phân loại nhóm tụt:**
- Từ khóa hotkey: tụt bao nhiêu, tụt sâu hay nhẹ, nhận đúng trang đích không?
- Từ khóa volume cao: tụt bao nhiêu, tụt sâu hay nhẹ, nhận đúng trang đích không?
- Từ khóa info: tụt bao nhiêu, tụt sâu hay nhẹ, nhận đúng trang đích không?
- Từ khóa brand: tụt bao nhiêu, tụt sâu hay nhẹ, nhận đúng trang đích không?

**Mức độ tụt:**
- Tụt sâu (out khỏi top 10): bao nhiêu %, còn nhận đúng URL không?
- Tụt nhẹ (trong top 3/5/10): bao nhiêu %, còn nhận đúng URL không?
- Out khỏi top 100: bao nhiêu %, còn nhận đúng URL không?

**Đánh giá top:**
- Top 10 có bị biến động hết không, hay chỉ web của mình?
- Các từ khóa tụt bị thay thế bởi 1 web hay nhiều web?

#### 1.3 Những tác động
- Check xem thời gian từ khi bắt đầu tụt về trước đó 1 tuần đã có tác động gì.
- Nếu URL tụt thuộc nhóm có tác động → liệt kê tác động: content, offpage, onpage, technical, inlink… Đánh giá từng tác động tốt hay xấu.
- Nếu URL tụt không thuộc nhóm có tác động → check link, technical, đối thủ xem có gì mới và đánh giá tác động.

---

### Trường hợp 2 — Website tụt traffic

#### 2.1 Tình trạng tổng quan
- Traffic hiện tại: đánh giá lưu lượng truy cập (theo tháng/tuần/ngày)
- Số lượng traffic hiện tại: so sánh với mục tiêu
- Mức thiếu hụt traffic: quy ra tổng tháng rồi tính trung bình lượng truy cập cần đạt mỗi ngày
- Traffic ngày gần nhất
- Xu hướng traffic: phân tích mức tăng/giảm gần đây

#### 2.2 Tình trạng chi tiết

**Nguồn gốc tăng traffic:**
- Bài đăng mới: các bài nào mang lại tăng trưởng?
- Bài cũ tăng traffic: xác định bài, thời gian và mức tăng cụ thể
- Rút kinh nghiệm gì từ các bài tăng traffic?

> *(Framework còn tiếp — cần bổ sung thêm các trường hợp và checklist tác động)*

---

## 6. Báo cáo auto-generate (5 blocks)

| Block | Nội dung |
|---|---|
| 1 | Tổng quan — 5 chỉ số + nhận xét auto |
| 2 | Phân tích tụt top — 8 số liệu + bảng top 10 tụt nặng + nhận xét |
| 3 | Phân tích traffic — bảng tăng/giảm mạnh nhất |
| 4 | Chi tiết phân loại mức độ tụt |
| 5 | Checklist kiểm tra tác động — 8 mục theo framework SEONGON |

**Nút hành động:** Sao chép báo cáo | In báo cáo

---

## 7. Hướng dẫn nhúng vào WordPress

```html
<iframe 
  src="/path/to/gsc-analysis-tool.html" 
  width="100%" 
  height="900px" 
  frameborder="0">
</iframe>
```

Dùng block **Custom HTML** trong Gutenberg editor.

---

## 8. Việc cần làm tiếp theo

- [ ] Hoàn thiện framework phân tích (bổ sung các trường hợp còn thiếu)
- [ ] Kiểm tra & test tool với dữ liệu GSC thực tế
- [ ] Điều chỉnh UI cho sát với style SEONGON
- [ ] Push file hoàn chỉnh lên GitHub (cần Personal Access Token)
- [ ] Nhúng vào WordPress và test trên web thật
- [ ] Viết nội dung mô tả 80–100 từ cho hero banner
