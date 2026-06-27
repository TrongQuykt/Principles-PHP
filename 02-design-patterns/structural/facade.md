# Facade

**Priority: 2** | Nhóm: [Structural Patterns](../README.md)

---

## Định nghĩa

**Facade** là pattern cung cấp một interface đơn giản hoá cho một subsystem phức tạp.
Nó ẩn đi sự phức tạp bên trong và cung cấp một "cửa sổ" duy nhất để tương tác với subsystem.

Nguồn: [Wikipedia — Facade Pattern](https://en.wikipedia.org/wiki/Facade_pattern)

---

## Khi nào dùng

- Subsystem có quá nhiều class và method phức tạp
- Muốn cung cấp API đơn giản cho các tác vụ phổ biến
- Muốn giảm coupling giữa client code và subsystem

---

## Ví dụ — Order Processing Facade

```php
// Subsystem phức tạp gồm nhiều service
class InventoryService { public function reserveStock(int $productId, int $qty): void { ... } }
class PaymentService { public function charge(Order $order): PaymentResult { ... } }
class ShippingService { public function createShipment(Order $order): string { ... } }
class NotificationService { public function notifyOrderSuccess(Order $order): void { ... } }
class InvoiceService { public function generate(Order $order): string { ... } }

// Facade: gom toàn bộ luồng vào một nơi, interface đơn giản
class OrderFacade
{
    public function __construct(
        private InventoryService $inventory,
        private PaymentService $payment,
        private ShippingService $shipping,
        private NotificationService $notification,
        private InvoiceService $invoice,
    ) {}

    public function placeOrder(array $cartData): OrderResult
    {
        return DB::transaction(function () use ($cartData) {
            $order = Order::create($cartData);

            $this->inventory->reserveStock($order->product_id, $order->quantity);
            $paymentResult = $this->payment->charge($order);
            $trackingNumber = $this->shipping->createShipment($order);
            $invoicePath = $this->invoice->generate($order);
            $this->notification->notifyOrderSuccess($order);

            return new OrderResult(
                order: $order,
                tracking: $trackingNumber,
                invoice: $invoicePath,
                payment: $paymentResult
            );
        });
    }
}

// Controller chỉ gọi Facade — không cần biết về các service bên trong
class OrderController extends Controller
{
    public function store(StoreOrderRequest $request, OrderFacade $facade): JsonResponse
    {
        $result = $facade->placeOrder($request->validated());
        return response()->json(['status' => 'success', 'data' => $result]);
    }
}
```

---

## Liên kết

- [Separation of Concerns](../../01-foundations/separation-of-concerns.md)
- [Adapter](./adapter.md)
