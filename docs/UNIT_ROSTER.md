# Thiết kế các loại quân người que

## Hướng nghệ thuật

Giữ hình dáng người que đơn giản, dễ đọc khi nhìn nhỏ. Phân biệt quân bằng vũ khí, mũ, khiên và tư thế. Hai phe dùng cùng bộ dáng nhưng khác màu thân và huy hiệu.

Mỗi quân cần ba hoạt ảnh đầu tiên: đứng chờ, di chuyển, tấn công. Sau prototype mới thêm trúng đòn, ngã gục và kỹ năng.

## Roster prototype

| Quân | Vai trò | Điểm mạnh | Điểm yếu | Dấu hiệu nhận dạng |
|---|---|---|---|---|
| Lính kiếm | Cận chiến cơ bản | Rẻ, linh hoạt | Tầm đánh ngắn | Kiếm ngắn, khiên nhỏ |
| Vệ binh | Giữ tuyến | Chịu đòn tốt, bảo vệ quân xa | Chậm, sát thương thấp | Khiên lớn, mũ kín |
| Cung thủ | Đánh tầm xa | Gây sát thương từ sau tuyến | Mong manh nếu bị áp sát | Cung dài, dáng kéo dây |
| Kỵ binh giáo | Xung kích | Mạnh khi lao vào mục tiêu | Dễ bị chặn hoặc kéo vào giao tranh dài | Giáo dài, tư thế lao tới |
| Đột kích | Vòng sau, nhắm quân xa | Di chuyển nhanh, áp sát mục tiêu yếu | Máu thấp, yếu khi đối mặt vệ binh | Song đao, thân hạ thấp |

## Vòng chơi cần thử

1. Người chơi nhận tài nguyên theo thời gian và từ giao tranh.
2. Chọn quân để gọi dựa trên giá và đội hình hiện tại.
3. Đơn vị tự tiến ra mặt trận; người chơi có một số lệnh chiến thuật giới hạn.
4. Phá hủy căn cứ đối thủ để thắng.

## Cân bằng

- Vệ binh giúp cung thủ sống lâu hơn, nhưng không nên tự mình thắng lính kiếm.
- Lính kiếm cần áp sát và đánh vệ binh; cung thủ cần thời gian và khoảng cách.
- Kỵ binh giáo cần có cơ chế phong tỏa hoặc xung kích để tạo lựa chọn đối phó.
- Đột kích cần có đường vòng rõ ràng và dễ bị bắt nếu lao vào sai thời điểm.

Đây là roster ý tưởng, chưa phải thông số cân bằng.
