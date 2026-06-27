# OOP PHP — Lập Trình Hướng Đối Tượng

**Priority: 1 (Foundations — SOLID)** | Nhóm: [PHP Syntax](./README.md)

---

## Các khái niệm cốt lõi PHP 8.x

### Class, Interface, Abstract Class

```php
// Interface — hợp đồng (không có implementation)
interface Payable
{
    public function charge(int $amount): PaymentResult;
    public function refund(string $txId): RefundResult;
}

// Abstract Class — có thể có implementation một phần
abstract class BasePaymentGateway implements Payable
{
    abstract protected function buildHeaders(): array;

    // Concrete method — dùng chung cho tất cả gateway
    public function verifySignature(string $payload, string $signature): bool
    {
        return hash_hmac('sha256', $payload, config('app.secret')) === $signature;
    }
}

// Concrete Class
class VNPayGateway extends BasePaymentGateway
{
    protected function buildHeaders(): array
    {
        return ['X-Api-Key' => config('services.vnpay.key')];
    }

    public function charge(int $amount): PaymentResult { ... }
    public function refund(string $txId): RefundResult { ... }
}
```

---

### Constructor Promotion (PHP 8.0+)

```php
// Cách cũ (verbose)
class OrderService
{
    private OrderRepository $repo;
    private PaymentGatewayInterface $payment;

    public function __construct(
        OrderRepository $repo,
        PaymentGatewayInterface $payment
    ) {
        $this->repo    = $repo;
        $this->payment = $payment;
    }
}

// Cách mới PHP 8 — Constructor Promotion
class OrderService
{
    public function __construct(
        private readonly OrderRepository $repo,
        private readonly PaymentGatewayInterface $payment,
    ) {}
}
```

---

### Enums (PHP 8.1+)

```php
// Backed Enum — có value
enum OrderStatus: string
{
    case Pending   = 'pending';
    case Confirmed = 'confirmed';
    case Shipped   = 'shipped';
    case Delivered = 'delivered';
    case Cancelled = 'cancelled';

    public function label(): string
    {
        return match($this) {
            self::Pending   => 'Chờ xác nhận',
            self::Confirmed => 'Đã xác nhận',
            self::Shipped   => 'Đang giao',
            self::Delivered => 'Đã giao',
            self::Cancelled => 'Đã huỷ',
        };
    }

    public function canTransitionTo(self $new): bool
    {
        return match($this) {
            self::Pending   => in_array($new, [self::Confirmed, self::Cancelled]),
            self::Confirmed => in_array($new, [self::Shipped, self::Cancelled]),
            self::Shipped   => $new === self::Delivered,
            default         => false,
        };
    }
}

// Dùng trong Eloquent cast
protected $casts = ['status' => OrderStatus::class];

// Kiểm tra
$order->status === OrderStatus::Pending;
$order->status->label(); // 'Chờ xác nhận'
```

---

### Named Arguments (PHP 8.0+)

```php
// Truyền argument theo tên — rõ ràng hơn
$result = array_slice(
    array: $items,
    offset: 0,
    length: 15,
    preserve_keys: true
);

Carbon::create(year: 2024, month: 6, day: 27);
```

---

### Match Expression (PHP 8.0+)

```php
// Mạnh hơn switch — strict comparison, trả về value, ném exception nếu không match
$label = match($order->status) {
    OrderStatus::Pending   => 'Chờ xử lý',
    OrderStatus::Shipped   => 'Đang giao',
    OrderStatus::Delivered => 'Hoàn thành',
    default                => 'Không xác định',
};
```

---

### Readonly Properties (PHP 8.1+)

```php
class Money
{
    public function __construct(
        public readonly int $amount,
        public readonly string $currency = 'VND',
    ) {}
    // $money->amount = 100; ← Fatal Error — immutable!
}
```

---

### Traits — Tái sử dụng code ngang hàng

```php
trait HasTimestamps
{
    public function getCreatedAtFormatted(): string
    {
        return $this->created_at->format('d/m/Y H:i');
    }
}

trait Auditable
{
    public function createdBy(): BelongsTo
    {
        return $this->belongsTo(User::class, 'created_by');
    }
}

class Order extends Model
{
    use HasTimestamps, Auditable, SoftDeletes;
}
```

---

## Liên kết

- [SOLID](../01-foundations/SOLID.md)
- [Service Container](../09-laravel-core/service-container.md)
