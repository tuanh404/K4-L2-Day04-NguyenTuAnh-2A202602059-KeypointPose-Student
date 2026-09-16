# Báo cáo Ngày 4 - Keypoint & Pose

Họ tên: **Nguyễn Tú Anh**   Nhóm: **Cá nhân**   Ngày: **16/09/2026**

## 1. Nhãn của tôi

| Chỉ số | Giá trị |
| --- | ---: |
| Số ảnh đã gán | 20 |
| Số skeleton | 29 |
| v=2 / v=1 / v=0 | 328 / 130 / 35 |
| Thời gian trung bình mỗi ảnh | 3 phút |

Ba khớp có `%v=1` cao nhất (chép từ `reports/visibility_report.md`):

1. `left_ear`: 66% (19/29).
2. `right_ear`: 52% (15/29).
3. `left_hip`: 41% (12/29).

Chúng có đúng là những khớp tôi thấy khó gán nhất không? Đúng, nhưng vì hai lý do khác nhau. Tai thường bị
tóc hoặc mũ bảo hiểm che nên xuất hiện `v=1` nhiều; vị trí giải phẫu của tai vẫn tương đối dễ
ước lượng theo đầu. Hông không có bề mặt khớp nhìn thấy trực tiếp và thường bị áo, tư thế
nghiêng hoặc phương tiện che, nên vừa hay bị che vừa khó xác định vị trí giải phẫu.

## 2. Chấm với gold

| Chỉ số | Trước rework | Sau rework |
| --- | ---: | ---: |
| OKS trung bình | 0.9083 | 0.9224 |
| OKS@0.50 | 1.0000 | 1.0000 |
| OKS@0.75 | 0.9655 | 1.0000 |
| Lỗi `dao_trai_phai` | 0 | 0 |
| Lỗi `nham_nguoi` | 1 | 0 |
| Lỗi `xoa_khop_bi_che` | 1 | 0 |

Các lỗi khác trước rework gồm hai lỗi `thieu_khop` và một lỗi `truot_han`; sau rework các lỗi
này đều về 0. Kết quả cuối có đủ 29/29 người, không thiếu hoặc thừa người và không còn skeleton
nào có OKS dưới 0.75.

**Tôi đã sửa gì giữa hai lần chạy** (ghi cụ thể: ảnh nào, người thứ mấy, khớp nào):

- `train_06`, người thứ 1, `left_ear` và `right_ear`: bỏ `Outside`, đặt lại hai điểm tai ở vị
  trí giải phẫu ước lượng và dùng `v=1` vì tai bị mũ bảo hiểm che nhưng đầu vẫn nằm trong ảnh.
- `train_06`, người thứ 1, `right_hip`: bỏ `Outside`, đặt lại hông phải theo trục thân và dùng
  `v=1` vì hông bị người/xe che nhưng vẫn nằm trong khung.
- `train_03`, người thứ 1, `right_ear`: kéo điểm về đúng vị trí tai phải; trước đó điểm lệch
  41 px và bị phân loại là `truot_han`.
- `train_04`, người thứ 1, `left_wrist`: chuyển điểm về đúng cổ tay trái của người đang gán;
  trước đó điểm nằm gần cổ tay của người bên cạnh và bị phân loại là `nham_nguoi`.

**Lỗi đảo trái/phải của tôi xảy ra ở ảnh nào?**

Không có lỗi đảo trái/phải trong toàn bộ 20 ảnh. Các cảnh báo hình học ở tư thế nghiêng được
kiểm tra lại bằng đường nối của toàn bộ tay/chân thay vì chỉ so tọa độ ngang giữa mắt và vai.

## 3. Kiểm chéo

Bạn cùng nhóm: **Không áp dụng — bài cá nhân**

Khớp lệch `%v=1` nhiều nhất giữa hai bảng đếm:

| Khớp | Bạn | Họ | Lệch | Nguyên nhân (guideline hay gán sai?) |
| --- | ---: | ---: | ---: | --- |
| Không áp dụng | — | — | — | Không có người đối chiếu trong bài cá nhân |
| Không áp dụng | — | — | — | Không có người đối chiếu trong bài cá nhân |

