# Phân tích Tác nhân và Phân quyền (Actors & Roles)

Tài liệu này xác định các nhóm người dùng (Tác nhân) tương tác với hệ thống Quản lý CTĐT, chức năng chính của họ và ma trận phân quyền (RBAC) để đảm bảo hệ thống hoạt động đúng quy trình bảo mật.

## 1. Danh sách Tác nhân (Actors)

Dưới đây là 5 nhóm tác nhân chính tham gia vào hệ thống trong Giai đoạn 1:

### 1.1. System Admin (Quản trị viên Hệ thống)
- **Định nghĩa:** Người chịu trách nhiệm quản trị vận hành kỹ thuật, thiết lập dữ liệu nền tảng cho hệ thống.
- **Vai trò:** Cấu hình hệ thống, tạo tài khoản ban đầu cho các Khoa/Phòng ban.

### 1.2. Program Manager (Người quản lý CTĐT)
- **Định nghĩa:** Thường là Cán bộ Phòng Đào tạo hoặc Trưởng khoa/Phó khoa, phụ trách trực tiếp một hoặc nhiều CTĐT.
- **Vai trò:** Là người điều phối chính, khởi tạo CTĐT, lên danh sách môn học, phân công giảng viên biên soạn và theo dõi tiến độ tổng thể.

### 1.3. Lecturer (Giảng viên)
- **Định nghĩa:** Các giảng viên được phân công biên soạn đề cương chi tiết môn học.
- **Vai trò:** Nhập liệu, soạn thảo và hoàn thiện đề cương môn học theo phân công trên hệ thống.

### 1.4. Reviewer (Tổ trưởng bộ môn L1 / Thành viên Hội đồng)
- **Định nghĩa:** Các chuyên gia, giảng viên kỳ cựu hoặc Tổ trưởng bộ môn (L1) được giao nhiệm vụ phản biện, đánh giá chất lượng CTĐT và đề cương.
- **Vai trò:** Xem xét nội dung, để lại nhận xét (comment/feedback) và yêu cầu chỉnh sửa (nếu có).

### 1.5. Approver (Trưởng khoa L2 / Ban Giám hiệu)
- **Định nghĩa:** Cấp xét duyệt trung gian hoặc cao nhất, quyết định việc thông qua CTĐT ở cấp Khoa hoặc Trường.
- **Vai trò:** Phê duyệt CTĐT (Online) và ghi nhận trạng thái lưu trữ bản cứng (Offline).

### 1.6. QA (Chuyên viên Đảm bảo chất lượng)
- **Định nghĩa:** Thành viên thuộc Ban Đảm bảo chất lượng (AUN-QA, ABET) của trường.
- **Vai trò:** Kiểm định sự phù hợp của chuẩn đầu ra (CLO/PLO), xem xét và xử lý các cảnh báo từ AI Validator.

---

## 2. Ma trận Phân quyền (Role-Based Access Control - RBAC)

Hệ thống áp dụng mô hình phân quyền dựa trên Role. Dưới đây là ma trận thao tác quyền hạn (C: Create, R: Read, U: Update, D: Delete, E: Execute/Approve).

| Module / Chức năng | Admin | Program Manager | Lecturer | Reviewer | Approver | QA |
| :--- | :---: | :---: | :---: | :---: | :---: | :---: |
| **Quản lý Tài khoản & Phân quyền** | C,R,U,D | R | - | - | - | - |
| **Quản lý Danh mục (Ngành, Khoa...)** | C,R,U,D | R | R | R | R | R |
| **Khởi tạo & Sửa đổi CTĐT** | R | C,R,U,D | R | R | R | R |
| **Phân công Giảng viên** | R | C,R,U | R (Xem việc của mình) | - | - | - |
| **Biên soạn Đề cương Môn học** | R | R | C,R,U | R | R | R |
| **Đánh giá / Comment (Feedback)** | R | C,R,U | R,U (Trả lời) | C,R,U | R | C,R,U |
| **Phê duyệt / Từ chối (Approval)** | R | R (Xem trạng thái) | - | E (Recommend) | E (Final Approve) | - |
| **Quản lý bản lưu Scan (Bản cứng)** | R | C,R,U | R | R | R, U | R |
| **Theo dõi tiến độ (Dashboard)** | R | R | R (Cá nhân) | - | R (Tổng quan) | R |
| **Kiểm định & Override AI (AI Validator)** | - | R | - | R | R | E (Override) |

*(Ghi chú: 
1. Quyền `U` đối với Lecturer trong Đề cương chỉ áp dụng khi Đề cương chưa được duyệt cuối cùng. Khi đã Approved, không ai được sửa ngoại trừ Manager xin mở khóa từ Admin.
2. **Quy tắc Ủy quyền (Delegation):** Khi một Approver (VD: Trưởng Khoa) ủy quyền cho một tài khoản khác (VD: Phó Khoa), hệ thống sẽ **tạm thời cấp quyền Approver** cho tài khoản đó trong thời gian ủy quyền. Mọi thao tác duyệt sẽ được lưu Audit Log với cờ `delegated_by` trỏ về ID người ủy quyền gốc để đảm bảo tính minh bạch tuyệt đối.)*
