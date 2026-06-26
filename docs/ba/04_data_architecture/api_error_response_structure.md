# Cấu trúc Phản hồi Lỗi API (API Error Response Structure)

Tài liệu này định nghĩa một chuẩn JSON duy nhất cho mọi lỗi trả về từ Backend. Việc đồng nhất này giúp Frontend dễ dàng bắt lỗi (catch), hiển thị Toast thông báo, hoặc focus vào đúng trường form bị sai (Validation).

---

## 1. Cấu trúc Chuẩn (Standard Error JSON)

Khi API gặp lỗi, HTTP Status Code sẽ là `4xx` hoặc `5xx`, và Response Body phải có định dạng sau:

```json
{
  "status": 400,
  "error_code": "ERR_VALIDATION_FAILED",
  "message": "Dữ liệu đầu vào không hợp lệ.",
  "details": [
    {
      "field": "start_year",
      "issue": "Năm bắt đầu phải lớn hơn hoặc bằng năm hiện tại."
    },
    {
      "field": "major_code",
      "issue": "Mã ngành không được để trống."
    }
  ],
  "trace_id": "req_8f7b3a2c91",
  "timestamp": "2026-06-24T10:05:00Z"
}
```

### Giải thích các trường:
- `status`: HTTP Status Code (ví dụ: 400, 401, 403, 404, 409, 500).
- `error_code`: Chuỗi string (UPPER_SNAKE_CASE) dùng để định danh loại lỗi nội bộ. Frontend có thể dùng code này để mapping ra đa ngôn ngữ (i18n).
- `message`: Câu thông báo thân thiện, có thể hiển thị trực tiếp cho end-user.
- `details`: (Tùy chọn) Mảng chứa chi tiết lỗi. Thường dùng trong lỗi Validation form, chỉ rõ trường (`field`) nào bị sai và lỗi gì (`issue`).
- `trace_id`: ID duy nhất của request (Dùng cho Log/APM như Datadog, ELK để trace debug khi có bug production).
- `timestamp`: Thời điểm xảy ra lỗi.

---

## 2. Bảng Error Codes Phổ biến (Common Error Codes Matrix)

Dưới đây là một số mã lỗi chuẩn hóa nên được sử dụng xuyên suốt hệ thống:

### 2.1. Lỗi Xác thực & Phân quyền (Auth / Permissions - 401, 403)
| HTTP | Error Code | Message hiển thị mẫu | Ghi chú |
| :--- | :--- | :--- | :--- |
| `401`| `ERR_UNAUTHORIZED` | Phiên đăng nhập hết hạn hoặc token không hợp lệ. | Buộc Frontend đá về màn Login. |
| `403`| `ERR_FORBIDDEN` | Bạn không có quyền thực hiện hành động này. | |
| `403`| `ERR_ROLE_NOT_MATCH` | Chỉ Trưởng bộ môn mới được duyệt cấp 1. | Lỗi phân quyền cụ thể. |

### 2.2. Lỗi Validation (Client Errors - 400, 422)
| HTTP | Error Code | Message hiển thị mẫu | Ghi chú |
| :--- | :--- | :--- | :--- |
| `400`| `ERR_VALIDATION_FAILED`| Dữ liệu đầu vào không hợp lệ. | Luôn đi kèm mảng `details`. |
| `400`| `ERR_MISSING_REQUIRED_FIELD` | Thiếu trường dữ liệu bắt buộc. | |
| `400`| `ERR_FILE_TOO_LARGE` | Dung lượng file vượt quá 50MB. | Dùng cho upload file. |
| `400`| `ERR_INVALID_FILE_TYPE`| Hệ thống chỉ hỗ trợ định dạng PDF hoặc DOCX. | |

### 2.3. Lỗi Tài nguyên & Xung đột (Resource & Conflicts - 404, 409)
| HTTP | Error Code | Message hiển thị mẫu | Ghi chú |
| :--- | :--- | :--- | :--- |
| `404`| `ERR_RESOURCE_NOT_FOUND` | Đề cương hoặc CTĐT không tồn tại. | Dùng khi ID không tìm thấy. |
| `404`| `ERR_USER_NOT_FOUND` | Không tìm thấy người dùng. | |
| `409`| `ERR_CONFLICT_STATE` | Không thể duyệt đề cương đang ở trạng thái DRAFT. | Sai state machine. |
| `409`| `ERR_CONCURRENT_EDIT` | Đề cương này vừa được người khác cập nhật, vui lòng tải lại trang. | Dùng cho Version Mismatch (Concurrency). |

### 2.4. Lỗi Hệ thống & Bên thứ 3 (Server Errors - 500, 502, 503)
| HTTP | Error Code | Message hiển thị mẫu | Ghi chú |
| :--- | :--- | :--- | :--- |
| `500`| `ERR_INTERNAL_SERVER` | Có lỗi hệ thống xảy ra, vui lòng thử lại sau. | Dấu vết lỗi thật (stacktrace) KHÔNG bao giờ được trả về client. |
| `502`| `ERR_AI_PARSING_FAILED` | Engine bóc tách AI hiện không phản hồi. | Lỗi giao tiếp với microservice OCR/AI. |
| `429`| `ERR_TOO_MANY_REQUESTS` | Bạn thực hiện thao tác quá nhanh, hãy chờ một lát. | Rate limiting (chống spam / DDoS). |

---

## 3. Quy trình Xử lý ở Frontend (Best Practices)

1.  **Global Axios Interceptor:** Bắt tất cả lỗi ở một chỗ tập trung (Axios Interceptors).
    *   Nếu `status == 401`: Xóa token localStorage -> Redirect `/login`.
    *   Nếu `status >= 500`: Hiển thị màn hình lỗi hoặc Toast đỏ: "Hệ thống đang bảo trì".
2.  **Form Validation:** Nếu nhận được `ERR_VALIDATION_FAILED`, bóc tách mảng `details` để map trực tiếp lỗi đỏ vào dưới ô input (`field`) tương ứng.
3.  **Hỗ trợ kỹ thuật:** Khi người dùng phản hồi lỗi, Frontend nên hiển thị `trace_id` góc dưới Toast (VD: "Lỗi hệ thống. Trace ID: req_xxx") để Admin dễ dàng grep log.
