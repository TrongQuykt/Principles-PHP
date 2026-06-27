# Feature Flags — Cờ Tính Năng

**Priority: 2** | Nhóm: [Features](./README.md)

---

## Định nghĩa

**Feature Flags** (hay Feature Toggles) là một kỹ thuật trong phát triển phần mềm cho phép bật hoặc tắt một tính năng tại runtime mà không cần triển khai (deploy) lại code.

Nguồn: [Wikipedia — Feature toggle](https://en.wikipedia.org/wiki/Feature_toggle)

---

## Lợi ích

| Lợi ích | Giải thích |
|---|---|
| **Decoupling** | Tách biệt giữa việc Deploy (đưa code lên server) và Release (cho người dùng thấy tính năng). |
| **Canary Releases** | Chỉ bật tính năng cho 5% người dùng để kiểm tra lỗi trước khi mở hoàn toàn. |
| **A/B Testing** | Thử nghiệm hai phiên bản khác nhau của cùng một tính năng. |
| **Kill Switch** | Tắt ngay lập tức tính năng bị lỗi mà không cần rollback cả system. |

---

## Phân loại Feature Flags

1. **Release Toggles**: Dùng để ẩn các tính năng đang phát triển (In-progress) khỏi môi trường production.
2. **Experiment Toggles**: Dùng cho A/B testing.
3. **Ops Toggles**: Dùng để quản lý các cấu hình hạ tầng hoặc control các tính năng gây nặng tải.
4. **Permission Toggles**: Bật tính năng dựa trên subscription level hoặc role của user.

---

## Ví dụ thực thi (PHP)

```php
// Dùng thư viện như Laravel Pennant hoặc config đơn giản
if (Feature::active('new-checkout-flow')) {
    return $this->newCheckout();
}

return $this->legacyCheckout();
```

---

## Quản lý Technical Debt

Feature Flags là một dạng **Technical Debt** (nợ kỹ thuật). Nếu không được dọn dẹp, chúng sẽ làm code trở nên cực kỳ rối rắm với quá nhiều câu lệnh `if/else`.

**Quy tắc dọn dẹp**:
- Sau khi tính năng đã ổn định 100%, phải xoá code kiểm tra flag và code cũ ngay lập tức.
- Đặt thời hạn (expiry date) cho mỗi flag.

---

## Liên kết

- [KISS](../01-foundations/KISS.md)
- [YAGNI](../01-foundations/YAGNI.md)
- [Trang chủ FeatureFlags.io](https://featureflags.io/)
