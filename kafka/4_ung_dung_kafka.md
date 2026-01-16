# Kafka - 5 Ứng Dụng Thực Tế Biến Kafka Thành Xương Sống Dữ Liệu


Kafka ban đầu được xây dựng tại **LinkedIn** để xử lý log với quy mô cực lớn. Tuy nhiên, nhờ thiết kế thông minh:

- Lưu trữ message bền vững (durable log)
- Cho phép consumer **đọc dữ liệu theo tốc độ riêng**
- Tách biệt hoàn toàn producer và consumer

Kafka nhanh chóng tiến hoá thành **nền tảng xử lý dữ liệu thời gian thực hàng đầu thế giới**.

---

## 1️⃣ Log Streaming & Observability (Centralized Logging)

### Bài toán

Trong hệ thống microservices:

- Service giỏ hàng
- Service đặt hàng
- Service thanh toán

… tất cả đều liên tục sinh ra log.

### Kafka giải quyết thế nào?

- Kafka đứng ở **trung tâm**, thu gom toàn bộ log
- Đẩy dữ liệu sang các hệ thống phân tích như:
  - Elasticsearch
  - Kibana

### Kết quả

- Giám sát **hàng triệu log theo thời gian thực**
- Không gián đoạn, không mất dữ liệu

> **Từ khoá quan trọng:**
> - Observability
> - Log Streaming
> - Centralized Logging

---

## 2️⃣ Realtime Recommendation System (Hệ thống gợi ý)

Bạn đã từng thắc mắc:

> Vì sao Spotify, Shopee lại đoán đúng thứ bạn sắp tìm?

### Câu trả lời nằm ở Kafka

- Mọi hành vi người dùng:
  - Click
  - View
  - Add to cart
- Được ghi nhận thành **event stream** và gửi vào Kafka

### Dòng dữ liệu này được xử lý bởi

- Apache Flink
- Apache Spark Streaming
- Feeding cho các mô hình Machine Learning

### Kết quả

- Gợi ý cá nhân hoá xuất hiện **gần như tức thì**

---

## 3️⃣ Monitoring & Alerting (Giám sát và cảnh báo)

### Cách hoạt động

- Mỗi microservice liên tục gửi metric:
  - CPU
  - Memory
  - Latency
  - Error rate

… vào Kafka.

- Dữ liệu được xử lý realtime bởi Flink

### Khi có bất thường

- Alert được kích hoạt ngay lập tức

> Khi một service **sắp gục**, Kafka là **người đầu tiên biết**.

Điều này cho phép đội ngũ kỹ sư:

- Phát hiện sớm
- Xử lý trước khi sự cố lan rộng

---

## 4️⃣ Change Data Capture (CDC – Bắt thay đổi dữ liệu)

### Bài toán

Làm sao đồng bộ dữ liệu giữa nhiều hệ thống?

### Kafka CDC hoạt động ra sao?

- Bắt sự kiện thay đổi từ **transaction log** của database
- Phát sự kiện này đến toàn bộ hệ thống downstream

### Hệ sinh thái được đồng bộ

- Elasticsearch
- Redis
- Database Replica

### Kết quả

- Đồng bộ **gần real-time**
- Không cần query lại database gốc

---

## 5️⃣ System Migration (Di chuyển hệ thống – Zero Downtime)

### Kịch bản thực tế

- Nâng cấp hệ thống từ **V1 → V2**

### Kafka đóng vai trò gì?

- Là **cầu nối trung gian**
- Là **buffer an toàn** giữa hai hệ thống

### Cách triển khai

- Hệ thống cũ tiếp tục gửi dữ liệu vào Kafka
- Hệ thống mới đọc dữ liệu từ Kafka
- Hai hệ thống chạy **song song**

### Kết quả

- Zero Downtime
- Không mất dữ liệu
- Migration mượt mà

---

## Tổng kết

Chúng ta đã đi qua 5 ứng dụng cốt lõi:

1. Log Streaming & Observability
2. Recommendation System
3. Monitoring & Alerting
4. Change Data Capture
5. System Migration

> **Kafka chính là hệ thần kinh trung ương của thế giới dữ liệu hiện đại.**

Nếu hệ thống của bạn đang **phát triển từng ngày**, Kafka **không còn là lựa chọn** – mà là **nền tảng bắt buộc**.
