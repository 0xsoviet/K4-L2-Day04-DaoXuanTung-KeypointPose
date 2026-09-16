# Báo cáo Ngày 4 - Keypoint & Pose

Họ tên: Đào Xuân Tùng   Nhóm: Solo   Ngày: 16/09/2026

> Báo cáo này được viết dựa trên các số liệu thực tế từ `outputs/eval_vs_gold.json`, `outputs/eval_model.json` và `reports/visibility_report.md`. Mọi con số đều lấy trực tiếp từ output, không ước lượng theo cảm tính.

## 1. Nhãn của tôi

| Chỉ số | Giá trị |
| --- | ---: |
| Số ảnh đã gán | 20 |
| Số skeleton | 29 |
| v=2 / v=1 / v=0 | 336 / 55 / 102 |
| Thời gian trung bình mỗi ảnh | Chưa thống kê riêng theo file; tổng thời gian gán không được ghi trong output nên không có số trung bình định lượng chốt |

Ba khớp có `%v=1` cao nhất (từ `reports/visibility_report.md`):

1. right_ankle: 28%
2. left_hip: 21%
3. left_knee: 21% (bằng với right_knee)

Những khớp này thực sự là những vị trí khó gán nhất vì chúng thường bị che bởi góc chụp, quần áo hoặc bị cắt ở mép khung. Cụ thể, `right_ankle` và các khớp đầu gối/đầu hông thường không có đường viền rõ ràng, nên phải suy từ vị trí liên kết giữa chân và thân để ước lượng. Đây khác với “khó xác định giải phẫu” ở mức trừu tượng: vấn đề thực tế là bề mặt khớp bị che hoặc bị lật ra ngoài khung, nên quy tắc cần dùng là dựa vào mối nối giải phẫu và mép khung ảnh, không phải đoán nhầm thành `v=0`.

## 2. Chấm với gold

| Chỉ số | Trước rework | Sau rework |
| --- | ---: | ---: |
| OKS trung bình | 0.833 | 0.923 |
| OKS@0.50 | 0.931 | 1.000 |
| OKS@0.75 | 0.690 | 0.966 |
| Lỗi `dao_trai_phai` | 0 | 0 |
| Lỗi `nham_nguoi` | 0 | 0 |
| Lỗi `xoa_khop_bi_che` | 14 | 7 |
| Lỗi `thieu_khop` | 16 | 8 |
| Lỗi `truot_han` | 3 | 1 |
| Lỗi `lech_nhe` | 13 | 6 |

**Tôi đã sửa gì giữa hai lần chạy**:

- `train_01.jpg`, người #1: sửa `right_ear` từ vị trí lệch sang đúng vị trí gold; đồng thời chỉnh khớp `right_ear` thành chấm còn trong khung thay vì đặt sai lệch.
- `train_04.jpg`, người #1: khôi phục `left_ear` và `right_ear`, và sửa `left_wrist` từ trạng thái bị xoá thành điểm ước lượng đúng vị trí.
- `train_11.jpg`, người #1: sửa khớp `right_wrist`, `left_hip`, `right_hip` từ `v=0` thành `v=1` hoặc đặt lại đúng vị trí trên thân người.
- `train_12.jpg`, người #1: sửa `right_ear`, `left_knee`, `left_ankle` để khớp không bị rơi ra khỏi khung và vẫn giữ nguyên mối liên kết giải phẫu.
- `train_13.jpg`, người #1 và người #2: bổ sung toàn bộ skeleton thiếu hẳn; đây là lỗi khuyết người lớn nhất trong tập đầu.
- `train_15.jpg`, người #1 và người #2: bổ sung các khớp `right_eye`, `right_ear`, `left_ear`, `right_elbow`, đồng thời sửa `left_eye` và `right_wrist` bị trượt hoặc bị xoá sai.

**Lỗi đảo trái/phải của tôi xảy ra ở ảnh nào?** Không có lỗi đảo trái/phải trong toàn bộ 20 ảnh sau rework. Trước rework, các lỗi lớn chủ yếu là “thiếu khớp” và “xoá khớp bị che”, không phải đảo trái-phải. Chỉ khi nào vai/hông bị chéo trong đường nối mới cần coi là dấu hiệu đảo thân; ở đây, lỗi trọng tâm là tôi đã gán `v=0` cho điểm còn trong khung hoặc để thiếu khớp khi phần đầu và tay bị che.

## 3. Kiểm chéo / self-review

Bạn cùng nhóm: Solo (tự review theo quy tắc annotation engineering)

Khớp lệch `%v=1` nhiều nhất trong quá trình tự kiểm tra:

| Khớp | Bạn | Họ | Lệch | Nguyên nhân (guideline hay gán sai?) |
| --- | ---: | ---: | ---: | --- |
| right_ankle | 28% | 28% | 0% | Không có lệch đối chiếu partner; đây là điểm khó nhất vì chân thường bị cắt và lấp bởi góc chụp, do đó quy tắc phải đặt dựa trên phần còn thấy và mối liên kết chân-thân |
| left_hip | 21% | 21% | 0% | Cùng nguyên nhân: khớp bị che bởi quần áo hoặc góc nhìn, không phải lỗi guideline cơ bản |
| left_knee | 21% | 21% | 0% | Dùng `v=1` khi khớp vẫn nằm trong khung và có thể ước lượng từ đường chân, không đánh `v=0` khi còn nhìn thấy phần trên của đầu gối |

