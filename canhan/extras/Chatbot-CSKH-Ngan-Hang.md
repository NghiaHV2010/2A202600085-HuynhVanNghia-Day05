# THƯ VIỆN DẠNG LỖI (FAILURE MODE LIBRARY)
## Sản phẩm: Chatbot CSKH Ngân hàng (AI-powered)

**Bối cảnh dự án (Đề 1):** Chatbot hỗ trợ trả lời các câu hỏi về tài khoản, thẻ, vay. Yêu cầu hiểu tiếng Việt có dấu/không dấu, cam kết phản hồi trong 3 giây và hoạt động 24/7.

---

### 1. Ảo giác & Bịa thông tin ngoài phạm vi (Out-of-Scope Hallucination)
Nguy cơ AI tự động tạo ra câu trả lời cho những nghiệp vụ không được huấn luyện, dẫn đến rủi ro pháp lý (tương tự sự cố Air Canada).

*   **Khi nào xảy ra (Trigger):** Khách hàng hỏi các vấn đề ngoài 3 nghiệp vụ chính. Ví dụ: *"Ngân hàng có tư vấn đầu tư chứng khoán không?"* hoặc *"So sánh lãi suất bên bạn với ngân hàng X"*.
*   **Chuyện gì xảy ra (Hậu quả):** Mô hình AI tự "bịa" ra chính sách đầu tư ảo hoặc thông tin sai lệch. Nếu khách hàng làm theo và bị thiệt hại, ngân hàng phải chịu hoàn toàn trách nhiệm pháp lý.
*   **Xử lý thế nào (Mitigation):**
    *   Thiết lập hàng rào bảo vệ (Guardrails) giới hạn phạm vi truy vấn.
    *   Thiết kế kịch bản Fallback (từ chối khéo léo): *"Xin lỗi, tôi chỉ hỗ trợ các vấn đề về Tài khoản, Thẻ và Vay. Để tìm hiểu về đầu tư, tôi xin phép chuyển thông tin đến chuyên viên tư vấn."*

### 2. Cung cấp dữ liệu cũ / Sai lệch số liệu (Outdated/Incorrect Critical Data)
Nguy cơ cung cấp sai các chỉ số tài chính nhạy cảm mang tính thời điểm.

*   **Khi nào xảy ra (Trigger):** Khách hàng hỏi về các con số thay đổi liên tục. Ví dụ: *"Lãi suất vay mua nhà hiện tại là bao nhiêu?"* nhưng bot đang sử dụng dữ liệu cũ chưa được cập nhật.
*   **Chuyện gì xảy ra (Hậu quả):** Khách hàng nhận thông tin sai (ví dụ: bot báo 6.5%, thực tế là 8%) dẫn đến định hướng tài chính sai lệch. Gây phẫn nộ, khiếu nại và khủng hoảng truyền thông.
*   **Xử lý thế nào (Mitigation):**
    *   Tuyệt đối không gán cứng (hardcode) số liệu vào não bot. Yêu cầu bot gọi API thời gian thực (Real-time RAG) vào hệ thống Core Banking.
    *   Luôn đính kèm Disclaimer và tem thời gian: *"Lãi suất tham khảo tính đến ngày [Hôm nay] là X%. Vui lòng liên hệ quầy để có thông tin chính xác nhất cho hồ sơ của bạn."*

### 3. Hiểu sai ý định do tiếng Việt mơ hồ/không dấu (Ambiguity & Unaccented Vietnamese)
Rủi ro từ bài toán NLP (Xử lý ngôn ngữ tự nhiên) khi tiếp nhận đầu vào thiếu chuẩn xác.

*   **Khi nào xảy ra (Trigger):** Khách hàng nhắn tin không dấu (VD: *"Toi muon khoa tai khoan"*) hoặc cung cấp thông tin không đầy đủ (VD: *"Tôi bị mất thẻ"*).
*   **Chuyện gì xảy ra (Hậu quả):** Phân loại sai ý định (Intent misclassification). Bot có thể hướng dẫn khóa sai loại thẻ (khóa thẻ ATM thay vì thẻ tín dụng), làm chậm trễ quá trình bảo vệ tài sản, dẫn đến mất tiền oan cho khách hàng.
*   **Xử lý thế nào (Mitigation):**
    *   Áp dụng cơ chế làm rõ (Multi-turn conversation): Không để bot tự đoán. Bot phải hỏi ngược lại: *"Bạn đang muốn báo mất thẻ tín dụng (Credit) hay thẻ ghi nợ (ATM)?"*
    *   Kết hợp giao diện nút bấm (Quick Replies) để chuẩn hóa lựa chọn của người dùng.

### 4. Rò rỉ dữ liệu do thiếu xác thực (Security / Authentication Failure)
Rủi ro bảo mật nghiêm trọng nhất khi bot trả lời thông tin cá nhân mà không qua các bước kiểm tra danh tính.

*   **Khi nào xảy ra (Trigger):** Người dùng (hoặc kẻ gian) yêu cầu truy xuất dữ liệu cá nhân. Ví dụ: *"Kiểm tra số dư tài khoản đuôi 1234"* hoặc *"Liệt kê giao dịch tháng trước"*.
*   **Chuyện gì xảy ra (Hậu quả):** Bot vô tình cung cấp thông tin tài chính dựa trên tài khoản mạng xã hội mà chưa xác thực chính chủ. Dẫn đến vi phạm quy định bảo vệ dữ liệu, phạt hành chính và mất uy tín nghiêm trọng.
*   **Xử lý thế nào (Mitigation):**
    *   Xây dựng Cổng xác thực (Authentication Gating): Khi phát hiện ý định liên quan đến thông tin cá nhân, bot lập tức yêu cầu đăng nhập ứng dụng hoặc gửi mã OTP xác thực.
    *   Áp dụng che giấu dữ liệu (Data Masking) trong đoạn chat: *"Tài khoản ****1234 hiện có 50.xxx.xxx VNĐ"*.

### 5. Quá tải hệ thống / Vi phạm cam kết thời gian (SLA Timeout Under Peak Load)
Rủi ro kỹ thuật khi mô hình AI xử lý chậm, vi phạm cam kết phản hồi dưới 3 giây.

*   **Khi nào xảy ra (Trigger):** Lượng truy cập tăng đột biến (dịp lễ/Tết, lỗi hệ thống diện rộng) hoặc người dùng đặt câu hỏi quá dài/phức tạp khiến bot mất nhiều thời gian truy vấn.
*   **Chuyện gì xảy ra (Hậu quả):** Độ trễ vượt quá ngưỡng 3 giây, thậm chí treo bot. Trong các trường hợp khẩn cấp (báo khóa thẻ do lừa đảo), việc bot im lặng gây hoảng loạn tột độ và thiệt hại tài chính thực tế cho khách hàng.
*   **Xử lý thế nào (Mitigation):**
    *   Thiết lập luồng dự phòng (Fallback Rule-based): Nếu xử lý quá 2 giây, kích hoạt ngay tin nhắn tạm: *"Hệ thống đang kiểm tra thông tin, quý khách vui lòng đợi trong giây lát..."*
    *   Ưu tiên "Luồng xanh" (Fast-track) cho các từ khóa khẩn cấp ("mất thẻ", "lừa đảo"): Bỏ qua xử lý LLM phức tạp, lập tức hiển thị nút khóa thẻ khẩn cấp với độ trễ < 1 giây.
