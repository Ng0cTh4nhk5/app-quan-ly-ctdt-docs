# Ma trận Thông báo & Lỗi (Error Message Matrix)

Tài liệu này tổng hợp toàn bộ các thông báo (Success, Warning, Error) mà người dùng sẽ nhìn thấy trên giao diện (UI) dựa trên các Use Case và Error Code từ Backend. Mục tiêu là đảm bảo Voice & Tone chuyên nghiệp, nhất quán và thân thiện.

---

## 1. Nguyên tắc viết thông báo (Tone of Voice)

*   **Rõ ràng, trực tiếp:** Nói thẳng vấn đề, không dùng thuật ngữ kỹ thuật (VD: Không dùng "Lỗi 500 DB Connection", dùng "Hệ thống đang bảo trì").
*   **Có hướng dẫn (Actionable):** Nếu có lỗi, luôn nói cho người dùng biết họ cần làm gì tiếp theo (VD: "Vui lòng kiểm tra lại kết nối mạng").
*   **Lịch sự, không đổ lỗi:** Không dùng từ mang tính ra lệnh hoặc đổ lỗi cho người dùng (VD: Thay vì "Bạn nhập sai định dạng", dùng "Định dạng file không được hỗ trợ").

---

## 2. Thông báo Lỗi Hệ thống & Trạng thái (System Errors)

| Error Code Backend | HTTP | UI Component | Tiêu đề hiển thị | Nội dung chi tiết (Message) | Hành động gợi ý |
| :--- | :---: | :--- | :--- | :--- | :--- |
| `ERR_INTERNAL_SERVER` | 500 | Error Page / Toast Đỏ | Lỗi Hệ thống | Đã có lỗi xảy ra từ phía máy chủ. Vui lòng thử lại sau. | Nút "Tải lại trang" |
| `ERR_SERVICE_UNAVAILABLE` | 503 | Error Page | Đang Bảo trì | Hệ thống đang được bảo trì định kỳ để nâng cấp trải nghiệm. | Thử lại sau 15 phút. |
| `ERR_NETWORK` | N/A | Toast Đỏ | Lỗi Kết nối | Không thể kết nối tới máy chủ. Vui lòng kiểm tra lại Internet. | Nút "Thử lại" |
| `ERR_TOO_MANY_REQUESTS`| 429 | Toast Đỏ | Thao tác quá nhanh | Bạn đang gửi yêu cầu quá nhanh. Vui lòng chờ một lát. | Đợi 1 phút. |
| `ERR_CONCURRENT_EDIT` | 409 | Modal Cảnh báo| Lỗi Phiên bản | Đề cương này vừa được một Giảng viên khác cập nhật. | Nút "Tải lại trang (Bỏ qua bản đang sửa)" |

---

## 3. Thông báo Xác thực & Phân quyền (Auth & Permissions)

| Error Code Backend | HTTP | UI Component | Tiêu đề hiển thị | Nội dung chi tiết (Message) | Hành động gợi ý |
| :--- | :---: | :--- | :--- | :--- | :--- |
| `ERR_UNAUTHORIZED` | 401 | Toast Đỏ | Hết phiên đăng nhập | Phiên làm việc của bạn đã hết hạn. Vui lòng đăng nhập lại. | Chuyển hướng về `/login` |
| `ERR_FORBIDDEN` | 403 | Toast Đỏ / 403 Page | Không có quyền | Bạn không có quyền thực hiện thao tác này. | Liên hệ Admin. |
| `ERR_LOGIN_FAILED` | 401 | In-line Form | Sai thông tin | Tên đăng nhập hoặc mật khẩu không chính xác. | Nhập lại / Quên mật khẩu. |
| `ERR_ACCOUNT_LOCKED` | 403 | Modal | Tài khoản bị khóa | Tài khoản của bạn đã bị khóa tạm thời do nhập sai quá 5 lần. | Vui lòng chờ 15 phút. |

---

## 4. Thông báo Validation & Thao tác (Validation Errors)

| Error Code Backend | HTTP | UI Component | Tiêu đề | Nội dung chi tiết (Message) | Ghi chú |
| :--- | :---: | :--- | :--- | :--- | :--- |
| `ERR_MISSING_FIELD` | 400 | In-line Field | (Bên dưới ô input)| *Trường này không được bỏ trống.* | Bôi đỏ viền input. |
| `ERR_INVALID_EMAIL` | 400 | In-line Field | (Bên dưới ô input)| *Email không đúng định dạng (VD: example@uni.edu.vn).* | |
| `ERR_CLONE_EXISTS` | 409 | Toast Đỏ | Lỗi Sao chép | Tên Chương trình đào tạo này đã tồn tại trong hệ thống. | Yêu cầu đổi tên khác. |
| `ERR_SUBMIT_REJECT` | 400 | Modal | Không thể gửi duyệt | Chương trình chưa thỏa mãn điều kiện gửi duyệt. | Xem lại Checklist. |

---

## 5. Thông báo File & Upload (Media & Files)

| Error Code Backend | HTTP | UI Component | Tiêu đề | Nội dung chi tiết (Message) | Ghi chú |
| :--- | :---: | :--- | :--- | :--- | :--- |
| `ERR_FILE_TOO_LARGE` | 400 | Toast Đỏ / In-line| File quá lớn | Kích thước file vượt quá mức cho phép (Tối đa 50MB). | |
| `ERR_INVALID_FILE_TYPE`| 400 | Toast Đỏ / In-line| Sai định dạng file | Chỉ hỗ trợ tải lên các file có định dạng PDF, DOCX. | |
| `ERR_BATCH_UPLOAD_PARTIAL`| 207 | Modal | Hoàn tất một phần | Upload thành công 8/10 file. 2 file bị lỗi định dạng. | Hiển thị list file lỗi. |
| `ERR_PDF_GENERATION` | 500 | Toast Đỏ | Lỗi Xuất PDF | Hệ thống không thể tạo file PDF lúc này (Lỗi Font). | Thử xuất Excel thay thế. |

---

## 6. Các thông báo Thành công điển hình (Success Toasts)

Màu nền: Xanh lá cây (Green). Tự động biến mất sau 3-5 giây.

*   "Đã lưu bản nháp thành công."
*   "Cập nhật thông tin cá nhân thành công."
*   "Đã gửi Đề cương để phê duyệt."
*   "Phân công Giảng viên thành công."
*   "Đã phê duyệt 3/3 đề cương." (Duyệt hàng loạt)
*   "Cài đặt uỷ quyền đã được kích hoạt."
