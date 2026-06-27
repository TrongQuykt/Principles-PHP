# Separation of Concerns

**Priority: 1** | Nhóm: [Foundations](./README.md)

---

## Định nghĩa

> "Different areas of functionality should be managed by distinct and minimally overlapping modules of code."
>
> — Christopher Diggins, Artima

**Separation of Concerns (SoC)** là nguyên tắc chia hệ thống thành các phần riêng biệt,
mỗi phần chịu trách nhiệm về **một mối quan tâm** (concern) cụ thể.
Các phần này có sự chồng lấp tối thiểu với nhau.

Nguồn: [Wikipedia — Separation of Concerns](https://en.wikipedia.org/wiki/Separation_of_concerns)

---

## "Concern" là gì?

Một **concern** là một khía cạnh riêng biệt của hệ thống:

| Concern | Ví dụ |
|---|---|
| Business logic | Tính toán giá, kiểm tra tồn kho |
| Data persistence | Lưu/đọc từ database |
| Presentation | Hiển thị HTML, render JSON |
| Authentication | Xác thực danh tính |
| Logging | Ghi log sự kiện |
| Validation | Kiểm tra dữ liệu đầu vào |

---

## Ứng dụng trong kiến trúc web

### Phân tầng theo SoC trong Laravel

```
HTTP Request
     |
     v
Middleware              <- Concern: Authentication, Rate Limiting
     |
     v
FormRequest             <- Concern: Input Validation
     |
     v
Controller              <- Concern: Điều phối, trả HTTP Response
     |
     v
Service                 <- Concern: Business Logic
     |
     v
Repository/Model        <- Concern: Data Access
     |
     v
Database
```

Mỗi tầng chỉ biết về tầng ngay dưới nó. Controller không biết database, Service không biết HTTP.

---

## Ví dụ vi phạm và đúng

### Sai — Trộn lẫn các concern

```php
class OrderController extends Controller
{
    public function store(Request $request): JsonResponse
    {
        // Concern: Validation (nên ở FormRequest)
        if (!$request->has('product_id') || !is_numeric($request->product_id)) {
            return response()->json(['error' => 'Invalid product_id'], 422);
        }

        // Concern: Business Logic (nên ở Service)
        $product = Product::find($request->product_id);
        if ($product->stock < $request->quantity) {
            return response()->json(['error' => 'Out of stock'], 400);
        }
        $total = $product->price * $request->quantity * (1 - $product->discount / 100);

        // Concern: Data Persistence (nên ở Repository/Model)
        DB::beginTransaction();
        $order = Order::create([
            'user_id' => auth()->id(),
            'total'   => $total,
        ]);
        $order->items()->create([
            'product_id' => $product->id,
            'quantity'   => $request->quantity,
        ]);
        $product->decrement('stock', $request->quantity);
        DB::commit();

        // Concern: Notification (nên ở Event/Listener)
        Mail::to(auth()->user()->email)->send(new OrderConfirmationMail($order));

        return response()->json(['status' => 'success', 'data' => $order]);
    }
}
```

### Đúng — Mỗi tầng chỉ làm một việc

```php
// Concern: Validation
class StoreOrderRequest extends FormRequest
{
    public function rules(): array
    {
        return [
            'product_id' => 'required|integer|exists:products,id',
            'quantity'   => 'required|integer|min:1',
        ];
    }
}

// Concern: Business Logic
class OrderService
{
    public function createOrder(array $data): Order
    {
        $product = Product::findOrFail($data['product_id']);

        if ($product->stock < $data['quantity']) {
            throw new InsufficientStockException();
        }

        return DB::transaction(function () use ($data, $product) {
            $order = Order::create([
                'user_id' => auth()->id(),
                'total'   => $this->calculateTotal($product, $data['quantity']),
            ]);
            $order->items()->create($data);
            $product->decrement('stock', $data['quantity']);
            return $order;
        });
    }
}

// Concern: HTTP Coordination
class OrderController extends Controller
{
    public function store(StoreOrderRequest $request, OrderService $orderService): JsonResponse
    {
        $order = $orderService->createOrder($request->validated());
        return response()->json(['status' => 'success', 'data' => $order], 201);
    }
}

// Concern: Notification (qua Event)
class OrderCreatedListener
{
    public function handle(OrderCreated $event): void
    {
        Mail::to($event->order->user->email)->send(new OrderConfirmationMail($event->order));
    }
}
```

---

## SoC trong Frontend

```
src/
├── components/        <- Concern: UI rendering
├── hooks/             <- Concern: State & side effects
├── lib/api.ts         <- Concern: Data fetching
├── types/             <- Concern: Type contracts
└── utils/             <- Concern: Pure utility functions
```

---

## Mối quan hệ với SRP (SOLID)

**SRP** (Single Responsibility Principle) là SoC áp dụng ở cấp độ **class/function**.
**SoC** là nguyên tắc ở cấp độ **module/tầng kiến trúc**.

Cả hai cùng hướng đến một mục tiêu: giảm coupling, tăng cohesion.

---

## Checklist — Separation of Concerns

- [ ] Controller chỉ làm nhiệm vụ điều phối, không chứa business logic
- [ ] Service chỉ chứa business logic, không biết về HTTP
- [ ] Validation được đặt trong FormRequest, không trong Controller
- [ ] Notification được trigger qua Event, không inline trong Service
- [ ] Frontend tách biệt: component, hooks, API client, types

---

## Liên kết

- [SOLID](./SOLID.md)
- [Layered Architecture](../03-architecture/layered-architecture.md)
- [MVC](../03-architecture/mvc.md)
