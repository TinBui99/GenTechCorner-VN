# API Design: Best Practices for Future-Proof Systems

## Problem Statement
- Khoảng **70% thất bại của hệ thống** xuất phát từ việc **API thiết kế sai**.
- API kém làm mọi layer phía trên hệ thống trở nên không ổn định → **thiết kế từ đầu là cực kỳ quan trọng**.

---

## API as a Gateway
- API nên được xem như **cổng chính của toàn bộ hệ thống**.
- API cần **clear, consistent, simple, và extensible** cho tất cả developer.

### Các trụ cột cần thiết cho API vững chắc:
- **Clarity** – rõ ràng
- **Consistency** – nhất quán
- **Easy extensibility** – dễ mở rộng
- **Understandability** – dễ hiểu
- **Strong resource separation** – tách biệt rõ ràng các resource

---

## Bốn nguyên tắc cơ bản của API Design

| Principle | Mô tả | Ví dụ / Khái niệm chính |
|-----------|-------|-------------------------|
| **Resource-Based Design (RFU)** | API nên được tổ chức xung quanh **resources** (danh từ) thay vì actions (động từ) | `/post` đại diện cho posts; `GET /post` để fetch posts, `POST /post` để tạo post |
| **Versioning** | Giới thiệu phiên bản API mới (ví dụ v2 bên cạnh v1) để đảm bảo backward compatibility | Tránh phá vỡ ứng dụng cũ khi update API |
| **Pagination** | Quản lý dataset lớn bằng cách chia response thành các chunk nhỏ để duy trì performance và stability | Request 20 record từ offset 40 thay vì lấy tất cả cùng lúc |
| **Idempotency** | Đảm bảo các request lặp lại dẫn đến cùng trạng thái, tránh duplicate transactions hoặc records | Dùng Idempotency Key để tránh bị tính phí nhiều lần hoặc tạo đơn trùng |

---

## Ví dụ thực tế ứng dụng
- Social network API design theo các nguyên tắc trên:
```http
Fetch posts: GET /sev1/sech/post
Create post: POST /sev1/sech/post
Comment on post: POST /sev1/sech/post/{id}/comments
Pagination: limit=20
```
- Ví dụ này thể hiện **API logic, consistent, extensible**, phản ánh tiêu chuẩn industry được sử dụng bởi Google, Facebook, Stripe, v.v.

---

## Bài học cốt lõi để thiết kế API bền vững (Future-Proof API Design)
- **Design based on resources** – thiết kế theo resource
- **Always implement versioning** – luôn áp dụng versioning
- **Use pagination** – xử lý dataset lớn
- **Guarantee idempotency in write operations** – đảm bảo idempotency khi ghi

> Bốn trụ cột này là **la bàn hướng dẫn** cho software architects xây dựng hệ thống **maintainable, scalable, stable**.

---

## Kết luận
- Thiết kế API tốt không chỉ là coding – mà là **chiến lược dài hạn**, nền tảng cho phát triển, mở rộng, và bảo trì hệ thống lâu dài.

