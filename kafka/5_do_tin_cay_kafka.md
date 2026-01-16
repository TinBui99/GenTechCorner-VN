# Kafka - Độ Tin Cậy & Mất Message

> **Kafka rất nhanh, rất mạnh – nhưng không phải là bất khả chiến bại.**
>
> Hiểu *Kafka có thể mất message ở đâu* chính là chìa khóa để bạn xây dựng một hệ thống thực sự bền vững.

---

## Kafka có thật sự "không bao giờ mất dữ liệu"?

Khi nhắc đến Kafka, chúng ta thường nghe về **độ tin cậy gần như tuyệt đối**. Kafka thường được ví như một *pháo đài dữ liệu* – kiên cố, vững chắc và khó bị đánh sập.

Nhưng sự thật là:

> ⚠️ **Kafka hoàn toàn có thể làm mất message** nếu chúng ta cấu hình hoặc sử dụng không đúng cách.

Điều quan trọng cần nhớ:

> **Độ tin cậy của Kafka không phải là tính năng mặc định.**
> Nó là kết quả của *cấu hình đúng*, *hiểu rõ kiến trúc* và *giám sát cẩn thận*.

Để hiểu rõ điều này, hãy cùng theo dõi **hành trình của một message** – từ lúc được sinh ra cho đến khi được xử lý.

---

## Hành trình của một message trong Kafka

Một message đi qua **3 chặng chính**:

1. **Producer** – nơi message được tạo ra
2. **Broker** – nơi message được lưu trữ
3. **Consumer** – nơi message được xử lý

Ở mỗi chặng, đều tồn tại những **điểm rủi ro tiềm ẩn**.

---

## 1️⃣ Producer – Message có thể mất ngay từ điểm xuất phát

Khi ứng dụng gọi `send()`:

- Message **không được gửi ngay lập tức**
- Nó đi qua một pipeline gồm:

```
Create message
   ↓
Record Accumulator (buffer tạm)
   ↓
Sender Thread → gửi sang broker
```

### ⚠️ Điểm rủi ro

Nếu **ứng dụng bị crash** khi message còn nằm trong **Record Accumulator**, message sẽ **mất vĩnh viễn** – chưa kịp rời khỏi producer.

### ✅ Cách gia cố Producer

Để giảm rủi ro, producer cần được cấu hình cẩn thận:

- **`acks=all`**  
  → Chỉ coi là gửi thành công khi *tất cả replica* đã xác nhận

- **`retries > 0`**  
  → Tự động gửi lại nếu có lỗi tạm thời

- **`enable.idempotence=true`**  
  → Đảm bảo *không ghi trùng message* dù có retry nhiều lần

> 🔑 **Nguyên tắc quan trọng:**
> 
> Đừng bao giờ mặc định rằng Kafka sẽ tự bảo vệ dữ liệu cho bạn.

---

## 2️⃣ Broker – Nơi tốc độ và rủi ro song hành

### Vì sao Kafka nhanh?

Kafka ghi dữ liệu theo chiến lược:

1. **Ghi vào RAM trước** (rất nhanh)
2. Sau đó mới **flush xuống disk**

### ⚠️ Các rủi ro tại Broker

#### 🔸 Rủi ro 1: Mất điện / crash đột ngột

- Message còn trong RAM
- Chưa kịp ghi xuống disk
- → **Dữ liệu bốc hơi**

#### 🔸 Rủi ro 2: Sao chép chưa kịp hoàn tất

- Leader ghi message
- Leader chết trước khi follower sync xong
- → Message bị mất

### ✅ Cách gia cố Broker

- **Replication factor ≥ 3**
- Thiết lập:

```properties
min.insync.replicas=2
```

Ý nghĩa:
- Broker **chỉ nhận message mới** khi có *ít nhất 2 replica* đã sync

- Theo dõi **replica lag** thường xuyên

> ⚖️ **Đánh đổi cốt lõi:**
> 
> Kafka được thiết kế để rất nhanh – nhưng tốc độ luôn đi kèm rủi ro nếu không ưu tiên durability.

---

## 3️⃣ Consumer – Message có thể "mất cơ hội xử lý"

Consumer dùng **offset** như một bookmark để biết đã đọc đến đâu.

### Hai chiến lược commit offset

#### 🔸 Auto Commit

- Kafka tự động commit offset theo chu kỳ
- Không quan tâm message đã xử lý xong hay chưa

⚠️ **Rủi ro:**
- App crash sau khi commit
- Logic xử lý chưa xong
- → Message bị *bỏ lỡ mãi mãi*

#### 🔸 Manual Commit

- App tự quyết định khi nào commit offset
- Chỉ commit **sau khi xử lý thành công**

### ✅ Chiến lược thực tế

- Bình thường: **Auto Commit** → hiệu năng cao
- Khi có lỗi: **Sync / Manual Commit** → an toàn

### Dead Letter Queue (DLQ)

- Message lỗi liên tục
- Không xử lý được
- → Đưa vào **Dead Letter Queue** để phân tích sau

> 🧠 Insight quan trọng:
> 
> Message có thể vẫn còn trong Kafka,
> nhưng **cơ hội xử lý nó đã biến mất** – và điều đó cũng được coi là *mất dữ liệu*.

---

## Tổng kết: Xây dựng Kafka bền vững

### 3 điểm rủi ro lớn nhất

1. **Producer** làm rơi message trước khi gửi
2. **Broker** mất dữ liệu do crash hoặc replication chưa kịp
3. **Consumer** commit offset quá sớm

### Bài học lớn nhất

> **Độ tin cậy của Kafka không nằm ở công cụ – mà nằm trong tay người sử dụng.**

Nó đòi hỏi:

- Hiểu rõ từng mắt xích
- Cấu hình cẩn trọng
- Giám sát liên tục

Khi làm đúng, hệ thống Kafka sẽ trở thành:

> 🚀 Một dòng chảy dữ liệu liền mạch, an toàn và cực kỳ mạnh mẽ