Luật mới đã bổ sung vào `GUIDELINE_MINI.md` sau khi thống nhất:

- Không áp dụng bước thống nhất với bạn cùng nhóm. Quy tắc cá nhân được củng cố sau rework:
  nếu không nhìn thấy khớp nhưng vị trí giải phẫu vẫn nằm trong biên ảnh thì đặt điểm ước
  lượng và dùng `v=1`; chỉ dùng `v=0` khi vị trí giải phẫu thật sự vượt ra ngoài biên ảnh.

## 4. Model

| Chỉ số | yolo26n-pose gốc | Sau fine-tune | Chênh |
| --- | ---: | ---: | ---: |
| pose_mAP50 | 0.8450 | 0.8450 | +0.0000 |
| pose_mAP50-95 | 0.6853 | 0.6908 | +0.0055 |
| pose_precision | 0.9734 | 0.9792 | +0.0058 |
| pose_recall | 0.8462 | 0.8462 | +0.0000 |
| box_mAP50-95 | 0.8119 | 0.8041 | -0.0078 |

### Trả lời năm câu hỏi ở cuối notebook

1. `pose_mAP50-95` tăng từ 0.6853 lên 0.6908, tức tăng 0.0055. Mức tăng nhỏ cho thấy 20 ảnh
   đã giúp model thích nghi nhẹ với các tư thế nghiêng, người bị mũ/phương tiện che và cảnh
   nhiều người, nhưng số ảnh quá ít để tạo ra thay đổi lớn. `pose_precision` cũng tăng 0.0058,
   trong khi `pose_recall` giữ nguyên.

2. Sau fine-tune, `box_mAP50-95` là 0.8041 còn `pose_mAP50-95` là 0.6908, chênh 0.1133.
   Model tìm người dễ hơn tìm chính xác khớp: hộp người có thể dựa vào toàn bộ hình dáng cơ
   thể, còn keypoint đòi hỏi đúng vị trí từng khớp và chịu ảnh hưởng mạnh bởi che khuất, tư thế
   nghiêng và kích thước người.

3. Ở `test_02`, tập nhãn chỉ có một người nhưng model báo hai người. Ảnh có một người dạng
   silhouette và hai con chim, nên đối tượng nhỏ có đường viền rõ có thể gây dự đoán dư. Tôi
   gọi đây là lỗi **trượt hẳn**, vì model tạo một detection/pose trên vùng không phải người
   thay vì chỉ lệch nhẹ quanh một khớp thật.

4. `train_13` có OKS thấp nhất giữa model và nhãn của tôi: 0.306. Nhãn thủ công đáng tin hơn
   trong trường hợp này vì cả ba skeleton của `train_13` vẫn ghép được với gold và skeleton
   thấp nhất cũng đạt OKS 0.7932, vượt cổng 0.75. Ảnh có ba người ở các tỉ lệ khác nhau và hai
   người trung tâm chồng lấn, nên model dễ ghép hoặc định vị sai khớp.

5. Có. Ảnh có skeleton tôi gán thấp nhất so với gold cũng là `train_13` (OKS thấp nhất 0.7932),
   và đây đồng thời là ảnh model bất đồng với nhãn nhiều nhất (OKS 0.306). Điều này cho thấy
   khó khăn đến từ nội dung ảnh: người nhỏ, nhiều người và vùng cơ thể chồng lấn làm cả người
   gán lẫn model khó xác định đúng chủ thể và vị trí khớp.

## 5. Một rule evidence bạn đã dùng

Ở `train_06`, người thứ 1, `right_hip` không nhìn thấy trực tiếp vì bị thân người và xe máy
che. Tuy nhiên vai, trục thân và phần chân cho thấy vị trí giải phẫu của hông vẫn nằm trong
khung ảnh. Vì vậy tôi đặt điểm tại vị trí ước lượng và dùng `v=1`; dùng `v=0` sẽ sai vì hông
không vượt ra ngoài biên ảnh. Sau khi sửa quy tắc này, lỗi `xoa_khop_bi_che` giảm từ 1 xuống 0.
