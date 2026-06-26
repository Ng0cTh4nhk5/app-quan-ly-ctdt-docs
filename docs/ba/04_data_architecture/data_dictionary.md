# Từ điển Dữ liệu (Data Dictionary - Physical Level)

Tài liệu này định nghĩa cấu trúc bảng ở mức Cơ sở dữ liệu vật lý (Physical Database Schema) dành riêng cho Backend Team. 

*Ghi chú chung:*
- Tất cả các bảng ngầm định có các trường Audit: `created_at` (TIMESTAMP), `updated_at` (TIMESTAMP), `created_by` (INT), `updated_by` (INT).
- Soft delete được áp dụng cho các dữ liệu quan trọng thông qua trường `is_deleted` (BOOLEAN, mặc định FALSE) hoặc `deleted_at` (TIMESTAMP).

---

## Nhóm 1: Quản trị Hệ thống & Phân quyền (System & Access Control)

### 1. Bảng `users` (Người dùng)
| Tên cột | Kiểu dữ liệu | PK/FK | Bắt buộc | Mặc định | Ghi chú |
| :--- | :--- | :---: | :---: | :--- | :--- |
| `id` | INT (Auto Inc) | PK | Yes | | Khóa chính. |
| `username` | VARCHAR(50) | | Yes | | Tên đăng nhập (Unique). |
| `email` | VARCHAR(100) | | Yes | | Email liên hệ (Unique). |
| `password_hash` | VARCHAR(255)| | Yes | | Mật khẩu đã mã hóa (Bcrypt/Argon2). |
| `full_name` | VARCHAR(100) | | Yes | | Họ và tên đầy đủ. |
| `avatar_url` | VARCHAR(500) | | No | NULL | URL ảnh đại diện. |
| `faculty_id` | INT | FK | No | NULL | Ref: `faculties(id)`. |
| `status` | ENUM | | Yes | 'ACTIVE' | 'ACTIVE', 'INACTIVE', 'LOCKED'. |

### 2. Bảng `roles` (Vai trò)
| Tên cột | Kiểu dữ liệu | PK/FK | Bắt buộc | Mặc định | Ghi chú |
| :--- | :--- | :---: | :---: | :--- | :--- |
| `id` | INT (Auto Inc) | PK | Yes | | |
| `code` | VARCHAR(50) | | Yes | | VD: 'ADMIN', 'MANAGER', 'REVIEWER', 'LECTURER'. Unique. |
| `name` | VARCHAR(100) | | Yes | | Tên hiển thị. |
| `description` | TEXT | | No | NULL | |

### 3. Bảng `user_roles` (Phân quyền người dùng - N:M)
| Tên cột | Kiểu dữ liệu | PK/FK | Bắt buộc | Mặc định | Ghi chú |
| :--- | :--- | :---: | :---: | :--- | :--- |
| `user_id` | INT | FK | Yes | | Ref: `users(id)`. |
| `role_id` | INT | FK | Yes | | Ref: `roles(id)`. |
*Composite PK: (user_id, role_id).*

### 4. Bảng `faculties` (Khoa / Viện / Bộ môn)
| Tên cột | Kiểu dữ liệu | PK/FK | Bắt buộc | Mặc định | Ghi chú |
| :--- | :--- | :---: | :---: | :--- | :--- |
| `id` | INT (Auto Inc) | PK | Yes | | |
| `code` | VARCHAR(20) | | Yes | | Mã Khoa (Unique). |
| `name` | VARCHAR(255) | | Yes | | Tên Khoa. |
| `parent_id` | INT | FK | No | NULL | Dùng cho cấu trúc cây (Viện -> Khoa -> Bộ môn). Ref: `faculties(id)`. |

---

## Nhóm 2: Cấu trúc Chương trình Đào tạo (Curriculum Structure)

### 5. Bảng `training_programs` (Chương trình đào tạo)
| Tên cột | Kiểu dữ liệu | PK/FK | Bắt buộc | Mặc định | Ghi chú |
| :--- | :--- | :---: | :---: | :--- | :--- |
| `id` | INT (Auto Inc) | PK | Yes | | |
| `major_code` | VARCHAR(20) | | Yes | | Mã ngành. Indexed. |
| `name_vi` | VARCHAR(255) | | Yes | | Tên Tiếng Việt. |
| `name_en` | VARCHAR(255) | | No | NULL | Tên Tiếng Anh. |
| `degree_level` | ENUM | | Yes | 'BACHELOR' | 'BACHELOR', 'MASTER', 'PHD', 'ASSOCIATE'. |
| `start_year` | SMALLINT | | Yes | | Năm áp dụng. |
| `status` | ENUM | | Yes | 'DRAFT' | 'DRAFT', 'PENDING', 'APPROVED', 'INACTIVE'. |
| `version` | INT | | Yes | 1 | Quản lý phiên bản CTĐT (Optimistic Locking). |
| `is_locked` | BOOLEAN | | Yes | FALSE | Cờ khoá tài liệu (Document Freezing) khi đang chờ duyệt. |

