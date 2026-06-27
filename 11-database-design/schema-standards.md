# Schema Standards — Chuẩn Thiết Kế Schema Database

**Priority: 1** | Nhóm: [Database Design](./README.md)

---

## Naming Conventions — Bắt buộc tuân thủ

| Đối tượng | Convention | Ví dụ |
|---|---|---|
| Tên bảng | `snake_case`, số nhiều | `order_items`, `product_variants`, `user_roles` |
| Tên cột | `snake_case`, số ít | `base_price`, `discount_percentage`, `is_active` |
| Khoá chính | `id` (bigint, auto-increment) | `id` |
| Khoá ngoại | `{tên_bảng_số_ít}_id` | `user_id`, `product_id`, `order_id` |
| Timestamps | `created_at`, `updated_at` | Tự động bởi Laravel `timestamps()` |
| Soft delete | `deleted_at` | Bởi `softDeletes()` |
| Boolean | Prefix `is_` hoặc `has_` | `is_active`, `has_verified_email` |
| Datetime period | `{event}_at` | `shipped_at`, `paid_at`, `cancelled_at` |

---

## Kiểu dữ liệu — Dùng đúng type

| Mục đích | Type đúng | Tránh dùng |
|---|---|---|
| Tiền tệ (VND) | `BIGINT UNSIGNED` (đồng, không decimal) | `FLOAT`, `DOUBLE` (rounding error) |
| Tỉ lệ % | `TINYINT UNSIGNED` (0-100) | `VARCHAR` |
| Trạng thái (enum) | `VARCHAR(20)` hoặc `TINYINT` | `INT` không có nghĩa |
| Mô tả ngắn | `VARCHAR(255)` | `TEXT` cho chuỗi ngắn |
| Nội dung dài | `TEXT` hoặc `LONGTEXT` | `VARCHAR` cho nội dung dài |
| Dữ liệu động | `JSON` | `TEXT` chứa chuỗi JSON |
| UUID | `CHAR(36)` hoặc `BINARY(16)` | `VARCHAR(255)` |
| Boolean | `TINYINT(1)` | `VARCHAR` ('yes'/'no') |
| IP Address | `VARCHAR(45)` | `INT` |

---

## Migration chuẩn — Template

```php
public function up(): void
{
    Schema::create('orders', function (Blueprint $table) {
        // Primary Key
        $table->id(); // BIGINT UNSIGNED AUTO_INCREMENT PRIMARY KEY

        // Foreign Keys — đặt gần nhau
        $table->foreignId('user_id')->constrained()->cascadeOnDelete();
        $table->foreignId('coupon_id')->nullable()->constrained()->nullOnDelete();

        // Business fields
        $table->string('order_code', 30)->unique(); // Unique business identifier
        $table->unsignedBigInteger('subtotal');     // VND, không dùng float
        $table->unsignedBigInteger('discount')->default(0);
        $table->unsignedBigInteger('shipping_fee')->default(0);
        $table->unsignedBigInteger('grand_total');
        $table->string('status', 20)->default('pending'); // Enum-like
        $table->string('payment_method', 30)->nullable();
        $table->string('payment_status', 20)->default('unpaid');
        $table->json('shipping_address');          // Lưu snapshot địa chỉ lúc đặt hàng
        $table->text('note')->nullable();

        // Audit fields
        $table->ipAddress('ip_address')->nullable();
        $table->string('user_agent', 500)->nullable();

        // Timestamps — LUÔN có
        $table->timestamps();                      // created_at, updated_at (UTC)
        $table->softDeletes();                     // deleted_at

        // Indexes
        $table->index('order_code');
        $table->index(['user_id', 'status']);
        $table->index(['status', 'created_at']);
        $table->index('payment_status');
    });
}
```

---

## Quy tắc thiết kế quan hệ

```php
// One-to-Many — cascadeOnDelete khi xoá parent phải xoá children
$table->foreignId('order_id')->constrained()->cascadeOnDelete();

// Many-to-Many — Pivot table theo quy tắc: alphabetical order
Schema::create('product_tag', function (Blueprint $table) { // product + tag
    $table->foreignId('product_id')->constrained()->cascadeOnDelete();
    $table->foreignId('tag_id')->constrained()->cascadeOnDelete();
    $table->primary(['product_id', 'tag_id']); // Composite PK
    $table->timestamps();
});

// Nullable FK — khi cho phép unlink
$table->foreignId('category_id')->nullable()->constrained()->nullOnDelete();
```

---

## Snapshot Pattern — Tránh data drift

Khi user thay đổi địa chỉ sau khi đặt hàng, đơn hàng cũ không nên bị ảnh hưởng:

```php
// Sai — lưu FK, bị ảnh hưởng khi user sửa địa chỉ
$table->foreignId('address_id')->constrained();

// Đúng — snapshot JSON tại thời điểm đặt hàng
$table->json('shipping_address'); // {"name":"An","phone":"0901...","province":"HCM"}
```

---

## Liên kết

- [Indexing Strategy](./indexing.md)
- [Eloquent ORM](../09-laravel-core/eloquent-orm.md)
- [Normalization](./normalization.md)
