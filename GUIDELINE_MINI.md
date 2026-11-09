# Mini guideline — Object Detection ADAS

## 1. Định nghĩa lớp

| Lớp | Định nghĩa | Ví dụ/điểm phân biệt |
|---|---|---|
| `car` (0) | Xe ô tô con, chủ yếu phục vụ di chuyển cá nhân, bao gồm sedan, hatchback, SUV, taxi và xe bán tải. | Phân biệt với `truck` ở mục đích và kiểu thân xe. Xe bán tải thuộc `car` theo schema của bài. |
| `truck` (1) | Xe tải chở hàng, bao gồm xe tải thùng, xe tải lớn và xe đầu kéo. | Thường có cabin và khu vực chở hàng riêng. Không nhầm xe bán tải với xe tải. |
| `bus` (2) | Xe chuyên chở nhiều hành khách, bao gồm xe buýt và minibus chở khách. | Thường có nhiều hàng ghế và cửa sổ phục vụ hành khách. Phân biệt với van theo thiết kế và công năng. |
| `van` (3) | Xe van hoặc minivan phục vụ vận chuyển hàng hóa hay hành khách. | Thường có thân xe dạng hộp, khoang kín và kích thước nhỏ hơn xe buýt lớn. |

## 2. Quy tắc chung

- **Bounding box:** Mỗi xe hợp lệ được gán một bounding box riêng. Box cần ôm sát phần thân xe nhìn thấy trong ảnh, hạn chế lấy thêm nền đường, xe bên cạnh hoặc các vật thể khác.
- **Vật thể bị che khuất:** Nếu xe bị che một phần nhưng vẫn xác định được loại xe, tiếp tục gán nhãn cho phần nhìn thấy. Không tự ước lượng phần thân xe bị che hoàn toàn.
- **Vật thể bị cắt bởi rìa ảnh:** Nếu xe nằm một phần ngoài khung hình nhưng vẫn nhận diện được loại xe, vẽ box sát phần còn nhìn thấy, với cạnh box chạm rìa ảnh khi cần thiết.
- **Vật thể rất nhỏ hoặc mơ hồ:** Chỉ gán nhãn khi đủ đặc điểm để xác định loại xe. Nếu không thể phân biệt chắc chắn, ghi nhận trường hợp để hỏi giảng viên, không tự đoán lớp.
- **Xe đang đỗ:** Vẫn gán nhãn bình thường theo loại phương tiện.
- **Nhiều xe chồng lấn:** Mỗi xe phải có bounding box riêng, không gộp nhiều xe thành một box.
- **Đối tượng không thuộc phạm vi:** Không gán nhãn người đi bộ, xe máy, xe đạp, biển báo, đèn giao thông, hình xe trên quảng cáo hoặc hình phản chiếu không phải xe thật trong cảnh.

## 3. Ghi lại quyết định cho ít nhất ba ca mơ hồ

*Bảng dưới là mẫu tình huống cần kiểm tra trên ảnh train. Chỉ giữ trường hợp thực sự xuất hiện, thay mã ảnh và điều chỉnh quyết định theo quan sát của bản thân.*

| Ảnh | Tình huống | Quyết định cuối | Lý do theo schema |
|---|---|---|---|
| `drive_XXX.jpg` | Một ô tô bị xe phía trước che một phần. | [Ghi quyết định thực tế] | Nếu nhận diện được loại xe, gán một box quanh phần nhìn thấy, không đoán phần bị che. |
| `drive_YYY.jpg` | Một phương tiện nằm sát mép ảnh, một phần thân xe nằm ngoài khung hình. | [Ghi quyết định thực tế] | Nếu xác định được lớp xe, box chỉ bao quanh phần xuất hiện trong ảnh và chạm mép ảnh. |
| `drive_ZZZ.jpg` | Một phương tiện có hình dạng khó phân biệt giữa `van` và `bus`. | [Ghi quyết định sau khi kiểm tra] | Xem xét đặc điểm và mục đích của xe. Nếu vẫn mơ hồ, hỏi giảng viên trước khi quyết định lớp. |
