# Đặc tả Chức năng: Phân hệ Đánh giá & Phê duyệt

Tài liệu này đặc tả chi tiết giao diện, trường dữ liệu và hành động của các màn hình dành cho Reviewer (Người kiểm duyệt) và Approver (Trưởng khoa/Ban giám hiệu phê duyệt).

---

## 1. Màn hình Bảng điều khiển Kiểm duyệt (Approval Dashboard)
*   **Mục đích:** Hiển thị danh sách các Đề cương hoặc CTĐT đang chờ duyệt.
*   **Người dùng:** Reviewer, Approver.
*   **URL dự kiến:** `/approvals/pending`

### A. Thành phần Giao diện (UI Layout)
*   **Tabs View:** 
    *   Tab 1: Cần tôi duyệt (My Pending Approvals - Hiển thị mặc định)
    *   Tab 2: Lịch sử đã duyệt (Approval History).
*   **Danh sách (Table):** Hiển thị các thông tin: Tên tài liệu (Đề cương môn X), Loại tài liệu, Người nộp (Tên Giảng viên), Ngày nộp, Nút "Mở để Duyệt".

---

## 2. Màn hình Đánh giá Chi tiết & Comment (Review Workspace)
*   **Mục đích:** Xem chi tiết đề cương, đối chiếu, thêm bình luận (comment) và ra quyết định (Approve/Reject).
*   **Người dùng:** Reviewer, Approver.
*   **URL dự kiến:** `/approvals/review/{id}`

### A. Thành phần Giao diện (UI Layout)
*   **Layout:** Kiến trúc **Split-screen** (Chia đôi màn hình).
    *   *Cột Trái (65% width):* Hiển thị nội dung Đề cương chi tiết (Read-only dạng Document View).
    *   *Cột Phải (35% width):* Khu vực Panel Tương tác (Approval Panel) chứa Comment history và các nút Quyết định.

### B. Đặc tả Trường Dữ liệu (Approval Panel)

| Tên trường | Loại Input | Bắt buộc | Validation Rules |
| :--- | :--- | :---: | :--- |
| Nhận xét chung | Text Area | Yes (Nếu Reject) | Bắt buộc nhập lý do nếu chọn hành động Yêu cầu chỉnh sửa (Reject). |
| Đính kèm file | File Upload | No | Cho phép upload file minh chứng (Doc, PDF, ảnh). Max 10MB. |

### C. Hành động & Xử lý sự kiện (Actions)
*   **Optimistic Locking (Chống xung đột thao tác):** Khi bấm nút quyết định, hệ thống phải kiểm tra `version_id` của tài liệu. Nếu tài liệu đã bị người khác duyệt trước đó vài giây, hệ thống từ chối thao tác và báo lỗi *"Tài liệu đã được xử lý bởi người khác"*.
*   **Nút "Approve" (Đồng ý duyệt) - Nút màu Xanh:**
    *   *Click:* Hiện Modal xác nhận.
    *   *Xử lý:* Cập nhật trạng thái thành `Approved` (hoặc chuyển sang level duyệt tiếp theo). Hiện Toast "Đã duyệt thành công". Gửi Email cho Giảng viên báo tin vui.
*   **Nút "Yêu cầu chỉnh sửa" (Request Revision/Reject) - Nút màu Đỏ/Cam:**
    *   *Click:* Validate ô "Nhận xét chung". Nếu trống, highlight viền đỏ bắt nhập.
    *   *Xử lý:* Đổi trạng thái về `Revision Requested`. Mở khóa (Unlock) quyền Edit cho Giảng viên. Gửi Notification + Nội dung nhận xét cho Giảng viên.

### D. Tiện ích Inline Comment (Tính năng nâng cao)
*   **Bôi đen để Comment:** Tại Cột Trái (Nội dung đề cương), Reviewer có thể dùng chuột bôi đen (highlight) một dòng văn bản. Hệ thống hiện tooltip biểu tượng 💬. Bấm vào mở hộp thoại ghi chú trực tiếp vào dòng đó.

---

