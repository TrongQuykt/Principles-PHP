# DRY — Don't Repeat Yourself

**Priority: 1** | Nhóm: [Foundations](./README.md)

---

## Định nghĩa

> "Every piece of knowledge must have a single, unambiguous, authoritative representation
> within a system."
>
> — Andy Hunt & Dave Thomas, The Pragmatic Programmer

**DRY** yêu cầu mỗi **kiến thức** (logic, quy tắc, dữ liệu, cấu hình) chỉ được thể hiện
tại **một nơi duy nhất** trong codebase. Khi cần thay đổi, chỉ cần thay đổi ở một chỗ.

Nguồn: [Wikipedia — Don't repeat yourself](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself)

---

## DRY không có nghĩa là "không viết code giống nhau"

DRY là về **kiến thức**, không phải về **cú pháp**. Hai đoạn code trông giống nhau
nhưng đại diện cho hai khái niệm khác nhau thì không vi phạm DRY.

**Ví dụ: Không vi phạm DRY dù code giống nhau**

```php
// Module shopping cart
function validateQuantity(int $qty): bool
{
    return $qty >= 1 && $qty <= 100;
}

// Module warehouse
function validateStockLevel(int $qty): bool
{
    return $qty >= 1 && $qty <= 100;
}
```

Mặc dù hai hàm có logic giống nhau, nhưng chúng đại diện cho hai **quy tắc kinh doanh khác nhau**.
Nếu quy tắc của warehouse thay đổi, shopping cart không nên bị ảnh hưởng.
Gộp chúng vào một hàm chung sẽ tạo ra **coupling không cần thiết**.

---

## Các dạng vi phạm DRY phổ biến

### 1. Sao chép logic business

```php
// Vi phạm: Công thức tính giá sau giảm giá lặp lại ở nhiều nơi

// Trong ProductController:
$finalPrice = $product->price * (1 - $product->discount / 100);

// Trong CartService:
$finalPrice = $item->price * (1 - $item->discount / 100);

// Trong InvoiceService:
$finalPrice = $order->unit_price * (1 - $order->discount_pct / 100);
```

```php
// Đúng: Tập trung vào một nơi

// app/Helpers/PriceCalculator.php
class PriceCalculator
{
    public static function applyDiscount(float $price, float $discountPercentage): float
    {
        return $price * (1 - $discountPercentage / 100);
    }
}

// Dùng ở mọi nơi:
$finalPrice = PriceCalculator::applyDiscount($product->price, $product->discount);
```

### 2. Magic numbers lặp lại

```php
// Vi phạm
if ($cart->total > 500000) { ... }    // Trong CartService
if ($order->amount > 500000) { ... }  // Trong OrderService
if ($payment->value > 500000) { ... } // Trong PaymentService
```

```php
// Đúng: Đặt constant ở một chỗ
// config/business.php
return [
    'free_shipping_threshold' => 500000,
];

// Dùng ở mọi nơi:
if ($cart->total > config('business.free_shipping_threshold')) { ... }
```

### 3. Lặp lại cấu hình

```php
// Vi phạm: Hardcode URL ở nhiều nơi
$response = Http::get('https://api.vnpay.vn/v2/paymentUrl');   // Trong PaymentService
$callback = 'https://api.vnpay.vn/v2/callback';                  // Trong WebhookController
```

```php
// Đúng
// .env
VNPAY_BASE_URL=https://api.vnpay.vn/v2

// Dùng:
$response = Http::get(config('services.vnpay.base_url') . '/paymentUrl');
```

### 4. Lặp lại trong views/templates

```html
<!-- Vi phạm: Cùng một component được paste nhiều nơi -->
<!-- Trong product-list.blade.php -->
<div class="card">
    <h3>{{ $product->name }}</h3>
    <p>{{ number_format($product->price) }}đ</p>
</div>

<!-- Trong search-results.blade.php - copy paste y chang -->
<div class="card">
    <h3>{{ $product->name }}</h3>
    <p>{{ number_format($product->price) }}đ</p>
</div>
```

```html
<!-- Đúng: Tạo partial component -->
<!-- resources/views/components/product-card.blade.php -->
<div class="card">
    <h3>{{ $product->name }}</h3>
    <p>{{ number_format($product->price) }}đ</p>
</div>

<!-- Dùng ở mọi nơi -->
<x-product-card :product="$product" />
```

---

## Abstraction Principle — Nguyên tắc liên quan

> "Each significant piece of functionality in a program should be implemented
> in just one place in the source code."

Đây là phát biểu khác của DRY ở cấp độ functionality.
Nguồn: [Wikipedia — Abstraction Principle](https://en.wikipedia.org/wiki/Abstraction_principle_(programming))

---

## Khi nào KHÔNG nên áp dụng DRY một cách cứng nhắc

| Tình huống | Lý do |
|---|---|
| Hai đoạn code giống nhau nhưng đại diện cho hai business rule khác nhau | Gộp vào sẽ tạo coupling không cần thiết |
| Abstraction để tái sử dụng phức tạp hơn việc lặp lại | Vi phạm KISS |
| Chỉ xảy ra 2 lần với ít thay đổi dự kiến | Rule of three: xem xét sau lần thứ 3 |

**Rule of Three**: Khi bạn viết một thứ gì đó lần 3, hãy refactor.
Lần 1 — viết. Lần 2 — sao chép (và chấp nhận). Lần 3 — abstract.

---

## Checklist — DRY

- [ ] Logic business quan trọng chỉ tồn tại ở một nơi
- [ ] Không có magic number nào được hardcode ở nhiều chỗ
- [ ] Các config value được đặt trong config file/env
- [ ] Các component UI được tách thành reusable component

---

## Liên kết

- [KISS](./KISS.md)
- [YAGNI](./YAGNI.md)
- [Refactoring](../04-code-quality/refactoring.md)
