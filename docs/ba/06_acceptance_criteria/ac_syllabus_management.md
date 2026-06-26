# Tiêu chí Nghiệm thu (Acceptance Criteria): Quản lý Đề cương

Tài liệu này cung cấp các kịch bản kiểm thử (Test Scenarios) bằng ngôn ngữ Gherkin (BDD) dành riêng cho đội ngũ QA/QC hoặc Kiểm thử Tự động.

---

## Tính năng 1: Biên soạn Đề cương (Syllabus Editing)

**Scenario 1: Auto-save khi soạn thảo**
> **Given** tôi đang ở màn hình soạn thảo Đề cương (DRAFT)
> **When** tôi nhập nội dung vào ô "Mô tả môn học"
> **And** tôi ngừng gõ phím trong 3 giây
> **Then** hệ thống tự động gọi API lưu nháp
> **And** hiển thị dòng chữ nhỏ "Đã lưu bản nháp lúc 10:05".

**Scenario 2: Validation chặn nộp Đề cương khi thiếu CLO**
> **Given** tôi đang soạn thảo Đề cương
> **When** tôi chưa thêm bất kỳ Chuẩn đầu ra môn học (CLO) nào
> **And** tôi bấm nút "Nộp phê duyệt"
> **Then** hệ thống chặn lại và báo lỗi "Môn học phải có ít nhất 1 Chuẩn đầu ra (CLO)".

**Scenario 3: Validation tỷ trọng điểm số**
> **Given** tôi đang nhập tỷ trọng điểm ở phần Đánh giá
> **When** tôi nhập Điểm giữa kỳ là 50% và Điểm cuối kỳ là 40% (Tổng = 90%)
> **And** tôi bấm "Lưu" hoặc "Nộp phê duyệt"
> **Then** hệ thống báo lỗi "Tổng tỷ trọng điểm phải bằng đúng 100%".

**Scenario 4: Validation xác nhận trách nhiệm AI (AI Accountability)**
> **Given** tôi đã sử dụng tính năng "Generate with AI" để tạo dàn ý
> **When** tôi bấm "Nộp phê duyệt" nhưng chưa tick vào ô "Tôi xác nhận đã kiểm duyệt và chịu trách nhiệm về nội dung do AI tạo ra"
> **Then** hệ thống chặn lại và hiển thị cảnh báo đỏ ở khu vực Checkbox
> **And** nút Nộp phê duyệt bị disable cho đến khi Checkbox được tick.

---

## Tính năng 2: Chặn Xung đột Dữ liệu (Concurrency Control)

**Scenario 4: Chặn lưu khi có người khác đã sửa (Concurrent Edit Detection)**
> **Given** tôi và Giảng viên B cùng mở chung 1 đề cương để sửa
> **And** Giảng viên B đã bấm "Lưu nháp" thành công
> **When** tôi tiếp tục gõ và bấm "Lưu nháp"
> **Then** API trả về lỗi 409
> **And** hệ thống hiển thị thông báo "Đề cương này vừa được người khác cập nhật. Vui lòng tải lại trang để tránh mất dữ liệu mới".

---

## Tính năng 3: Giao diện Xem Trước (Read-only Preview / Print)

**Scenario 5: Render bản in chuẩn**
> **Given** Đề cương đã được điền đầy đủ dữ liệu
> **When** tôi bấm nút "Xem trước / In"
> **Then** hệ thống chuyển sang giao diện Document View (không có ô input)
> **And** hiển thị Header chứa Logo Trường, Tên Khoa
> **And** hiển thị Footer chứa số trang (1/5).

**Scenario 6: Tải xuống PDF**
> **Given** tôi đang ở giao diện "Xem trước"
> **When** tôi bấm "Xuất PDF"
> **Then** hệ thống tạo file PDF với tên định dạng `DeCuong_[MaMonHoc]_V1.pdf`
> **And** file tải xuống thành công không bị vỡ font Tiếng Việt.

---

## Tính năng 4: So sánh Phiên bản (Version Diff View)

**Scenario 7: Hiển thị Diff rõ ràng giữa 2 phiên bản**
> **Given** Đề cương có V1 và V2
> **When** tôi chọn "So sánh V1 và V2"
> **Then** màn hình chia làm 2 cột: Cột trái (V1, nền xám), Cột phải (V2, nền trắng)
> **And** đoạn text bị xoá ở V1 bị gạch ngang màu đỏ
> **And** đoạn text được thêm ở V2 được highlight màu xanh lá.

**Scenario 8: Không có thay đổi**
> **Given** tôi cố tình chọn so sánh V1 với chính V1
> **When** tôi bấm So sánh
> **Then** giao diện báo "Không có sự khác biệt nào giữa 2 phiên bản này".

---

## Tính năng 5: Lịch sử Góp ý (Revision Log)

**Scenario 9: Hiển thị Log góp ý**
> **Given** Đề cương từng bị từ chối 1 lần bởi Tổ trưởng với nhận xét "Thiếu thực hành"
> **When** tôi vào tab "Lịch sử duyệt" (Revision Log)
> **Then** tôi thấy 1 Timeline item ghi: "Tổ trưởng A đã Yêu cầu chỉnh sửa vào [Ngày giờ]. Nhận xét: Thiếu thực hành".

**Scenario 10: Form bổ sung giải trình khi nộp lại**
> **Given** Đề cương đang ở trạng thái REVISION (Bị trả về)
> **When** tôi sửa xong và bấm "Nộp lại"
> **Then** hệ thống yêu cầu tôi điền "Nội dung giải trình (đã sửa những gì)"
> **And** nội dung này sẽ được ghi vào Revision Log cho Reviewer đọc ở vòng sau.
