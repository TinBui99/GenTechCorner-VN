# System Requirements: Functional vs Nonfunctional

## Khái niệm cốt lõi

### Functional Requirements
- Trả lời câu hỏi: “Hệ thống sẽ làm gì?”  
- Xác định các tính năng và hành vi cụ thể, ví dụ: gửi hoặc nhận tin nhắn trong ứng dụng chat.  
- Chỉ tập trung vào actions và functionalities mà hệ thống phải cung cấp, không đề cập đến hiệu năng hay chất lượng.

### Nonfunctional Requirements
- Trả lời câu hỏi: “Hệ thống nên hoạt động như thế nào?”  
- Định nghĩa các **quality attributes** và tiêu chí vận hành quyết định sự thành công của hệ thống, bao gồm:
  - **Performance:** độ trễ và tốc độ phản hồi
  - **Scalability:** khả năng xử lý số lượng user hoặc request tăng lên
  - **Availability:** tỉ lệ uptime (ví dụ: 99.99%)
  - **Consistency:** tính toàn vẹn và chính xác của dữ liệu
  - **Reliability:** khả năng chịu lỗi và tự phục hồi của hệ thống

---

## Những hiểu biết quan trọng

- Nhầm lẫn hoặc bỏ qua nonfunctional requirements giống như **xây tòa nhà trên nền cát**, dẫn đến architectural flaws nghiêm trọng.  
- Functional requirements thường đơn giản, quen thuộc, nhưng **nonfunctional requirements quyết định toàn bộ kiến trúc và khả năng bền vững của hệ thống**.

### Ví dụ trong ứng dụng chat
- **Functional:** Gửi và nhận tin nhắn  
- **Nonfunctional:**
  - Giao tin realtime với latency < 100ms → dùng WebSocket thay vì HTTP
  - Phục vụ 1 triệu concurrent users → cần horizontally scalable architecture
  - Đảm bảo không mất tin nhắn dù server gặp sự cố → yêu cầu robust queuing và stable storage systems

**Mindset difference:**  
- Novice developer: chỉ tập trung “what”  
- Experienced architect: xử lý cả “what” và “how” để xây dựng hệ thống **scalable, resilient, high-performance**

---

## Bảng các chỉ số và khái niệm quan trọng

| Requirement Type | Câu hỏi tập trung | Ví dụ trong Chat App | Ảnh hưởng kiến trúc |
|-----------------|-----------------|--------------------|------------------|
| Functional      | Hệ thống làm gì? | Gửi/nhận tin nhắn, gửi hình ảnh, tạo phòng chat | Triển khai tính năng cơ bản, logic coding |
| Nonfunctional   | Hệ thống hoạt động như thế nào? | Latency < 100ms, 1 triệu concurrent users, 99.99% uptime, data consistency, fault tolerance | Dùng WebSocket, horizontal scaling, reliable queues và storage |

---

## Notes

- Functional requirements: **chủ yếu tập trung vào tính năng**.  
- Nonfunctional requirements: **quyết định kiến trúc, khả năng mở rộng và ổn định**.  
- Có thể vẽ sơ đồ trực quan để minh họa sự khác biệt giữa functional và nonfunctional trong ứng dụng chat.
