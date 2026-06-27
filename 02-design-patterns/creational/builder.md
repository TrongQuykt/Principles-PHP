# Builder

**Priority: 2** | Nhóm: [Creational Patterns](../README.md)

---

## Định nghĩa

**Builder** là pattern tách biệt quá trình xây dựng một object phức tạp khỏi representation của nó.
Cho phép tạo object với nhiều tham số tuỳ chọn mà không cần constructor khổng lồ hoặc nhiều overload.

Nguồn: [Wikipedia — Builder Pattern](https://en.wikipedia.org/wiki/Builder_pattern)

---

## Khi nào dùng

- Object có nhiều tham số tuỳ chọn (optional parameters)
- Cần tạo nhiều biến thể của cùng một object
- Constructor với hơn 3-4 tham số là dấu hiệu nên dùng Builder

---

## Ví dụ — Email Builder

```php
// Không dùng Builder: Constructor không rõ nghĩa
$email = new Email(
    'user@example.com',
    null,
    ['admin@example.com'],
    'Order Confirmation',
    '<h1>Thank you</h1>',
    null,
    'high',
    true
);
// Tham số thứ 7 là gì? Thứ 8 là gì? Không thể biết mà không xem constructor.

// Dùng Builder: Rõ ràng, fluent, dễ đọc
$email = (new EmailBuilder())
    ->to('user@example.com')
    ->bcc(['admin@example.com'])
    ->subject('Order Confirmation')
    ->htmlBody('<h1>Thank you</h1>')
    ->priority('high')
    ->trackOpens()
    ->build();
```

---

## Implementation

```php
class EmailBuilder
{
    private array $data = [
        'to'         => null,
        'cc'         => [],
        'bcc'        => [],
        'subject'    => '',
        'html_body'  => null,
        'text_body'  => null,
        'priority'   => 'normal',
        'track_opens'=> false,
    ];

    public function to(string $email): static
    {
        $this->data['to'] = $email;
        return $this;
    }

    public function cc(array $emails): static
    {
        $this->data['cc'] = $emails;
        return $this;
    }

    public function bcc(array $emails): static
    {
        $this->data['bcc'] = $emails;
        return $this;
    }

    public function subject(string $subject): static
    {
        $this->data['subject'] = $subject;
        return $this;
    }

    public function htmlBody(string $html): static
    {
        $this->data['html_body'] = $html;
        return $this;
    }

    public function priority(string $priority): static
    {
        $this->data['priority'] = $priority;
        return $this;
    }

    public function trackOpens(): static
    {
        $this->data['track_opens'] = true;
        return $this;
    }

    public function build(): Email
    {
        if (empty($this->data['to'])) {
            throw new \InvalidArgumentException('Email recipient (to) is required.');
        }
        return new Email($this->data);
    }
}
```

---

## Query Builder — Ví dụ thực tế trong Laravel

Laravel's Eloquent Query Builder là ứng dụng phổ biến nhất của pattern này:

```php
$orders = Order::query()
    ->where('status', 'pending')
    ->where('created_at', '>=', now()->subDays(7))
    ->with(['user', 'items.product'])
    ->orderByDesc('created_at')
    ->limit(50)
    ->get();
```

---

## Liên kết

- [Factory Method](./factory-method.md)
- [KISS](../../01-foundations/KISS.md)
