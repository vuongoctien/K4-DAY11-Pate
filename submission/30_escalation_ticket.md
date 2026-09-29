# Escalation ticket

## Ticket 1

- **Frame:** `adasind_014670.jpg` (đối tượng Car L1 tại tọa độ xbr=1080)
- **Ảnh chụp:** `submission/screenshots/evidence_edge_truncation.jpg`
- **Expected impact:** Tác động trực tiếp đến độ tin cậy của ground truth và đánh giá hiệu năng mô hình (mAP). Tại vùng rìa quang học fisheye, hiện tượng biến dạng cong và mờ biên khiến annotator khó xác định ranh giới thật của phương tiện. Nếu không thống nhất ngưỡng cắt biên, mô hình học máy sẽ bị phạt sai (false penalty) hoặc học sai hình học đối tượng cắt cụt, ảnh hưởng nghiêm trọng đến an toàn phát hiện vật cản sát thân xe trong hệ thống SVM.
- **Owner:** `ai_team` (phối hợp cùng `guideline` và `data_ops`)
- **Recommendation:**
  1. Thống nhất áp dụng ngưỡng tối thiểu 15% diện tích nhìn thấy cho nhãn có thuộc tính `truncated` ở vùng `edge_zone` theo Guideline Patch v1.1.0.
  2. Với các phần đối tượng bị cắt quá mờ nằm sát vành đen, chuẩn hóa việc sử dụng polygon `ignore_region` với `reason=unreadable` thay vì cố gắng nội suy box.
  3. Đội ngũ AI Team cần tích hợp camera calibration matrix để hiệu chỉnh méo hình học hoặc áp dụng fisheye augmentation trong quá trình huấn luyện mô hình YOLO tiếp theo.
