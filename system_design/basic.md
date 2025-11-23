# System Design

## Sự khác biệt giữa Junior và Senior Developer khi thiết kế hệ thống

<img width="500" alt="system-design-levels" src="https://github.com/user-attachments/assets/f60683a6-c0fc-49a0-a90d-38d1855a152c" />

Developer cấp cao không chỉ viết code mà còn **hiểu cách hệ thống vận
hành ở quy mô lớn**, biết dự đoán sự cố và tối ưu ngay từ giai đoạn
thiết kế.

------------------------------------------------------------------------

## Three Pillars of System Design

### 1. **Scalability**

-   Khả năng phát triển để xử lý tải người dùng tăng liên tục.
-   Hạn chế lỗi khi hệ thống gặp traffic spike đột biến.

### 2. **Reliability**

-   Hệ thống hoạt động ổn định 24/7.
-   Tránh downtime và duy trì tính sẵn sàng.

### 3. **Performance**

-   Cải thiện tốc độ và khả năng phản hồi của hệ thống.
-   Giảm độ trễ và tăng trải nghiệm người dùng.

------------------------------------------------------------------------

## Công cụ và kỹ thuật phổ biến trong thiết kế hệ thống

### **Load Balancer**

Phân phối lưu lượng truy cập đều lên nhiều máy chủ, giúp tránh quá tải
và tăng tính ổn định.

### **Caching**

Lưu trữ tạm dữ liệu được truy cập thường xuyên để: 
- Giảm tải cho cơ sở
dữ liệu
- Tăng tốc độ trả dữ liệu

**Ví dụ:** Redis, Memcached

### **CDN (Content Delivery Network)**

-   Phân phối nội dung (video, hình ảnh, file) từ các máy chủ gần người
    dùng nhất.
-   Giảm độ trễ và tăng tốc độ tải.

**Ví dụ:** Cloudflare, Akamai, AWS CloudFront

------------------------------------------------------------------------

## Ví dụ thực tế: Phân phối hình ảnh trên Facebook

-   Khi người dùng tải ảnh lên, ảnh được **sao chép ngay lập tức** đến
    nhiều datacenter trên toàn cầu.
-   News Feed được **pre-compute** để giảm thời gian tải khi mở ứng
    dụng.
-   Người xem được phục vụ ảnh từ **CDN gần nhất**, không phải từ server
    gốc.

➡️ **Kết quả:** Hình ảnh được hiển thị gần như tức thời cho bạn bè ở bất
kỳ quốc gia nào.

------------------------------------------------------------------------

## Summary

-   **Scalability -- Reliability -- Performance** là ba trụ cột nền tảng
    của System Design.
-   Sử dụng kết hợp **Load Balancer + Caching + CDN** giúp hệ thống
    nhanh hơn, ổn định hơn và xử lý được quy mô lớn.
-   Các công ty lớn như Facebook áp dụng triệt để những nguyên tắc này
    để mang lại trải nghiệm mượt mà cho hàng tỷ người dùng.
