# Tiêu chuẩn Giao diện và Tiện ích UI (UI/UX Guidelines)

Để đảm bảo tính nhất quán (Consistency) và nâng cao trải nghiệm người dùng, nhóm phát triển Frontend cần tuân thủ các quy chuẩn thiết kế và tiện ích dưới đây.

## 1. Hệ thống Thiết kế (Design System)
*   **Typography:** Sử dụng Font chữ rõ ràng, chuẩn Google Fonts (ví dụ: Inter, Roboto). Kích cỡ font đảm bảo dễ đọc (Base size: 14px - 16px).
*   **Màu sắc (Colors):**
    *   Màu chủ đạo (Primary): Tùy biến theo bộ nhận diện thương hiệu của Trường Đại học.
    *   Màu trạng thái (Semantic Colors): Đỏ (Lỗi/Trễ hạn), Xanh lá (Thành công/Đã duyệt), Vàng (Cảnh báo/Đang xử lý), Xanh dương (Thông tin/Chờ duyệt).
*   **Bố cục (Layout):** Áp dụng kiến trúc Sidebar cố định bên trái (Chứa Menu điều hướng) và Header ở trên cùng (Chứa Profile, Thông báo). Khu vực chính (Main Content) có khả năng cuộn.

## 2. Tiêu chuẩn Nhập liệu (Input & Forms)
*   **Rich Text Editor:** Áp dụng cho các trường yêu cầu soạn thảo văn bản dài (Đề cương). Editor phải gọn nhẹ, loại bỏ các nút không cần thiết để tránh rối mắt, hỗ trợ Paste (dán) văn bản từ Word giữ nguyên định dạng cơ bản.
*   **Validation (Xác thực dữ liệu):**
    *   Client-side Validation: Báo lỗi ngay lập tức bằng dòng text đỏ nhỏ dưới ô Input khi người dùng chuyển tab hoặc gõ sai định dạng.
    *   Không vô hiệu hóa (Disable) nút Submit, thay vào đó khi người dùng bấm Submit sẽ cuộn màn hình đến vị trí ô bị lỗi đầu tiên và focus vào đó.
*   **Dynamic Arrays (Dữ liệu động):** Hỗ trợ thêm/xóa dòng trực tiếp cho các danh sách (Ví dụ: Thêm Chuẩn đầu ra).

## 3. Trạng thái & Phản hồi (States & Feedbacks)
*   **Loading State:** Sử dụng Skeleton Loading (khung xám nhấp nháy) thay vì màn hình trắng hoặc con xoay (Spinner) toàn màn hình khi tải trang.
*   **Empty State:** Thiết kế hình ảnh minh họa thân thiện và nút Call-to-action (VD: "Chưa có đề cương nào. Bấm vào đây để tạo mới") khi danh sách trống.
*   **Toast Notifications:** Các hành động thành công (Lưu thành công, Nộp thành công) hiển thị thông báo popup nhỏ ở góc màn hình tự biến mất sau 3 giây.
*   **Auto-save (Lưu tự động):** Các màn hình soạn thảo có cơ chế lưu nháp ngầm, hiển thị dòng text nhỏ "Đã lưu nháp lúc 10:05" ở góc màn hình.

## 4. Tối ưu Hiệu suất thao tác (Productivity)
*   **Tìm kiếm & Lọc (Search & Filter):** Các bảng (Tables) đều có thanh tìm kiếm (Instant Search - debounce 300ms) và bộ lọc nhiều tiêu chí.
*   **Keyboard Shortcuts:** Hỗ trợ một số phím tắt cơ bản cho Admin và Giảng viên (Ví dụ: `Ctrl+S` để lưu, `Esc` để đóng Modal).
