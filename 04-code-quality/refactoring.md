# Refactoring

**Priority: 4** | Nhóm: [Code Quality](./README.md)

---

## Định nghĩa

**Refactoring** là quá trình tái cấu trúc code hiện có **mà không thay đổi hành vi bên ngoài**,
nhằm cải thiện cấu trúc, khả năng đọc, và khả năng bảo trì.

Nguồn: [Wikipedia — Code refactoring](https://en.wikipedia.org/wiki/Code_refactoring)

---

## Nguyên tắc khi Refactor

### 1. Không refactor và thêm tính năng cùng lúc

Đây là quy tắc quan trọng nhất:

```
Bước 1: Viết test để cover behavior hiện tại
Bước 2: Refactor code
Bước 3: Verify test vẫn pass
Bước 4: Chỉ sau đó mới thêm tính năng mới
```

### 2. Refactor trong phạm vi nhỏ

Mỗi refactoring phải là một commit riêng, nhỏ và có thể review.

### 3. Tuân thủ Surgical Changes

Chỉ refactor những gì thuộc phạm vi task hiện tại. Xem [Surgical Changes](../07-karpathy-principles/surgical-changes.md).

---

## Code Smells — Dấu hiệu cần Refactor

| Code Smell | Mô tả | Giải pháp |
|---|---|---|
| Long Method | Hàm quá dài (>20 dòng) | Extract Method |
| Large Class | Class quá nhiều trách nhiệm | Extract Class |
| Duplicate Code | Logic lặp lại ở nhiều nơi | Extract Method/Class |
| Long Parameter List | Hàm có >4 tham số | Introduce Parameter Object |
| Feature Envy | Method dùng data của class khác nhiều hơn class mình | Move Method |
| Magic Numbers | Số cứng không có tên | Introduce Constant |
| Deep Nesting | If/loop lồng nhau quá sâu | Extract Method, Guard Clauses |
| God Class | Một class biết/làm mọi thứ | Split Responsibilities |

---

## Kỹ thuật Refactoring phổ biến

### Extract Method

```php
// Trước
public function processOrder(Order $order): void
{
    // Tính phí vận chuyển
    if ($order->total > 500000) {
        $shippingFee = 0;
    } elseif ($order->address->province === 'HCM') {
        $shippingFee = 15000;
    } else {
        $shippingFee = 30000;
    }

    // Áp dụng discount
    $discount = $order->coupon ? $order->total * 0.1 : 0;

    $order->update(['shipping_fee' => $shippingFee, 'discount' => $discount]);
}

// Sau — extract method rõ ràng
public function processOrder(Order $order): void
{
    $order->update([
        'shipping_fee' => $this->calculateShippingFee($order),
        'discount'     => $this->calculateDiscount($order),
    ]);
}

private function calculateShippingFee(Order $order): int
{
    if ($order->total > 500000) return 0;
    return $order->address->province === 'HCM' ? 15000 : 30000;
}

private function calculateDiscount(Order $order): float
{
    return $order->coupon ? $order->total * 0.1 : 0;
}
```

### Guard Clauses — giảm nesting

```php
// Trước — deep nesting
public function processPayment(Order $order): PaymentResult
{
    if ($order->status === 'pending') {
        if ($order->total > 0) {
            if ($this->gateway->isAvailable()) {
                return $this->gateway->charge($order->total);
            } else {
                throw new GatewayUnavailableException();
            }
        } else {
            throw new InvalidAmountException();
        }
    } else {
        throw new InvalidOrderStatusException();
    }
}

// Sau — Guard Clauses, nesting giảm từ 3 xuống 0
public function processPayment(Order $order): PaymentResult
{
    if ($order->status !== 'pending') {
        throw new InvalidOrderStatusException();
    }
    if ($order->total <= 0) {
        throw new InvalidAmountException();
    }
    if (!$this->gateway->isAvailable()) {
        throw new GatewayUnavailableException();
    }

    return $this->gateway->charge($order->total);
}
```

---

## Liên kết

- [Surgical Changes](../07-karpathy-principles/surgical-changes.md)
- [Testing Principles](./testing-principles.md)
- [SOLID](../01-foundations/SOLID.md)
