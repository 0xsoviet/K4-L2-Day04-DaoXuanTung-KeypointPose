# Visibility report

- Thư mục nhãn: `dataset/labels/train`
- 20 ảnh, 29 skeleton, trung bình 13.48 khớp có v > 0 mỗi người
- Tổng: v=2 336 | v=1 55 | v=0 102

| # | Khớp | v=2 | v=1 | v=0 | %v=1 |
| ---: | --- | ---: | ---: | ---: | ---: |
| 0 | nose | 23 | 0 | 6 | 0% |
| 1 | left_eye | 20 | 1 | 8 | 3% |
| 2 | right_eye | 20 | 1 | 8 | 3% |
| 3 | left_ear | 12 | 2 | 15 | 7% |
| 4 | right_ear | 17 | 1 | 11 | 3% |
| 5 | left_shoulder | 27 | 1 | 1 | 3% |
| 6 | right_shoulder | 29 | 0 | 0 | 0% |
| 7 | left_elbow | 23 | 2 | 4 | 7% |
| 8 | right_elbow | 27 | 2 | 0 | 7% |
| 9 | left_wrist | 21 | 2 | 6 | 7% |
| 10 | right_wrist | 19 | 7 | 3 | 24% |
| 11 | left_hip | 21 | 6 | 2 | 21% |
| 12 | right_hip | 23 | 4 | 2 | 14% |
| 13 | left_knee | 15 | 6 | 8 | 21% |
| 14 | right_knee | 15 | 6 | 8 | 21% |
| 15 | left_ankle | 13 | 6 | 10 | 21% |
| 16 | right_ankle | 11 | 8 | 10 | 28% |

## Đọc bảng này thế nào

1. Khớp nào có **%v=1 cao**: khớp hay bị che. Cổ tay và hông thường là hai vị trí cần xem lại guideline trước khi kết luận.
2. Khớp nào có **v=0 cao bất thường**: mọi người đang dùng Outside ở chỗ đáng lẽ là Occluded. Đó là lỗi số 3 của slide 46, và nó xoá thẳng khớp đó khỏi bảng điểm OKS.
3. Khi so hai người: **lệch lớn = bất đồng về guideline**, không phải về bức ảnh. Sửa guideline trước, sửa nhãn sau.
