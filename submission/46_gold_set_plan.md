# Đề xuất gold set theo camera — tình huống giả lập

**Đầu bài:** 50.000 frame từ bốn camera SVM, ngân sách chọn 200 frame để review/gold. Đây là tình huống trên slide,
**không phải** 50.000 frame có trong repo. Phân bổ đúng 200 ở `45_sampling_plan.csv` cho bốn camera, mỗi camera có
normal và hard slice. “Gold set” ở đây là **kế hoạch tạo** reference sau kiểm chứng, không phải teaching reference
ADASIND hoặc nhãn bạn vừa vẽ. Nếu cần, dùng `notebooks/day11-svm360-colab.ipynb` để thử tổng phân bổ; notebook
không làm thay phần lý do.

| camera_id | Hard case cần chọn | Vì sao dễ sai | Annotation space / calibration cần giữ | Cách review trước khi gọi là gold |
|---|---|---|---|---|
| front | Ngược sáng bình minh/hoàng hôn; đèn pha xe đối diện ban đêm; xe chuyển làn đột ngột ở cự ly gần. | Lóa quang học (glare) làm mất chi tiết viền xe; méo thấu kính đáy ảnh gây sai lệch điểm tiếp đất của bánh xe. | Ảnh fisheye gốc kèm ma trận thông số nội suy (intrinsic) và góc đặt camera so với trọng tâm xe (extrinsic rig). | Hai reviewer A và B độc lập gán nhãn; các ca có IoU < 0.7 hoặc lệch class bắt buộc chuyển C phân xử dựa trên rule. |
| rear | Lùi chuồng ban đêm có đèn rọi ngược; chướng ngại vật thấp (trẻ em, trụ bê tông, gờ giảm tốc) sát cản sau. | Góc nhìn chúc từ trên xuống kết hợp méo fisheye khiến vật thể thấp bị bẹp dạng dải cong, dễ nhầm với mặt đường. | Hệ tọa độ mặt phẳng bãi đỗ kết hợp ma trận calibration camera lùi để tính khoảng cách thực tế mét (metric space). | Soát 100% các box ở cự ly dưới 2m sát cản sau; bắt buộc kiểm tra thuộc tính `truncated` nếu vật thể chạm mép ảnh dưới. |
| left | Xe hai bánh vượt sát sườn ở tốc độ cao; bóng râm tán cây che khuất một phần thân xe. | Vùng sườn xe chịu độ méo quang học cực đại tại hai rìa kính; chuyển động tương đối nhanh gây nhòe mờ (motion blur). | Lưu trữ timestamp đồng bộ mili-giây chuẩn phần cứng với camera trước và sau để kiểm tra liên kết vật thể qua seam. | Kiểm tra kỹ thuộc tính `edge_zone` và `truncated`; đối chiếu chuỗi 3 frame liên tiếp để loại trừ hiện tượng bắt nhầm bóng râm. |
| right | Người đi bộ bước xuống từ vỉa hè; xe đạp đi cùng chiều trong điểm mù cột B/gương phụ; vạch đỗ xe phức tạp. | Bị che khuất từng phần bởi cột tiêu/cây cối; người đi bộ bị biến dạng uốn cong hình cánh cung ở rìa ngoài cùng. | Không gian ảnh fisheye 2D kết hợp phép chiếu bird's-eye-view (BEV) và bản đồ tĩnh của bãi đỗ. | Bắt buộc đối chiếu quy tắc R05 để phân biệt người ngồi trên xe đạp với người dắt xe; blind review 2 vòng độc lập. |

- **Khi nào cần refresh gold set (đổi camera, calibration hoặc rule):**
  1. Khi có sự thay đổi về phần cứng cảm biến (thay loại thấu kính fisheye, thay đổi độ phân giải hoặc góc mở FOV).
  2. Khi xe được bảo dưỡng/căn chỉnh lại vị trí gắn camera (re-calibration góc đặt camera ngoại suy).
  3. Khi có bản cập nhật Guideline mới (như việc nâng cấp lên v1.1.0 quy định ngưỡng cắt biên và vùng ignore).
  4. Định kỳ kiểm tra độ trôi dữ liệu (data drift) theo mùa vụ (mùa mưa, ban đêm mùa đông) để bổ sung thêm các mẫu đặc thù.
- **Một ca seam/cross-camera cần policy và evidence trước khi ghép hai box:**
  - *Tình huống:* Một phương tiện di chuyển từ vùng quan sát của camera trước (front) sang camera sườn trái (left) và xuất hiện đồng thời trên vùng chồng hình (seam) của cả 2 camera.
  - *Yêu cầu bằng chứng và chính sách:* Tuyệt đối không tự tiện ghép 2 box riêng lẻ thành 1 track ID duy nhất nếu chưa đáp ứng đủ ba điều kiện: (1) Bằng chứng đồng bộ thời gian (hardware-synchronized timestamp) chính xác giữa hai camera; (2) Ma trận calibration ngoại suy đồng nhất để tính toán vị trí 3D trong không gian thực của xe; (3) Chính sách đầu ra (output policy) rõ ràng xác định hệ thống cần 2 bounding box độc lập trên từng mặt phẳng ảnh 2D hay một khối hộp 3D Bounding Box thống nhất trên không gian bao quanh xe (Surround View).
- **Vì sao peer agreement hoặc quality report trên ảnh một camera chưa chứng minh gold set đúng cho cả bốn camera:**
  - Bốn camera SVM quanh xe có độ cao lắp đặt, góc chúi, điều kiện phản xạ ánh sáng và mức độ biến dạng thấu kính hoàn toàn khác nhau (camera trước hướng thẳng mặt đường, camera sau chúc thấp, camera hai bên sườn nhìn quét ngang). Một bộ nhãn đạt độ đồng thuận cao (peer agreement) hay chỉ số IoU tốt trên camera trước không thể đảm bảo rằng các quy tắc đó áp dụng trên camera sườn hay camera lùi sẽ không bị gãy (do góc nhìn và mức độ méo rìa khác biệt). Do đó, chất lượng gold set phải được kiểm chứng độc lập trên từng camera và trên toàn bộ hệ thống phối hợp.
