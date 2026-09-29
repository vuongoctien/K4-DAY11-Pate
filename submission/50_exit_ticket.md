# Exit ticket

Đọc `docs/10-svm360-reading-vi.md` trước khi trả lời câu 1–2. Các câu về zone, `why`, rework, parking và sampling
đã nằm trong file tương ứng nên không hỏi lại ở đây.

1. **Một vật ở vùng seam giữa hai camera thật xuất hiện với hai box khác nhau: đó là lỗi `DUPLICATE` hay cần một quy tắc riêng? Vì sao?**
   - Đây **không phải là lỗi `DUPLICATE`**, mà bắt buộc **cần một quy tắc riêng (Cross-camera Seam Policy)**.
   - *Giải thích:* Mỗi camera trong hệ thống 4 camera SVM có vị trí gắn, độ cao và góc nhìn quang học độc lập. Khi một phương tiện nằm trong vùng chồng lấp thị trường (seam / overlap FOV) giữa hai camera (ví dụ front và left), vật thể được chiếu đồng thời lên hai mặt phẳng cảm biến với hai dạng biến dạng méo thấu kính hoàn toàn khác nhau. Ở mức độ 2D của từng luồng camera, cả hai box đều là hiện tượng quang học thực tế và cần thiết để huấn luyện mô hình object detector trên từng thấu kính độc lập. Chỉ khi bài toán chuyển lên không gian hợp nhất 3D BEV (Bird's Eye View) thì hệ thống mới áp dụng thuật toán dung hợp (fusion) để gộp thành một bounding box 3D duy nhất.

2. **Một vật đi qua nhiều frame trên cùng camera: khi nào giữ cùng track ID, khi nào thêm keyframe hoặc trạng thái Outside? Nêu bằng chứng sẽ cần trước khi nối track qua hai camera.**
   - *Giữ cùng Track ID:* Khi đối tượng di chuyển liên tục qua các frame hoặc chỉ bị che khuất tạm thời trong thời gian rất ngắn (<1-2 giây) mà quỹ đạo chuyển động có thể nội suy suy diễn mượt mà (smooth trajectory), không bị nhầm lẫn nhận diện (identity switch) với phương tiện khác.
   - *Thêm keyframe:* Khi đối tượng có sự biến đổi đáng kể về hình học (đổi hướng rẽ đột ngột, tăng/giảm tỷ lệ kích thước nhanh khi tiến lại gần) hoặc thay đổi trạng thái thuộc tính (bắt đầu bị che khuất `occluded` hoặc chạm mép vành kính `truncated`).
   - *Trạng thái Outside:* Khi vật thể di chuyển hoàn toàn ra ngoài khung hình hoặc đi vào vùng tối không đọc được của vành kính (`lens_border`), đánh dấu thuộc tính `outside=true` tại frame đó để báo hiệu ngắt hiển thị nhưng vẫn bảo lưu track ID phòng trường hợp vật thể quay trở lại.
   - *Bằng chứng cần thiết trước khi nối track qua hai camera:* (1) Đồng bộ thời gian mức phần cứng (hardware timestamp); (2) Ma trận hiệu chuẩn ngoại suy (extrinsic calibration) xác định vị trí 3D trong hệ quy chiếu xe; (3) Tính nhất quán về hướng vector vận tốc chuyển động và đặc trưng nhận diện ngoại quan (Re-ID appearance features) khi rời biên camera này để đi vào biên camera kia.

3. **Nhìn lại cả buổi: một chỗ bạn tin nhãn mình đúng nhưng reference hoặc người soát nghĩ khác (dẫn frame/`object_ref`), bạn đã xử lý thế nào, và nếu làm lại slice này bạn sẽ đổi gì trong cách làm?**
   - *Tình huống bất đồng thực tế:* Tại frame `adasind_001320.jpg` đối tượng `L7/L8`, người gán nhãn A ban đầu tin rằng việc tách người lái xe thành một box `Pedestrian` độc lập và chiếc xe máy thành box `Bike` sẽ giúp phân loại chi tiết hơn. Tuy nhiên, người soát B khi làm QA mù đã gắn cờ cảnh báo vi phạm quy tắc R05.
   - *Cách xử lý:* Người điều phối C đã triệu tập nhóm, mở ảnh gốc và đối chiếu trực tiếp với tài liệu quy chuẩn `docs/02-rules-vi.md`. Nhóm nhận thấy quy tắc R05 quy định rất rõ: "người ngồi lái + xe hai bánh là một Bike thống nhất" nhằm tránh cho hệ thống tự hành hiểu nhầm có người đi bộ độc lập băng qua đường. Nhóm đã thống nhất gộp lại thành 1 box `Bike` ở đợt rework P5 và ghi nhận đầy đủ vào `40_decision_log.csv`.
   - *Bài học kinh nghiệm nếu làm lại:* Nhóm sẽ dành trọn vẹn 10 phút đầu của pha P1 để cả ba thành viên cùng đọc kỹ 11 quy tắc (R01-R11) và thảo luận các ca dễ gây hiểu nhầm (Rider, cắt biên quang học, vùng ignore) trước khi bắt tay vào gán nhãn, giúp hạn chế tối đa việc phải chỉnh sửa lại ở P5.
