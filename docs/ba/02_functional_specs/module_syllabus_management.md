# Đặc tả Chức năng: Phân hệ Biên soạn Đề cương (Syllabus Management)

Tài liệu này đặc tả chi tiết giao diện, trường dữ liệu và hành động của các màn hình thuộc phân hệ Biên soạn Đề cương (dành cho Giảng viên).

---

## 1. Màn hình "Việc của tôi" (My Tasks / My Syllabuses)
*   **Mục đích:** Nơi Giảng viên xem danh sách các môn học được phân công biên soạn đề cương kèm theo thời hạn (Deadline).
*   **Người dùng:** Lecturer (Giảng viên).
*   **URL dự kiến:** `/my-tasks`

### A. Thành phần Giao diện (UI Layout)
*   **Header Toolbar:** Tiêu đề "Nhiệm vụ biên soạn", bộ lọc trạng thái (Chưa bắt đầu, Đang soạn, Cần chỉnh sửa, Chờ duyệt, Đã duyệt).
*   **Main Content:** Danh sách hiển thị dạng Bảng (Data Table) để dễ dàng nhìn thấy Deadline.

### B. Đặc tả Dữ liệu hiển thị (Bảng)

| Tên cột | Định dạng hiển thị | Ý nghĩa |
| :--- | :--- | :--- |
| Tên môn học | Text in đậm (Clickable) | Chuyển sang màn hình Soạn thảo khi click. |
| CTĐT áp dụng | Text | Tên CTĐT mà môn học này thuộc về. |
| Deadline | Ngày (dd/mm/yyyy) | Chữ màu Đỏ nếu trễ hạn (Overdue). |
| Trạng thái | Badge màu sắc | `Todo` (Xám), `In Progress` (Vàng), `Revision Requested` (Cam), `Pending Approval` (Xanh dương), `Approved` (Xanh lá). |
| Hành động | Nút Icon (Edit) | Nút thao tác nhanh để vào màn hình soạn. |

---

## 2. Màn hình Soạn thảo Đề cương (Syllabus Editor)
*   **Mục đích:** Giao diện nhập liệu tập trung cho phép giảng viên soạn thảo chi tiết đề cương môn học.
*   **Người dùng:** Lecturer (Giảng viên được phân công).
*   **URL dự kiến:** `/syllabus/edit/{id}`

### A. Thành phần Giao diện (UI Layout)
*   **Sticky Topbar:** Luôn hiển thị trên cùng khi cuộn trang. Chứa nút "Lưu nháp", "Gửi trình duyệt", và dòng text nhỏ báo trạng thái Auto-save ("Đã lưu lúc 10:25").
*   **Sidebar (Trái):** Mục lục thu gọn (Table of Contents) (Thông tin chung, CLO, Nội dung, Tài liệu, Đánh giá) giúp nhảy nhanh (scroll) đến vùng nhập liệu.
*   **Main Content:** Dạng Single-page Form dài, chia thành các Card/Section tương ứng với Mục lục.

### B. Đặc tả Trường Dữ liệu Nhập liệu (Form Fields)

**Section 1: Thông tin chung (General Info)**

| Tên trường | Loại Input | Bắt buộc | Validation Rules |
| :--- | :--- | :---: | :--- |
| Tên môn học | Textbox | Yes | Read-only (Lấy từ Master Data). |
| Số tín chỉ | Number | Yes | Read-only. |
| Điều kiện tiên quyết | Dropdown (Multi) | No | Chọn từ danh sách môn học. |
| Mô tả vắn tắt | Text Area | Yes | Tối đa 1000 ký tự. |

**Section 2: Chuẩn đầu ra môn học (CLO - Course Learning Outcomes)**
*   Dạng: Dynamic Form Array.

| Tên trường | Loại Input | Bắt buộc | Validation Rules |
| :--- | :--- | :---: | :--- |
| Mã CLO | Textbox | Yes | Tự động sinh (CLO1, CLO2...). Read-only. |
| Mô tả CLO | Text Area | Yes | Nhập text chi tiết. |
| Ánh xạ PLO (Mapping) | Dropdown (Multi) | Yes | Bắt buộc chọn ít nhất 1 PLO của CTĐT tương ứng. Báo lỗi nếu lưu mà chưa chọn. |

**Section 3: Nội dung chi tiết (Content & Schedule)**
*   Dạng: Bảng động thêm dòng (Thêm Tuần học / Bài học).

| Tên trường | Loại Input | Bắt buộc | Validation Rules |
| :--- | :--- | :---: | :--- |
| Buổi / Tuần | Number | Yes | |
| Nội dung bài giảng | Rich-text | Yes | Chỉ cung cấp công cụ Bold, Italic, Bullet. |
| CLO tương ứng | Dropdown (Multi) | Yes | Chọn từ danh sách CLO đã định nghĩa ở Section 2. |

