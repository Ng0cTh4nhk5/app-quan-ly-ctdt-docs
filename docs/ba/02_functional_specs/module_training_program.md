# Đặc tả chức năng: Quản lý Chương trình đào tạo (Training Program)

Phân hệ cốt lõi để khởi tạo, cấu trúc và theo dõi trạng thái hoàn thành của toàn bộ môn học trong một Chương trình đào tạo (CTĐT).

---

## 1. Màn hình Danh sách CTĐT (SCR-TP-01)

### 1.1. Quyền truy cập
- Program Manager (Có quyền Thêm/Sửa/Xóa).
- Lecturer, Reviewer, Approver (Chỉ xem các CTĐT thuộc khoa của mình).

### 1.2. Mô tả giao diện
- **Thanh công cụ (Action Bar):** 
  - Nút `+ Tạo CTĐT mới`.
  - Bộ lọc (Filter): Theo trạng thái (Draft, Pending, Approved), Theo năm áp dụng, Theo cấp bậc (Cử nhân, Thạc sĩ).
  - Khung tìm kiếm: Theo tên, mã CTĐT.
- **Data Table (hoặc Card View):** 
  - Tên CTĐT | Ngành | Năm áp dụng | Tín chỉ | Tiến độ Đề cương (Progress Bar) | Trạng thái.
- **Nút Hành động (Actions) trên mỗi dòng:** Xem chi tiết, Sao chép (Clone), Sửa (nếu Draft), Xóa (nếu Draft).

---

## 2. Wizard Khởi tạo CTĐT (SCR-TP-02)

### 2.1. Mục đích
Hướng dẫn người dùng tạo CTĐT theo từng bước logic để không bị ngợp thông tin.

### 2.2. Chi tiết các Bước (Steps)

**Bước 1: Thông tin chung (General Info)**
- Tên CTĐT (Tiếng Việt & Tiếng Anh).
- Chọn Mã Ngành, Bậc Đào tạo, Năm áp dụng.
- Mục tiêu đào tạo (Rich-text editor).

**Bước 2: Chuẩn đầu ra chương trình (PLO - Program Learning Outcomes)**
- Cung cấp giao diện bảng nhập liệu nhanh (Inline Editing).
- Các cột: Mã PLO (Tự động tăng như PLO1, PLO2) | Mô tả chi tiết.
- Nút `+ Thêm dòng`.

**Bước 3: Khung chương trình (Curriculum Design)**
- Giao diện kéo thả (Drag & Drop) hoặc thêm môn học vào từng Học kỳ (Semester 1 -> 8).
- Mỗi học kỳ tính tổng số tín chỉ.
- Khi thêm môn, có thể chọn môn từ Ngân hàng Môn học chung hoặc Tự định nghĩa môn mới.

### 2.3. Quy tắc nghiệp vụ
- Tự động lưu nháp (Auto-save draft) khi chuyển đổi giữa các bước.
- Có thể thoát ra giữa chừng và làm tiếp vào lần sau.

---

## 3. Màn hình Chi tiết CTĐT (SCR-06-TP-DETAIL) - *[NEW]*

### 3.1. Mô tả giao diện
Khi nhấp vào một CTĐT trong danh sách, mở ra màn hình chi tiết gồm Header và các Tabs:

- **Header:** Tên CTĐT, Badge Trạng thái, Thanh Progress báo tỷ lệ các môn đã hoàn thành đề cương (VD: 80/100 tín chỉ đã có đề cương Approved).
- **Tab 1: Thông tin & PLO:** Xem lại nội dung ở Bước 1 & 2 của Wizard.
- **Tab 2: Cấu trúc (Curriculum) & Phân công:**
  - Hiển thị danh sách môn học nhóm theo Học kỳ.
  - Mỗi môn học hiển thị: Số TC, Tên GV phụ trách, Trạng thái Đề cương môn học (Chưa soạn / Đang soạn / Chờ duyệt / Đã duyệt).
  - Nút "Phân công GV" cho mỗi môn (Nếu có quyền Manager).
- **Tab 3: Phiên bản & Lịch sử (Version History):** Timeline các lần duyệt.

