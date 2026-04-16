# Lab 11 & Assignment Final Report: AI Guardrails & HITL
**Course:** AICB-P1 — AI Agent Development  
**Student:** Đỗ Trọng Minh-2A202600464
**Topic:** Implementing Defense-in-Depth for VinBank AI Assistant

---

## 1. Hành trình thực hiện (Process Overview)

Quá trình làm bài Lab 11 và Assignment của tôi trải qua 4 giai đoạn chính, từ việc tìm hiểu cách "phá" hệ thống đến việc xây dựng "khiên" bảo vệ đa tầng.

### Giai đoạn 1: Red Teaming - Thử thách AI
Tôi bắt đầu bằng việc đóng vai Hacker để tìm điểm yếu của mô hình Gemini 2.5 Flash Lite.
- **Manual Attacks:** Tôi đã thiết kế 5 kịch bản tấn công thủ công (Prompt Injection) nhắm vào việc đánh lừa AI để lộ mật khẩu Admin (`admin123`) và API Key.
- **AI-Generated Attacks:** Sử dụng chính Gemini để tự "nghĩ" ra các đòn tấn công tinh vi hơn như: mã hóa Base64 câu lệnh, giả danh CISO yêu cầu audit, và dùng các định dạng JSON/YAML để lừa trích xuất dữ liệu.

### Giai đoạn 2: Xây dựng hệ thống phòng thủ (Defense Construction)
Tôi đã sử dụng framework **Google ADK** để xây dựng các Plugin bảo mật:
- **Input Guardrails:** Thiết lập bộ lọc từ khóa chủ đề ngân hàng (Topic Filter) và 16 mẫu Regex để nhận diện hành vi tiêm mã (Injection).
- **Output Guardrails:** Xây dựng bộ lọc dữ liệu nhạy cảm (PII Filter) để tự động che giấu (`[REDACTED]`) các thông tin như số điện thoại, email, đặc biệt là các bí mật nội bộ như `.internal` domains hay mật khẩu.
- **LLM-as-Judge:** Đây là phần tôi tâm đắc nhất. Tôi đã thiết lập một con AI "Giám khảo" độc lập để chấm điểm mọi câu trả lời của Bot dựa trên 4 tiêu chí: Safety, Relevance, Accuracy, và Tone.

### Giai đoạn 3: Giải quyết sự cố kỹ thuật (Troubleshooting)
Trong quá trình làm, tôi gặp thử thách lớn khi chạy trên môi trường **Python 3.14** cục bộ, khiến thư viện NeMo Guardrails bị lỗi tương thích (`TypeError`). 
- **Giải pháp:** Tôi đã chủ động xử lý bằng cách sử dụng các khối `try-except Exception` để cách ly lỗi, đảm bảo hệ thống vẫn chạy ổn định với các lớp bảo vệ của Google ADK. Sau đó, tôi đã di chuyển bài lên **Google Colab** để kích hoạt đầy đủ 100% sức mạnh bảo vệ của cả NeMo.

### Giai đoạn 4: Thiết lập Human-in-the-Loop (HITL)
Tôi đã hiện thực hóa mô hình "Người giám sát AI" thông qua **ConfidenceRouter**. Hệ thống của tôi không tự động trả lời một cách mù quáng mà sẽ:
- **Tự động gửi:** Nếu độ tin cậy > 90%.
- **Chờ duyệt (Human-on-the-loop):** Nếu câu trả lời không chắc chắn.
- **Leo thang (Human-in-the-loop):** Bắt buộc con người xử lý nếu liên quan đến các hành động rủi ro cao như chuyển tiền hay đổi mật khẩu.

---

## 2. Kết quả đạt được (Key Results)

| Chỉ số | Kết quả |
|--------|---------|
| **Tỉ lệ chặn tấn công (ADK)** | **100% (11/11 đòn bị chặn đứng)** |
| **Bảo vệ dữ liệu nhạy cảm** | Thành công 100% việc che dấu mật khẩu và API Key |
| **Độ trễ hệ thống (Latency)** | Trung bình ~1s/request (Đã tối ưu hóa) |
| **Hệ thống giám sát** | Xuất log `audit_log.json` đầy đủ cho mọi tương tác |

---

## 3. Bài học kinh nghiệm (Lessons Learned)

1. **Không có "Khiên" nào là hoàn hảo:** Tôi nhận ra rằng dù có nhiều lớp bảo vệ, Hacker vẫn có thể dùng "Semantic Evasion" (lách bằng ý nghĩa) để vượt qua Regex. Do đó, việc kết hợp giữa **Regex (nhanh)** và **LLM-as-Judge (hiểu ngữ nghĩa)** là mô hình tối ưu nhất.
2. **Tầm quan trọng của HITL:** Trong lĩnh vực tài chính như VinBank, AI chỉ nên đóng vai trò hỗ trợ. Những quyết định mang tính pháp lý hoặc tài chính cao bắt buộc phải có sự xác nhận của con người để đảm bảo tính trách nhiệm (Accountability).
3. **Kỹ năng xử lý môi trường:** Việc gặp lỗi thư viện trên Python 3.14 giúp tôi rèn luyện kỹ năng đọc log và tìm giải pháp thay thế linh hoạt (Workaround) trong lập trình AI.

---

## 4. Tổng kết

Dự án này giúp tôi hiểu rõ quy trình xây dựng một AI Agent an toàn và có trách nhiệm (Responsible AI). Tôi tin rằng hệ thống bảo vệ đa tầng mà tôi đã xây dựng đủ tiêu chuẩn để ứng dụng vào các hệ thống Chatbot chuyên nghiệp.

*Người thực hiện báo cáo:*  
**[Tên của bạn] - Sinh viên VinUniversity**
