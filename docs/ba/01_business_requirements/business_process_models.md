# Sơ đồ Quy trình Nghiệp vụ (Business Process Models)

Tài liệu này mô tả trực quan các luồng quy trình nghiệp vụ (Workflows) trên Hệ thống Quản lý Chương trình Đào tạo bằng biểu đồ Sequence. Các sơ đồ này là cơ sở để phát triển các chức năng và thiết kế UI/UX ở các giai đoạn sau.

---

## 1. Luồng Phê duyệt Đa cấp (Multi-level Approval Workflow)

Đây là luồng cốt lõi mô tả cách một Đề cương được duyệt qua nhiều cấp (Tổ trưởng -> Trưởng Khoa -> BGH).

```mermaid
sequenceDiagram
    autonumber
    actor Lecturer as Giảng viên
    actor L1 as Tổ trưởng Bộ môn (Reviewer L1)
    actor L2 as Trưởng Khoa (Approver L2)
    actor BGH as Ban Giám hiệu (Final Approver)
    actor Sys as Hệ thống
    
    Lecturer->>Lecturer: Hoàn thiện Đề cương
    Lecturer->>L1: Gửi Trình duyệt (Status: PENDING)
    
    alt Tổ trưởng từ chối
        L1-->>Lecturer: Yêu cầu sửa (Status: REVISION)
        Lecturer->>L1: Sửa xong, nộp lại
    else Tổ trưởng đồng ý
        L1->>L2: Phê duyệt Cấp 1 (Status: REVIEWED_L1)
    end
    
    alt Trưởng Khoa từ chối
        L2-->>Lecturer: Trả về yêu cầu sửa (Status: REVISION)
    else Trưởng Khoa đồng ý
        L2->>BGH: Chuyển lên BGH (Status: REVIEWED_L2)
    end

    alt BGH từ chối
        BGH-->>L2: Trả về yêu cầu sửa (Status: REVISION)
    else BGH đồng ý
        BGH->>Sys: Phê duyệt Cuối (Status: APPROVED)
        Sys-->>Lecturer: Gửi Email Thông báo Thành công
    end
```

*(Ghi chú: L1 và L2 đóng vai trò là Reviewer/Approver cấp trung gian tuỳ thuộc vào cấu hình của từng Khoa. BGH hoặc Hội đồng Khoa học đóng vai trò Final Approver. Quản lý CTĐT (Program Manager) chỉ theo dõi luồng này.*

**👉 Ràng buộc Chống Tự duyệt (Anti Self-Approval):** Hệ thống chặn tuyệt đối việc một User vừa đóng vai trò người soạn thảo (Lecturer) vừa đóng vai trò duyệt chính đề cương đó ở cấp L1/L2. Nếu Tổ trưởng bộ môn soạn đề cương, đề cương phải được đẩy lên cấp L2 hoặc một Reviewer khác được chỉ định duyệt, không được phép Tự duyệt ở cấp L1.)*

---

## 2. Luồng Uỷ quyền Phê duyệt (Delegation Workflow)

Mô tả cách một Trưởng Khoa (Delegator) ủy quyền cho Phó Khoa (Delegatee) duyệt thay mình trong một khoảng thời gian.

```mermaid
sequenceDiagram
    autonumber
    actor Boss as Trưởng Khoa (Delegator)
    actor Vice as Phó Khoa (Delegatee)
    actor Sys as Hệ thống
    actor Lecturer as Giảng viên
    
    Boss->>Sys: Tạo thiết lập Ủy quyền (Chọn Phó Khoa, Ngày bắt đầu/kết thúc)
    Sys-->>Vice: Gửi thông báo "Bạn được ủy quyền duyệt đề cương"
    Sys->>Sys: Kích hoạt Ủy quyền (is_active = true)
    
    Lecturer->>Sys: Gửi Trình duyệt Đề cương lên Khoa
    Sys->>Sys: Kiểm tra có Rule Ủy quyền đang Active không?
    Sys->>Vice: Chuyển Đề cương vào Inbox của Phó Khoa
    
    Vice->>Sys: Thực hiện Phê duyệt thay (Approve/Reject)
    Sys->>Sys: Ghi Log: "Được duyệt bởi Phó Khoa (Uỷ quyền từ Trưởng Khoa)"
    
    Note over Sys: Khi đến ngày hết hạn hoặc Hủy ngang
    Sys->>Sys: Cronjob tự động vô hiệu hóa (is_active = false)
    Sys->>Sys: Thu hồi các Đề cương chưa duyệt về lại Inbox Trưởng Khoa
    Sys-->>Boss: Thông báo "Thời gian Ủy quyền đã kết thúc"
```

*(**👉 Ràng buộc Chống Ủy quyền Bắc cầu (Anti Chaining Delegation):** Nếu Trưởng Khoa (A) ủy quyền cho Phó Khoa (B), thì Phó Khoa (B) KHÔNG ĐƯỢC PHÉP dùng quyền Approver được thừa hưởng đó để ủy quyền tiếp cho người khác (C). Hệ thống chỉ chấp nhận tối đa 1 cấp ủy quyền.)*

---

## 3. Luồng Số hoá Tài liệu cũ (Auto Digitization Workflow)

Mô tả luồng bóc tách dữ liệu AI từ các file PDF/Word có sẵn thành dữ liệu chuẩn trong hệ thống.

```mermaid
sequenceDiagram
    autonumber
    actor Manager as Quản lý CTĐT
    participant Sys as Hệ thống (Web App)
    participant AI as AI Parsing Service
    participant S3 as Lưu trữ Cloud
    
    Manager->>Sys: Kéo thả (Drag & Drop) 10 file PDF/Word Đề cương cũ
    Sys->>S3: Upload files gốc
    S3-->>Sys: Trả về URL files
    Sys->>AI: Gửi yêu cầu Parsing bất đồng bộ (Batch)
    Sys-->>Manager: "Đang xử lý. Vui lòng chờ..."
    
    loop Xử lý từng file
        AI->>AI: Đọc file, bóc tách CLO, PLO, Nội dung, Lịch trình
    end
    AI-->>Sys: Trả về dữ liệu thô (JSON)
    Sys-->>Manager: Thông báo "Hoàn tất bóc tách 10/10 files"
    
    Manager->>Sys: Mở màn hình Đối chiếu (Manual Mapping)
    Note over Manager, Sys: Trái: File gốc PDF | Phải: Form dữ liệu đã bóc
    Manager->>Manager: Kiểm tra, copy/paste sửa lỗi nhận diện sai
    Manager->>Sys: Lưu vào Database chính thức (DRAFT)
```

---

## 4. Quy trình Trợ lý AI (Gợi ý Đề cương - Phụ lục)

Luồng nghiệp vụ minh họa việc AI hỗ trợ đề xuất Đề cương môn học.

```mermaid
sequenceDiagram
    autonumber
    actor Lecturer as Giảng viên
    participant System as Hệ thống
    participant AI as AI Engine (Crawler & GenAI)
    
    Lecturer->>System: Nhập Mục tiêu môn học & Yêu cầu cơ bản
    System->>AI: Gửi yêu cầu gợi ý (Prompt)
    AI->>AI: Crawl & Phân tích Đề cương từ các ĐH uy tín
    AI-->>System: Trả về Bản thảo Đề cương (Số chương, tiểu mục...)
    System-->>Lecturer: Hiển thị Bản thảo trên Giao diện Biên soạn
    
    Lecturer->>Lecturer: Review, Tinh chỉnh & Hoàn thiện Đề cương
    Lecturer->>System: Lưu & Nộp Đề cương
```