## 3. Cập nhật File Bản cứng (Upload Scanned PDF)
*   **Mục đích:** Admin tải lên bản Scan chữ ký đỏ để làm tài liệu Pháp lý.
*   **Quy tắc Nghiệp vụ:**
    *   Chỉ upload khi Đề cương `Approved`. File upload là Bản gốc Pháp lý duy nhất.
    *   **Thay thế File (Replace PDF):** Trong trường hợp upload nhầm file, Admin có quyền "Thay thế file PDF". Hành động này BẮT BUỘC tạo một Audit Log ghi rõ: `Replaced_By` (Người thay thế), `Reason` (Lý do thay thế, bắt buộc nhập), và `Old_File_URL` (Link file cũ để đối chiếu nếu cần).

---

## 4. Quy trình Phê duyệt Đa cấp & Cam kết (SLAs)

### A. Document Freezing (Khóa Trạng thái Tài liệu)
*   Ngay khi tài liệu được nộp và chuyển sang trạng thái `Pending Level 1`, hệ thống BẮT BUỘC KHÓA (Lock) quyền Edit và Delete đối với tác giả. Tác giả không thể tự ý sửa hay xóa tài liệu đang được duyệt, nhằm bảo vệ tính toàn vẹn dữ liệu (tránh Orphaned records).

### B. Cam kết Thời gian Xử lý (Approval SLA)
*   **Auto-Reminder (Nhắc nhở tự động):** Nếu tài liệu nằm ở trạng thái `Pending` quá **3 ngày**, hệ thống tự động gửi Email/Notification nhắc nhở Approver hiện tại.
*   **Auto-Escalate (Leo thang tự động):** Nếu quá **7 ngày** không được xử lý, tài liệu sẽ tự động leo thang (Escalate) lên Approver cấp cao hơn (hoặc thông báo cho System Admin để xử lý), tránh tình trạng hồ sơ bị "ngâm".

---

## 5. Duyệt Hàng loạt (Batch Review)
*   **Giao diện:** Cung cấp checkbox trong màn hình Dashboard.
*   **Quy tắc Nghiệp vụ:**
    *   Thực thi duyệt hàng loạt trong Background Job.
    *   **Partial Success (Thành công một phần):** Khác với giao dịch All-or-Nothing, nếu chọn duyệt 15 tài liệu nhưng có 5 tài liệu lỗi (do dính conflict hoặc chưa đủ điều kiện), hệ thống VẪN lưu thành công 10 tài liệu hợp lệ.
    *   **Báo cáo:** Trả về "Batch Operation Report" liệt kê rõ tài liệu nào pass, tài liệu nào fail kèm lý do cụ thể.

---

## 6. Ủy quyền Phê duyệt (Delegation)
*   **Mục đích:** Xử lý tình huống Approver vắng mặt.

### A. Thiết lập & Ràng buộc
*   **Timezone:** Thời gian ủy quyền bắt buộc thiết lập theo múi giờ hệ thống `UTC+7` đến cấp độ HH:mm.
*   **Delegation Scope (Phạm vi):** Ủy quyền không cấp toàn quyền truy cập tài khoản. Nó chỉ giới hạn cấp quyền duyệt hồ sơ CTĐT/Đề cương, không cấp quyền xem các module nhạy cảm khác (VD: Nhân sự, Báo cáo tài chính).
*   **Anti-Circular Delegation:** Hệ thống chặn việc ủy quyền vòng tròn (VD: A -> B, B -> A) hoặc ủy quyền chồng chéo thời gian cho cùng một chức vụ.

### B. Thực thi Uỷ quyền & Thu hồi
*   **Audit Log:** Khi Delegatee duyệt, lịch sử lưu: "Được duyệt bởi [Tên Delegatee] (Uỷ quyền bởi [Tên Delegator])".
*   **Early Revocation (Thu hồi sớm):** Delegator có thể bấm "Thu hồi ủy quyền" bất kỳ lúc nào trước hạn. Nếu thu hồi trong lúc Delegatee đang mở màn hình Review, quyền `Approve` của Delegatee sẽ bị khóa ngay lập tức (Hard lock) khi họ bấm nút Lưu.
