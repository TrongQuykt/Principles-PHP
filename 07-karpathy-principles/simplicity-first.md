# Simplicity First

**Priority: 0** | Nhóm: [Karpathy Principles](./README.md)

---

## Định nghĩa

Viết **lượng code tối thiểu** cần thiết để giải quyết vấn đề được yêu cầu.
Không thêm bất kỳ gì không được yêu cầu rõ ràng.

> "If 200 lines could be 50, rewrite it."
>
> — Karpathy Principles

---

## Vấn đề cần giải quyết

LLM và lập trình viên thường có xu hướng **overengineering**:
- Thêm abstraction cho code chỉ dùng một lần
- Tạo "framework nhỏ" thay vì viết thẳng vào vấn đề
- Thêm error handling cho tình huống không thể xảy ra
- Tạo configuration cho những thứ không bao giờ cần thay đổi
- Viết 1000 dòng khi 100 dòng là đủ

---

## Các quy tắc cụ thể

### Không thêm gì không được yêu cầu

| Không được làm | Lý do |
|---|---|
| Thêm tính năng "vì sau này có thể cần" | Vi phạm YAGNI |
| Tạo abstraction cho code chỉ dùng 1 lần | Thêm complexity không cần thiết |
| Thêm flexibility/configurability không được yêu cầu | Overengineering |
| Viết error handling cho impossible scenario | Code thừa, gây nhiễu |

### Kiểm tra đơn giản trước khi thêm phức tạp

Trước khi tạo một class, hãy hỏi: "Một hàm đơn giản có giải quyết được không?"

Trước khi tạo một interface/abstraction, hỏi: "Có hơn 2 implementation khác nhau không?"

### Test Simplicity

> "Liệu một senior engineer có nói đây là overcomplicated không?  
> Nếu có — đơn giản hoá."

---

## Ví dụ áp dụng

### Sai — Overengineered

```php
// Yêu cầu: Lưu log khi user đăng nhập thành công

// Developer tạo:
interface LoggerInterface { ... }
abstract class BaseLogger { ... }
class LoginEventLogger extends BaseLogger implements LoggerInterface
{
    private LogFormatterInterface $formatter;
    private LogStorageInterface $storage;
    private LogFilterInterface $filter;
    
    public function __construct(
        LogFormatterInterface $formatter,
        LogStorageInterface $storage,
        LogFilterInterface $filter
    ) { ... }
    
    public function log(LoginEvent $event): void { ... }
}
// ... thêm 200 dòng nữa cho Providers, Factories, ...
```

### Đúng — Simple

```php
// Yêu cầu: Lưu log khi user đăng nhập thành công

// Đơn giản, đủ dùng, có thể refactor sau nếu cần
public function login(LoginRequest $request): JsonResponse
{
    $user = Auth::user();
    Log::info('User logged in', ['user_id' => $user->id, 'ip' => $request->ip()]);
    return response()->json(['status' => 'success']);
}
```

### Sai — Abstraction quá sớm

```php
// Chỉ có 1 loại payment gateway (VNPay), nhưng developer tạo:
interface PaymentGatewayInterface
{
    public function charge(int $amount): PaymentResult;
    public function refund(string $transactionId): RefundResult;
    public function getStatus(string $transactionId): PaymentStatus;
}

class VNPayGateway implements PaymentGatewayInterface { ... }
class PaymentGatewayFactory { ... }
```

### Đúng — Chỉ implement những gì cần ngay bây giờ

```php
// Chỉ có VNPay, viết thẳng, khi có gateway thứ 2 mới abstract
class VNPayService
{
    public function charge(int $amount): array { ... }
}
```

---

## Mối quan hệ với KISS và YAGNI

Simplicity First là cách áp dụng **KISS** (Priority 1) và **YAGNI** (Priority 1) trong thực tế hàng ngày:

- **KISS** nói: Đơn giản là mục tiêu
- **YAGNI** nói: Đừng thêm những gì chưa cần
- **Simplicity First** nói: Cả hai, áp dụng ngay từ dòng code đầu tiên

---

## Checklist — Simplicity First

- [ ] Tôi chỉ implement những gì được yêu cầu rõ ràng
- [ ] Không có abstraction nào được tạo cho single-use code
- [ ] Không có error handling cho scenario không thể xảy ra
- [ ] Không có configuration cho thứ không bao giờ thay đổi
- [ ] Nếu code trên 100 dòng, tôi đã xem xét có thể viết lại ngắn hơn không

---

## Liên kết

- [Think Before Coding](./think-before-coding.md)
- [Surgical Changes](./surgical-changes.md)
- [KISS](../01-foundations/KISS.md)
- [YAGNI](../01-foundations/YAGNI.md)
