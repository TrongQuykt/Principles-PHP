# Layered Architecture — Kiến Trúc Phân Tầng

**Priority: 3** | Nhóm: [Architecture](./README.md)

---

## Định nghĩa

**Layered Architecture** (còn gọi là N-Tier Architecture) tổ chức code theo các tầng nằm chồng lên nhau,
mỗi tầng chỉ có thể giao tiếp với tầng ngay bên dưới nó.

Nguồn: [Wikipedia — Multitier Architecture](https://en.wikipedia.org/wiki/Multitier_architecture)

---

## Các tầng trong web application

```
Presentation Layer     <- HTTP, JSON, Blade/HTML
      |
      |  (chỉ gọi xuống)
      v
Application Layer      <- Controller, FormRequest, API Resource
      |
      v
Business Logic Layer   <- Service, Domain Objects, Business Rules
      |
      v
Data Access Layer      <- Repository, Model, ORM
      |
      v
Infrastructure Layer   <- Database, Cache, Queue, External APIs
```

---

## Quy tắc phụ thuộc

- Tầng trên **phụ thuộc** vào tầng dưới
- Tầng dưới **KHÔNG** biết gì về tầng trên
- Không được bỏ qua tầng (no layer skipping)

```php
// SAI: Controller gọi thẳng vào Database
class OrderController extends Controller
{
    public function store(Request $request)
    {
        DB::table('orders')->insert([...]); // Bỏ qua Service và Model layer
    }
}

// ĐÚNG: Theo đúng thứ tự tầng
class OrderController extends Controller
{
    public function store(StoreOrderRequest $request, OrderService $service): JsonResponse
    {
        $order = $service->createOrder($request->validated()); // Gọi đúng tầng dưới
        return response()->json(['data' => $order]);
    }
}
```

---

## Cấu trúc thư mục Laravel theo Layered Architecture

```
app/
├── Http/
│   ├── Controllers/          <- Application Layer
│   ├── Requests/             <- Application Layer (Validation)
│   └── Resources/            <- Presentation Layer (JSON transformation)
├── Services/                 <- Business Logic Layer
├── Repositories/             <- Data Access Layer
├── Models/                   <- Data Access Layer (Eloquent)
└── Infrastructure/           <- Infrastructure Layer (adapters, external)
```

---

## Liên kết

- [MVC](./mvc.md)
- [Clean Architecture](./clean-architecture.md)
- [Separation of Concerns](../01-foundations/separation-of-concerns.md)