**Section 4: Tài liệu & Đánh giá (Materials & Assessment)**

| Tên trường | Loại Input | Bắt buộc | Validation Rules |
| :--- | :--- | :---: | :--- |
| Tài liệu bắt buộc | Rich-text | Yes | |
| Điểm Quá trình (%) | Number Input | Yes | Tổng % các cột điểm phải = 100%. Nếu != 100% hiện chữ Đỏ. |
| Điểm Cuối kỳ (%) | Number Input | Yes | |

### C. Hành động & Xử lý sự kiện (Actions)
*   **Tính năng Auto-save (Lưu tự động):**
    *   Cứ sau 3 phút hoặc khi người dùng ngưng gõ 10 giây (Debounce), hệ thống tự động gọi API lưu nháp ngầm (Background Save).
    *   Hiển thị thông báo nhỏ "Đã lưu nháp" trên Topbar.
*   **Nút "Lưu nháp" (Save Draft):**
    *   *Click:* Gọi API lưu ngay lập tức. Hiển thị Toast "Đã lưu bản nháp". Không validate các trường bắt buộc (Cho phép lưu dở dang).
*   **Nút "Gửi trình duyệt" (Submit for Approval):**
    *   *Click:* Hệ thống Trigger Client-side Validation.
    *   *Trường hợp lỗi:* Cuộn màn hình (Scroll to view) mượt mà đến vùng ô input bị lỗi đầu tiên, highlight viền đỏ.
    *   *Trường hợp thành công:* Hiện Modal xác nhận "Bạn không thể chỉnh sửa sau khi nộp. Xác nhận gửi?". Nếu "Đồng ý", chuyển trạng thái sang `Pending Approval` và khóa form (Read-only). Gửi Notification cho Reviewer.

### D. Quy tắc Nghiệp vụ (Business Logic)
*   **Ma trận CLO-PLO:** Hệ thống ngầm kiểm tra. Nếu có một PLO của CTĐT chưa được ánh xạ bởi bất kỳ CLO nào của môn học này, đưa ra cảnh báo (Warning) màu vàng khi bấm Submit (nhưng không chặn Submit).
*   **Quyền truy cập:** Chỉ user có ID khớp với `lecturer_id` được phân công mới mở được chế độ Edit. Người khác mở link sẽ là trang 403 Forbidden hoặc Read-only.

---

## 3. Lịch sử Cập nhật (Revision Log) & So sánh Phiên bản (Version Diff)
*   **Mục đích:** Giúp Giảng viên và Reviewer theo dõi những thay đổi giữa các phiên bản nháp hoặc giữa bản hiện tại và bản đã duyệt năm trước.

### A. Lịch sử Cập nhật (Revision Log)
*   **Vị trí:** Một Tab "Lịch sử" bên trong màn hình Soạn thảo hoặc màn hình Chi tiết Đề cương.
*   **Nội dung hiển thị:** Danh sách theo chiều dọc (Timeline) các mốc thời gian (vd: V1.0 - Đã duyệt, V1.1 Draft - Đang sửa, V1.2 - Chờ duyệt).
*   Mỗi mốc lịch sử lưu lại thông tin: Thời gian, Người sửa, Nội dung tóm tắt (nếu có nhập khi lưu).

### B. So sánh Phiên bản (Version Diff)
*   **Tính năng:** Nút "So sánh với bản gốc" hoặc "So sánh phiên bản".
*   **Giao diện Diff:** Tương tự như Github PR, hiển thị dạng hai cột (Split View) hoặc một cột (Unified View).
    *   Màu đỏ gạch ngang: Nội dung bị xóa.
    *   Màu xanh lá (highlight): Nội dung mới thêm vào.
*   **Quy tắc:** Chỉ cung cấp Diff cho các trường văn bản dài (Rich-text) và các trường thông tin quan trọng (Tín chỉ, CLO, PLO).

---

## 4. Chế độ Đọc & In ấn (Read-only & Print)
*   **Mục đích:** Cung cấp bản xem trước (Preview) chuẩn form mẫu để in ra PDF hoặc gửi đi.

### A. Chế độ Đọc (Read-only / Preview)
*   Giao diện trình bày tương tự như một tờ A4 trên trình duyệt, không có các khung nhập liệu, chỉ có Text hiển thị đẹp mắt.
*   Hỗ trợ Watermark trạng thái chìm dưới nền (Ví dụ: "DRAFT", "PENDING APPROVAL", "APPROVED").

### B. In ấn (Export to PDF/Word)
*   Nút `Xuất PDF` hoặc `Xuất Word`.
*   *Quy tắc Export:* Document được generate từ Backend dựa trên Template (Mẫu chuẩn của trường quy định).
*   Nếu Đề cương đã `Approved`, bản Export PDF sẽ có thêm chữ ký số hoặc mã QR xác thực.
