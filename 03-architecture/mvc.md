# MVC — Model-View-Controller

**Priority: 3** | Nhóm: [Architecture](./README.md)

---

## Định nghĩa

**MVC** là pattern kiến trúc chia ứng dụng thành 3 lớp với trách nhiệm riêng biệt:

| Lớp | Nhiệm vụ |
|---|---|
| **Model** | Dữ liệu, business rules, quan hệ database |
| **View** | Hiển thị giao diện người dùng |
| **Controller** | Điều phối: nhận request → gọi Model → trả về View/Response |

Nguồn: [Wikipedia — MVC](https://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93controller)

---

## Luồng xử lý trong Laravel

```
Browser
   |
   | HTTP Request
   v
Router (routes/web.php hoặc routes/api.php)
   |
   v
Middleware (Auth, CORS, Rate Limit...)
   |
   v
Controller
   ├── Gọi FormRequest để validate
   ├── Gọi Service (nếu có) để xử lý business logic
   ├── Gọi Model/Repository để truy cập data
   └── Trả về Response (JSON hoặc View)
   |
   v
Model (Eloquent)
   |
   v
Database
```

---

## Quy tắc phân chia trách nhiệm

### Model — Chỉ làm

```php
class Product extends Model
{
    // Fillable, casts
    protected $fillable = ['name', 'price', 'stock'];
    protected $casts = ['price' => 'float'];

    // Relationships
    public function category(): BelongsTo { return $this->belongsTo(Category::class); }
    public function variants(): HasMany { return $this->hasMany(ProductVariant::class); }

    // Scopes
    public function scopeAvailable(Builder $query): Builder
    {
        return $query->where('stock', '>', 0)->where('is_active', true);
    }

    // Accessors
    public function getFormattedPriceAttribute(): string
    {
        return number_format($this->price) . 'đ';
    }
}
```

### Controller — Chỉ điều phối

```php
class ProductController extends Controller
{
    // Đúng: Chỉ nhận request, gọi service, trả response
    public function store(StoreProductRequest $request, ProductService $service): JsonResponse
    {
        $product = $service->createProduct($request->validated());
        return response()->json(['status' => 'success', 'data' => $product], 201);
    }

    // Sai: Controller chứa business logic
    public function store(Request $request): JsonResponse
    {
        // Validation ở đây = sai
        // Business logic ở đây = sai
        // DB query trực tiếp = sai
    }
}
```

---

## MVC vs MVC thuần trong Laravel

Laravel mở rộng MVC truyền thống với thêm các lớp:

```
MVC Chuẩn        Laravel Nâng Cao
-----------      ----------------
Controller   ->  Controller + FormRequest + Middleware
Model        ->  Model + Repository + Service
View         ->  Blade View + API Resource / JSON Response
```

---

## Liên kết

- [Layered Architecture](./layered-architecture.md)
- [Separation of Concerns](../01-foundations/separation-of-concerns.md)