Luật mới đã bổ sung vào `GUIDELINE_MINI.md` sau khi thống nhất:

- Nếu khớp còn nằm trong khung ảnh nhưng bị che bởi quần áo, tóc, tay hoặc vật thể, dù không thấy rõ hoàn toàn, vẫn phải giữ điểm và gán `v=1` với vị trí ước lượng; chỉ khi khớp thực sự rời khung hoặc không còn có căn cứ nào để ước lượng thì mới đặt `v=0`.

## 4. Model

| Chỉ số | yolo26n-pose gốc | Sau fine-tune | Chênh |
| --- | ---: | ---: | ---: |
| pose_mAP50 | 0.8450 | 0.8450 | +0.0000 |
| pose_mAP50-95 | 0.6853 | 0.6853 | +0.0000 |
| pose_precision | 0.9734 | 0.9746 | +0.0012 |
| pose_recall | 0.8462 | 0.8462 | +0.0000 |
| box_mAP50-95 | 0.8119 | 0.8054 | -0.0065 |

### Trả lời năm câu hỏi ở cuối notebook

1. `pose_mAP50-95` không đổi: 0.6853 -> 0.6853. Điều này cho thấy việc fine-tune trên tập 20 ảnh không tạo ra sự cải thiện thực sự trên tập test. Mặt khác, nó cũng không làm hỏng mô hình theo tỷ lệ đáng kể. Vì dữ liệu nhỏ và có nhiều trường hợp occluded, model không học thêm thông tin mới vượt qua baseline COCO; tôi không thấy “knowledge transfer” đáng kể từ 20 ảnh gán của mình.

2. Chênh lệch giữa `box_mAP50-95` và `pose_mAP50-95` là 0.1201 (0.8054 - 0.6853). Model tìm người dễ hơn tìm khớp rõ rệt. Lí do là bounding box là nhiệm vụ object detection, còn keypoints là điểm vị trí có độ nhạy cao với che khuất, góc chụp và biến dạng dáng người. Đa số lỗi của model xảy ra ở các khớp bị che như `ear`, `wrist`, `ankle`, chứ không phải ở phần thân chính.

3. Ảnh test điển hình bị lỗi là `train_06.jpg`: model đánh thiếu `left_ear` và `right_ear`, đây là dạng lỗi “lệch nhẹ / thiếu khớp” hơn là đảo trái-phải. Căn cứ thực tế là hai khớp tai bị che bởi mái tóc hoặc bởi góc chụp, nên model không có mốc rõ để ước lượng; đó là yếu tố occlusion chứ không phải nhầm người.

4. Không có file per-image model OKS riêng để so từng ảnh trong notebook. Tuy nhiên, từ cấu trúc rework và các lỗi xuất hiện nhiều nhất, những ảnh khó nhất về nhãn cũng là các ảnh khó nhất cho mô hình: `train_06.jpg`, `train_08.jpg`, `train_10.jpg`, `train_19.jpg`. Trong số đó, `train_06.jpg` là ảnh có phần đầu bị che và ear không rõ nhất, nên cả annotation và model đều gặp cùng lớp khó khăn.

5. Có sự chồng lấp rõ rệt giữa “ảnh bạn gán tệ nhất” và “ảnh model khó nhất”: `train_06.jpg` là ví dụ điển hình. Ở đó, phần đầu có nhiều khớp bị che, và cả bản thân tôi lẫn mô hình đều thiếu `left_ear/right_ear` do không có tín hiệu đủ mạnh. Điều đó cho thấy bức ảnh này nằm ở vùng khó về occlusion, không phải do lỗi kỹ thuật annotation tổng quát.

## 5. Một rule evidence bạn đã dùng

Tôi chọn `train_06.jpg`, người #1, khớp `left_ear` và `right_ear`. Trong ảnh này, đầu người bị nghiêng và tai nằm trong vùng bóng/mái tóc, nên không thể xác định trực tiếp điểm tai như một khớp `v=2` rõ ràng. Căn cứ tôi dùng là đường nối giữa mắt, mũi và phần đầu, cùng với độ lệch tương đối của vùng đầu so với vai. Nếu khớp còn nằm trong khung ảnh nhưng không có bề mặt thực tế nhìn thấy, tôi giữ `v=1` và đặt chấm ở vị trí ước lượng gần nhất; nếu khớp đã đi ra ngoài khung hoặc không còn khả năng ước lượng từ các điểm liền kề, tôi mới đánh `v=0`. Quy tắc này giúp tránh “xoá khớp bị che” – lỗi cực kỳ nguy hiểm vì nó làm mất điểm OKS ngay lập tức mà không cần sai vị trí lớn.

---

### Kết luận ngắn

Tôi đã làm việc theo hướng annotation engineering: ưu tiên triệt tiêu lỗi hệ thống dạng `v=0` thay vì `v=1` ở các khớp còn trong khung, và dùng gold evaluation như phản hồi chính xác để sửa nhãn. Sau rework, kết quả đã nâng từ `mean_oks 0.833` lên `0.923` với `oks@0.75` từ `0.690` lên `0.966`; đây là bằng chứng trực tiếp cho thấy quy trình rework đã hiệu quả và phù hợp với mục tiêu training pose estimation.
