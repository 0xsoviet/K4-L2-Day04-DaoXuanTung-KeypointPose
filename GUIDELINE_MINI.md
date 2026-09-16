# Mini guideline - nhóm: Solo  |  người gán: Đào Xuân Tùng  |  ngày: 16/09/2026

> File này được điền trong quá trình gán nhãn và cập nhật sau khi rà soát `visibility_report` và `eval_vs_gold` theo phương pháp solo review. Mục tiêu là loại bỏ các trường hợp gán sai do nhầm `v=1` với `v=0`, vì đây là lỗi hệ thống làm mất điểm OKS mà không cần sai vị trí lớn.

## 1. Luật bắt buộc (quy định gán nhãn / solo review)

- Bộ 17 điểm COCO, đúng tên, đúng thứ tự. Lấy từ file `.SVG` chung.
- Mọi người trong ảnh đều có đủ 17 điểm. Điểm không dùng được thì gắn cờ, không xoá.
- Trái/phải tính theo cơ thể người, không theo bức ảnh.
- Bị che, còn trong khung -> `v = 1`, vẫn đặt chấm ở vị trí ước lượng.
- Ra ngoài mép ảnh -> `v = 0`, không đặt chấm.
- Không dùng `Hidden` (`h`) - nó không được lưu vào file.

## 2. Luật của nhóm bạn (solo, tự xác định và ghi lại)

| Tình huống | Luật nhóm bạn chọn | Vì sao |
| --- | --- | --- |
| Hông của người mặc quần áo dài | Nếu hông vẫn còn nằm trong hình và có thể ước lượng từ đường thân, giữ `v = 1`, không xoá; chỉ dùng `v = 0` khi hông thật sự bị cắt ngoài khung. | Hông là khớp rất hay bị che bởi quần áo. Nếu xoá, mô hình không học được mối nối thân-chân; việc giữ chấm sẽ tốt hơn nhiều so với bỏ hẳn. |
| Tai bị tóc hoặc mũ bảo hiểm che một phần | Nếu vẫn còn thể hiện một phần hình dáng tai hoặc nếu điểm có thể ước lượng từ mép đầu và xương quỹ đạo mắt, dùng `v = 1`; chỉ `v = 0` khi tai đã ra khỏi ảnh hoặc không còn định vị được. | Đây là nơi lỗi phổ biến nhất trong `train_06.jpg`, `train_19.jpg` và `train_20.jpg`. Khớp không phải “đã mất hẳn” chỉ vì bị che. |
| Người bị cắt ở mép ảnh (chỉ thấy từ hông trở lên) | Nếu khớp còn nằm trong khung nhưng chỉ một nửa thân hoặc một phần đầu bị mất, dùng `v = 1` và đặt vào vị trí ước lượng; nếu phần khớp đã nằm ngoài khung, dùng `v = 0`. | Đây là chính sách “căn cứ trên khung hình, không căn cứ trên hoàn hảo”. Nếu chỉ ẩn một phần là `v=1`; nếu mất hẳn khỏi nền là `v=0`. |
| Cổ tay nằm sau tay lái / sau thân mình | Nếu khu vực cổ tay vẫn nằm trong thân người và có thể xác định từ đường cánh tay hoặc các điểm trên cánh tay, gán `v = 1`; chỉ `v = 0` khi cổ tay đã hoàn toàn bị che dưới vật thể và không thể ước lượng từ cấu trúc. | Cổ tay là khớp có tần suất thấp nhất trong visibility report nhưng rất dễ bị đánh sai thành `v=0` khi thực tế còn trong khung. |
| Hai người chồng lên nhau | Xác định người nào gần mặt trước, giữ full 17 điểm cho người đó; nếu khớp của người bị chồng lên không thể phân biệt, đánh `v=1` nếu còn suy đoán được, còn không thì `v=0` nhưng phải có căn cứ thị giác rõ. | Không được gán nhầm nguyên tử hình học từ người khác; luôn xác định người trước khi quyết định khớp nào thuộc ai. |
| Người nhỏ đến mức nào thì không gán nữa | Không bỏ người vì kích thước nhỏ nếu người đó còn đủ được 17 điểm và thể hiện rõ thân thể; chỉ bỏ khi người thân quá nhỏ và không thể xác định các điểm thân hoặc mốc giải phẫu. | Đối với bài train này, người nhỏ vẫn được gán nếu còn đủ thông tin; không gán “vì quá nhỏ” là cách dễ tạo ra `missing person` không cần thiết. |