### 6. Bảng `program_learning_outcomes` (PLO - Chuẩn đầu ra CTĐT)
| Tên cột | Kiểu dữ liệu | PK/FK | Bắt buộc | Mặc định | Ghi chú |
| :--- | :--- | :---: | :---: | :--- | :--- |
| `id` | BIGINT (Auto Inc)| PK | Yes | | |
| `program_id` | INT | FK | Yes | | Ref: `training_programs(id)`. Indexed. |
| `plo_code` | VARCHAR(10) | | Yes | | VD: "PLO1". Composite Unique (program_id, plo_code). |
| `description` | TEXT | | Yes | | Nội dung chi tiết chuẩn đầu ra. |

### 7. Bảng `courses` (Môn học)
| Tên cột | Kiểu dữ liệu | PK/FK | Bắt buộc | Mặc định | Ghi chú |
| :--- | :--- | :---: | :---: | :--- | :--- |
| `id` | INT (Auto Inc) | PK | Yes | | |
| `code` | VARCHAR(20) | | Yes | | Mã môn học (Unique). |
| `name_vi` | VARCHAR(255) | | Yes | | |
| `name_en` | VARCHAR(255) | | No | NULL | |
| `credits` | DECIMAL(4,1) | | Yes | | Số tín chỉ. |
| `faculty_id` | INT | FK | Yes | | Khoa quản lý môn học. Ref: `faculties(id)`. |

---

## Nhóm 3: Đề cương chi tiết (Syllabus Details)

### 8. Bảng `syllabuses` (Đề cương môn học)
| Tên cột | Kiểu dữ liệu | PK/FK | Bắt buộc | Mặc định | Ghi chú |
| :--- | :--- | :---: | :---: | :--- | :--- |
| `id` | BIGINT (Auto Inc)| PK | Yes | | |
| `course_id` | INT | FK | Yes | | Ref: `courses(id)`. Indexed. |
| `training_program_id`| INT | FK | Yes | | Ref: `training_programs(id)`. Một đề cương gắn với 1 CTĐT cụ thể. |
| `lecturer_id` | INT | FK | Yes | | Người biên soạn. Ref: `users(id)`. |
| `description` | TEXT | | Yes | | Mô tả môn học. |
| `scanned_file_url`| VARCHAR(500) | | No | NULL | URL file PDF chữ ký đỏ trên Cloud (nếu là số hóa). |
| `status` | ENUM | | Yes | 'TODO' | 'TODO', 'DRAFT', 'PENDING', 'REVISION', 'APPROVED'. |
| `version` | INT | | Yes | 1 | Phiên bản đề cương (Optimistic Locking). |
| `is_locked` | BOOLEAN | | Yes | FALSE | Cờ khoá tài liệu (Document Freezing) khi đang chờ duyệt. |

### 9. Bảng `course_learning_outcomes` (CLO - Chuẩn đầu ra môn học)
| Tên cột | Kiểu dữ liệu | PK/FK | Bắt buộc | Mặc định | Ghi chú |
| :--- | :--- | :---: | :---: | :--- | :--- |
| `id` | BIGINT (Auto Inc)| PK | Yes | | |
| `syllabus_id` | BIGINT | FK | Yes | | Ref: `syllabuses(id)`. |
| `clo_code` | VARCHAR(10) | | Yes | | VD: "CLO1". |
| `description` | TEXT | | Yes | | Nội dung chi tiết chuẩn đầu ra môn học. |

### 10. Bảng `clo_plo_mapping` (Ma trận Mapping CLO và PLO)
| Tên cột | Kiểu dữ liệu | PK/FK | Bắt buộc | Mặc định | Ghi chú |
| :--- | :--- | :---: | :---: | :--- | :--- |
| `clo_id` | BIGINT | FK | Yes | | Ref: `course_learning_outcomes(id)`. |
| `plo_id` | BIGINT | FK | Yes | | Ref: `program_learning_outcomes(id)`. |
*Composite PK: (clo_id, plo_id).*

