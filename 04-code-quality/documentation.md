# Documentation

**Priority: 4** | Nhóm: [Code Quality](./README.md)

---

## Nguyên tắc

> "Write code for the maintainer."
>
> — C2 Wiki

Code tốt là code **tự mô tả** (self-documenting). Comment là công cụ cuối cùng,
không phải công cụ đầu tiên.

---

## Khi nào VIẾT comment

| Tình huống | Ví dụ |
|---|---|
| Giải thích **tại sao** (why), không giải thích **làm gì** (what) | `// Làm tròn xuống vì VNPay không chấp nhận số thập phân` |
| Mô tả business rule không hiển nhiên | `// Đơn hàng > 30 phút chưa thanh toán sẽ tự cancel (theo SLA)` |
| TODO có ticket tracking | `// TODO(#234): Chuyển sang Redis khi throughput vượt 1000 req/s` |
| Warning về gotcha | `// CAUTION: Không cache method này — data thay đổi mỗi request` |

---

## Khi nào KHÔNG viết comment

```php
// Sai — comment chỉ nhắc lại code
// Lấy user theo ID
$user = User::findOrFail($id);

// Sai — comment thay cho đặt tên tốt
// Tính tổng sau giảm giá
$x = $p * (1 - $d / 100);

// Đúng — đặt tên tốt, không cần comment
$discountedPrice = $basePrice * (1 - $discountPercentage / 100);
```

---

## PHPDoc — API public

Mọi **public method** của class Library/Service cần có PHPDoc:

```php
/**
 * Tạo đơn hàng mới và trừ tồn kho tương ứng.
 *
 * @param  array{product_id: int, quantity: int, user_id: int}  $data
 * @return Order
 *
 * @throws InsufficientStockException Khi tồn kho không đủ
 * @throws \Illuminate\Database\Eloquent\ModelNotFoundException Khi product không tồn tại
 */
public function createOrder(array $data): Order
```

---

## README của module

Mỗi folder chính cần có `README.md` với:

```markdown
# Tên Module

Mô tả ngắn gọn module này làm gì.

## Trách nhiệm

- Điểm 1
- Điểm 2

## Cách sử dụng

[Code example]

## Không thuộc trách nhiệm của module này

- Điểm ngoài scope
```

---

## Liên kết

- [Naming Conventions](./naming-conventions.md)
- [Code Review](./code-review.md)
