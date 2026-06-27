# Surgical Changes

**Priority: 0** | Nhóm: [Karpathy Principles](./README.md)

---

## Định nghĩa

Khi chỉnh sửa code hiện có, **chỉ chạm vào những gì bạn phải chạm**.
Không "cải thiện", không refactor, không format những gì ngoài phạm vi yêu cầu.

> "Every changed line should trace directly to the user's request."
>
> — Karpathy Principles

---

## Vấn đề cần giải quyết

LLM và lập trình viên thường có xu hướng:
- Tái cấu trúc code không liên quan khi sửa một bug nhỏ
- "Chuẩn hoá" formatting của cả file trong khi chỉ sửa 1 dòng
- Xoá dead code không liên quan
- Thêm comment cho những đoạn code không được yêu cầu sửa

Mỗi thay đổi ngoài phạm vi đều là **rủi ro regression** tiềm ẩn.

---

## Hai loại clean-up

### Clean-up bạn PHẢI làm

Khi thay đổi của bạn **tạo ra** orphan code:

```php
// Trước khi sửa:
use App\Services\OldEmailService;  // Bạn dùng cái này

// Sau khi sửa (bạn đổi sang service mới):
use App\Services\NewEmailService;

// BẮT BUỘC xoá import cũ vì THAY ĐỔI CỦA BẠN tạo ra orphan này
// use App\Services\OldEmailService;  <-- xoá dòng này
```

### Clean-up bạn KHÔNG ĐƯỢC làm

Khi dead code tồn tại từ trước, không liên quan đến yêu cầu hiện tại:

```php
// Bạn đang sửa function processPayment()
// Nhưng bạn thấy function calculateTax() bên cạnh không được dùng

// SAI: Xoá calculateTax() vì "tiện thể dọn dẹp"
// ĐÚNG: Để nguyên calculateTax(), ghi chú trong code review
//        "Tôi nhận thấy calculateTax() có vẻ không được dùng,
//         có thể xem xét xoá trong task khác?"
```

---

## Quy tắc chi tiết

### Khi edit code hiện có

| Được phép | Không được phép |
|---|---|
| Sửa code được yêu cầu rõ ràng | "Cải thiện" code liền kề không liên quan |
| Xoá import/variable do thay đổi của bạn tạo ra | Refactor code không phải là mục tiêu |
| Sửa bug được báo cáo | Fix bug khác mà bạn tình cờ thấy (trừ khi được phê duyệt) |
| Match style của file hiện tại | Chuẩn hoá style toàn file |

### Match existing style

Kể cả khi style hiện tại không theo chuẩn của bạn — hãy match nó:

```php
// File hiện tại dùng single quotes
$name = 'John';
$email = 'john@example.com';

// Đừng tự ý đổi sang double quotes khi thêm dòng mới
// SAI:
$phone = "0901234567";

// ĐÚNG (match style hiện tại):
$phone = '0901234567';
```

---

## Cách xử lý khi phát hiện vấn đề ngoài phạm vi

Thay vì tự ý sửa, hãy ghi nhận và báo cáo:

```
Trong quá trình thực hiện, tôi nhận thấy:
- Function `calculateDiscount()` có vẻ không còn được sử dụng
- File `OldPaymentService.php` có thể là dead code

Tôi không thay đổi các phần này vì ngoài phạm vi yêu cầu.
Có muốn tôi xử lý trong task riêng không?
```

---

## Ví dụ áp dụng

### Yêu cầu: Sửa bug validation email

```php
// TRƯỚC (có bug):
public function rules(): array
{
    return [
        'email' => 'required|string',  // Thiếu email validation
        'name'  => 'required|string|max:255',
    ];
}

// ĐÚNG — Chỉ sửa dòng cần sửa:
public function rules(): array
{
    return [
        'email' => 'required|email|max:255',  // Chỉ sửa dòng này
        'name'  => 'required|string|max:255', // Giữ nguyên
    ];
}

// SAI — Vừa sửa vừa refactor không được yêu cầu:
public function rules(): array
{
    // Thêm comment không được yêu cầu
    // Đổi format array sang dạng khác
    // Thêm validation rules cho các field khác "tiện thể"
    return [
        'email' => ['required', 'email', 'max:255'],
        'name'  => ['required', 'string', 'max:255'],
        'phone' => ['nullable', 'string'],  // Không được yêu cầu!
    ];
}
```

---

## Checklist — Surgical Changes

- [ ] Mỗi dòng thay đổi của tôi có thể trace trực tiếp đến yêu cầu
- [ ] Tôi không thay đổi formatting của các dòng không liên quan
- [ ] Tôi không refactor code ngoài phạm vi
- [ ] Tôi đã xoá các import/variable do thay đổi của tôi tạo ra orphan
- [ ] Tôi không xoá dead code có sẵn từ trước (trừ khi được yêu cầu)
- [ ] Style của code mới match với style của file hiện tại

---

## Liên kết

- [Think Before Coding](./think-before-coding.md)
- [Simplicity First](./simplicity-first.md)
- [Refactoring](../04-code-quality/refactoring.md)