### 11. Bảng `syllabus_content_schedule` (Kế hoạch giảng dạy / Nội dung)
| Tên cột | Kiểu dữ liệu | PK/FK | Bắt buộc | Mặc định | Ghi chú |
| :--- | :--- | :---: | :---: | :--- | :--- |
| `id` | BIGINT (Auto Inc)| PK | Yes | | |
| `syllabus_id` | BIGINT | FK | Yes | | Ref: `syllabuses(id)`. |
| `session_no` | INT | | Yes | | Buổi học / Tuần học số mấy. |
| `topic` | VARCHAR(500) | | Yes | | Chủ đề bài học. |
| `teaching_method` | VARCHAR(255) | | No | NULL | Phương pháp giảng dạy (Lý thuyết, Thực hành,...). |
| `assessment_method`| VARCHAR(255) | | No | NULL | Phương pháp đánh giá. |

---

## Nhóm 4: Quy trình Phê duyệt & Lịch sử (Workflow & Auditing)

### 12. Bảng `approval_logs` (Nhật ký phê duyệt)
| Tên cột | Kiểu dữ liệu | PK/FK | Bắt buộc | Mặc định | Ghi chú |
| :--- | :--- | :---: | :---: | :--- | :--- |
| `id` | BIGINT (Auto Inc)| PK | Yes | | |
| `entity_type` | ENUM | | Yes | | 'SYLLABUS', 'TRAINING_PROGRAM'. |
| `entity_id` | BIGINT | | Yes | | ID của Đề cương hoặc CTĐT. Indexed. |
| `action` | ENUM | | Yes | | 'SUBMIT', 'APPROVE', 'REJECT', 'REQUEST_CHANGE'. |
| `comment` | TEXT | | No | NULL | Nhận xét của Reviewer/Approver. |
| `actor_id` | INT | FK | Yes | | Người thực hiện hành động. Ref: `users(id)`. |
| `level` | INT | | Yes | 1 | Cấp độ duyệt (1: Bộ môn, 2: Khoa, 3: Trường). |

### 12b. Bảng `approval_tasks` (Nhiệm vụ Phê duyệt & SLA)
| Tên cột | Kiểu dữ liệu | PK/FK | Bắt buộc | Mặc định | Ghi chú |
| :--- | :--- | :---: | :---: | :--- | :--- |
| `id` | BIGINT (Auto Inc)| PK | Yes | | |
| `entity_type` | ENUM | | Yes | | 'SYLLABUS', 'TRAINING_PROGRAM'. |
| `entity_id` | BIGINT | | Yes | | ID của Đề cương hoặc CTĐT. Indexed. |
| `assignee_id` | INT | FK | Yes | | Người được phân công duyệt. Ref: `users(id)`. |
| `due_date` | TIMESTAMP | | Yes | | Hạn chót phê duyệt (SLA Tracking). |
| `reminder_sent_at`| TIMESTAMP | | No | NULL | Thời gian đã gửi nhắc nhở gần nhất. |
| `escalation_level`| INT | | Yes | 0 | Mức độ leo thang (0: Bình thường, 1: Nhắc nhở, 2: Cấp trên). |
| `status` | ENUM | | Yes | 'PENDING' | 'PENDING', 'RESOLVED', 'ESCALATED'. |

### 13. Bảng `delegation_rules` (Quy tắc Uỷ quyền)
| Tên cột | Kiểu dữ liệu | PK/FK | Bắt buộc | Mặc định | Ghi chú |
| :--- | :--- | :---: | :---: | :--- | :--- |
| `id` | INT (Auto Inc) | PK | Yes | | |
| `delegator_id` | INT | FK | Yes | | Người uỷ quyền (Trưởng khoa...). Ref: `users(id)`. |
| `delegatee_id` | INT | FK | Yes | | Người được uỷ quyền. Ref: `users(id)`. |
| `start_date` | TIMESTAMP | | Yes | | Thời điểm bắt đầu uỷ quyền. |
| `end_date` | TIMESTAMP | | Yes | | Thời điểm kết thúc uỷ quyền. |
| `scope` | ENUM | | Yes | 'ALL' | 'ALL', 'SPECIFIC_PROGRAM'. |
| `target_id` | INT | | No | NULL | ID của CTĐT/Khoa nếu scope cụ thể. |
| `is_active` | BOOLEAN | | Yes | TRUE | Trạng thái uỷ quyền. |

