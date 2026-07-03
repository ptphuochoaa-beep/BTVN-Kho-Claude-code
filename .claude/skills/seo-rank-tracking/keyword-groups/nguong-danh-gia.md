# Ngưỡng đánh giá mức độ tụt/tăng top

Dùng ở Bước 3 của `seo-rank-tracking` để phân loại mức độ biến động vị trí. Áp dụng trên
`Δ vị trí = vị trí kỳ này − vị trí kỳ trước` (dương = tụt, âm = tăng).

## Phân loại mức độ tụt

| Mức độ | Điều kiện | Ghi chú |
|---|---|---|
| **Tụt nhẹ** | Vẫn còn trong top 10 sau khi tụt, hoặc tụt trong nội bộ top 3/5/10 (đổi hạng nhưng chưa rời nhóm) | Ví dụ: 2 → 6 vẫn tính "tụt nhẹ" vì còn top 10 |
| **Tụt sâu** | Rời khỏi top 10 nhưng vẫn còn trong top 100 | Ví dụ: 8 → 23 |
| **Out top 100** | Không còn xếp hạng trong top 100, hoặc API/SERP không trả về vị trí | Phân biệt với "từ khoá mới" — chỉ tính khi trước đó CÓ xếp hạng |

## Phân loại mức độ tăng (đối xứng, dùng khi báo cáo tăng trưởng)

| Mức độ | Điều kiện |
|---|---|
| **Tăng nhẹ** | Cải thiện vị trí nhưng vẫn ở cùng nhóm top (vd trong top 10) |
| **Tăng mạnh** | Từ ngoài top 10 vào top 10 |
| **Từ khoá mới lên top** | Trước đó ngoài top 100/chưa xếp hạng, nay vào top 100 |

## Ngưỡng cảnh báo bổ sung (tuỳ chỉnh theo dự án)

- **% tụt/nhóm đáng báo động**: > 20% từ khoá trong 1 nhóm cùng tụt trong 1 kỳ → ưu tiên điều tra
  nguyên nhân chung (kỹ thuật, cấu trúc site, update thuật toán) thay vì xử lý từng từ khoá lẻ.
- **Số ngày tụt liên tục coi là "xu hướng"**: từ 3 lần check liên tiếp trở lên cùng chiều tụt.
- **Volume tối thiểu để coi là "volume cao"**: mặc định 1.000 lượt tìm kiếm/tháng — chỉnh lại
  theo ngành/dự án cụ thể nếu cần.

> Các ngưỡng trên là mặc định tham khảo — điều chỉnh trực tiếp trong file này để áp dụng cho
> từng dự án/khách hàng khác nhau mà không cần sửa SKILL.md.
