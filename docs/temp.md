# BÁO CÁO/YÊU CẦU XÂY DỰNG HỆ THỐNG QUẢN LÝ CHƯƠNG TRÌNH ĐÀO TẠO

## 1. Tổng quan & Dữ liệu đầu vào
- Khi mở một ngành đào tạo mới, trường đại học cần xây dựng **Đề án mở ngành** và **Chương trình đào tạo (CTĐT)**.
- **Cấu trúc của một CTĐT bao gồm:**
  - Chuẩn đầu ra của chương trình (PLO - Program Learning Outcomes).
  - Danh sách các môn học và sự đóng góp của từng môn vào các PLO.
  - Kế hoạch đào tạo: Phân bổ khối lượng kiến thức, danh sách môn học theo từng năm học/học kỳ.
- **Cấu trúc của Đề cương chi tiết môn học:**
  - Chuẩn đầu ra của môn học (CLO - Course Learning Outcomes).
  - Mục tiêu, tóm tắt nội dung môn học.
  - Tên môn học (Tiếng Việt & Tiếng Anh), số tín chỉ.
  - Nội dung giảng dạy chi tiết theo từng chương/mục.
  - Tài liệu tham khảo.

## 2. Quy trình làm việc trên hệ thống (Giai đoạn 1 - Nhập liệu thủ công)

### Bước 1: Khởi tạo CTĐT
- **Admin** tạo tài khoản và phân quyền cho **Người quản lý CTĐT** (qua email).
- **Người quản lý CTĐT** đăng nhập hệ thống, khởi tạo một CTĐT mới (gồm: Mã, tên, mục tiêu, thông tin tổng quát).
- Lên danh sách các môn học dự kiến trong CTĐT (chia theo khối kiến thức: cơ sở ngành, chuyên ngành...).

### Bước 2: Phân công biên soạn Đề cương môn học
- **Người quản lý CTĐT** tạo danh sách các giảng viên tham gia biên soạn.
- Tiến hành phân công: Chọn giảng viên và giao phụ trách các môn học cụ thể.

### Bước 3: Biên soạn Đề cương (Dành cho Giảng viên)
- **Giảng viên** nhận thông báo, đăng nhập hệ thống và xem danh sách các môn được phân công.
- Chọn từng môn học để điền thông tin đề cương chi tiết theo template/biểu mẫu chuẩn của hệ thống (Tên, số tín chỉ, tóm tắt, các chương, tài liệu tham khảo...).

### Bước 4: Theo dõi tiến độ
- **Người quản lý CTĐT** sử dụng tính năng theo dõi tiến độ để nắm bắt tình hình:
  - Giảng viên nào đã hoàn thành bao nhiêu môn, còn bao nhiêu môn đang làm.
  - Đối chiếu với deadline để có biện pháp nhắc nhở, đôn đốc.
  - Xem trực tiếp kết quả/nội dung giảng viên đã nhập trên hệ thống.

### Bước 5: Đánh giá của Hội đồng chuyên môn
- **Người quản lý CTĐT** thiết lập danh sách Hội đồng đánh giá và cấp tài khoản.
- **Thành viên Hội đồng** đăng nhập, xem và nhận xét CTĐT thông qua các biểu mẫu đánh giá.
- **Người quản lý CTĐT** tổng hợp ý kiến, tiếp thu và điều chỉnh để hoàn thiện CTĐT.

### Bước 6: Phê duyệt của Hội đồng khoa học & Ban Giám hiệu
- CTĐT hoàn thiện được gửi lên **Hội đồng Khoa học cấp trường** (gồm các chuyên gia phù hợp với chuyên môn của ngành) để đánh giá và thông qua.
- Sau khi HĐKH đồng ý, trình lên **Ban Giám hiệu** phê duyệt.
- CTĐT chính thức được công nhận, ban hành và giao về các khoa để thực thi.

## 3. Định hướng phát triển: Số hóa tự động và Tích hợp AI

### Giai đoạn 2: Tự động hóa quá trình nhập liệu (Số hóa dữ liệu cũ)
- Cho phép người dùng import toàn bộ thư mục chứa các file Word (đề án mở ngành, đề cương môn học cũ).
- Hệ thống tự động đọc, bóc tách dữ liệu (parse) và chuyển đổi thành dữ liệu có cấu trúc (số hóa) đưa vào cơ sở dữ liệu.
- Người dùng chỉ cần xem lại (review) và xác nhận.
- CSDL phải hỗ trợ quản lý nhiều CTĐT cho nhiều cấp bậc khác nhau (Cử nhân, Thạc sĩ, Tiến sĩ).

### Giai đoạn 3: Tích hợp trợ lý AI
Hệ thống sẽ được nâng cấp với các tính năng AI hỗ trợ ở 3 khâu cốt lõi:
1. **Hỗ trợ xây dựng CTĐT ban đầu:**
   - Dựa trên mục tiêu và tiêu chí do Người quản lý đưa ra, AI tự động thu thập thông tin CTĐT của các trường đại học uy tín (trong nước & quốc tế) có cùng ngành/chuyên ngành.
   - AI xử lý, đối chiếu và tự động đề xuất một bản dự thảo CTĐT.
   - Người quản lý review, tinh chỉnh và chốt CTĐT cuối cùng.
2. **Hỗ trợ biên soạn đề cương môn học:**
   - Dựa vào mục tiêu môn học, AI tham khảo các nguồn đề cương liên quan và tự động đề xuất: số lượng chương, cấu trúc tiểu mục.
   - Giảng viên sẽ review và chỉnh sửa lại thành đề cương hoàn chỉnh, giúp tiết kiệm thời gian biên soạn.
3. **Hỗ trợ kiểm định chất lượng:**
   - Sau một chu kỳ đào tạo (VD: 4 năm), khi cần đánh giá kiểm định, AI sẽ kết hợp kết quả học tập của sinh viên với các tiêu chuẩn kiểm định để tự động hỗ trợ quy trình đánh giá.
   - Giúp việc kiểm định dễ dàng, có tính hệ thống và khách quan hơn so với phương pháp thủ công hiện tại.

## 4. Tóm tắt kế hoạch thực hiện
- **Tạm gác lại** các tính năng AI ở giai đoạn đầu.
- **Tập trung xây dựng nền tảng lõi trước**: Hệ thống quản lý người dùng, phân quyền, các form nhập liệu thủ công, quy trình duyệt và theo dõi tiến độ.
- **Phát triển tính năng import/số hóa tự động** để chuyển đổi dữ liệu cũ vào hệ thống.
- **Thống nhất lại quy trình** với các bên liên quan trước khi tiến hành triển khai các bước tiếp theo.