Với mỗi luật, có thể dùng các ảnh mẫu từ tập train như `train_06.jpg`, `train_08.jpg`, `train_10.jpg`, `train_15.jpg` để minh họa. Mẫu thực tế quan trọng vì khớp bị che không thể giải thích bằng lời văn chung chung.

## 3. Ba ca mơ hồ đã gặp (bắt buộc, ghi ít nhất 3)

### Ca 1 - ảnh `train_06.jpg`, người thứ `1`, khớp `left_ear` / `right_ear`

- Mơ hồ ở chỗ nào: đầu nghiêng và tai bị mái tóc/điều kiện ánh sáng che gần hết, nên không có bề mặt rõ.
- Bạn quyết thế nào: giữ `v = 1` nếu vẫn có thể ước lượng từ mắt-vai và hình dạng đầu; chỉ bỏ khi tai đã nằm ngoài mép khung.
- Vì sao: khớp tai không phải “không có gì cả”; nếu bị che nhưng còn trong thân đầu, cần giữ chấm để model học tiếp.
- Nếu người khác quyết ngược lại thì model học sai cái gì: model có thể học thói quen bỏ hẳn chỏm đầu khi bị che, dẫn đến mất thông tin quan trọng ở vùng đầu.

### Ca 2 - ảnh `train_08.jpg`, người thứ `1`, khớp `left_knee` / `left_ankle`

- Mơ hồ ở chỗ nào: chân bị che hoặc méo do góc chụp, vùng đầu gối và mắt cá chân không rõ nét.
- Bạn quyết thế nào: giữ `v = 1` cho đầu gối và mắt cá nếu còn nhìn thấy vị trí tương ứng trên đường chân; không đánh `v = 0` nếu chỗ đó vẫn nằm trong khung.
- Vì sao: đường chân và thân tạo thành mối tuyến, nên phần chỗ còn thấy cuối cùng vẫn đủ để ước lượng.
- Nếu người khác quyết ngược lại thì model học sai cái gì: mô hình sẽ coi “chân bị che = không có chân”, làm giảm khả năng hiện thực hóa kiểu dáng chân dưới góc nghiêng.

### Ca 3 - ảnh `train_15.jpg`, người thứ `1`, khớp `right_eye` / `left_ear`

- Mơ hồ ở chỗ nào: phần đầu gần mép khung và thường bị lẫn với các chi tiết môi trường hoặc góc nghiêng hình.
- Bạn quyết thế nào: nếu mắt hoặc tai còn nằm trong khung và có thể xác định qua mức sáng hoặc hình dạng đầu, duy trì `v = 1`.
- Vì sao: chỉ cần một điểm ước lượng tốt hơn là bỏ hẳn khớp, bởi bỏ mất dạng đầu làm tăng tỷ lệ sai pose w.r.t. đầu và mặt.
- Nếu người khác quyết ngược lại thì model học sai cái gì: mô hình sẽ bỏ mất thông tin giữa mặt và thân, dẫn đến pose bị lệch về phần đầu.

## 4. Sau khi so visibility report với self-review

- Khớp lệch `%v=1` nhiều nhất: `right_ankle` (28%, còn trong vùng cao nhất của tập train).
- Nguyên nhân là **gần như là guideline chưa rõ ở mức cài đặt thực hành**, không phải là vấn đề “bức ảnh khó vô lý”: vì nhiều ảnh đều có cùng dạng occlusion ở chân và đầu.
- Luật mới bổ sung vào mục 2 sau khi thống nhất: “Nếu khớp còn nằm trong khung và có thể ước lượng từ các điểm lân cận, phải giữ `v=1`; không đánh `v=0` đối với phần bị che mà không xác định được hết khớp.”

---

### Ghi nhớ cuối cùng

Trong annotation dành cho pose estimation, quy tắc ưu tiên là “bảo toàn thông tin hơn là nén thông tin”. Một khớp bị che nhưng còn trong khung phải được giữ là `v=1` để mô hình còn có tín hiệu học. Chỉ khi khớp thực sự ra ngoài khung hoặc không còn định vị được thì mới dùng `v=0`. Đây là nguyên tắc đã được kiểm chứng bằng quá trình rework và đánh giá OKS với gold.
