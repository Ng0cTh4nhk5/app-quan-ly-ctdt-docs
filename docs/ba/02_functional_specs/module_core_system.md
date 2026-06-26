# Đặc tả chức năng: Core System & Quản trị Hệ thống

Tài liệu này đặc tả các chức năng nền tảng và quản trị chung của toàn hệ thống. Phân hệ này chủ yếu dành cho System Admin, nhưng cũng bao gồm các chức năng dùng chung cho mọi vai trò (như Đăng nhập, Hồ sơ cá nhân).

---

## 1. Màn hình Đăng nhập (SCR-00-LOGIN)

### 1.1. Mục đích
Cung cấp cổng xác thực duy nhất (Single Sign-On / Local Authentication) cho mọi người dùng để truy cập vào hệ thống.

### 1.2. Quyền truy cập
- Guest (Người dùng chưa đăng nhập).

### 1.3. Mô tả giao diện (Wireframe Elements)
- **Form Đăng nhập:**
  - Ô nhập `Username` (Mã số cán bộ / Mã Giảng viên) hoặc `Email` trường cấp.
  - Ô nhập `Password` (Mật khẩu). Có icon mắt để ẩn/hiện mật khẩu.
  - Nút "Quên mật khẩu".
  - Nút "Đăng nhập" (Primary Button).
  - (Tuỳ chọn) Nút "Đăng nhập bằng Microsoft/Google Workspace".

### 1.4. Quy tắc nghiệp vụ (Business Rules)
1. **Validation Input:** Username và Password không được để trống. Sai định dạng email cảnh báo ngay (nếu dùng email).
2. **Lockout Policy:** Nhập sai mật khẩu 5 lần liên tiếp sẽ bị khoá tài khoản tạm thời trong 15 phút.
3. **Mã hoá:** Mật khẩu truyền đi phải mã hoá (HTTPS), lưu trong database dạng BCRYPT.
4. **Token:** Đăng nhập thành công trả về JWT Token (thời hạn cấu hình trong Admin Settings) và Refresh Token.

### 1.5. Xử lý ngoại lệ (Edge Cases)
- **Sai mật khẩu/Tài khoản không tồn tại:** Hiển thị chung một thông báo "Tên đăng nhập hoặc mật khẩu không chính xác" (tránh dò tìm tài khoản).
- **Tài khoản bị khoá:** "Tài khoản của bạn đã bị khoá. Vui lòng liên hệ Admin để được hỗ trợ."

---

## 2. Màn hình Hồ sơ Cá nhân (SCR-02-PROFILE)

### 2.1. Mục đích
Cho phép người dùng xem thông tin cá nhân và thay đổi mật khẩu định kỳ.

### 2.2. Quyền truy cập
- Tất cả các roles.

### 2.3. Mô tả giao diện
Giao diện chia làm 2 tab:
- **Tab 1: Thông tin cá nhân (Read-only phần lớn)**
  - Hình đại diện (Avatar).
  - Họ và Tên.
  - Mã Giảng viên / Cán bộ.
  - Email, Số điện thoại.
  - Đơn vị công tác (Khoa/Viện).
  - Vai trò (Role) trên hệ thống (chỉ xem).
- **Tab 2: Đổi mật khẩu**
  - Mật khẩu hiện tại.
  - Mật khẩu mới (có thanh báo độ mạnh mật khẩu).
  - Nhập lại mật khẩu mới.

### 2.4. Quy tắc nghiệp vụ
1. **Sửa thông tin:** Người dùng chỉ được tự sửa Avatar và Số điện thoại. Tên, Email, Đơn vị do Admin đồng bộ từ hệ thống HRM của trường.
2. **Đổi mật khẩu:** Mật khẩu mới phải thoả mãn chính sách: Tối thiểu 8 ký tự, có chữ hoa, chữ thường và số. Không trùng với 3 mật khẩu gần nhất.

---

## 3. Màn hình Quản lý Người dùng (SCR-03-USER-MGMT)

### 3.1. Mục đích
Cho phép Admin thêm mới, chỉnh sửa thông tin, phân quyền, và kiểm soát trạng thái các tài khoản trong hệ thống.

### 3.2. Quyền truy cập
- System Admin.

