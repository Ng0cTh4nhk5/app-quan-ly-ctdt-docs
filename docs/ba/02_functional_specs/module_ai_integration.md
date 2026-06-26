# Đặc tả Chức năng: Giai đoạn 3 - Tích hợp AI (AI Integration)

Giai đoạn 3 đưa trí tuệ nhân tạo (Generative AI) vào sâu trong quy trình nghiệp vụ nhằm giảm tải khối lượng công việc hành chính và nâng cao chất lượng CTĐT. Ràng buộc quan trọng: AI chỉ đóng vai trò "Trợ lý", quyền quyết định cuối cùng và trách nhiệm pháp lý luôn thuộc về con người.

## 1. Cơ chế Truy vết AI (AI Generation Provenance)
*   **Mô tả:** Để đảm bảo tính minh bạch và phục vụ công tác thanh tra chất lượng sau này, mọi dữ liệu do AI sinh ra khi lưu vào Database phải được định danh.
*   **Ràng buộc Dữ liệu:** Các bảng chứa nội dung do AI hỗ trợ tạo ra (VD: Syllabus, CTDT) phải có thêm metadata:
    *   `ai_generated`: Boolean (Đánh dấu nội dung có sự can thiệp của AI).
    *   `ai_model_version`: String (VD: `gpt-4-turbo-2024-04-09` hoặc `claude-3-opus-20240229`).
    *   `prompt_hash`: String (Mã băm SHA-256 của Prompt đã dùng để sinh ra nội dung).
    *   `generated_timestamp`: DateTime.

## 2. RAG & Đề xuất Khung CTĐT (AI Suggestion Engine)
*   **Đối tượng dùng:** Program Manager.
*   **Mô tả:** Hệ thống AI đóng vai trò tư vấn khi nhà trường mở ngành học mới hoặc cập nhật ngành cũ, sử dụng dữ liệu tham chiếu (RAG).
*   **Chi tiết tính năng:**
    *   Manager nhập từ khóa ngành học và mục tiêu đầu ra chung. **Ràng buộc Input:** Giới hạn tối đa 500 ký tự để chống Prompt Injection.
    *   **Giới hạn Context Window:** Engine RAG trước khi gửi dữ liệu lên LLM phải áp dụng chiến lược Chunking (cắt nhỏ tài liệu), đảm bảo tổng số token của Prompt và Context không vượt quá giới hạn mô hình (VD: max 100k tokens).
    *   **Đầu ra (Output):** Ép buộc LLM trả về cấu trúc JSON nghiêm ngặt (Validate bằng JSON Schema) để tránh lỗi parse ở Frontend. Kết quả bao gồm: Danh sách môn học, Khối kiến thức, Số tín chỉ, và Điều kiện tiên quyết.
*   **Cơ chế RAG Fallback:** 
    *   Trong trường hợp hệ thống RAG (Vector DB) truy vấn trả về **0 kết quả**, hệ thống sẽ tự động chuyển sang fallback: **Cho phép AI sinh nội dung dựa trên kiến thức chung của LLM**.
    *   **Cảnh báo bắt buộc:** Giao diện Frontend phải hiển thị nhãn dán nổi bật: *"⚠️ Dữ liệu này được AI sinh ra từ kiến thức chung, KHÔNG dựa trên cơ sở dữ liệu tham chiếu chuẩn của nhà trường. Vui lòng đối chiếu kỹ."*

