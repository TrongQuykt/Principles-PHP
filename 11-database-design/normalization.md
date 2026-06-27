# Normalization — Chuẩn Hóa Database

**Priority: 1** | Nhóm: [Database Design](./README.md)

---

## Định nghĩa

**Normalization** là quá trình tổ chức database để giảm thiểu sự trùng lặp dữ liệu (data redundancy) và đảm bảo tính toàn vẹn (data integrity).

---

## Các dạng chuẩn (Normal Forms)

### 1NF — First Normal Form
Mỗi ô chỉ chứa một giá trị nguyên tử (atomic value). Không lưu nhiều giá trị trong một cột.

```
Sai (vi phạm 1NF):
| order_id | products         |
|----------|-----------------|
| 1        | "Sữa, Bánh, Thịt"|  ← Nhiều giá trị trong 1 ô

Đúng (tuân thủ 1NF):
Tách ra bảng order_items riêng
```

### 2NF — Second Normal Form (áp dụng cho bảng có composite PK)
Mọi cột không phải PK phải phụ thuộc hoàn toàn vào toàn bộ khóa chính.

```
Sai (vi phạm 2NF):
Bảng order_items(order_id, product_id, product_name, quantity)
          ← product_name chỉ phụ thuộc product_id, không phụ thuộc order_id

Đúng:
order_items(order_id, product_id, quantity, unit_price_snapshot)
products(id, name, ...)
```

### 3NF — Third Normal Form
Không có phụ thuộc bắc cầu (transitive dependency). Cột không khóa không phụ thuộc vào cột không khóa khác.

```
Sai (vi phạm 3NF):
orders(id, user_id, user_email, user_name, ...)
         ← user_email và user_name phụ thuộc user_id, không phải order.id

Đúng:
orders(id, user_id, ...)
users(id, email, name, ...)
```

---

## Denormalization — Khi nào được phép?

Denormalization (vi phạm 3NF có chủ đích) đôi khi được chấp nhận vì lý do hiệu năng:

```php
// Lưu snapshot price tại thời điểm mua — ĐÚNG (Snapshot Pattern)
Schema::create('order_items', function (Blueprint $table) {
    $table->id();
    $table->foreignId('order_id')->constrained()->cascadeOnDelete();
    $table->foreignId('product_id')->constrained()->restrictOnDelete();
    $table->string('product_name_snapshot');  // Lưu để hiển thị dù product bị xoá
    $table->unsignedBigInteger('unit_price_snapshot'); // Giá tại lúc mua
    $table->unsignedSmallInteger('discount_pct_snapshot')->default(0);
    $table->unsignedInteger('quantity');
    $table->unsignedBigInteger('subtotal');   // Computed nhưng lưu lại để tránh tính toán lại
    $table->timestamps();
});
```

---

## Liên kết

- [Schema Standards](./schema-standards.md)
- [Eloquent ORM](../09-laravel-core/eloquent-orm.md)
