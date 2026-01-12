# Hiểu về Slow query, deadlock và các nguyên nhân khiến DB chậm

> Tài liệu tổng hợp kinh nghiệm thực chiến của các senior backend engineer  
> Mục tiêu: thiết kế hệ thống **DB hoạt động ổn định, nhanh khi lên production**

---

## Mục lục
1. Connection Pooling  
2. Giới hạn & cấu hình Database  
3. Slow Query  
4. Deadlock  
5. Index Strategy  
6. Query Plan & EXPLAIN  
7. Cách hệ thống thực sự sập  
8. Checklist Production  
9. Tư duy Senior Dev  

---

## 1. Connection Pooling

### Vấn đề cốt lõi
Tạo mới database connection là **cực kỳ tốn kém**:

- TCP handshake
- Authentication
- Cấp phát memory & buffer
- Context switch CPU
- Thread / process management

> 1 request = 1 connection  
> → chắc chắn sập hệ thống.


### Connection Pool là gì?
Connection Pool là **kho chứa các connection đã mở sẵn**.

Luồng xử lý:
1. Request mượn connection
2. Thực thi query
3. Trả connection về pool
4. Pool hết → request xếp hàng chờ

---

### Pool size bao nhiêu là đủ?
| Runtime | Pool size gợi ý |
|------|------|
| Node.js | 10–20 |
| Java / Go | 20–50 |
| PostgreSQL (tổng) | ~1000–1200 |
| MySQL | Cao hơn nhưng vẫn cần giới hạn |

**Golden rule:**  
> Nhiều connection hơn **không làm DB nhanh hơn**

---

### Sai lầm phổ biến
❌ Pool size quá lớn → tranh chấp tài nguyên  
❌ Không giới hạn pool → DB sập  
❌ Không release connection → **connection leak**

---

### Read / Write Splitting
- Write pool nhỏ → DB primary
- Read pool lớn → DB replica
- Giảm tải ghi, scale đọc hiệu quả

---

## 2. Giới hạn & Cấu hình Database

### PostgreSQL
- `max_connections`
- `shared_buffers`
- `work_mem`
- `statement_timeout`
- `idle_in_transaction_session_timeout`

### MySQL
- `max_connections`
- `innodb_buffer_pool_size`
- `wait_timeout`
- `slow_query_log`

> Tổng connection từ **tất cả service × pool size**  
> **không được vượt khả năng DB**

---

## 3. Slow Query
### 3.1 Slow Query là gì?

Slow query là câu SQL vượt quá ngưỡng cho phép (thường > 200ms).

Slow query không chỉ làm chậm request
mà còn giết connection pool

### 3.2 Vì sao slow query nguy hiểm?

- Giữ connection lâu
- Pool cạn
- Request xếp hàng
- Retry → cascade failure
- DB overload → outage

### 3.3 Nguyên nhân phổ biến

- Thiếu index
- Full table scan
- SELECT *
- N+1 query (ORM)
- ORDER BY / GROUP BY lớn
- JOIN không index FK

### 3.4 Phát hiện slow query

PostgreSQL
```
EXPLAIN ANALYZE
log_min_duration_statement = 200ms
```

MySQL
```
slow_query_log = ON
long_query_time = 0.2
```
## 4. Deadlock
### 4.1 Deadlock là gì?

Deadlock xảy ra khi các transaction giữ lock của nhau và chờ vô hạn.

### 4.2 Nguyên nhân phổ biến

- Update nhiều bảng khác thứ tự
- Transaction dài
- Không index WHERE
- ORM auto transaction

### 4.3 Cách phòng tránh

- Update bảng theo thứ tự cố định
- Transaction ngắn nhất có thể
- Index đầy đủ
- Retry logic ở application layer
---

## 5. Index Strategy – Cách đánh index đúng

### 5.1 Nguyên tắc cốt lõi
> **Index theo QUERY, không theo TABLE**

Index chỉ có ý nghĩa khi phục vụ trực tiếp cho các truy vấn thực tế đang chạy trong hệ thống.



### 5.2 Khi nào cần index?
Nên tạo index cho các cột thường xuất hiện trong:

- `WHERE`
- `JOIN`
- `ORDER BY`
- `GROUP BY`


### 5.3 Composite Index – Thứ tự quyết định tất cả

**Query ví dụ:**
```sql
SELECT *
FROM orders
WHERE user_id = ? 
  AND status = ?
ORDER BY created_at DESC;
```
Index đúng:

