# Mini guideline - bài cá nhân

Người gán: **Nguyễn Tú Anh**  |  Ngày: **16/09/2026**

## 1. Luật bắt buộc

- Sử dụng bộ 17 keypoint COCO đúng tên và đúng thứ tự từ skeleton `.SVG` được cung cấp.
- Mỗi người được gán đủ 17 keypoint. Không xoá keypoint chỉ vì khó nhìn; phải đặt trạng thái
  visibility phù hợp.
- Trái/phải được xác định theo cơ thể người trong ảnh, không theo phía trái/phải của màn hình.
- Keypoint nhìn thấy rõ: `v=2`.
- Keypoint bị che nhưng vị trí giải phẫu vẫn nằm trong khung: ước lượng vị trí, đặt điểm và
  dùng `v=1` (`Occluded`).
- Keypoint thật sự nằm ngoài mép ảnh: dùng `v=0` (`Outside`).
- Không dùng `Hidden` (`h`) vì trạng thái này không được lưu đúng vào file export.
- Hoàn thành toàn bộ skeleton của một người trước khi chuyển sang người tiếp theo để tránh
  nối nhầm keypoint giữa hai người.

## 2. Quy tắc cá nhân áp dụng trong bài

| Tình huống | Quy tắc áp dụng | Lý do |
| --- | --- | --- |
| Hông của người mặc quần áo dài | Nếu hông còn trong khung nhưng bị quần áo che, ước lượng tâm khớp từ vai, trục thân và hướng chân rồi dùng `v=1`. Chỉ dùng `v=0` nếu hông thật sự ngoài ảnh. | Hông là điểm giải phẫu, không phải một điểm luôn nhìn thấy trên bề mặt quần áo. |
| Tai bị tóc hoặc mũ bảo hiểm che | Nếu xác định trực tiếp được vị trí tai thì dùng `v=2`; nếu phải suy luận vì tóc hoặc mũ che thì dùng `v=1`. Không dùng `v=0` khi đầu vẫn còn trong ảnh. | Tóc và mũ chỉ gây che khuất, không làm tai ra ngoài khung hình. |
| Người bị cắt ở mép ảnh | Xét riêng từng keypoint. Điểm vượt biên ảnh dùng `v=0`; điểm còn trong ảnh nhưng bị vật khác che dùng `v=1`. | Không thể áp cùng một cờ cho toàn bộ phần thân dưới chỉ vì người bị cắt một phần. |
| Cổ tay nằm sau tay lái hoặc sau thân người | Lần theo hướng từ vai đến khuỷu tay để ước lượng cổ tay và dùng `v=1` nếu vị trí đó còn trong ảnh. | Tay lái hoặc thân người là vật che, không phải biên ảnh. |
| Hai người chồng lên nhau | Gán hoàn chỉnh một người rồi mới gán người kia; mỗi điểm phải thuộc đúng cơ thể. Điểm bị người còn lại che dùng `v=1`. | Tránh đường xương kéo sang cơ thể bên cạnh và lỗi `nham_nguoi`. |
| Người có kích thước nhỏ | Vẫn gán nếu có thể nhận diện là người thuộc phạm vi ảnh; không tự đặt thêm ngưỡng kích thước ngoài hướng dẫn của bài. | Bộ ảnh đã được chọn để các đối tượng cần gán đủ lớn cho COCO-17. |

Trong visibility report hiện tại, ba keypoint có tỷ lệ `v=1` cao nhất là `left_ear` (62%),
`right_ear` (48%) và `left_hip` (45%). Điều này phù hợp với việc tai thường bị tóc/mũ che,
còn hông thường phải ước lượng qua quần áo hoặc vật cản.

## 3. Ba trường hợp mơ hồ đã gặp

### Ca 1 - ảnh `train_06`, người thứ 1, các keypoint trên mặt

- **Mơ hồ ở chỗ nào:** Mũ bảo hiểm che gần như toàn bộ mặt nên không nhìn thấy trực tiếp
  `nose`, hai mắt và hai tai.
- **Quyết định:** Vì đầu vẫn nằm hoàn toàn trong khung ảnh, các điểm mặt phải được ước lượng
  theo vị trí và hướng của đầu rồi đặt `v=1`, không đặt `v=0`.
- **Vì sao:** Đây là trường hợp bị vật che; vị trí giải phẫu của các điểm không vượt ra ngoài
  biên ảnh.
- **Nếu quyết định ngược lại:** Dùng `v=0` sẽ loại các điểm mặt khỏi quá trình tính điểm và
  khiến model không học cách suy luận keypoint khi người đội mũ bảo hiểm.

### Ca 2 - ảnh `train_04`, người thứ 1, hai đầu gối và hai cổ chân

- **Mơ hồ ở chỗ nào:** Phần thân dưới vừa bị xe che vừa tiếp tục xuống dưới mép ảnh, nên khó
  phân biệt khớp chỉ bị che hay đã nằm ngoài khung.
- **Quyết định:** Xét từng khớp theo trục hông và hướng chân. Keypoint có vị trí giải phẫu dưới
  mép ảnh được đặt `v=0`; keypoint còn trong khung nhưng bị xe che phải đặt ước lượng và dùng
  `v=1`.
- **Vì sao:** Visibility phụ thuộc vị trí thật của từng khớp so với biên ảnh, không phụ thuộc
  việc có nhìn thấy toàn bộ người hay không.
- **Nếu quyết định ngược lại:** Dùng `v=0` cho khớp còn trong ảnh làm mất dữ liệu; dùng `v=1`
  cho khớp ngoài ảnh lại tạo tọa độ ước lượng không có căn cứ.

### Ca 3 - ảnh `train_15`, người thứ 2, vai và hông trái/phải

- **Mơ hồ ở chỗ nào:** Người đứng nghiêng, đầu và thân không cùng hướng, đồng thời xe máy che
  một phần cơ thể. Vì vậy thứ tự ngang của mắt, vai và hông có thể tạo cảnh báo đảo trái/phải.
- **Quyết định:** Xác định trái/phải theo cơ thể người và kiểm tra liên tục hai chuỗi
  `shoulder → elbow → wrist` và `hip → knee → ankle`; không đổi điểm chỉ vì cảnh báo tự động.
- **Vì sao:** Ở tư thế nghiêng hoặc xoay người, so sánh riêng tọa độ ngang của mắt với vai/hông
  không đủ để kết luận bị đảo trái/phải.
- **Nếu quyết định ngược lại:** Đổi nhầm hai bên sẽ tạo xương bắt chéo và khiến model học sai
  định danh trái/phải khi huấn luyện.

## 4. Kiểm tra tính nhất quán cá nhân

Bài được thực hiện cá nhân nên không có visibility report của thành viên khác để đối chiếu.
Tôi kiểm tra tính nhất quán bằng ba bước:

1. Chạy `check_pose_labels.py` để kiểm tra đủ 20 file, đủ 17 điểm trên mỗi skeleton và phát
   hiện các trường hợp `v=0` hoặc trái/phải đáng nghi.
2. Chạy `visualize_pose.py`, kiểm tra toàn bộ 20 ảnh, đặc biệt các đường nối ở vai, hông và
   các đường xương có thể kéo sang người khác.
3. Chạy `visibility_report.py` và xem các cặp trái/phải có tỷ lệ visibility chênh lệch lớn.
   Các chênh lệch được đối chiếu lại với tư thế, vật che và mép ảnh trước khi quyết định sửa.

Phần kiểm chéo với bạn cùng nhóm: **không áp dụng vì đây là bài cá nhân**.
