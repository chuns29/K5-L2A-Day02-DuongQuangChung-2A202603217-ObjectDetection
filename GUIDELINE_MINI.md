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

*Các ví dụ dưới đây được chọn từ ảnh train. Quyết định chỉ áp dụng cho phần xe nhìn thấy trong ảnh; không suy đoán phần bị che hoặc nằm ngoài khung.*

| Ảnh | Tình huống | Quyết định cuối | Lý do theo schema |
|---|---|---|---|
| `drive_009.jpg` | Cảnh đông xe; xe buýt bị các xe khác che lấp một phần và chồng lấn với nhiều phương tiện. | Gán `bus` (2) cho xe có thân dài, nhiều cửa sổ hành khách; vẽ box riêng quanh phần xe buýt nhìn thấy. | Các đặc điểm thân xe và khoang hành khách đủ phân biệt xe buýt với xe con; không gộp box với xe bên cạnh hoặc ước lượng phần bị che. |
| `drive_004.jpg` | Một xe con ở sát rìa phải, thân xe bị khung ảnh cắt. | Gán `car` (0) nếu phần còn lại đủ nhận diện; giới hạn box trong ảnh và để cạnh box chạm rìa phải. | Xe vẫn nhận diện được là xe con; phần nằm ngoài ảnh không được nội suy. |
| `drive_002.jpg` | Xe van trắng bên trái xe buýt trắng ở giữa ảnh; các phương tiện có kích thước và hình dáng gần nhau trong cảnh đông xe. | Gán `van` (3) cho xe thân hộp, khoang kín; xe thân dài với nhiều cửa sổ hành khách là `bus` (2). | Dựa vào kiểu thân xe và đặc điểm khoang hành khách, không quyết định chỉ theo màu hoặc kích thước tương đối. Nếu không đủ dấu hiệu để phân biệt, hỏi giảng viên thay vì đoán. |