```sql
(user_id, status, created_at)
```
Thứ tự cột trong composite index phải khớp với thứ tự lọc và sắp xếp trong query.

### 5.4 Index cho JOIN
- Foreign Key: bắt buộc phải có index

- Primary Key: luôn luôn có index mặc định

### 5.5 Sai lầm phổ biến
- Index mọi cột một cách cảm tính
- Index được tạo nhưng không có query nào sử dụng
- Không kiểm tra EXPLAIN trước và sau khi thêm index

---

## 6. Query Plan & EXPLAIN  
*(Đọc DB đang nghĩ gì)*


### 6.1 Query Plan là gì?

**Query Plan** là cách Database **thực sự thực thi** câu SQL,  
không phải cách bạn *nghĩ* nó sẽ chạy.

> SQL chỉ là **ý định**  
> Query Plan mới là **hành động thật**


### 6.2 Vì sao Query Plan quan trọng?

Hai query **giống hệt về mặt kết quả** có thể:

- Một query chạy **5ms**
- Một query chạy **5s**

Sự khác biệt nằm ở:

- DB có dùng **index** hay không
- **Thứ tự JOIN**
- **Phương pháp scan & join**
- **Số dòng thực sự bị xử lý**

👉 **Query Plan quyết định 80–90% hiệu năng hệ thống.**

---

### 6.3 Cách dùng EXPLAIN

#### PostgreSQL
```sql
EXPLAIN ANALYZE SELECT ...
```
### MySQL
```sql
EXPLAIN SELECT ...
```

⚠️ EXPLAIN ANALYZE chạy query thật
Chỉ dùng trên staging / production phải cực kỳ cẩn thận

### 6.4 Những thứ BẮT BUỘC phải nhìn trong Query Plan
1️⃣ Seq Scan vs Index Scan
Index Scan → ✅ Tốt

Seq Scan → ❌ Nguy hiểm với table lớn

Nguyên tắc:
```
Seq Scan trên table vài trăm dòng → OK
Seq Scan trên table vài triệu dòng → ❌ BUG THIẾT KẾ
```

2️⃣ Rows (Estimate vs Actual)
Ví dụ PostgreSQL:

```
rows=100 (actual rows=10,000)
```
→ Optimizer ước lượng sai
→ Dẫn tới chọn JOIN / index sai

Nguyên nhân thường gặp:

- Statistic lỗi thời
- Index không phù hợp

3️⃣ Cost không quan trọng bằng Actual Time
cost → Ước đoán
actual time → Sự thật

👉 Senior dev chỉ tin actual time

4️⃣ Join Method
Join Method	Khi nào tốt
| Nested Loop |	Dataset nhỏ |
| Hash Join	| Join lớn, không index|
| Merge Join	| Có index & dữ liệu đã sort |

❌ Nested Loop + dataset lớn = thảm họa hiệu năng

### 6.5 Dấu hiệu Query Plan có vấn đề
Seq Scan trên bảng lớn
Nested Loop với hàng triệu row
Rows estimate lệch quá nhiều
Actual time tăng theo cấp số nhân

### 6.6 Cách sửa Query Plan (thứ tự ưu tiên)
- Viết lại query (giảm JOIN, subquery)
- Đánh index đúng thứ tự
- Giảm SELECT *
- Chia query lớn thành nhiều bước
- Cập nhật statistics (ANALYZE)

### 6.7 Senior Rule về Query Plan
❌ Không sửa query khi chưa nhìn EXPLAIN

❌ Không thêm index khi chưa hiểu query

✅ Query Plan là sự thật duy nhất

### 6.8 Kết luận
Query nhanh hay chậm không nằm ở SQL đẹp

Nằm ở Query Plan

Biết đọc EXPLAIN = bước chuyển từ mid → senior


## 7. Điều khiến hệ thống thực sự sập?

Slow Query
→ Connection giữ lâu
→ Pool cạn
→ Request queue
→ Retry
→ DB overload
→ OUTAGE

---

## 8. Checklist Production

- [ ] Bắt buộc dùng connection pool
- [ ] Giới hạn pool size
- [ ] Enable slow query log
- [ ] Monitor connection count
- [ ] Validate index bằng EXPLAIN
- [ ] Retry deadlock
- [ ] Read/write split khi scale

---

## 9. Tư duy Senior Dev

> Junior dev viết feature  
> Senior dev ngăn hệ thống sập

- Hiểu trade-off
- Tối ưu cho production
- Bảo vệ database trước tiên

---

📌 *Tài liệu dùng làm README GitHub / Internal Engineering Handbook*
