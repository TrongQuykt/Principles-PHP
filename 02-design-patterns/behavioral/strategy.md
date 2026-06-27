# Strategy

**Priority: 2** | Nhóm: [Behavioral Patterns](../README.md)

---

## Định nghĩa

**Strategy** là pattern định nghĩa một nhóm các algorithm, đóng gói từng cái vào một class riêng,
và cho phép chúng có thể thay thế lẫn nhau. Client chọn strategy phù hợp tại runtime.

Nguồn: [Wikipedia — Strategy Pattern](https://en.wikipedia.org/wiki/Strategy_pattern)

---

## Khi nào dùng

- Có nhiều cách thực hiện một tác vụ, có thể thay thế nhau
- Muốn loại bỏ các điều kiện if/else chọn algorithm
- Cần thêm algorithm mới mà không sửa code hiện tại

---

## Ví dụ — Discount Strategy

```php
// 1. Strategy Interface
interface DiscountStrategy
{
    public function apply(float $price, array $context = []): float;
    public function describe(): string;
}

// 2. Các Strategy cụ thể
class PercentageDiscount implements DiscountStrategy
{
    public function __construct(private float $percentage) {}

    public function apply(float $price, array $context = []): float
    {
        return $price * (1 - $this->percentage / 100);
    }

    public function describe(): string
    {
        return "Giảm {$this->percentage}%";
    }
}

class FixedAmountDiscount implements DiscountStrategy
{
    public function __construct(private float $amount) {}

    public function apply(float $price, array $context = []): float
    {
        return max(0, $price - $this->amount);
    }

    public function describe(): string
    {
        return "Giảm " . number_format($this->amount) . "đ";
    }
}

class BuyXGetYDiscount implements DiscountStrategy
{
    public function __construct(private int $buyX, private int $getFree) {}

    public function apply(float $price, array $context = []): float
    {
        $quantity = $context['quantity'] ?? 1;
        $freeItems = intdiv($quantity, $this->buyX) * $this->getFree;
        $payableItems = $quantity - $freeItems;
        return ($price / $quantity) * $payableItems;
    }

    public function describe(): string
    {
        return "Mua {$this->buyX} tặng {$this->getFree}";
    }
}

// 3. Context sử dụng Strategy
class PriceCalculator
{
    public function calculate(float $price, DiscountStrategy $strategy, array $context = []): array
    {
        $finalPrice = $strategy->apply($price, $context);
        return [
            'original_price' => $price,
            'final_price'    => $finalPrice,
            'discount'       => $price - $finalPrice,
            'description'    => $strategy->describe(),
        ];
    }
}

// 4. Sử dụng
$calculator = new PriceCalculator();

$result = $calculator->calculate(
    price: 200000,
    strategy: new PercentageDiscount(10)
);
// ['original_price' => 200000, 'final_price' => 180000, ...]
```

---

## So sánh với if/else (trước khi áp dụng Strategy)

```php
// Trước — vi phạm OCP, khó mở rộng
public function applyDiscount(string $type, float $price): float
{
    if ($type === 'percentage_10') {
        return $price * 0.9;
    } elseif ($type === 'fixed_50k') {
        return max(0, $price - 50000);
    }
    // Thêm loại mới = sửa hàm này
    return $price;
}
```

---

## Liên kết

- [Factory Method](../creational/factory-method.md)
- [SOLID — OCP](../../01-foundations/SOLID.md)
