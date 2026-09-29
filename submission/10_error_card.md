# Error analysis card

## Zone × block

| zone | block | what | count |
|---|---|---|---:|
| center | B1 | MISSING | 2 |
| center | B1 | SPURIOUS | 6 |
| center | C0 | BOX_GEOMETRY | 1 |
| center | C0 | SPURIOUS | 1 |
| edge | B1 | MISSING | 1 |
| edge | B1 | SPURIOUS | 1 |
| mid | B1 | MISSING | 6 |
| mid | B1 | SPURIOUS | 7 |
| mid | C0 | SPURIOUS | 1 |

## Top defects
- SPURIOUS: 16 (ví dụ frame adasind_019560.jpg)
- MISSING: 9 (ví dụ frame adasind_001320.jpg)
- BOX_GEOMETRY: 1 (ví dụ frame adasind_019560.jpg)

## Phân tích của bạn

Phân tích tập trung vào lỗi cấu trúc tiêu biểu nhất ở phía người gán nhãn và lỗi phổ biến nhất ở phía model trên slice B1-edge:

- **Nguyên nhân khả dĩ (`why`) và lý do nhận định**:
  - Đối với nhãn thủ công (L): Lỗi tiêu biểu là tách sai đối tượng người lái xe hai bánh tại frame `adasind_001320.jpg` (`object_ref: L7/L8`). Phân loại nguyên nhân: `E1_annotator_error`. Người gán nhãn hiểu nhầm quy tắc tách người đi bộ, dẫn đến vẽ một box `Pedestrian` cho người lái và một box `Bike` cho chiếc xe.
  - Đối với model tự động (M): Lỗi `SPURIOUS` (16 ca) và `MISSING` (9 ca) tăng vọt tại vùng `mid` và `edge`. Phân loại nguyên nhân: `E4_model_domain`. Trọng số model YOLO26m pre-trained trên ảnh perspective chuẩn không thích ứng tốt với độ cong phi tuyến tính của thấu kính fisheye, dẫn đến trích xuất sai vùng đặc trưng và nhầm lẫn cấu trúc vỉa hè/bóng râm thành phương tiện.
- **Cách sửa và phân công xử lý (`owner`)**:
  - Ca lỗi Rider: Giao cho `owner: annotator` (Nguyễn Minh Tú - vai A) thực hiện rework trong CVAT ở pha P5. Hành động: gộp 2 box người và xe thành 1 box duy nhất mang nhãn `Bike` bao trùm toàn bộ theo đúng quy tắc R05; xóa box `Pedestrian` thừa. Reviewer (Nguyễn Văn Tuyển - vai B) kiểm tra xác nhận và Vương Ngọc Tiến (vai C) chốt nghiệm thu trong delta.md.
  - Ca lỗi model: Giao cho `owner: ai_team` kiến nghị bổ sung dữ liệu huấn luyện fisheye chuyên dụng (hoặc áp dụng phép tiền xử lý hiệu chỉnh méo hình - undistortion) vào pipeline mô hình hóa.
- **Bằng chứng đối chiếu**:
  - Ảnh chụp màn hình: File `submission/screenshots/evidence_r2_qa_rider.jpg`.
  - Dòng dữ liệu tương ứng: Các dòng `r1_craft` (L7), `r2_qa` (L8), `r3_diag` (L7) trong file `submission/findings.csv`.
  - Quy tắc kỹ thuật: Quy tắc R05 ("Rider và Bike") được quy định rõ trong tài liệu `docs/02-rules-vi.md`.
