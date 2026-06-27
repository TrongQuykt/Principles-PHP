# Indexing Strategy — Chiến Lược Đánh Index

**Priority: 1** | Nhóm: [Database Design](./README.md)

---

## Index hoạt động như thế nào?

Không có index: Database phải đọc toàn bộ bảng (Full Table Scan) — O(n).
Có index: Database đọc cấu trúc B-Tree — O(log n).

Với bảng 1 triệu rows: Full Scan cần 1,000,000 bước, B-Tree index chỉ cần ~20 bước.

---

## Khi nào đánh Index

**Nên đánh index**:
- Cột trong mệnh đề `WHERE` thường xuyên
- Cột trong `ORDER BY` và `GROUP BY`
- Foreign key columns
- Cột dùng cho `JOIN`
- Cột có tính chọn lọc cao (cardinality cao)

**Không đánh index**:
- Cột có cardinality thấp (ít giá trị duy nhất, VD: `is_active` boolean — xem xét kỹ)
- Bảng rất nhỏ (< 1000 rows)
- Cột hiếm khi được query
- Không nên index quá nhiều cột — ảnh hưởng INSERT/UPDATE

---

## Các loại Index trong MySQL

```php
// Migration Laravel

// Single Column Index
$table->index('status');
$table->index('email');

// Unique Index
$table->unique('slug');
$table->unique('email');

// Composite Index — thứ tự cột RẤT QUAN TRỌNG
// Query WHERE user_id = ? AND status = ? → index(user_id, status) hiệu quả
// Query WHERE status = ? → index(user_id, status) KHÔNG hiệu quả
$table->index(['user_id', 'status']);    // (user_id, status)
$table->index(['status', 'created_at']); // (status, created_at)

// Full-text Index — cho tìm kiếm text
$table->fullText('description');

// Đặt tên index tùy chỉnh
$table->index(['category_id', 'is_active', 'created_at'], 'products_category_active_date_idx');
```

---

## Composite Index — Quy tắc Leftmost Prefix

```sql
-- Index: (user_id, status, created_at)
-- Queries được hưởng lợi:
WHERE user_id = 5                                    -- OK
WHERE user_id = 5 AND status = 'pending'             -- OK
WHERE user_id = 5 AND status = 'pending' AND created_at > '2024-01-01' -- OK

-- Queries KHÔNG hưởng lợi:
WHERE status = 'pending'                             -- Không (thiếu user_id ở đầu)
WHERE created_at > '2024-01-01'                      -- Không (thiếu user_id, status)
WHERE user_id = 5 AND created_at > '2024-01-01'      -- Chỉ dùng được phần user_id
```

---

## Index cho Soft Delete

```php
// Khi dùng SoftDeletes, các query thường có WHERE deleted_at IS NULL
// Composite index với deleted_at thường tối ưu hơn
$table->index(['deleted_at', 'status']);
$table->index(['user_id', 'deleted_at']);

// Hoặc dùng MySQL Partial Index (Filtered index) nếu hỗ trợ
```

---

## Monitoring và Profiling

```php
// Dùng Laravel Telescope hoặc Debugbar để xem query time

// Raw EXPLAIN trong Tinker
DB::select('EXPLAIN SELECT * FROM orders WHERE user_id = ? AND status = ?', [1, 'pending']);

// Bật query log trong development
DB::enableQueryLog();
// ... code ...
$queries = DB::getQueryLog();
```

---

## Liên kết

- [Database Optimization](../06-performance/database-optimization.md)
- [Eloquent ORM](../09-laravel-core/eloquent-orm.md)
