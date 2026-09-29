# Zone table (slice của bạn)

Lệnh `python3 lab11.py model` tự ghi bảng số (cùng cách đếm với `r1_craft/compare.md` và `model_compare.md`); chạy lại lệnh sẽ cập nhật bảng và giữ nguyên phần nhận xét. Bạn chỉ viết mục Nhận xét.

| Zone | n_ref | L missing | L spurious | M missing (`LR_noM` + `R_only`) | M thừa (`LM_noR` + `M_only`) | Lỗi L chính (`what`) |
|---|---:|---:|---:|---:|---:|---|
| center | 7 | 0 | 1 | 2 | 4 | SPURIOUS (1) |
| mid | 9 | 0 | 0 | 6 | 7 | — |
| edge | 4 | 0 | 0 | 1 | 1 | — |

## Nhận xét

- Zone người (L) và model (M) gặp nhiều vấn đề nhất:
  - Phía người gán nhãn (L): Đạt độ chính xác rất cao trên toàn bộ 3 zone; chỉ xuất hiện 1 lỗi spurious ở vùng `center` do tách nhầm người lái xe hai bánh thành một box `Pedestrian` độc lập (vi phạm quy tắc R05 về Rider). Vùng `mid` và `edge` không có missing hay spurious.
  - Phía model (M): Vùng `mid` là nơi model bị gãy nặng nề nhất với 6 ca missing (bỏ sót 66.7% đối tượng tham chiếu) và 7 ca bắt thừa (spurious). Vùng `center` model cũng có 2 missing và 4 spurious. Vùng `edge` có 1 missing và 1 spurious trên tổng số 4 vật thể.
- Giả thuyết nguyên nhân và giới hạn của slice ba frame:
  - Nguyên nhân chính: Hiện tượng biến dạng góc rộng đặc trưng của ống kính fisheye (radial optical distortion) tăng dần từ tâm ra rìa khiến hình học đối tượng bị bẻ cong phi tuyến tính. Model YOLO26m vốn được pre-train chủ yếu trên ảnh phối cảnh thông thường (perspective camera) nên gặp khó khăn lớn trong việc trích xuất đặc trưng tại vùng `mid` và `edge`, dẫn đến tỷ lệ miss và false alarm cao. Ngoài ra, việc che khuất một phần giữa các phương tiện giao thông hỗn hợp tại vùng trung tâm cũng gây nhiễu cho model.
  - Giới hạn: Tập dữ liệu thực hành của slice chỉ gồm 3 frame trên một camera trước duy nhất của bộ ADASIND, do đó số liệu chỉ phản ánh hành vi cục bộ trong điều kiện ban ngày trên một góc nhìn, chưa đủ để khái quát hóa cho toàn bộ hệ thống quan sát quanh xe 4 camera (SVM) trong các điều kiện ánh sáng, thời tiết và vận tốc khác nhau.
