# Đặc tả Chức năng: Bảng Điều khiển (Dashboard)

Phân hệ này cung cấp cái nhìn tổng quan về hệ thống, hiển thị các số liệu thống kê và công việc cần xử lý dựa trên vai trò (Role-based) của người dùng.

---

## 1. Dashboard dành cho Ban Giám Hiệu (Executive Dashboard)

### 1.1. Mục đích
Cung cấp cái nhìn toàn cảnh về tiến độ xây dựng Chương trình đào tạo (CTĐT) toàn trường để phục vụ công tác quản lý và ra quyết định.

### 1.2. Thành phần Giao diện (Widgets)
*   **Thống kê tổng quan (KPI Cards):**
    *   Tổng số CTĐT đang áp dụng.
    *   Tổng số Môn học.
    *   Tỷ lệ Đề cương đã được duyệt toàn trường (%).
    *   Số lượng CTĐT đang trong giai đoạn soạn thảo/chờ duyệt.
*   **Biểu đồ (Charts):**
    *   *Biểu đồ Cột (Bar Chart):* Tiến độ hoàn thành Đề cương theo từng Khoa/Viện. Giúp nhận biết khoa nào đang chậm tiến độ.
    *   *Biểu đồ Tròn (Pie Chart):* Trạng thái Đề cương toàn trường (Draft, In Progress, Pending Approval, Approved).
*   **Danh sách công việc (Task List):**
    *   Danh sách CTĐT đang chờ BGH phê duyệt cuối cùng (Kèm nút "Duyệt nhanh").

---

## 2. Dashboard dành cho Trưởng Khoa / Reviewer (Manager Dashboard)

### 2.1. Mục đích
Quản lý tiến độ của các giảng viên trong khoa, phê duyệt đề cương cấp khoa.

### 2.2. Thành phần Giao diện (Widgets)
*   **Thống kê tổng quan (KPI Cards):**
    *   Tổng số Đề cương thuộc Khoa quản lý.
    *   Số lượng Đề cương đang chờ Trưởng Khoa duyệt.
    *   Số lượng Đề cương đã quá hạn soạn thảo (Overdue).
*   **Tiến độ Giảng viên (Leaderboard/Table):**
    *   Danh sách Giảng viên trong khoa kèm trạng thái: Số môn được giao, Số môn đã hoàn thành, Số môn trễ hạn.
*   **Danh sách chờ duyệt (Pending Approvals):**
    *   Danh sách các Đề cương mà Giảng viên vừa nộp, chờ Trưởng Khoa kiểm tra. Nút bấm đi thẳng tới màn hình Duyệt.

---

## 3. Dashboard dành cho Giảng viên (Lecturer Dashboard)

### 3.1. Mục đích
Tập trung vào các nhiệm vụ cá nhân (My Tasks) để giảng viên biết chính xác mình cần làm gì hôm nay.

### 3.2. Thành phần Giao diện (Widgets)
*   **Nhiệm vụ của tôi (My Tasks):**
    *   Hiển thị danh sách Môn học được phân công soạn đề cương. 
    *   Sắp xếp theo Deadline tăng dần (Việc khẩn cấp lên đầu).
    *   Màu đỏ nếu Overdue (Trễ hạn), màu vàng nếu sắp đến hạn (Dưới 3 ngày).
*   **Phản hồi gần đây (Recent Feedbacks):**
    *   Các Đề cương bị Trưởng Khoa từ chối (Reject) kèm lý do sửa. Bấm vào để đi đến màn hình chỉnh sửa.
*   **Lối tắt (Quick Actions):**
    *   Nút "Soạn đề cương mới" (Dẫn đến màn hình chọn môn học).
    *   Nút "Xem thư viện đề cương" (Xem các đề cương mẫu đã được duyệt của trường).

---

## 4. Dashboard dành cho Quản trị viên (System Admin Dashboard)

### 4.1. Mục đích
Theo dõi sức khỏe hệ thống và quản lý tài khoản.

### 4.2. Thành phần Giao diện (Widgets)
*   **Thống kê Hệ thống:**
    *   Tổng số Người dùng (Active / Locked).
    *   Dung lượng lưu trữ đã sử dụng (Ví dụ: File đính kèm, PDF scan).
    *   Số lượng truy cập trong ngày (Daily Active Users).
*   **Cảnh báo (System Alerts):**
    *   Hiển thị các thao tác bất thường từ Audit Log (Ví dụ: Đăng nhập sai nhiều lần, Xóa nhiều dữ liệu).
*   **Truy cập nhanh:**
    *   Link đến Quản lý Người dùng, Cài đặt Hệ thống, Audit Log.
