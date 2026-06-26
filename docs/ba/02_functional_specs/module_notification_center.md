# Đặc tả Chức năng: Trung tâm Thông báo (Notification Center)

Phân hệ chịu trách nhiệm thông tin liên lạc nội bộ, cảnh báo nhắc việc và gửi email hệ thống tới người dùng.

---

## 1. Giao diện Chuông thông báo (In-App Notification)

### 1.1. Vị trí hiển thị
*   Icon Chuông báo nằm trên Topbar, góc phải màn hình, có kèm Badge màu đỏ hiển thị số lượng thông báo chưa đọc.

### 1.2. Trải nghiệm người dùng (UX)
*   Khi Click vào icon chuông: Mở ra một Dropdown Panel hiển thị danh sách 10 thông báo mới nhất.
*   **Phân loại thông báo (Categorization):**
    *   *Nhắc việc (Tasks):* "Đề cương môn AI sắp đến hạn nộp (Còn 2 ngày)".
    *   *Hành động (Action Required):* "Bạn có 5 đề cương đang chờ duyệt."
    *   *Trạng thái (Status Update):* "Đề cương môn Lập trình Web của bạn đã được duyệt bởi Trưởng khoa."
    *   *Hệ thống (System Info):* "Hệ thống sẽ bảo trì vào 23:00 tối nay."
    *   *Cảnh báo Lỗi (System Error Alert):* "Tiến trình xử lý file PDF thất bại (Corrupted), vui lòng thử lại." (Dành cho xử lý các Background Jobs/Async tasks bị lỗi).
*   **Tương tác:**
    *   Bấm vào một thông báo: Tự động đánh dấu `Đã đọc` (Mark as read) và chuyển hướng (Redirect) người dùng đến màn hình chi tiết tương ứng (vd: màn hình sửa đề cương).
    *   Nút "Đánh dấu tất cả là đã đọc" (Mark all as read).
    *   Nút "Xem tất cả" (View all) dẫn sang trang Quản lý thông báo chi tiết.

---

## 2. Gửi Email Tự động (Email Notifications)

### 2.1. Mục đích
Đảm bảo người dùng không bỏ lỡ các công việc quan trọng ngay cả khi họ không đăng nhập vào hệ thống.

### 2.2. Các Trigger (Sự kiện) gửi Email
1.  **Phân công công việc:** Gửi email cho giảng viên khi họ được Trưởng khoa giao viết đề cương môn học mới.
2.  **Thông báo trễ hạn (Overdue Reminder):** Tự động gửi email vào 8:00 sáng mỗi ngày nếu công việc bị trễ hạn.
3.  **Thay đổi trạng thái duyệt:**
    *   Gửi email cho Reviewer khi có người nộp đề cương lên.
    *   Gửi email cho Giảng viên khi đề cương bị Reject (yêu cầu sửa) hoặc Approved.
4.  **Bảo mật:** Gửi email OTP khi Quên mật khẩu.

### 2.3. Quy tắc Thiết kế Email
*   Sử dụng HTML Template có logo trường, màu sắc chuẩn nhận diện thương hiệu.
*   Nội dung ngắn gọn, phải có nút **Call-to-Action (CTA)** (Ví dụ: Nút "Nhấn vào đây để xem chi tiết").

---

## 3. Cài đặt Thông báo (Notification Settings)

### 3.1. Mục đích
Cho phép người dùng cá nhân hóa trải nghiệm, tránh bị làm phiền (Spam) bởi quá nhiều email.

### 3.2. Giao diện Cài đặt
*   Nằm trong màn hình Hồ sơ cá nhân (Profile) > Tab "Cài đặt Thông báo".
*   Danh sách các loại sự kiện, mỗi sự kiện có 2 Checkbox/Toggle Switch: `In-App` và `Email`.
    *   Sự kiện: Khi tôi được phân công (Không thể tắt).
    *   Sự kiện: Khi đề cương của tôi được duyệt (Mặc định Bật).
    *   Sự kiện: Báo cáo tổng hợp hàng tuần (Mặc định Tắt).
*   Người dùng có quyền Tắt gửi Email cho các sự kiện không bắt buộc để giảm lượng mail.
