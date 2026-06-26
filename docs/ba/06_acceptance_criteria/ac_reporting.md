# Tiêu chí Nghiệm thu (Acceptance Criteria): Dashboard & Báo cáo

Tài liệu này cung cấp các kịch bản kiểm thử (Test Scenarios) bằng ngôn ngữ Gherkin (BDD) dành cho module Báo cáo (Reporting) và Dashboard.

---

## Tính năng 1: Role-based Dashboard

**Scenario 1: Dashboard của Giảng viên**
> **Given** tôi đăng nhập với quyền Giảng viên (LECTURER)
> **When** tôi truy cập trang chủ (Dashboard)
> **Then** tôi thấy danh sách "Đề cương cần biên soạn" của riêng tôi
> **And** tôi thấy số lượng đề cương đang bị trả về (Cần sửa)
> **And** tôi KHÔNG thấy biểu đồ thống kê tiến độ của toàn khoa.

**Scenario 2: Dashboard của Trưởng khoa (Program Manager / Approver)**
> **Given** tôi đăng nhập với quyền Trưởng khoa
> **When** tôi truy cập trang chủ (Dashboard)
> **Then** tôi thấy Widget "Đề cương cần tôi duyệt"
> **And** tôi thấy biểu đồ Doughnut Chart thể hiện trạng thái (Approved/Pending/Draft) của các đề cương thuộc Khoa mình.

---

## Tính năng 2: Báo cáo Tiến độ Biên soạn (Progress Report)

**Scenario 3: Lọc dữ liệu báo cáo**
> **Given** tôi đang ở trang "Báo cáo Tiến độ"
> **When** tôi chọn Filter: Năm học "2026", Trạng thái "PENDING"
> **And** tôi bấm "Lọc"
> **Then** bảng dữ liệu bên dưới chỉ hiển thị các đề cương chờ duyệt của năm 2026
> **And** biểu đồ cột bên trên được render lại khớp với số liệu trong bảng.

**Scenario 4: Xử lý Empty State**
> **Given** tôi đang ở trang "Báo cáo Tiến độ"
> **When** tôi chọn Filter mà không có dữ liệu nào khớp (VD: Năm 2030)
> **Then** bảng dữ liệu hiển thị hình ảnh "Không tìm thấy dữ liệu"
> **And** biểu đồ hiển thị dạng khung trống có chữ "Chưa có dữ liệu".

---

## Tính năng 3: Ma trận CLO-PLO (Matrix Report)

**Scenario 5: Render Ma trận thành công**
> **Given** tôi ở trang "Báo cáo Ma trận CLO-PLO"
> **When** tôi chọn một Chương trình Đào tạo cụ thể
> **Then** hiển thị một bảng lưới (Grid) với các cột là PLO (PLO1, PLO2...) và các hàng là Môn học/CLO
> **And** các ô có giao cắt (Mapping) sẽ được bôi màu xanh hoặc đánh dấu 'X'.

---

## Tính năng 4: Xuất File (Export & Download)

**Scenario 6: Xuất file Excel thành công**
> **Given** tôi đang xem Báo cáo Tiến độ có dữ liệu
> **When** tôi bấm nút "Xuất Excel"
> **Then** hiển thị Toast "Hệ thống đang chuẩn bị file, vui lòng đợi..."
> **And** sau vài giây, trình duyệt tự động tải xuống file `BaoCaoTienDo_2026.xlsx`.

**Scenario 7: Xuất hàng loạt Đề cương ra PDF (Tạo file ZIP)**
> **Given** tôi ở màn hình danh sách Đề cương
> **When** tôi tick chọn 10 đề cương
> **And** tôi chọn Hành động "Xuất PDF hàng loạt"
> **Then** hệ thống gọi API export bất đồng bộ (async)
> **And** tôi nhận được một thông báo (Notification) "File xuất đã sẵn sàng"
> **When** tôi click vào thông báo đó
> **Then** trình duyệt tải xuống file `DeCuong_Batch_10Files.zip`.

**Scenario 8: Xử lý Timeout khi xuất dữ liệu quá lớn**
> **Given** tôi bấm "Xuất Excel" cho một lượng dữ liệu khổng lồ (VD: 50.000 dòng)
> **When** API xử lý lâu hơn 30 giây
> **Then** Frontend không bị treo (frozen)
> **And** hệ thống chuyển sang chế độ chạy ngầm và hiện Toast "Dữ liệu lớn đang được xử lý ngầm. Link tải sẽ được gửi qua Thông báo khi hoàn tất".
