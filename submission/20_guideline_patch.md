# Guideline patch

- **Rule mới đề xuất:** Bổ sung quy tắc R06b định lượng cho việc xử lý đối tượng bị cắt biên quang học bởi vành kính fisheye tại vùng rìa (`edge zone`): "Mọi đối tượng thuộc 6 class chính bị cắt bởi biên ảnh hoặc polygon `lens_border` phải được đánh dấu thuộc tính `truncated=true` và `edge_zone=true`. Nếu diện tích phần nhìn thấy của đối tượng dưới 15% so với hình thể ước lượng đầy đủ hoặc không đủ đặc trưng nhận diện chắc chắn class, không được vẽ bounding box giả định mà phải khoanh vùng loại trừ bằng polygon `ignore_region` với thuộc tính `reason=unreadable`."
- **Áp dụng cho:** Tất cả 6 class (`Car`, `Bus`, `Truck`, `ThreeWheeler`, `Bike`, `Pedestrian`), các thuộc tính `truncated`, `edge_zone`, và polygon `ignore_region` (`reason: lens_border/unreadable`).
- **Vì sao luật hiện tại (`docs/02-rules-vi.md`) không đủ:** Quy tắc R06 và R09 hiện tại mới chỉ định tính việc gán `truncated` khi đối tượng bị cắt mép, chưa có ngưỡng định lượng tối thiểu (minimum visible threshold) ở ranh giới mờ của thấu kính fisheye, dẫn đến bất đồng ý kiến giữa các annotator và reviewer khi một góc nhỏ của phương tiện xuất hiện sát vành đen quang học.
- **`rules_version` mới:** `v1.1.0` (nâng cấp từ `v1.0.0`).
- **Hiệu lực từ:** Áp dụng ngay từ pha kiểm thử và sửa lỗi `rework` (P5) trên hồ sơ lab và làm chuẩn mực bắt buộc cho đợt lấy mẫu tạo gold set 4 camera ở P6.
