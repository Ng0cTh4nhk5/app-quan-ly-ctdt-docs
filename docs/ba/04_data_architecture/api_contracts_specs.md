# Đặc tả Cấu trúc API (API Contracts / Payloads)

Tài liệu này định nghĩa cấu trúc JSON chuẩn (Request/Response) cho các API cốt lõi trong hệ thống Quản lý CTĐT.

*Lưu ý: Mọi API Response thành công đều được bọc trong cấu trúc chuẩn: `{ "status": 200, "message": "Success", "data": { ... } }`. Đối với lỗi, xem file `api_error_response_structure.md`.*

---

## 1. Authentication & User Profile
### 1.1. Login (Xác thực)
*   **Endpoint:** `POST /api/v1/auth/login`
*   **Payload:** `{ "username": "gv01", "password": "password123" }`
*   **Response:** Token JWT kèm thông tin User + Role.
```json
{
  "status": 200, "message": "Login successful",
  "data": {
    "token": "eyJhbGciOi...",
    "user": { "id": 1, "username": "gv01", "full_name": "Nguyen Van A" },
    "roles": ["LECTURER", "REVIEWER"]
  }
}
```

### 1.2. Cập nhật Profile
*   **Endpoint:** `PUT /api/v1/users/profile`
*   **Payload:** `{ "full_name": "Nguyen Van A", "avatar_url": "https://s3/avatar.png" }`

---

## 2. Core System (Quản lý User & Cấu hình - Admin)
### 2.1. Lấy danh sách Người dùng (Có Phân trang & Filter)
*   **Endpoint:** `GET /api/v1/users?page=1&limit=20&role=LECTURER&status=ACTIVE`
*   **Response:**
```json
{
  "status": 200, "message": "Success",
  "data": {
    "items": [ { "id": 1, "username": "gv01", "status": "ACTIVE", "roles": [...] } ],
    "meta": { "total_items": 150, "total_pages": 8, "current_page": 1, "per_page": 20 }
  }
}
```

### 2.2. Tạo mới / Cập nhật User
*   **Endpoint:** `POST /api/v1/users` / `PUT /api/v1/users/{id}`
*   **Payload:** `{ "username": "gv02", "email": "gv02@uni.edu", "faculty_id": 5, "roles": [3] }`

### 2.3. Lấy Audit Logs
*   **Endpoint:** `GET /api/v1/system/audit-logs?user_id=1&from=2026-01-01&to=2026-01-31`

---

## 3. Quản lý Chương trình Đào tạo (Training Programs)
### 3.1. Tạo mới CTĐT (Draft)
*   **Endpoint:** `POST /api/v1/training-programs`
*   **Payload:** `{ "major_code": "IT01", "start_year": 2026, "degree_level": "BACHELOR" }`

### 3.2. Cập nhật Thông tin & PLO của CTĐT
*   **Endpoint:** `PUT /api/v1/training-programs/{id}`
*   **Payload:** 
```json
{
  "name_vi": "Công nghệ Thông tin",
  "plos": [
    { "plo_code": "PLO1", "description": "Kiến thức cơ sở..." },
    { "plo_code": "PLO2", "description": "Kiến thức chuyên ngành..." }
  ]
}
```

### 3.3. Sao chép CTĐT (Clone)
*   **Endpoint:** `POST /api/v1/training-programs/{id}/clone`
*   **Payload:** `{ "new_name_vi": "CNTT - 2027", "new_start_year": 2027 }`
*   **Response:** Trả về ID của CTĐT mới.

### 3.4. Nộp CTĐT để xét duyệt (Submit)
*   **Endpoint:** `POST /api/v1/training-programs/{id}/submit`
*   **Response:** 
```json
{
  "status": 200, "message": "Đã gửi phê duyệt thành công",
  "data": { "new_status": "PENDING" }
}
```

---

## 4. Quản lý Đề cương Môn học (Syllabuses)
### 4.1. Cập nhật Đề cương (Draft)
*   **Endpoint:** `PUT /api/v1/syllabuses/{id}/draft`
*   **Payload:** 
```json
{
  "description": "Môn học cấu trúc dữ liệu...",
  "prerequisite_courses": ["IT001"],
  "clos": [
    { "clo_code": "CLO1", "description": "Hiểu thuật toán", "mapped_plo_ids": [1, 2] }
  ],
  "content_schedule": [
    { "session_no": 1, "topic": "Giới thiệu chung", "teaching_method": "Lý thuyết" }
  ]
}
```

