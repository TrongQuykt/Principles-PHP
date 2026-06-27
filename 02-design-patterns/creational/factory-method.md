# Factory Method

**Priority: 2** | Nhóm: [Creational Patterns](../README.md)

---

## Định nghĩa

**Factory Method** là pattern định nghĩa một interface để tạo object, nhưng cho phép
các subclass quyết định class nào sẽ được khởi tạo. Pattern này ẩn đi logic tạo object
phức tạp khỏi code sử dụng nó.

Nguồn: [Wikipedia — Factory Method Pattern](https://en.wikipedia.org/wiki/Factory_method_pattern)

---

## Khi nào dùng

- Cần tạo object mà không muốn code phụ thuộc vào class cụ thể
- Có nhiều loại object tương tự, chọn loại nào tuỳ thuộc vào điều kiện runtime
- Muốn tập trung logic khởi tạo vào một chỗ

---

## Ví dụ — Payment Gateway Factory

```php
// 1. Định nghĩa interface chung
interface PaymentGateway
{
    public function charge(int $amount, string $currency): PaymentResult;
    public function refund(string $transactionId): RefundResult;
}

// 2. Các implementation cụ thể
class VNPayGateway implements PaymentGateway
{
    public function charge(int $amount, string $currency): PaymentResult { ... }
    public function refund(string $transactionId): RefundResult { ... }
}

class MoMoGateway implements PaymentGateway
{
    public function charge(int $amount, string $currency): PaymentResult { ... }
    public function refund(string $transactionId): RefundResult { ... }
}

// 3. Factory
class PaymentGatewayFactory
{
    public static function make(string $provider): PaymentGateway
    {
        return match ($provider) {
            'vnpay' => new VNPayGateway(config('services.vnpay')),
            'momo'  => new MoMoGateway(config('services.momo')),
            default => throw new \InvalidArgumentException("Unknown payment provider: {$provider}"),
        };
    }
}

// 4. Sử dụng — code không cần biết về VNPay hay MoMo
class PaymentService
{
    public function processPayment(Order $order): PaymentResult
    {
        $gateway = PaymentGatewayFactory::make($order->payment_method);
        return $gateway->charge($order->total, 'VND');
    }
}
```

---

## Lợi ích

| Lợi ích | Giải thích |
|---|---|
| Ẩn complexity | Code sử dụng không cần biết cách tạo object |
| Dễ thêm provider mới | Chỉ cần thêm class mới + 1 case trong factory |
| Tuân thủ OCP | Mở rộng bằng class mới, không sửa code cũ |
| Dễ test | Có thể inject mock gateway trong test |

---

## Liên kết

- [Strategy Pattern](../behavioral/strategy.md) — thường dùng kèm Factory
- [SOLID — OCP](../../01-foundations/SOLID.md)
