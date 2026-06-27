# Authorization — Phân Quyền & Kiểm Soát Truy Cập

**Priority: 5** | Nhóm: [Auth & Session](./README.md)

---

## Định nghĩa

**Authorization** (Phân quyền) là quá trình xác định liệu một người dùng (đã được xác thực) có quyền thực hiện một hành động cụ thể trên một tài nguyên cụ thể hay không.

---

## Laravel Gates & Policies

### 1. Gate (Simple, Function-based)
Dùng cho các quyền đơn giản, không gắn liền với một model cụ thể.

```php
// app/Providers/AppServiceProvider.php
use Illuminate\Support\Facades\Gate;

public function boot(): void
{
    Gate::define('manage-products', function (User $user) {
        return $user->hasRole('admin') || $user->hasRole('manager');
    });
}

// Sử dụng trong Controller
if (Gate::denies('manage-products')) {
    abort(403);
}

// Sử dụng trong Blade
@can('manage-products')
    <button>Thêm sản phẩm</button>
@endcan
```

### 2. Policy (Class-based)
Dùng cho các quyền xoay quanh một Model cụ thể. Đây là cách làm chuẩn cho các dự án enterprise.

```bash
php artisan make:policy OrderPolicy --model=Order
```

```php
// app/Policies/OrderPolicy.php
class OrderPolicy
{
    /**
     * Chỉ chủ nhân đơn hàng hoặc admin mới được xem.
     */
    public function view(User $user, Order $order): bool
    {
        return $user->id === $order->user_id || $user->isAdmin();
    }

    /**
     * Chỉ được huỷ nếu đơn hàng đang ở trạng thái 'pending'.
     */
    public function cancel(User $user, Order $order): bool
    {
        return $user->id === $order->user_id && $order->status === 'pending';
    }
}
```

```php
// Sử dụng trong Controller
public function show(Order $order)
{
    $this->authorize('view', $order); // Tự động gọi OrderPolicy@view
    return view('orders.show', compact('order'));
}
```

---

## Principle of Least Privilege (Nguyên tắc Đặc quyền Tối thiểu)

Mỗi user, role hoặc service chỉ nên được cấp những quyền hạn **tối thiểu cần thiết** để hoàn thành công việc của mình.

### Ví dụ phân quyền (RBAC - Role Based Access Control)

| Role | Quyền hạn |
|---|---|
| **Customer** | Xem sản phẩm, tạo đơn hàng, quản lý đơn hàng của chính mình |
| **Staff** | Customer + quản lý trạng thái đơn hàng, xem kho |
| **Manager** | Staff + quản lý danh mục sản phẩm, xem báo cáo doanh thu |
| **Admin** | Manager + quản lý User, cấu hình hệ thống |

---

## Middleware-based Authorization

```php
// Bảo vệ route bằng Gate
Route::middleware('can:manage-products')->group(function () {
    Route::post('/api/products', [ProductController::class, 'store']);
});

// Hoặc dùng middleware auth kết hợp với abilities (Sanctum)
Route::middleware('auth:sanctum', 'abilities:orders:read')->get('/orders', ...);
```

---

## Liên kết

- [Authentication](./authentication.md)
- [Session Security](./session.md)
- [OWASP Top 10 — Broken Access Control](../05-security/owasp-top10.md)
