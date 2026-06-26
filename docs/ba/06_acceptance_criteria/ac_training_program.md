# Tiêu chí Nghiệm thu (Acceptance Criteria): Quản lý CTĐT

Tài liệu này cung cấp các kịch bản kiểm thử (Test Scenarios) bằng ngôn ngữ Gherkin (BDD) dành riêng cho đội ngũ QA/QC hoặc Kiểm thử Tự động.

---

## Tính năng 1: Phân quyền truy cập (Role-based Access)

**Scenario 1: Truy cập màn hình tạo CTĐT với quyền Program Manager**
> **Given** tôi đăng nhập với tài khoản có Role là "Program Manager"
> **When** tôi truy cập vào menu "Chương trình Đào tạo"
> **Then** tôi nhìn thấy nút "+ Tạo mới CTĐT"
> **And** tôi có thể bấm vào nút đó để mở form tạo mới.

**Scenario 2: Chặn truy cập với quyền LECTURER**
> **Given** tôi đăng nhập với tài khoản có Role là "LECTURER" (Giảng viên)
> **When** tôi truy cập vào menu "Chương trình Đào tạo"
> **Then** tôi không nhìn thấy nút "+ Tạo mới CTĐT"
> **And** nếu tôi cố tình nhập URL trang tạo mới, hệ thống trả về trang lỗi 403 "Không có quyền truy cập".

---

## Tính năng 2: Khởi tạo Chương trình đào tạo mới

**Scenario 3: Khởi tạo thành công khi nhập đầy đủ thông tin hợp lệ**
> **Given** tôi đang đăng nhập với quyền "Program Manager"
> **And** tôi đang ở màn hình "Khởi tạo CTĐT - Bước 1"
> **When** tôi chọn Ngành học "Công nghệ Thông tin"
> **And** tôi nhập Tên CTĐT là "CNTT Chất lượng cao"
> **And** tôi nhập Năm áp dụng là "Năm hiện tại + 1" (VD: 2027)
> **And** tôi bấm nút "Tiếp tục"
> **Then** hệ thống chuyển tôi sang "Bước 2 - Chuẩn đầu ra"
> **And** dữ liệu bản nháp được tự động lưu vào Database với trạng thái DRAFT.

**Scenario 4: Validation chặn người dùng khi nhập thiếu trường bắt buộc**
> **Given** tôi đang ở màn hình "Khởi tạo CTĐT - Bước 1"
> **When** tôi để trống trường "Tên CTĐT"
> **And** tôi bấm nút "Tiếp tục"
> **Then** hệ thống **không** chuyển sang Bước 2
> **And** hiển thị thông báo lỗi màu đỏ "Trường này không được bỏ trống" dưới ô Tên CTĐT.

**Scenario 5: Validation chặn khi nhập năm áp dụng trong quá khứ**
> **Given** tôi đang ở màn hình "Khởi tạo CTĐT - Bước 1"
> **When** tôi nhập Năm áp dụng là "2020"
> **And** tôi bấm nút "Tiếp tục"
> **Then** hiển thị thông báo lỗi "Năm bắt đầu áp dụng phải từ năm hiện tại trở đi".

---

## Tính năng 3: Sao chép Chương trình Đào tạo (Clone)

**Scenario 6: Clone CTĐT thành công**
> **Given** tôi đang xem chi tiết CTĐT "CNTT Khóa 2024"
> **When** tôi bấm nút "Sao chép CTĐT"
> **And** tôi điền Tên mới là "CNTT Khóa 2025"
> **And** tôi bấm "Xác nhận"
> **Then** hệ thống tạo ra một CTĐT mới có tên "CNTT Khóa 2025"
> **And** sao chép toàn bộ PLO và danh sách môn học từ Khóa 2024
> **And** KHÔNG sao chép nội dung Đề cương chi tiết (reset trạng thái môn học về TODO).

**Scenario 7: Chặn Clone khi trùng tên**
> **Given** hệ thống đã có CTĐT tên "CNTT Khóa 2025"
> **When** tôi thao tác Clone và đặt tên mới là "CNTT Khóa 2025"
> **Then** hệ thống báo lỗi "Tên Chương trình đào tạo đã tồn tại".

---

## Tính năng 4: Thiết lập Ma trận Chuẩn đầu ra (PLO)

