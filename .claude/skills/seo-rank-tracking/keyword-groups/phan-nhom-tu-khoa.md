# Quy tắc phân nhóm từ khoá

Dùng để gắn nhóm cho từng từ khoá trước khi tính % tụt/tăng theo nhóm ở Bước 2-3 của
`seo-rank-tracking`. Có 2 cách map, ưu tiên theo thứ tự:

1. **Danh sách tường minh** ở bảng bên dưới — nếu từ khoá có mặt trong danh sách, dùng nhóm đã gán.
2. **Quy tắc theo mẫu** (pattern) — nếu không có trong danh sách, thử khớp theo các dấu hiệu ở
   mục "Quy tắc nhận diện nhanh".

Nếu không khớp cả hai, xếp vào **"Chưa phân nhóm"** và liệt kê riêng cho người dùng tự bổ sung
vào danh sách này — không tự suy đoán để tránh gán sai nhóm.

## Định nghĩa 4 nhóm

| Nhóm | Định nghĩa | Đặc điểm nhận diện |
|---|---|---|
| **Brand** | Từ khoá chứa tên thương hiệu, tên miền, tên viết tắt công ty | Chứa "seongon", tên viết tắt, tên miền |
| **Hotkey** | Từ khoá chuyển đổi cao, gắn trực tiếp với dịch vụ/sản phẩm cốt lõi, KPI kinh doanh | Có cụm "dịch vụ", "báo giá", "thuê", "công ty", "agency" |
| **Volume cao** | Từ khoá search volume lớn nhưng không hẳn là dịch vụ cốt lõi (đầu phễu) | Volume tra được > ngưỡng do người dùng đặt (vd 1,000/tháng) |
| **Info** | Từ khoá dạng câu hỏi/kiến thức, mang tính học thuật, không có ý định mua ngay | Có "là gì", "như thế nào", "cách", "hướng dẫn" |

## Danh sách phân nhóm tường minh (tự cập nhật)

> Bổ sung dần vào bảng này khi phát hiện từ khoá bị gán sai bởi quy tắc mẫu ở dưới.

| Từ khoá | Nhóm |
|---|---|
| seongon | brand |
| seo dao seongon | brand |
| dịch vụ seo | hotkey |
| seo tổng thể | hotkey |
| seo là gì | info |
| cách viết bài chuẩn seo | info |

## Quy tắc nhận diện nhanh (khi từ khoá không có trong danh sách trên)

- Chứa tên thương hiệu/tên miền công ty → **brand**
- Bắt đầu/chứa: "là gì", "như thế nào", "cách", "hướng dẫn", "tại sao", "nên hay không" → **info**
- Chứa: "dịch vụ", "báo giá", "thuê", "công ty", "agency", "tuyển dụng" + tên ngành dịch vụ chính → **hotkey**
- Còn lại, nếu có dữ liệu volume tra cứu kèm theo và vượt ngưỡng người dùng đặt → **volume cao**
- Không khớp gì → **Chưa phân nhóm**
