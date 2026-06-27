# Caching Strategies

**Priority: 6** | Nhóm: [Performance](./README.md)

---

## Nguyên tắc cơ bản

Cache là trade-off giữa **tốc độ** và **độ tươi của dữ liệu**.
Cache sai có thể dẫn đến dữ liệu cũ, inconsistency, hoặc security issue.

---

## Cache Layers

| Layer | Công nghệ | TTL phổ biến | Dùng cho |
|---|---|---|---|
| Application Cache | Laravel Cache (Redis/Memcached) | 5 phút - 24 giờ | Query results, computed values |
| HTTP Cache | Cache-Control headers | Vài giây - 1 tuần | Static pages, API responses |
| CDN Cache | Cloudflare, AWS CloudFront | 1 giờ - 1 ngày | Static assets, images |
| Database Query Cache | ORM caching | Per request | Repeated identical queries |

---

## Cache trong Laravel

```php
// Cache một query phức tạp
$topProducts = Cache::remember('products.top10', 3600, function () {
    return Product::with('category')
        ->where('is_featured', true)
        ->orderByDesc('sales_count')
        ->limit(10)
        ->get();
});

// Cache với tag để dễ invalidate
$products = Cache::tags(['products', 'category:' . $categoryId])
    ->remember("products.category.{$categoryId}", 1800, fn () =>
        Product::where('category_id', $categoryId)->get()
    );

// Invalidate khi data thay đổi
Cache::tags(['products'])->flush(); // Xoá tất cả product-related cache
```

---

## Cache Invalidation Strategies

| Strategy | Mô tả | Khi dùng |
|---|---|---|
| TTL-based | Cache tự expire sau X giây | Dữ liệu chấp nhận được delay ngắn |
| Event-based | Invalidate khi data thay đổi | Cần real-time accuracy |
| Tag-based | Invalidate theo nhóm | Nhiều cache item liên quan |

---

## Những gì KHÔNG nên cache

- Dữ liệu người dùng cụ thể mà không dùng user-scoped key
- Sensitive data (token, password, session)
- Kết quả query quá nhỏ (không đủ lợi ích)
- Dữ liệu thay đổi mỗi request

---

## Cache Key Conventions

```php
// Rõ ràng, có thể dự đoán, tránh collision
"products.top10"
"product.{id}"
"user.{id}.cart"
"category.{id}.products.page.{page}"
```

---

## Liên kết

- [Database Optimization](./database-optimization.md)
