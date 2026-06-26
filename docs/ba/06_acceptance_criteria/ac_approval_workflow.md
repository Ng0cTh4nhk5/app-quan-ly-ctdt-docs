# Tiêu chí Nghiệm thu (Acceptance Criteria): Luồng Phê duyệt

Tài liệu này cung cấp các kịch bản kiểm thử (Test Scenarios) bằng ngôn ngữ Gherkin (BDD) dành riêng cho đội ngũ QA/QC.

---

## Tính năng 1: Phê duyệt Đa cấp (Multi-level Sequencing)

**Scenario 1: Trưởng bộ môn duyệt (Level 1)**
> **Given** một đề cương đang ở trạng thái PENDING
> **And** tôi đăng nhập bằng Role "Tổ trưởng Bộ môn" (Level 1 Reviewer)
> **When** tôi bấm "Duyệt"
> **Then** trạng thái đề cương chuyển thành REVIEWED_L1
> **And** đề cương này sẽ xuất hiện trong Inbox của Trưởng Khoa (Level 2).

**Scenario 2: Trưởng khoa duyệt cuối (Level 2)**
> **Given** một đề cương đang ở trạng thái REVIEWED_L1
> **And** tôi đăng nhập bằng Role "Trưởng Khoa" (Level 2 Approver)
> **When** tôi bấm "Duyệt"
> **Then** trạng thái đề cương chuyển thành APPROVED
> **And** Giảng viên nhận được thông báo "Đề cương đã được duyệt chính thức".

**Scenario 3: Trưởng bộ môn từ chối (Level 1)**
> **Given** đề cương ở trạng thái PENDING
> **When** Tổ trưởng Bộ môn bấm "Yêu cầu sửa" (Reject)
> **Then** trạng thái chuyển thành REVISION
> **And** đề cương KHÔNG được đẩy lên cho Trưởng Khoa
> **And** Giảng viên nhận được yêu cầu sửa.

**Scenario 4: Quyền Override của Admin / BGH**
> **Given** đề cương đang ở trạng thái PENDING (chưa ai duyệt)
> **And** tôi đăng nhập bằng Role "Admin" hoặc "BGH"
> **When** tôi dùng quyền Override để bấm "Duyệt"
> **Then** trạng thái nhảy thẳng từ PENDING sang APPROVED (bỏ qua L1, L2)
> **And** hệ thống ghi Log cảnh báo "Duyệt vượt cấp bởi Admin".

---

## Tính năng 2: Duyệt Hàng loạt (Batch Review)

**Scenario 5: Duyệt nhiều đề cương cùng lúc**
> **Given** tôi đang ở tab "Cần tôi duyệt"
> **When** tôi tick chọn 3 đề cương đang ở trạng thái PENDING
> **And** tôi bấm nút "Duyệt tất cả đã chọn"
> **And** tôi nhập comment chung "Đạt yêu cầu" và Xác nhận
> **Then** cả 3 đề cương đồng loạt chuyển sang trạng thái duyệt tương ứng
> **And** hiển thị Toast "Đã duyệt thành công 3/3 đề cương".

**Scenario 6: Lỗi khi Batch Review chứa đề cương sai trạng thái**
> **Given** tôi tick chọn 2 đề cương: 1 cái PENDING, 1 cái APPROVED
> **When** tôi bấm nút "Duyệt tất cả đã chọn"
> **Then** hệ thống chặn lại và báo "Chỉ có thể thao tác với các đề cương đang chờ duyệt (PENDING)".

---

## Tính năng 3: Ủy quyền (Delegation)

**Scenario 7: Ủy quyền thành công**
> **Given** tôi là Trưởng Khoa A
> **When** tôi cài đặt ủy quyền cho Phó Khoa B từ ngày 01/06 đến 10/06
> **And** phạm vi ủy quyền là "Tất cả chương trình"
> **Then** trạng thái ủy quyền được lưu là ACTIVE
> **And** Phó Khoa B sẽ thấy các đề cương của Khoa A trong Inbox của họ.

**Scenario 8: Hết hạn ủy quyền tự động thu hồi**
> **Given** đã có rule ủy quyền từ Khoa A cho Phó Khoa B hết hạn vào 10/06
> **When** thời gian hệ thống là ngày 11/06
> **Then** cronjob hệ thống tự động đổi trạng thái is_active của rule thành FALSE
> **And** Phó Khoa B không còn quyền duyệt thay Khoa A nữa.

**Scenario 9: Không được tự ủy quyền cho chính mình**
> **Given** tôi đang tạo cấu hình ủy quyền
> **When** tôi chọn người được ủy quyền (Delegatee) chính là tên tôi
> **Then** hệ thống báo lỗi "Không thể ủy quyền cho chính mình".
