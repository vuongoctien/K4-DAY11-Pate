# Báo cáo QA độc lập (Blind QA Review)

- Người thực hiện review (vai B): Nguyễn Văn Tuyển (2A202602205)
- Chủ nhãn (vai A): Nguyễn Minh Tú (2A202602217)
- Slice kiểm tra: B1-edge
- Mã khóa bản export đã kiểm: 733D-E0AF
- Trạng thái: QA mù hoàn tất trên ảnh và guideline, chưa mở teaching reference hoặc model.

## Các nhận xét chi tiết

### 1. Frame adasind_001320.jpg — Đối tượng L2, L8 (Rider & Bike)
- **object_ref**: L2, L8
- **rule_id**: R05
- **Điều nhìn thấy**: Đối tượng người điều khiển xe hai bánh đang ngồi trên xe máy/xe đạp nhưng nhãn đang được tách thành 2 box riêng: L2 gán nhãn `Pedestrian` (người lái) và L8 gán nhãn `Bike` (xe).
- **Điều cần kiểm lại**: Theo quy tắc R05, người ngồi lái và xe hai bánh phải được gộp chung thành một đối tượng `Bike` duy nhất. Chỉ tách `Pedestrian` khi người đó dắt bộ xe hoặc đứng cạnh xe. Cần gộp 2 box này thành 1 box `Bike` bao trùm cả xe và người.

### 2. Frame adasind_014670.jpg — Đối tượng L1 (Car)
- **object_ref**: L1
- **rule_id**: R06, R09
- **Điều nhìn thấy**: Xe ô tô ở mép phải (xbr=1080) bị vành kính cắt một phần thân xe. Thuộc tính `truncated` đang để `false`.
- **Điều cần kiểm lại**: Đối tượng bị cắt bởi rìa ảnh / vòng kính quang học nên cần được đánh dấu thuộc tính `truncated=true` và `edge_zone=true`.

### 3. Frame adasind_034080.jpg — Đối tượng L1 (Car)
- **object_ref**: L1
- **rule_id**: R06
- **Điều nhìn thấy**: Xe ô tô ở mép trái (xtl=1.00) bị rìa ảnh cắt ngang. Box đã bám sát phần nhìn thấy nhưng cần kiểm tra lại độ khít của bounding box và thuộc tính cắt biên.
- **Điều cần kiểm lại**: Xác nhận `truncated=true` và kiểm tra lại đỉnh trên ytl để không lấy dư phần bóng đổ xuống mặt đường.

### 4. Frame adasind_001320.jpg — Đối tượng L7 (Pedestrian nhỏ ở xa)
- **object_ref**: L7
- **rule_id**: R01
- **Điều nhìn thấy**: Người đi bộ ở xa có chiều cao pixel đo được khoảng 33px.
- **Điều cần kiểm lại**: Quy tắc R01 quy định ngưỡng chiều cao tối thiểu H=40px trên ảnh gốc. Đối tượng này có chiều cao dưới ngưỡng, cần xem xét loại bỏ để tránh tạo FP (false positive).
