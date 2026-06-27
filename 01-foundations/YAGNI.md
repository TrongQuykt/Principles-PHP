# YAGNI — You Aren't Gonna Need It

**Priority: 1** | Nhóm: [Foundations](./README.md)

---

## Định nghĩa

> "Always implement things when you actually need them, never when you just foresee that you need them."
>
> — Ron Jeffries, Extreme Programming

**YAGNI** là nguyên tắc yêu cầu chỉ implement chức năng khi **thực sự cần** đến nó,
không phải khi bạn *nghĩ* sẽ cần trong tương lai.

Nguồn: [Wikipedia — YAGNI](https://en.wikipedia.org/wiki/YAGNI)

---

## Tại sao YAGNI quan trọng

Khi bạn implement chức năng "phòng xa":

1. **Tốn thời gian** cho những thứ có thể không bao giờ cần
2. **Tăng complexity** của codebase
3. **Code thừa** phải được maintain dù không dùng
4. **Requirement thay đổi** làm code "phòng xa" của bạn lỗi thời
5. **Tăng surface area** cho bug và security issue

---

## Các biểu hiện vi phạm YAGNI

| Biểu hiện | Ví dụ |
|---|---|
| "Sau này có thể cần" | Thêm support cho currency khác dù chỉ dùng VND |
| "Tiện thể làm luôn" | Thêm export Excel khi chỉ được yêu cầu export CSV |
| "Để flexible hơn" | Tạo plugin system khi chỉ có 1 implementation |
| "Cẩn thận cho sau" | Thêm audit log cho thứ chưa có yêu cầu |

---

## Ví dụ áp dụng

### Sai — Implement tính năng chưa cần

```php
// Yêu cầu: Lưu ảnh profile của user

class UserAvatarService
{
    // Được yêu cầu:
    public function uploadToLocal(UploadedFile $file, int $userId): string { ... }

    // KHÔNG được yêu cầu — "phòng xa" provider khác:
    public function uploadToS3(UploadedFile $file, int $userId): string { ... }
    public function uploadToCloudinary(UploadedFile $file, int $userId): string { ... }
    public function uploadToGCS(UploadedFile $file, int $userId): string { ... }

    // KHÔNG được yêu cầu — "tiện thể":
    public function resize(string $path, int $width, int $height): string { ... }
    public function convertToWebp(string $path): string { ... }
    public function generateThumbnail(string $path): string { ... }
}
```

### Đúng — Chỉ implement những gì được yêu cầu

```php
class UserAvatarService
{
    public function upload(UploadedFile $file, int $userId): string
    {
        $path = $file->store("avatars/{$userId}", 'public');
        return Storage::url($path);
    }
}
// Khi cần S3 → refactor. Khi cần resize → thêm. Bây giờ không cần → không làm.
```

---

### Sai — Over-configure

```php
// Yêu cầu: Email thông báo khi đặt hàng thành công

// config/notifications.php
return [
    'order_success' => [
        'channels' => ['email'],                    // Được yêu cầu
        'retry_attempts' => 3,                      // Không được yêu cầu
        'retry_delay' => 60,                        // Không được yêu cầu
        'fallback_channel' => 'sms',                // Không được yêu cầu
        'template_engine' => 'blade',               // Không cần configure
        'queue_driver' => 'redis',                  // Không được yêu cầu
        'priority' => 'high',                       // Không được yêu cầu
    ],
];
```

### Đúng — Configuration tối thiểu

```php
// Chỉ configure những gì thực sự cần thay đổi
Mail::to($user->email)->send(new OrderConfirmationMail($order));
// Khi cần queue, retry, fallback → thêm sau khi có yêu cầu thực
```

---

## YAGNI và Technical Debt

YAGNI không có nghĩa là viết code bừa bãi. Nguyên tắc này yêu cầu:

- Viết code **tốt** cho những gì **cần làm ngay bây giờ**
- Không viết code **thêm** cho những gì **chưa cần**

Khi requirement thay đổi, code tốt dễ mở rộng hơn là cố gắng đoán trước tương lai.

---

## Mối quan hệ với OCP (Open/Closed Principle)

YAGNI và OCP đôi khi tưởng như mâu thuẫn:

- **OCP** nói: Viết code mở để mở rộng
- **YAGNI** nói: Đừng thêm extension point cho những gì chưa cần

**Giải pháp**: Thiết kế code tốt (theo SOLID) nhưng **không thêm** implementation cụ thể
cho những use case chưa tồn tại. Extension point sẽ được thêm khi cần.

---

## Checklist — YAGNI

- [ ] Mọi chức năng tôi implement đều được yêu cầu rõ ràng
- [ ] Không có configuration nào cho thứ chưa có use case
- [ ] Không có abstract layer nào được tạo "phòng xa"
- [ ] Không có tính năng nào được thêm "tiện thể" ngoài scope

---

## Liên kết

- [KISS](./KISS.md)
- [DRY](./DRY.md)
- [Simplicity First](../07-karpathy-principles/simplicity-first.md)