### 4.2. So sánh 2 Phiên bản (Diff View)
*   **Endpoint:** `GET /api/v1/syllabuses/{id}/diff?v1=1&v2=2`
*   **Response:** Cấu trúc chứa danh sách các field thay đổi (old_value, new_value).

### 4.3. Render PDF Preview (Read-only)
*   **Endpoint:** `GET /api/v1/syllabuses/{id}/preview`
*   **Response:** Dữ liệu đã định dạng sẵn cho Frontend render bản in sạch.

---

## 5. Quy trình Phê duyệt (Approval Workflow)
### 5.1. Duyệt / Từ chối 1 Đề cương (Review)
*   **Endpoint:** `POST /api/v1/approvals/syllabuses/{id}/review`
*   **Payload:** 
```json
{
  "action": "REJECT",
  "comment": "Thiếu nội dung thực hành tuần 5.",
  "attachment_urls": ["link_anh.png"],
  "version": 2
}
```
*Lưu ý: Bắt buộc truyền `version` để kiểm tra Optimistic Locking.*

### 5.2. Duyệt Hàng loạt (Batch Review)
*   **Endpoint:** `POST /api/v1/approvals/syllabuses/batch`
*   **Payload:** 
```json
{
  "items": [
    { "id": 101, "version": 2 },
    { "id": 102, "version": 1 }
  ],
  "action": "APPROVE",
  "comment": "Đã kiểm tra cấu trúc đạt yêu cầu."
}
```
*Lưu ý: Bắt buộc truyền cặp `id` và `version` để kiểm tra Optimistic Locking.*

### 5.3. Cài đặt Uỷ quyền (Delegation)
*   **Endpoint:** `POST /api/v1/delegations`
*   **Payload:** `{ "delegatee_id": 5, "start_date": "2026-06-01", "end_date": "2026-06-15", "scope": "SPECIFIC_PROGRAM", "target_id": 10 }`

---

## 6. Số hóa Tự động (Auto Digitization)
### 6.1. Upload File Hàng loạt
*   **Endpoint:** `POST /api/v1/digitization/upload/batch`
*   **Payload:** FormData chứa mảng files.
*   **Response:** Trả về danh sách `task_ids` để polling trạng thái.

### 6.2. Kiểm tra trạng thái AI Parsing
*   **Endpoint:** `GET /api/v1/digitization/status/{task_id}`
*   **Response:** Trả về trạng thái hiện tại (VD: `PROCESSING`, `COMPLETED`, `FAILED`, `DEAD_LETTER`). Nếu là `DEAD_LETTER`, chứng tỏ file bị lỗi nặng (Zip Bomb) hoặc parse AI thất bại quá giới hạn retry.

### 6.3. Lưu kết quả đối chiếu thủ công (Manual Mapping)
*   **Endpoint:** `POST /api/v1/digitization/tasks/{task_id}/confirm`
*   **Payload:** Dữ liệu form sau khi người dùng đã chỉnh sửa/xác nhận từ kết quả AI.

---

## 7. Báo cáo & Dashboard (Reporting)
### 7.1. Báo cáo Tiến độ (Progress Report)
*   **Endpoint:** `GET /api/v1/reports/progress?program_id=1`
*   **Response:** 
```json
{
  "data": {
    "total_courses": 40,
    "status_counts": { "APPROVED": 30, "PENDING": 5, "DRAFT": 5 }
  }
}
```

### 7.2. Lấy Ma trận PLO-CLO
*   **Endpoint:** `GET /api/v1/reports/matrix?program_id=1`

### 7.3. Yêu cầu Xuất File (Export Async)
*   **Endpoint:** `POST /api/v1/reports/export`
*   **Payload:** `{ "report_type": "PROGRESS_REPORT", "format": "EXCEL", "filters": {...} }`
*   **Response:** Trả về `export_id` để polling nhận link tải S3.

---

## 8. Notification Center
### 8.1. Lấy danh sách Thông báo
*   **Endpoint:** `GET /api/v1/notifications?is_read=false&page=1`

### 8.2. Đánh dấu Đã đọc
*   **Endpoint:** `PUT /api/v1/notifications/{id}/read`

### 8.3. Lưu Cài đặt Thông báo (Notification Settings)
*   **Endpoint:** `PUT /api/v1/notifications/settings`
*   **Payload:** 
```json
{
  "receive_emails": true,
  "in_app_approval_alerts": true,
  "daily_digest": false
}
```