**Scenario 8: Sinh mã PLO tự động**
> **Given** tôi đang ở màn hình "Khởi tạo CTĐT - Bước 2 (PLO)"
> **And** danh sách hiện tại đã có 2 dòng PLO (Mã là PLO1, PLO2)
> **When** tôi bấm nút "+ Thêm Chuẩn đầu ra"
> **Then** một dòng mới được chèn thêm vào cuối danh sách
> **And** ô "Mã PLO" của dòng mới tự động điền sẵn là "PLO3" (Read-only).

**Scenario 9: Xóa PLO có cảnh báo**
> **Given** danh sách PLO đang có "PLO1" đã được mapping với các môn học
> **When** tôi bấm vào nút "Xóa" ở dòng PLO1
> **Then** hệ thống hiển thị Modal xác nhận "Xóa PLO này sẽ gỡ bỏ ánh xạ của các môn học liên quan. Bạn chắc chắn chứ?"
> **When** tôi chọn "Đồng ý"
> **Then** dòng PLO1 biến mất khỏi giao diện và backend cập nhật ánh xạ.

---

## Tính năng 5: Phân công Giảng viên

**Scenario 10: Phân công Giảng viên thành công**
> **Given** tôi đang ở tab "Danh sách môn học"
> **And** môn học đang ở trạng thái `Chưa phân công` (TODO)
> **When** tôi chọn Giảng viên "Nguyễn Văn A" và Hạn chót "30/12/2026"
> **And** tôi bấm "Xác nhận Phân công"
> **Then** trạng thái môn học đổi thành `Đang soạn`
> **And** hệ thống gửi 1 email tự động đến email của Giảng viên.

**Scenario 11: Validation chặn Hạn chót trong quá khứ**
> **Given** tôi đang thực hiện phân công GV
> **When** tôi chọn Hạn chót là một ngày trong quá khứ
> **And** tôi bấm "Xác nhận Phân công"
> **Then** hệ thống chặn và báo lỗi "Hạn nộp đề cương phải lớn hơn ngày hiện tại".

---

## Tính năng 6: Gửi Trình duyệt CTĐT (Submit Checklist)

**Scenario 12: Chặn Submit khi chưa đạt đủ Tín chỉ tối thiểu**
> **Given** tôi đang ở trang Chi tiết CTĐT ở trạng thái DRAFT
> **And** tổng số tín chỉ của CTĐT hiện là 110 (Yêu cầu tối thiểu: 120)
> **When** tôi bấm nút "Gửi Trình duyệt"
> **Then** hệ thống chặn lại
> **And** hiển thị bảng Checklist báo đỏ ở mục "Tổng số tín chỉ ≥ 120 (Hiện tại: 110)".

**Scenario 13: Chặn Submit khi chưa phân công hết môn học**
> **Given** tổng tín chỉ đã đạt 120
> **And** có 1 môn học chưa được gán Giảng viên biên soạn
> **When** tôi bấm nút "Gửi Trình duyệt"
> **Then** hệ thống chặn lại
> **And** hiển thị bảng Checklist báo đỏ ở mục "100% môn học đã được phân công Giảng viên".

**Scenario 14: Chặn Submit khi có đề cương chưa được Duyệt (Approved)**
> **Given** tất cả các điều kiện trên đã thỏa mãn
> **And** có 1 môn học có trạng thái Đề cương là PENDING (Chưa được duyệt)
> **When** tôi bấm nút "Gửi Trình duyệt"
> **Then** hệ thống chặn lại
> **And** hiển thị bảng Checklist báo đỏ ở mục "100% đề cương môn học phải ở trạng thái APPROVED".

**Scenario 15: Gửi Trình duyệt thành công**
> **Given** tất cả các checklist đều xanh (Valid)
> **When** tôi bấm nút "Gửi Trình duyệt"
> **Then** trạng thái CTĐT chuyển sang PENDING
> **And** CTĐT bị khóa (Read-only) không cho phép sửa đổi PLO hay danh sách môn học.

---

## Tính năng 7: Quản lý Phiên bản CTĐT (Version History)

**Scenario 16: Xem Lịch sử Phiên bản**
> **Given** CTĐT "CNTT Khóa 2024" đã có 3 phiên bản được duyệt (V1, V2, V3)
> **When** tôi vào Tab "Lịch sử Phiên bản"
> **Then** tôi thấy một Timeline hiển thị V1, V2, V3 kèm ngày giờ và người duyệt.

**Scenario 17: Phục hồi Phiên bản (Restore)**
> **Given** tôi đang xem phiên bản V2 trong phần Lịch sử
> **When** tôi bấm nút "Phục hồi về bản này"
> **And** tôi xác nhận thao tác
> **Then** hệ thống tạo ra một bản DRAFT mới (V4) với nội dung copy y hệt từ V2.
