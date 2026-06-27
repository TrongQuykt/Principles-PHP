# Adapter

**Priority: 2** | Nhóm: [Structural Patterns](../README.md)

---

## Định nghĩa

**Adapter** là pattern cho phép các object có interface không tương thích làm việc được với nhau.
Giống như bộ chuyển đổi phích cắm điện — nối hai đầu không khớp nhau.

Nguồn: [Wikipedia — Adapter Pattern](https://en.wikipedia.org/wiki/Adapter_pattern)

---

## Khi nào dùng

- Tích hợp thư viện bên thứ 3 mà không để code phụ thuộc vào API của thư viện đó
- Cần dùng class cũ (legacy) với interface mới
- Muốn cách ly code khỏi external service (dễ swap provider)

---

## Ví dụ — SMS Provider Adapter

```php
// Interface của hệ thống (chuẩn nội bộ)
interface SmsProvider
{
    public function send(string $phone, string $message): bool;
}

// Thư viện bên thứ 3: Twilio — có API khác hoàn toàn
class TwilioClient // Không thể sửa class này
{
    public function messages(): TwilioMessageFactory { ... }
}

class TwilioMessageFactory
{
    public function create(array $params): TwilioMessage { ... }
    // $params = ['from' => ..., 'to' => ..., 'body' => ...]
}

// Adapter: bọc Twilio vào interface của hệ thống
class TwilioSmsAdapter implements SmsProvider
{
    public function __construct(private TwilioClient $client) {}

    public function send(string $phone, string $message): bool
    {
        try {
            $this->client->messages()->create([
                'from' => config('services.twilio.from'),
                'to'   => $phone,
                'body' => $message,
            ]);
            return true;
        } catch (\Exception $e) {
            Log::error('Twilio send failed', ['error' => $e->getMessage()]);
            return false;
        }
    }
}

// Thêm provider mới (Vonage) không cần sửa code sử dụng
class VonageSmsAdapter implements SmsProvider
{
    public function send(string $phone, string $message): bool { ... }
}

// Code sử dụng — không biết và không cần biết về Twilio hay Vonage
class OtpService
{
    public function __construct(private SmsProvider $sms) {}

    public function sendOtp(string $phone, string $otp): void
    {
        $this->sms->send($phone, "Mã OTP của bạn là: {$otp}");
    }
}
```

---

## Lợi ích

| Lợi ích | Giải thích |
|---|---|
| Cách ly external dependency | Swap SMS provider chỉ cần đổi binding, không sửa business code |
| Dễ test | Mock SmsProvider thay vì mock Twilio SDK |
| Tuân thủ DIP | Business code phụ thuộc vào abstraction (interface) |

---

## Liên kết

- [Facade](./facade.md)
- [SOLID — DIP](../../01-foundations/SOLID.md)