---

## 4. Luồng Phân công Giảng viên (SCR-TP-03)

### 4.1. Mục đích
Giao nhiệm vụ biên soạn (hoặc review) đề cương cho giảng viên cụ thể.

### 4.2. Giao diện (Modal)
- Bật lên khi bấm "Phân công" tại Tab 2 của màn Detail.
- Dropdown chọn Giảng viên (Có search autocomplete, chỉ hiện GV thuộc Khoa quản lý môn đó).
- Bộ chọn Ngày (Date-picker) để xét Hạn chót (Deadline).
- Ô nhập Ghi chú/Yêu cầu riêng.

### 4.3. Business Rules
- Khi lưu: Trạng thái môn học chuyển từ `Chưa phân công` sang `Chưa soạn`.
- Tự động sinh Notification và Email gửi cho GV được gán.
- Có thể đổi GV phân công nếu Đề cương chưa được duyệt cuối cùng. Khi đổi, hệ thống sẽ cảnh báo: "GV mới sẽ tiếp tục viết trên bản nháp của GV cũ, hoặc xoá làm lại".

---

## 5. Tính năng Sao chép CTĐT (Clone) (SCR-07-TP-CLONE) - *[NEW]*

### 5.1. Mục đích
Tạo nhanh một CTĐT khóa mới (VD: Khoá 2027) dựa trên khung của CTĐT khóa cũ (Khoá 2026).

### 5.2. Luồng thực thi
1. Click "Sao chép" tại CTĐT cũ.
2. Form hiện ra yêu cầu nhập: Tên mới, Năm áp dụng mới.
3. Checkbox tuỳ chọn: "Bao gồm cả các môn học" (Mặc định tick).
4. **Cảnh báo (Quan trọng):** "Quá trình này chỉ sao chép cấu trúc môn học. Các Đề cương chi tiết sẽ KHÔNG được sao chép sang. Bạn sẽ phải tiến hành phân công GV làm lại đề cương (hoặc kế thừa) cho CTĐT mới này."

---

## 6. Luồng Gửi Trình duyệt (Submit for Review) (SCR-08-TP-SUBMIT) - *[NEW]*

### 6.1. Mục đích
Sau khi các môn học đã được soạn và duyệt đề cương, Manager nộp toàn bộ CTĐT lên Ban Giám Hiệu duyệt tổng thể.

### 6.2. Validation Rules (Quy tắc kiểm tra trước khi nộp)
Hệ thống tự động chạy một Checklist, nếu tất cả Pass (xanh) mới hiện nút Submit:
- [ ] Tổng số tín chỉ nằm trong khoảng Quy định (VD: 120 - 150).
- [ ] 100% môn học trong Khung chương trình đã được Phân công Giảng viên.
- [ ] Tối thiểu X% (Cấu hình) môn học đã có Đề cương đạt trạng thái Approved.
- [ ] 100% Chuẩn đầu ra (PLO) phải có ít nhất 1 môn học mapping tới.

Nếu có mục rớt (Fail), nút Submit bị mờ (Disabled), người dùng có thể click vào dòng báo lỗi để xem danh sách chi tiết (VD: xem danh sách môn chưa được gán GV).

---

## 7. Màn hình Lịch sử Phiên bản (Version History) (SCR-09-TP-HISTORY) - *[NEW]*

### 7.1. Mục đích
Lưu giữ các bản Snapshot của CTĐT sau mỗi năm áp dụng hoặc mỗi lần điều chỉnh lớn.

### 7.2. Giao diện
- Hiển thị thanh Timeline dọc bên trái:
  - `Phiên bản 1.0 - Áp dụng 2026 (Ngày duyệt: 10/12/2025)`
  - `Phiên bản 1.1 - Cập nhật môn AI (Ngày duyệt: 05/06/2026)`
- Khi click vào một phiên bản cũ, mở ra giao diện Chi tiết CTĐT ở chế độ **Read-only hoàn toàn**, có gắn watermark hoặc banner vàng: "Đây là dữ liệu lịch sử, không thể chỉnh sửa."