### 3.3. Mô tả giao diện
- **Topbar:** Thanh tìm kiếm (theo tên, mã), Filter (Theo Role, Theo Khoa, Theo Trạng thái). Nút "+ Thêm người dùng". Nút "Import Excel".
- **Data Table:**
  - Cột: Mã Cán bộ | Họ và Tên | Email | Khoa/Đơn vị | Vai trò (Tag) | Lần đăng nhập cuối | Trạng thái (Active/Locked).
  - Cột Action: Edit (Sửa), Lock (Khoá), Reset Password.

### 3.4. Cửa sổ thêm/sửa người dùng (Modal)
- Nhập thông tin cơ bản.
- **Phân quyền (Role Assignment):** Có thể gán nhiều Role cho 1 người (VD: Vừa là Lecturer, vừa là Program Manager).
- **Gán Đơn vị:** Thuộc khoa nào (để giới hạn phạm vi dữ liệu được thấy).

### 3.5. Quy tắc nghiệp vụ
1. **Import Excel:** Hỗ trợ tải file mẫu. Khi import, hệ thống validate từng dòng (trùng email, thiếu cột) và báo cáo số dòng thành công/lỗi.
2. **Khoá tài khoản:** Tài khoản bị khoá sẽ tự động bị force logout (thu hồi token) ngay lập tức nếu đang online.
3. **Reset Password:** Admin bấm Reset sẽ gửi email chứa link đổi mật khẩu một lần (OTP link) đến email người dùng.

---

## 4. Màn hình Cài đặt Hệ thống (SCR-04-SYS-SETTINGS)

### 4.1. Mục đích
Nơi lưu trữ các cấu hình động, thông số nghiệp vụ chung không cần hard-code.

### 4.2. Quyền truy cập
- System Admin.

### 4.3. Mô tả giao diện
Chia làm các thẻ (Tabs):
- **Tab 1: Cấu hình Đào tạo (Training Rules)**
  - Tín chỉ tối thiểu/tối đa cho bậc Cử nhân.
  - Tín chỉ tối thiểu/tối đa cho bậc Thạc sĩ.
- **Tab 2: Cấu hình Hệ thống (System Configs)**
  - Thời gian hết hạn phiên đăng nhập (phút).
  - Dung lượng file tối đa cho phép upload (MB).
- **Tab 3: Cấu hình Mail Server (SMTP)**
  - Host, Port, Username, Password, Email gửi đi. Có nút "Gửi email test".

### 4.4. Quy tắc nghiệp vụ
Mỗi khi cấu hình thay đổi, hệ thống phải ghi log (Audit Log) ai là người đổi, giá trị cũ là gì, giá trị mới là gì. Các cấu hình này được cache lại bằng Redis để tối ưu truy xuất.

---

## 5. Màn hình Nhật ký Hoạt động (SCR-05-AUDIT-LOG)

### 5.1. Mục đích
Ghi nhận mọi thao tác quan trọng (Tạo/Sửa/Xóa/Duyệt) để phục vụ việc truy vết (Traceability) khi có tranh chấp hoặc lỗi dữ liệu.

### 5.2. Quyền truy cập
- System Admin, Ban Giám Hiệu.

### 5.3. Mô tả giao diện
- **Topbar:** Filter cực kỳ chi tiết.
  - Từ ngày - Đến ngày.
  - Người thực hiện (Dropdown search).
  - Loại hành động (CREATE, UPDATE, DELETE, LOGIN, APPROVE).
  - Bảng bị tác động (User, TrainingProgram, Syllabus).
- **Data Table:**
  - Thời gian (Timestamp).
  - Tên Người thực hiện (kèm ID).
  - IP Address.
  - Loại hành động.
  - Đối tượng (Target Entity).
  - Nút "Xem chi tiết" (Icon Mắt).

### 5.4. Cửa sổ Chi tiết (Diff View)
Khi click vào 1 dòng hành động UPDATE, hiển thị so sánh JSON:
- Dữ liệu cũ (Before).
- Dữ liệu mới (After).
Làm nổi bật (Highlight) trường dữ liệu bị thay đổi.

### 5.5. Quy tắc nghiệp vụ
1. **Bất biến (Immutable):** Dữ liệu Audit Log chỉ được thêm vào (Insert-only). Tuyệt đối không ai (kể cả Admin) có quyền xoá hay sửa log thông qua giao diện.
2. **Retention Policy (Chính sách lưu trữ):** Log lưu trữ trên DB chính trong 1 năm. Sau đó tự động chạy job đẩy sang cold storage (S3) để giảm tải database.
