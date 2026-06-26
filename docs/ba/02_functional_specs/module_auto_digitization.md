# Đặc tả Chức năng: Giai đoạn 2 - Số hóa tự động (Auto Digitization)

Tài liệu này đặc tả quy trình chuyển đổi dữ liệu phi cấu trúc (File Word/PDF) của các năm học cũ thành dữ liệu có cấu trúc (Database) trên hệ thống.

---

## 1. Import Thư mục & File (Batch Upload)

### 1.1. Mục đích
Cho phép Quản trị viên (Program Manager/Admin) tải lên hệ thống một lúc nhiều file Đề cương hoặc toàn bộ bộ hồ sơ CTĐT.

### 1.2. Đặc tả Giao diện & Trải nghiệm (Asynchronous UX)
*   **Khu vực Tải lên (Dropzone):** Hỗ trợ kéo thả (Drag & Drop). Cho phép chọn nhiều file `.docx`, `.pdf`, hoặc một file nén `.zip`.
*   **Hàng đợi Tải lên (Upload Queue):** Hiển thị danh sách file đang upload kèm Progress Bar.
*   **Thông báo Bất đồng bộ (Async Notification):** Do quá trình OCR/Parsing tốn thời gian, hệ thống sẽ thực thi ngầm (Background Job). Khi Job chạy xong (thành công hoặc thất bại), hệ thống phải đẩy thông báo Real-time (qua WebSocket vào biểu tượng Quả chuông) hoặc gửi Email cho người dùng.

### 1.3. Ràng buộc Bảo mật & Kỹ thuật
*   **Chống Zip Bomb (Decompression Bomb):** 
    *   Giới hạn dung lượng giải nén tối đa là **500MB**.
    *   Giới hạn độ sâu thư mục con (Nesting depth) tối đa **3 levels**.
    *   Block toàn bộ các định dạng file lạ (chỉ cho phép `.pdf`, `.doc`, `.docx`).
*   **Kiểm tra trùng lặp (Duplicate Check):** Hệ thống tạo mã Hash (SHA-256) cho mỗi file upload. Nếu trùng mã Hash với file đã có, hiện cảnh báo "File đã tồn tại" để người dùng chọn Skip hoặc Overwrite.

---

## 2. Bóc tách Dữ liệu Tự động (Parsing Engine)

### 2.1. Logic bóc tách (Thuật toán)
*   **Nhận diện Header & Key-Value:** Dùng Regex tìm các cấu trúc như "I. Thông tin chung", `Tên môn học:`, `Số tín chỉ:`.
*   **Mức độ tự tin (Confidence Score):** Với mỗi trường dữ liệu bóc được, thuật toán gắn kèm một điểm tin cậy. Bất kỳ trường nào có **Score < 80%** sẽ tự động bị gắn cờ đỏ (Red Flag) bắt buộc con người phải xác nhận.

### 2.2. Xử lý File Hỏng (Poison Pill Handling)
*   Trong trường hợp hệ thống gặp một file PDF quá phức tạp hoặc bị hỏng (corrupted) làm Crash thuật toán OCR, hệ thống Background Job được phép Retry **tối đa 3 lần**.
*   Sau 3 lần thất bại, file này được chuyển vào **Dead Letter Queue (Hàng đợi lỗi)**. Trạng thái trên UI đổi từ `Đang xử lý` sang `Lỗi File Hỏng` để tránh việc file kẹt vĩnh viễn ở trạng thái chờ.

---

## 3. Giao diện Đối chiếu & Xác nhận (Manual Mapping)

### 3.1. Đặc tả Giao diện
*   **Kiến trúc Split-screen (Chia đôi màn hình):** Panel Trái hiển thị File Gốc (Highlight vàng đoạn text bóc tách được). Panel Phải hiển thị Form nhập liệu cấu trúc của hệ thống.

### 3.2. Quy tắc Nghiệp vụ Nghiêm ngặt
*   **Khóa Bản nháp (Smart Lock):** Khi User A bấm vào một Draft để thực hiện Review & Mapping, hệ thống lập tức Lock bản nháp đó. Nếu User B bấm vào, sẽ chỉ được xem ở chế độ Read-only kèm thông báo *"User A đang xử lý file này"*.
*   **Tải lên lại (Re-upload Versioning):** Nếu người dùng nhận ra file bị thiếu trang và upload lại file mới đè lên Draft cũ: 
    *   Hệ thống xóa toàn bộ Raw text cũ, chạy lại Engine OCR và reset điểm Confidence Score.
    *   UI phải hiện cảnh báo đỏ: *"Tải lại file sẽ xóa mọi chỉnh sửa thủ công bạn đã làm trước đó"*.
*   **Chuẩn hóa dữ liệu (Master Data Mapping):** Đối với các trường danh mục chuẩn (VD: Cấp độ Bloom, Chuẩn đầu ra PLO), hệ thống **NGHIÊM CẤM** việc nhập tay (Raw Text). Người dùng BẮT BUỘC phải chọn giá trị từ Dropdown List của Master Data. Nếu giá trị bóc tách được không tồn tại trong hệ thống, người dùng phải tạo Request gửi System Admin để bổ sung vào Master Data trước khi được phép Approve bản nháp.

### 3.3. Hành động
*   **Lưu Tạm (Save Draft):** Lưu quá trình rà soát để làm tiếp sau.
*   **Hoàn tất Số hóa (Approve & Convert):** Chuyển đổi bản nháp thành đối tượng Syllabus chính thức. File gốc được gắn vào mục "Tài liệu Pháp lý" đính kèm.
