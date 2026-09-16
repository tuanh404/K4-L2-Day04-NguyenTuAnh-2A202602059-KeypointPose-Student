# Visibility report

- Thư mục nhãn: `dataset\labels\train`
- 20 ảnh, 29 skeleton, trung bình 15.45 khớp có v > 0 mỗi người
- Tổng: v=2 329 | v=1 119 | v=0 45

| # | Khớp | v=2 | v=1 | v=0 | %v=1 |
| ---: | --- | ---: | ---: | ---: | ---: |
| 0 | nose | 21 | 7 | 1 | 24% |
| 1 | left_eye | 19 | 9 | 1 | 31% |
| 2 | right_eye | 21 | 7 | 1 | 24% |
| 3 | left_ear | 10 | 18 | 1 | 62% |
| 4 | right_ear | 14 | 14 | 1 | 48% |
| 5 | left_shoulder | 26 | 3 | 0 | 10% |
| 6 | right_shoulder | 27 | 2 | 0 | 7% |
| 7 | left_elbow | 23 | 6 | 0 | 21% |
| 8 | right_elbow | 26 | 2 | 1 | 7% |
| 9 | left_wrist | 19 | 10 | 0 | 34% |
| 10 | right_wrist | 20 | 7 | 2 | 24% |
| 11 | left_hip | 15 | 13 | 1 | 45% |
| 12 | right_hip | 22 | 5 | 2 | 17% |
| 13 | left_knee | 16 | 6 | 7 | 21% |
| 14 | right_knee | 20 | 1 | 8 | 3% |
| 15 | left_ankle | 15 | 5 | 9 | 17% |
| 16 | right_ankle | 15 | 4 | 10 | 14% |

## Đọc bảng này thế nào

1. Khớp nào có **%v=1 cao**: khớp hay bị che. Cổ tay và hông thường là hai vị trí cần xem lại guideline trước khi kết luận.
2. Khớp nào có **v=0 cao bất thường**: mọi người đang dùng Outside ở chỗ đáng lẽ là Occluded. Đó là lỗi số 3 của slide 46, và nó xoá thẳng khớp đó khỏi bảng điểm OKS.
3. Khi so hai người: **lệch lớn = bất đồng về guideline**, không phải về bức ảnh. Sửa guideline trước, sửa nhãn sau.
