# KISS — Keep It Simple, Stupid

**Priority: 1** | Nhóm: [Foundations](./README.md)

---

## Định nghĩa

> "Simplicity should always be a key goal. Simple code takes less time to write,
> has fewer bugs, and is easier to modify."
>
> — Christopher Diggins, Artima

**KISS** là nguyên tắc yêu cầu ưu tiên giải pháp đơn giản nhất có thể giải quyết được vấn đề.
Sự phức tạp không cần thiết là nguồn gốc của bug, khó bảo trì và technical debt.

Nguồn: [Wikipedia — KISS principle](https://en.wikipedia.org/wiki/KISS_principle)

---

## "Do the simplest thing that could possibly work"

Trước khi bắt tay vào code, hãy hỏi: **"Giải pháp đơn giản nhất có thể hoạt động là gì?"**

Câu hỏi này giúp:
- Tránh overengineering từ đầu
- Đưa ra giải pháp nhanh hơn để validate ý tưởng
- Dễ dàng mở rộng sau khi đã xác nhận hướng đúng

Nguồn: [Do The Simplest Thing That Could Possibly Work](http://c2.com/xp/DoTheSimplestThingThatCouldPossiblyWork.html)

---

## Biểu hiện của code vi phạm KISS

| Biểu hiện | Mô tả |
|---|---|
| Over-abstraction | Tạo interface/abstract class khi chỉ có 1 implementation |
| Premature generalization | Làm generic trước khi biết có nhiều use case |
| Deep inheritance | Chuỗi kế thừa dài hơn 3 cấp |
| Complex conditional | Nhiều tầng if/else lồng nhau |
| Clever code | Code ngắn nhưng khó hiểu, cần suy nghĩ nhiều để đọc |

---

## "Don't Make Me Think"

Code tốt là code mà người đọc **hiểu ngay lập tức** mục đích của nó mà không cần suy nghĩ lâu.
Nếu một đoạn code cần nhiều thời gian để hiểu — đó là dấu hiệu cần đơn giản hoá.

Nguồn: [Don't Make Me Think](http://www.sensible.com/dmmt.html) — Steve Krug

---

## Ví dụ áp dụng

### Sai — Over-abstraction

```php
// Yêu cầu: Gửi email thông báo đặt hàng

abstract class NotificationChannel
{
    abstract public function send(string $recipient, string $content): void;
}

abstract class EmailNotificationChannel extends NotificationChannel
{
    abstract protected function getTemplate(): string;
    abstract protected function buildPayload(array $data): array;
}

class OrderEmailNotification extends EmailNotificationChannel
{
    protected function getTemplate(): string
    {
        return 'emails.order-confirmation';
    }

    protected function buildPayload(array $data): array
    {
        return ['order' => $data['order']];
    }

    public function send(string $recipient, string $content): void
    {
        Mail::to($recipient)->send(
            new OrderConfirmationMail($this->buildPayload(['order' => $content]))
        );
    }
}
// Dùng mất 4 class để gửi một email
```

### Đúng — Simple

```php
// Đủ để giải quyết vấn đề, dễ đọc, dễ test
class OrderService
{
    public function createOrder(array $data): Order
    {
        $order = Order::create($data);
        Mail::to($order->user->email)->send(new OrderConfirmationMail($order));
        return $order;
    }
}
```

---

### Sai — Complex conditional

```php
public function getShippingFee(Order $order): int
{
    if ($order->user->membership === 'gold') {
        if ($order->total > 500000) {
            return 0;
        } elseif ($order->total > 200000) {
            if ($order->address->province === 'Hanoi' || $order->address->province === 'HCM') {
                return 10000;
            } else {
                return 20000;
            }
        } else {
            return 30000;
        }
    } elseif ($order->user->membership === 'silver') {
        // ... thêm nhiều điều kiện
    }
}
```

### Đúng — Break down thành các method rõ ràng

```php
public function getShippingFee(Order $order): int
{
    if ($this->isFreeShipping($order)) {
        return 0;
    }

    return $this->calculateBaseFee($order);
}

private function isFreeShipping(Order $order): bool
{
    return $order->user->membership === 'gold' && $order->total > 500000;
}

private function calculateBaseFee(Order $order): int
{
    $isMainCity = in_array($order->address->province, ['Hanoi', 'HCM']);
    $discount = $order->user->membership === 'gold' ? 0.5 : 1;
    
    return (int)(($isMainCity ? 10000 : 20000) * $discount);
}
```

---

## Mối quan hệ với các nguyên tắc khác

| Nguyên tắc | Mối quan hệ với KISS |
|---|---|
| YAGNI | Cùng hướng: đừng thêm complexity không cần thiết |
| DRY | Đôi khi xung đột: khi abstraction để tái sử dụng lại làm phức tạp hơn |
| Simplicity First (Karpathy) | Cách áp dụng KISS trong thực tiễn hàng ngày |

---

## Checklist — KISS

- [ ] Tôi đã thử cách đơn giản nhất trước khi chọn cách phức tạp
- [ ] Code có thể được hiểu bởi developer khác mà không cần giải thích
- [ ] Không có tầng abstraction nào được thêm mà không có lý do rõ ràng
- [ ] Không có conditional logic lồng nhau quá 2 cấp

---

## Liên kết

- [YAGNI](./YAGNI.md)
- [DRY](./DRY.md)
- [Simplicity First](../07-karpathy-principles/simplicity-first.md)
