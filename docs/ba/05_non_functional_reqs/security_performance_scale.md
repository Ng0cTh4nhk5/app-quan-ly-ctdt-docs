# Yêu cầu Phi chức năng (Non-Functional Requirements)

Bên cạnh các tính năng nghiệp vụ, hệ thống cần đáp ứng các tiêu chí về mặt kỹ thuật, bảo mật và hiệu năng sau đây để đảm bảo vận hành ổn định tại quy mô một Trường Đại học.

## 1. Yêu cầu Bảo mật (Security Requirements)
*   **Bảo vệ dữ liệu đăng nhập:**
    *   Mật khẩu phải được mã hóa (hashing) chuẩn (VD: bcrypt/argon2) trước khi lưu vào database. Không lưu bản rõ (plaintext).
    *   Sử dụng cơ chế xác thực Token (JWT - JSON Web Token) có thời hạn (Expiration) để quản lý phiên làm việc.
*   **Phân quyền chặt chẽ (Authorization):**
    *   Kiểm tra quyền truy cập ở cả hai phía: Giao diện (ẩn các nút không có quyền) và Backend API (Middleware xác thực).
    *   Bảo vệ dữ liệu riêng tư: Giảng viên chỉ được xem và sửa đề cương của mình. Không được sửa của người khác trừ khi được cấp quyền.
*   **Bảo vệ chống tấn công web cơ bản:**
    *   Ngăn chặn XSS (Cross-site Scripting): Mã hóa đầu vào/đầu ra, đặc biệt từ Rich-text Editor.
    *   Ngăn chặn CSRF (Cross-Site Request Forgery) và SQL Injection (Sử dụng ORM/Query Builder).
    *   Bảo mật đường truyền bằng giao thức HTTPS.

*   **Bảo vệ Hệ thống Số hóa (Digitization Security):**
    *   Quét mã độc (Anti-virus) trước khi phân tích file PDF/Ảnh.
    *   **Chống Zip Bomb / Quá tải bộ nhớ:** Giới hạn dung lượng file upload ở mức 50MB. Giới hạn dung lượng tối đa *sau khi giải nén/phân tích (uncompressed)* cũng ở mức 50MB hoặc tối đa 100 trang PDF để chống tấn công cạn kiệt tài nguyên.
*   **Quản lý Lưu lượng và Lạm dụng (Rate Limiting):**
    *   Giới hạn số lượng request gọi AI (AI Generation/Parsing endpoints) trên mỗi user theo giờ/ngày để chống abuse.
*   **Lưu vết Hệ thống (Audit Trails):**
    *   Mọi thao tác thay đổi trạng thái (Phê duyệt, Từ chối), Uỷ quyền, và quá trình sinh dữ liệu từ AI phải được ghi log (Immutable Logs) và lưu trữ ít nhất 1 năm để phục vụ đối soát.

## 2. Yêu cầu Hiệu năng (Performance Requirements)
*   **Thời gian phản hồi (Response Time):**
    *   Các thao tác tải trang và query dữ liệu thông thường phải phản hồi dưới 1 giây.
    *   Thao tác Upload file (Bản scan PDF) hoặc Export file báo cáo phải có tiến trình hiển thị (Loading/Progress bar) và không làm treo hệ thống.
*   **Hiệu năng xử lý số lượng truy cập (Concurrency):**
    *   Hệ thống cần chịu tải tốt vào các khung giờ cao điểm (Ví dụ: Giai đoạn nộp đề cương đồng loạt của hàng trăm giảng viên).
    *   Đảm bảo hoạt động ổn định với ~500 người dùng truy cập đồng thời (CCU).
*   **Xử lý Bất đồng bộ (Asynchronous Processing):**
    *   Các tác vụ nặng (Số hoá PDF, Auto Mapping AI, Export báo cáo) PHẢI chạy nền (Background workers).
    *   Sử dụng **Dead Letter Queue (DLQ)**: Khi tác vụ chạy ngầm gặp lỗi (Poison Pill), tự động Retry tối đa 3 lần. Nếu vẫn thất bại, chuyển trạng thái sang `DEAD_LETTER` để tránh crash vòng lặp worker.
*   **AI SLA & Circuit Breaker:**
    *   Thời gian chờ tối đa (Timeout) cho các API gọi AI (LLM) là **30 giây**. Vượt quá 30 giây sẽ kích hoạt Circuit Breaker, tự động ngắt kết nối và fallback về logic Rule-based hoặc báo lỗi an toàn cho người dùng để không khóa tài nguyên server.

## 3. Khả năng Mở rộng và Lưu trữ (Scalability & Storage)
*   **Kiến trúc Cloud-Ready:**
    *   Backend và Database nên được tách biệt (stateless) để dễ dàng scale up/scale out bằng Container (Docker).
*   **Quản lý File (Storage):**
    *   File Scan PDF hoặc các file minh chứng nên được lưu trữ thông qua các dịch vụ Cloud Storage (như AWS S3) hoặc ổ cứng NAS thay vì lưu trực tiếp trong thư mục mã nguồn để đảm bảo an toàn và giảm tải cho Web Server.
    *   Giới hạn dung lượng upload file (Max 50MB/file) để tránh quá tải dung lượng.
*   **Quản lý Tương tranh CSDL (Database Concurrency):**
    *   Sử dụng **Optimistic Locking** (dựa vào cột `version`) cho các thao tác phê duyệt/cập nhật tài liệu nhằm giải quyết xung đột khi nhiều người thao tác cùng lúc, tránh tình trạng Deadlock CSDL.

## 4. Tương thích (Compatibility)
*   Hệ thống hoạt động tốt trên các trình duyệt hiện đại (Google Chrome, Firefox, Safari, Edge).
*   Giao diện quản lý (Dashboard) hỗ trợ **Responsive Design**, có thể xem tốt trên Tablet (iPad) và mức độ cơ bản trên màn hình Điện thoại thông minh (Mobile browser) đối với các tính năng đọc/duyệt thông báo. Giao diện soạn thảo Rich Text ưu tiên sử dụng trên PC/Laptop.
