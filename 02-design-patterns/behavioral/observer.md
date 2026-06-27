# Observer

**Priority: 2** | Nhóm: [Behavioral Patterns](../README.md)

---

## Định nghĩa

**Observer** là pattern định nghĩa mối phụ thuộc one-to-many giữa các object:
khi một object (subject) thay đổi trạng thái, tất cả các observer của nó được thông báo và cập nhật tự động.

Nguồn: [Wikipedia — Observer Pattern](https://en.wikipedia.org/wiki/Observer_pattern)

---

## Khi nào dùng

- Nhiều component cần phản ứng với cùng một sự kiện
- Muốn tách biệt subject khỏi các observer (loose coupling)
- Số lượng observer không biết trước hoặc có thể thay đổi

---

## Ví dụ — Laravel Events (Observer Pattern trong thực tế)

```php
// 1. Event (Subject thông báo)
class OrderPlaced
{
    public function __construct(public readonly Order $order) {}
}

// 2. Các Listener (Observer)
class SendOrderConfirmationEmail
{
    public function handle(OrderPlaced $event): void
    {
        Mail::to($event->order->user->email)
            ->send(new OrderConfirmationMail($event->order));
    }
}

class ReserveInventory
{
    public function handle(OrderPlaced $event): void
    {
        foreach ($event->order->items as $item) {
            $item->product->decrement('stock', $item->quantity);
        }
    }
}

class NotifyWarehouse
{
    public function handle(OrderPlaced $event): void
    {
        // Gửi thông báo đến kho
        Warehouse::dispatch($event->order);
    }
}

// 3. Đăng ký (EventServiceProvider)
protected $listen = [
    OrderPlaced::class => [
        SendOrderConfirmationEmail::class,
        ReserveInventory::class,
        NotifyWarehouse::class,
    ],
];

// 4. Trigger Subject — OrderService không biết gì về các observer
class OrderService
{
    public function createOrder(array $data): Order
    {
        $order = Order::create($data);
        event(new OrderPlaced($order)); // Observer tự xử lý phần còn lại
        return $order;
    }
}
```

---

## Lợi ích

| Lợi ích | Giải thích |
|---|---|
| Loose coupling | OrderService không phụ thuộc vào Email, Inventory, Warehouse |
| Dễ mở rộng | Thêm observer mới không cần sửa OrderService |
| Tuân thủ OCP | Mở rộng bằng listener mới |
| Dễ test | Test từng listener độc lập |

---

## Liên kết

- [Strategy](./strategy.md)
- [Separation of Concerns](../../01-foundations/separation-of-concerns.md)