### 14. Bảng `audit_logs` (Nhật ký hệ thống)
| Tên cột | Kiểu dữ liệu | PK/FK | Bắt buộc | Mặc định | Ghi chú |
| :--- | :--- | :---: | :---: | :--- | :--- |
| `id` | BIGINT (Auto Inc)| PK | Yes | | |
| `user_id` | INT | FK | No | NULL | Người thực hiện (nếu là user). |
| `action` | VARCHAR(100) | | Yes | | VD: 'USER_LOGIN', 'SETTINGS_UPDATED'. |
| `resource_type` | VARCHAR(100) | | No | NULL | VD: 'USER', 'SYSTEM_CONFIG'. |
| `resource_id` | VARCHAR(100) | | No | NULL | ID của đối tượng bị tác động. |
| `details` | JSON | | No | NULL | Lưu trữ chi tiết thay đổi (old_value, new_value). |
| `ip_address` | VARCHAR(45) | | No | NULL | |

### 14b. Bảng `digitization_jobs` (Tiến trình Số hoá Background)
| Tên cột | Kiểu dữ liệu | PK/FK | Bắt buộc | Mặc định | Ghi chú |
| :--- | :--- | :---: | :---: | :--- | :--- |
| `id` | BIGINT (Auto Inc)| PK | Yes | | |
| `user_id` | INT | FK | Yes | | Người upload file. Ref: `users(id)`. |
| `file_url` | VARCHAR(500) | | Yes | | Đường dẫn S3 của file gốc. |
| `file_size_mb`| DECIMAL(5,2) | | Yes | | Kích thước file (kiểm soát dung lượng, tránh Zip Bomb). |
| `status` | ENUM | | Yes | 'PENDING' | 'PENDING', 'PROCESSING', 'COMPLETED', 'FAILED', 'DEAD_LETTER'. |
| `retry_count` | INT | | Yes | 0 | Số lần retry thất bại. Quá giới hạn -> DEAD_LETTER. |
| `error_message`| TEXT | | No | NULL | Lỗi exception (nếu có). |

### 14c. Bảng `ai_provenance_logs` (Lưu vết AI Generation)
| Tên cột | Kiểu dữ liệu | PK/FK | Bắt buộc | Mặc định | Ghi chú |
| :--- | :--- | :---: | :---: | :--- | :--- |
| `id` | BIGINT (Auto Inc)| PK | Yes | | |
| `entity_type` | VARCHAR(50) | | Yes | | Bảng bị tác động bởi AI (VD: 'SYLLABUS_CONTENT_SCHEDULE'). |
| `entity_id` | BIGINT | | Yes | | ID của record bị tác động. |
| `model_version`| VARCHAR(100) | | Yes | | Model AI đã dùng (VD: 'gemini-1.5-pro-v1'). |
| `prompt_hash` | VARCHAR(255) | | Yes | | Hash của prompt để tracking thay đổi prompt. |
| `tokens_used` | INT | | No | NULL | Số lượng token (input + output). |
| `ai_fallback_warning` | BOOLEAN | | Yes | FALSE | TRUE nếu output sinh ra lỗi JSON và kích hoạt Rule-based Fallback. |

---

## Nhóm 5: Tương tác & Tiện ích (Interactions & Utilities)

### 15. Bảng `notifications` (Thông báo)
| Tên cột | Kiểu dữ liệu | PK/FK | Bắt buộc | Mặc định | Ghi chú |
| :--- | :--- | :---: | :---: | :--- | :--- |
| `id` | BIGINT (Auto Inc)| PK | Yes | | |
| `user_id` | INT | FK | Yes | | Người nhận. Ref: `users(id)`. |
| `type` | ENUM | | Yes | | 'SYSTEM', 'APPROVAL', 'DEADLINE', 'DIGITIZATION'. |
| `title` | VARCHAR(255) | | Yes | | Tiêu đề thông báo. |
| `content` | TEXT | | Yes | | Nội dung HTML/Text. |
| `is_read` | BOOLEAN | | Yes | FALSE | Đã đọc hay chưa. |
| `reference_url` | VARCHAR(500) | | No | NULL | Link chuyển hướng khi click. |

### 16. Bảng `report_exports` (Lịch sử Xuất báo cáo)
| Tên cột | Kiểu dữ liệu | PK/FK | Bắt buộc | Mặc định | Ghi chú |
| :--- | :--- | :---: | :---: | :--- | :--- |
| `id` | BIGINT (Auto Inc)| PK | Yes | | |
| `report_type` | VARCHAR(50) | | Yes | | VD: 'PROGRESS_REPORT', 'PLO_CLO_MATRIX'. |
| `file_url` | VARCHAR(500) | | No | NULL | Link tải file (S3) sau khi gen xong (nếu chạy async). |
| `status` | ENUM | | Yes | 'PROCESSING'| 'PROCESSING', 'COMPLETED', 'FAILED'. |
| `requested_by` | INT | FK | Yes | | Người yêu cầu. Ref: `users(id)`. |
