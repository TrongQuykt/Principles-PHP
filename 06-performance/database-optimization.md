# Database Optimization

**Priority: 6** | Nhóm: [Performance](./README.md)

---

## N+1 Query Problem

**N+1** là vấn đề phổ biến nhất: 1 query lấy danh sách + N query con cho từng item.

```php
// VI PHẠM — N+1
$orders = Order::all(); // 1 query
foreach ($orders as $order) {
    echo $order->user->name;    // +1 query cho mỗi order
    echo $order->items->count(); // +1 query nữa
}
// Với 100 orders = 201 queries!

// ĐÚNG — Eager Loading
$orders = Order::with(['user', 'items'])->get(); // 3 queries tổng
foreach ($orders as $order) {
    echo $order->user->name;    // Đã có sẵn trong memory
    echo $order->items->count(); // Đã có sẵn
}
```

---

## Indexing

Đánh index đúng chỗ giảm thời gian query từ O(n) xuống O(log n).

```php
// Migration — đánh index cho các cột thường query
Schema::create('orders', function (Blueprint $table) {
    $table->id();
    $table->foreignId('user_id')->constrained()->index(); // Index FK
    $table->string('status')->index();                    // Index enum-like column
    $table->string('tracking_number')->nullable()->index();
    $table->timestamps();
    $table->softDeletes();

    // Composite index cho query phổ biến
    $table->index(['user_id', 'status']); // WHERE user_id = ? AND status = ?
    $table->index(['status', 'created_at']); // WHERE status = ? ORDER BY created_at
});
```

**Quy tắc đánh index**:
- Cột trong `WHERE` clause thường xuyên
- Cột trong `ORDER BY` và `GROUP BY`
- Foreign key columns
- Không index quá nhiều (mỗi index làm chậm INSERT/UPDATE)

---

## Pagination — Không dùng all()

```php
// KHÔNG BAO GIỜ
$orders = Order::all(); // Load toàn bộ bảng vào memory

// ĐÚNG — Paginate
$orders = Order::with('user')
    ->where('status', 'pending')
    ->latest()
    ->paginate(15);
```

---

## Select chỉ những gì cần

```php
// Xấu — load toàn bộ columns
$users = User::all();

// Tốt — chỉ load cần thiết
$users = User::select('id', 'name', 'email')->get();

// Tốt cho list/dropdown
$users = User::pluck('name', 'id');
```

---

## DB::transaction() cho operations phức tạp

```php
// Bọc tất cả writes liên quan vào 1 transaction
DB::transaction(function () use ($orderData) {
    $order = Order::create($orderData['order']);
    $order->items()->createMany($orderData['items']);
    
    foreach ($orderData['items'] as $item) {
        Product::where('id', $item['product_id'])
               ->decrement('stock', $item['quantity']);
    }
}); // Nếu bất kỳ bước nào fail — tất cả rollback
```

---

## Slow Query Monitoring

Dùng Laravel Telescope hoặc Debugbar để detect:
- Query chậm (> 100ms)
- Query trùng lặp
- N+1 queries

---

## Liên kết

- [Caching Strategies](./caching-strategies.md)
- [Design Patterns — Repository](../02-design-patterns/README.md)
