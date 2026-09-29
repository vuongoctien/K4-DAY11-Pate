# Kế hoạch review từ lỗi quan sát được

Từ `findings.csv` và `zone_table.md`, chọn **hai lát cắt của bài ADASIND một camera** cần review trước. Bảng này
giải thích dữ liệu thật bạn vừa làm; nó không thay cho kế hoạch bốn camera giả lập ở `45_sampling_plan.csv`.

| Lát cắt / frame | Số ca và loại lỗi | Vì sao review trước | Bằng chứng cần giữ |
|---|---|---|---|
| Lát cắt vùng rìa (`edge zone` / frame `adasind_014670.jpg`, `adasind_034080.jpg`) | 3 ca: lỗi thuộc tính `truncated` (L1), phân loại đối tượng mép kính (L3), và ranh giới quang học thấu kính. | Vùng rìa chịu biến dạng quang học fisheye lớn nhất, đối tượng nằm sát thân xe và điểm mù SVM. Đây là khu vực tiềm ẩn rủi ro va chạm cao nhất nếu thuật toán nhận diện sai. | Ảnh phóng to vùng rìa trong `screenshots/evidence_edge_truncation.jpg`, polygon `lens_border`, thuộc tính `truncated=true`. |
| Lát cắt phương tiện hai bánh và người lái (`center/mid zone` / frame `adasind_001320.jpg`) | 4 ca: tách nhầm Rider thành Pedestrian + Bike (L7/L8), che khuất xe ba bánh và người đi bộ. | Nhầm lẫn quy tắc R05 ảnh hưởng nghiêm trọng đến lớp nhãn `Bike` - class phương tiện chiếm tỷ trọng lớn trong giao thông đô thị; cần chuẩn hóa quy ước trước khi mở rộng. | Ảnh chụp màn hình `screenshots/evidence_r2_qa_rider.jpg`, box `Bike` bao trùm toàn bộ người lái và xe, checklist self-QC mục Rider. |

Giới hạn của kết luận từ ba frame ADASIND: Bộ ba frame thực hành chỉ đại diện cho một camera trước duy nhất trong điều kiện ban ngày khô ráo, góc nhìn tĩnh không phản ánh độ rung lắc khi xe vận hành tốc độ cao, thiếu hoàn toàn các bối cảnh ban đêm, trời mưa, sương mù và góc nhìn 3 camera còn lại (sau, trái, phải). Do đó các tỷ lệ lỗi ở đây chỉ mang tính chất định hướng rà soát, không thể suy diễn tuyệt đối cho toàn bộ hệ thống.

## Chuyển sang kế hoạch bốn camera giả lập

Cách soát độ phủ của 200 frame ở `45_sampling_plan.csv` (kể cả tránh đếm nhiều frame liền nhau trong cùng cảnh như nhiều ca độc lập), và vì sao kế hoạch đó chỉ giúp tìm ca cần soi, chưa đo được tỷ lệ lỗi:
1. **Kiểm soát độ phủ và phi tương quan thời gian:** Khi chọn mẫu 200 frame từ 50.000 frame, áp dụng quy tắc bước nhảy thời gian (temporal striding) tối thiểu 3 đến 5 giây giữa các frame được trích xuất trong cùng một chuỗi video, hoặc chỉ chọn keyframe khi có biến động về mật độ phương tiện và điều kiện chiếu sáng. Tuyệt đối không chọn các frame kế tiếp nhau (consecutive frames) vì chúng mang thông tin trùng lặp cao (high temporal correlation).
2. **Bản chất của kế hoạch lấy mẫu:** 200 frame được phân bổ theo phương pháp lấy mẫu phân tầng có chủ đích (stratified targeted sampling) theo ma trận 4 camera × normal/hard, ưu tiên các tình huống góc khuất, lóa đèn và biến dạng vùng nối (seam). Phương pháp này giúp phát hiện tối đa các trường hợp góc cạnh (edge cases) và điểm gãy của guideline, nhưng không phản ánh phân phối xác suất ngẫu nhiên của toàn bộ tập dữ liệu, vì vậy không dùng để suy rộng tỷ lệ lỗi phần trăm (error rate) cho toàn bộ hệ thống 50.000 frame.
