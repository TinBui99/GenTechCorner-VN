# High-Level Architecture (HLA)

High-Level Architecture giống như bản vẽ kiến trúc của một ngôi nhà, cung cấp **tầm nhìn tổng thể** về các thành phần chính của hệ thống và cách chúng tương tác, mà không đi vào chi tiết từng phần nhỏ.

---

## Bảy thành phần cốt lõi (Building Blocks) của hệ thống web lớn điển hình

| Thành phần | Vai trò |
|------------|--------|
| **Client** | Frontend, bao gồm web và mobile apps, nơi user tương tác |
| **API Gateway** | Cổng chính mà mọi request của user phải đi qua |
| **Load Balancer** | Phân phối traffic đồng đều, tránh tắc nghẽn hệ thống |
| **Service Layer** | “Bộ não” xử lý request |
| **Database** | “Thư viện” lưu trữ dữ liệu quan trọng |
| **Cache (CAS)** | “Ngăn kéo” lưu trữ dữ liệu truy cập thường xuyên để lấy nhanh |
| **Message Queue** | Trợ lý quản lý các tác vụ tốn thời gian theo cơ chế asynchronous |

**Ngoài ra:**
- **Object Storage và CDN:** “Kho” lưu trữ file multimedia nặng, phân phối nhanh qua Content Delivery Network.

---

## Ba luồng dữ liệu quan trọng trong kiến trúc hệ thống

### 1. Write Flow
- Dữ liệu (ví dụ post mới) đi từ **Client → API Gateway → Service Layer → Database**.
- Một số tác vụ được đẩy vào **Message Queue** để xử lý sau.

### 2. Read Flow
- Khi truy xuất dữ liệu, hệ thống kiểm tra **cache** trước để lấy nhanh.
- Nếu không có, dữ liệu được lấy từ **Database** và cập nhật cache.

### 3. Background Processing Flow
- Các tác vụ tốn thời gian hoặc phức tạp được đưa vào **Message Queue** và xử lý bởi **worker chuyên dụng**.
- Đảm bảo trải nghiệm user **mượt mà** mà không bị gián đoạn.

---

## Ví dụ thực tế: Tải News Feed trên mạng xã hội

1. User request feed → đi qua **API Gateway** → service kiểm tra **cache** → nếu có dữ liệu, trả ngay.
2. Nội dung multimedia (ảnh, video) tải riêng qua **CDN** để nhanh.
3. Dữ liệu hành vi user được gửi âm thầm vào **Message Queue** để phân tích background.

Toàn bộ quá trình hoàn thành trong vài mili giây, minh họa **sức mạnh của kiến trúc được thiết kế tốt**.

---

## Bài học cốt lõi: Tư duy của Architect

Ba công cụ cơ bản cho system architects:
1. **The Map:** High-level architecture cung cấp tầm nhìn tổng thể.
2. **Seven Building Blocks:** Các thành phần nền tảng phổ biến trong hệ thống lớn.
3. **Three Data Flows:** Các luồng dữ liệu cốt lõi đảm bảo hoạt động của hệ thống.

**Mastering top-down perspective:** Nhìn toàn bộ hệ thống trước khi đi vào chi tiết là điều phân biệt một **architect** với một **regular programmer**.

