# Đặc tả Chức năng: Hệ thống Báo cáo & Thống kê (Reporting)

Phân hệ cung cấp các báo cáo định dạng chuẩn để phục vụ công tác kiểm định chất lượng (như AUN-QA, chuẩn Bộ GD&ĐT) và quản lý nội bộ.

---

## 1. Báo cáo Tiến độ Biên soạn (Progress Report)
*   **Mục đích:** Đo lường tiến độ thực hiện việc soạn thảo và phê duyệt đề cương của toàn trường hoặc từng khoa.
*   **Filters:** Khoa/Viện, Trạng thái, Khoảng thời gian.
*   **Cấu trúc hiển thị:** Bảng dữ liệu (Tên Đơn vị | Tổng số môn | Đã phân công | Đang soạn | Đã duyệt | Trễ hạn).

## 2. Báo cáo Ma trận Chuẩn đầu ra (PLO-CLO Matrix Report)
*   **Mục đích:** Báo cáo cốt lõi phục vụ kiểm định chất lượng. Chứa ma trận map giữa Chuẩn đầu ra chương trình (PLO) và Chuẩn đầu ra môn học (CLO).
*   **Filters:** Chọn 1 CTĐT cụ thể.
*   **Cấu trúc hiển thị:** 
    *   Trục tung (Dọc): Danh sách các môn học trong CTĐT.
    *   Trục hoành (Ngang): Danh sách các PLO (PLO1, PLO2...).
    *   Ô giao thoa (Cell): Mức độ đáp ứng (I, R, M) hoặc Checkbox (X).

## 3. Báo cáo Cơ cấu Tín chỉ (Credit Structure Report)
*   **Mục đích:** Đảm bảo cấu trúc chương trình học tuân thủ quy định về tỷ lệ phân bổ khối kiến thức.
*   **Filters:** CTĐT, Bậc đào tạo.
*   **Cấu trúc hiển thị:**
    *   Thống kê tỷ lệ % giữa: Khối kiến thức đại cương vs Chuyên ngành.
    *   Thống kê tỷ lệ % giữa: Tín chỉ Lý thuyết vs Thực hành/Đồ án.
    *   Biểu đồ cột chồng (Stacked Bar) trực quan.

## 4. Báo cáo Tải trọng Giảng viên (Lecturer Assignment Report)
*   **Mục đích:** Giúp Trưởng khoa xem khối lượng công việc biên soạn của từng giảng viên để điều phối hợp lý.
*   **Filters:** Khoa/Viện, Bộ môn.
*   **Cấu trúc hiển thị:** Tên Giảng viên | Số đề cương được giao | Số đề cương hoàn thành | Số đề cương bị trả về | Điểm đánh giá chất lượng (nếu có).

---

## 5. Tính năng Xuất dữ liệu (Exporting Engine)

### 5.1. Định dạng hỗ trợ
*   **Excel (.xlsx):** Dùng cho Progress Report, Credit Structure Report. Dữ liệu xuất ra giữ nguyên format header, màu sắc.
*   **PDF (.pdf):** Chuyên dùng cho PLO-CLO Matrix Report để chống tràn viền khi in ấn, phù hợp nộp minh chứng kiểm định.

### 5.2. Tối ưu Xử lý (Async Export)
*   Với CTĐT lớn (trên 100 môn học), việc sinh ma trận PDF mất thời gian. Hệ thống chuyển sang xử lý ngầm (Background Job).
*   Hiển thị thông báo: "Báo cáo đang được tạo. Hệ thống sẽ gửi thông báo cho bạn khi hoàn tất tải xuống."
