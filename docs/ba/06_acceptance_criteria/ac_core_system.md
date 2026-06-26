# Tiêu chí Nghiệm thu (Acceptance Criteria): Core System (Xác thực, Phân quyền, User, Settings)

Tài liệu này cung cấp các kịch bản kiểm thử (Test Scenarios) bằng ngôn ngữ Gherkin (BDD) dành cho các tính năng nền tảng của hệ thống.

---

## Tính năng 1: Đăng nhập (Authentication)

**Scenario 1: Đăng nhập thành công**
> **Given** tôi đang ở trang Đăng nhập
> **When** tôi nhập username "gv_test" và password đúng "123456"
> **And** tôi bấm "Đăng nhập"
> **Then** hệ thống cấp token và chuyển hướng tôi vào trang Dashboard
> **And** góc trên bên phải hiển thị tên của tôi.

**Scenario 2: Đăng nhập thất bại (Sai mật khẩu)**
> **Given** tôi đang ở trang Đăng nhập
> **When** tôi nhập username "gv_test" và password sai
> **Then** hiển thị thông báo lỗi "Tên đăng nhập hoặc mật khẩu không đúng".

**Scenario 3: Khóa tài khoản sau 5 lần nhập sai (Brute-force protection)**
> **Given** tôi nhập sai mật khẩu 5 lần liên tiếp
> **When** tôi thử đăng nhập lần thứ 6 dù nhập đúng hay sai
> **Then** hệ thống chặn lại và báo "Tài khoản đã bị tạm khóa 15 phút do nhập sai quá nhiều lần".

---

## Tính năng 2: Quản lý Hồ sơ cá nhân (User Profile)

**Scenario 4: Thay đổi mật khẩu thành công**
> **Given** tôi đang ở trang "Đổi mật khẩu"
> **When** tôi nhập mật khẩu cũ đúng
> **And** nhập mật khẩu mới thỏa mãn độ phức tạp (có chữ, số, ký tự đặc biệt, >= 8 ký tự)
> **And** nhập lại mật khẩu mới khớp nhau
> **And** bấm "Lưu thay đổi"
> **Then** hệ thống báo "Đổi mật khẩu thành công"
> **And** tất cả phiên đăng nhập hiện tại ở các thiết bị khác bị thoát ra (Force logout).

**Scenario 5: Upload Avatar sai định dạng**
> **Given** tôi đang ở phần "Cập nhật ảnh đại diện"
> **When** tôi tải lên một file có đuôi `.pdf`
> **Then** hệ thống báo lỗi "Chỉ hỗ trợ định dạng JPG, PNG, JPEG".

---

## Tính năng 3: Quản lý Người dùng (User Management - Admin)

**Scenario 6: Thêm mới người dùng thành công**
> **Given** tôi đăng nhập bằng quyền Admin và ở màn hình User Management
> **When** tôi bấm "+ Thêm User"
> **And** nhập các thông tin: username, email, chọn Khoa, chọn Role là LECTURER
> **And** bấm "Lưu"
> **Then** user mới được tạo ra
> **And** hệ thống tự động gửi email chứa thông tin đăng nhập và mật khẩu ngẫu nhiên tới email của user.

**Scenario 7: Chặn thêm trùng username hoặc email**
> **Given** đã có user với username "gv01"
> **When** tôi tạo user mới và nhập username "gv01"
> **Then** hệ thống báo "Tên đăng nhập đã tồn tại trong hệ thống".

**Scenario 8: Khoá tài khoản (Deactivate User)**
> **Given** tôi bấm "Khoá tài khoản" đối với user "gv02"
> **When** user "gv02" đang đăng nhập ở một tab khác
> **Then** mọi request API tiếp theo của "gv02" sẽ trả về 401 Unauthorized
> **And** tab của "gv02" bị văng ra màn hình Đăng nhập.

---

## Tính năng 4: Cài đặt Hệ thống (System Settings - Admin)

**Scenario 9: Thay đổi cấu hình tín chỉ**
> **Given** tôi ở màn hình "Cài đặt Hệ thống" (Quyền Admin)
> **When** tôi chỉnh "Số tín chỉ tối thiểu" từ 120 lên 130
> **And** tôi bấm "Lưu cấu hình"
> **Then** cấu hình được lưu
> **And** các CTĐT đang DRAFT khi Submit sẽ phải thỏa mãn tổng tín chỉ >= 130 mới được đi tiếp.

---

## Tính năng 5: Nhật ký Hệ thống (Audit Log)

**Scenario 10: Ghi log mọi hành động nhạy cảm**
> **Given** Admin vừa thay đổi quyền của user A từ LECTURER sang REVIEWER
> **When** tôi truy cập màn hình Audit Log
> **Then** xuất hiện 1 dòng log: "Admin đã cập nhật quyền cho user A từ LECTURER sang REVIEWER vào lúc 10:00"
> **And** tôi có thể bấm vào để xem cấu trúc JSON thay đổi chi tiết.

**Scenario 11: Lọc log theo thời gian**
> **Given** tôi ở màn hình Audit Log
> **When** tôi chọn Filter từ ngày "01/01/2026" đến "31/01/2026"
> **Then** bảng chỉ hiển thị các log phát sinh trong tháng 1.