## 3. AI Content Generator (Biên soạn Đề cương)
*   **Đối tượng dùng:** Lecturer (Giảng viên).
*   **Mô tả:** Hỗ trợ Giảng viên biên soạn dàn ý và thông tin nền tảng cho đề cương môn học.
*   **Chi tiết tính năng:**
    *   **Ràng buộc Sinh nội dung:** Prompt gửi cho AI BẮT BUỘC phải đính kèm biến `total_credits` (Tổng số tín chỉ môn học). AI không được phép sinh số tiết phân bổ vượt quá tổng thời lượng tín chỉ quy định.
    *   **Chống ảo giác (Anti-Hallucination) cho Tài liệu tham khảo:** Hệ thống bắt buộc gọi API đối chiếu với cơ sở dữ liệu Thư viện hoặc nguồn học thuật (Crossref).
    *   **Timeout & Fallback:** Nếu API ngoại vi đối chiếu tài liệu quá hạn 10 giây (Timeout = 10s), hệ thống không chờ đợi thêm mà đánh dấu tài liệu đó là `Unverified`. Giảng viên phải tự nhập DOI/ISBN thủ công.
*   **Tiện ích UI:**
    *   Nút "Generate with AI" kèm skeleton loading (cần xử lý UX cho thời gian chờ 5-15s).
    *   Hiển thị nội dung do AI sinh bằng màu nền highlight (VD: vàng nhạt).
    *   **AI Accountability:** Bắt buộc có Checkbox *"Tôi xác nhận đã kiểm duyệt và chịu trách nhiệm về tính chính xác của nội dung do AI tạo ra"* trước khi Lưu/Nộp duyệt.

## 4. AI Validator (Kiểm định chuẩn đầu ra & Mapping)
*   **Đối tượng dùng:** Reviewer & QA (Ban đảm bảo chất lượng).
*   **Mô tả:** Đánh giá tự động sự liên kết (Alignment) giữa CLO và PLO phục vụ kiểm định AUN-QA, ABET.
*   **Chi tiết tính năng:**
    *   AI Engine dùng kỹ thuật Semantic Text Similarity để phân tích ngữ nghĩa giữa CLO và PLO.
    *   **Ngưỡng chấm điểm (Thresholds):**
        *   `Similarity Score < 0.65`: Đánh dấu là "Lỗ hổng" (Gaps / Misalignment) - Viền đỏ.
        *   `Similarity Score từ 0.65 - 0.85`: Đánh dấu "Cảnh báo" (Warning) - Viền cam.
        *   `Similarity Score > 0.85`: "Đạt" (Good) - Viền xanh.
*   **Tiện ích UI / Logic:**
    *   **Human Override (Giải quyết xung đột):** Chuyên gia QA có quyền bấm nút "Bỏ qua cảnh báo của AI" (Dismiss). 
    *   **Ràng buộc Override:** Bắt buộc phải nhập Lý do (Justification) tối thiểu **50 ký tự** vào một popup để lưu Log kiểm định, chống việc ghi bừa "ok", "đã xem".

## 5. Các yêu cầu phi chức năng (NFR) cho Module AI
*   **Quản lý chi phí (Cost & Quota Control):** Áp dụng Rate Limiting cho tất cả các tính năng AI. VD: Giới hạn 50 requests/ngày/User để tránh lạm dụng chi phí API. Khi vượt Quota, UI vô hiệu hóa nút Generate và báo lỗi `HTTP 429`.
*   **Bảo mật dữ liệu (Data Privacy):** 
    *   Tất cả dữ liệu nội bộ gửi qua API của LLM (OpenAI, Azure, Anthropic) phải tuân thủ chính sách bảo mật Zero-Data Retention (Không dùng dữ liệu để train model).
    *   Thực hiện ẩn danh (Anonymization) thông tin nhạy cảm của GV/Trường trước khi đẩy vào prompt.
*   **Xử lý ngoại lệ (Exception & Fallback):** 
    *   **Cơ chế Retry:** Áp dụng thuật toán Exponential Backoff tối đa 3 lần thử lại khi gặp lỗi API timeout hoặc quá tải mạng (500/502/503/504).
    *   **Graceful Degradation:** Nếu thử lại vẫn thất bại, hệ thống tự động tắt tính năng AI (ẩn các nút Generate) và chuyển màn hình sang chế độ "Nhập thủ công" kèm thông báo: *"Dịch vụ AI hiện đang bảo trì, vui lòng sử dụng chế độ nhập liệu thủ công."*
