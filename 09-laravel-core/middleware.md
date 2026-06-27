# Middleware — Pipeline Xử Lý Request

**Priority: 3** | Nhóm: [Laravel Core](./README.md)

---

## Định nghĩa

**Middleware** cung cấp cơ chế xử lý request HTTP trước và sau khi đến Controller.
Đây là nơi xử lý các mối quan tâm chính (concerns) không thuộc business logic:
Authentication, Authorization, Rate Limiting, Logging, CORS, v.v.

---

## Tạo Middleware

```bash
php artisan make:middleware EnsureUserIsVerified
```

```php
class EnsureUserIsVerified
{
    public function handle(Request $request, Closure $next): Response
    {
        if (!$request->user()->hasVerifiedEmail()) {
            return response()->json([
                'status'  => 'error',
                'message' => 'Email chưa được xác minh.',
            ], 403);
        }

        return $next($request);
    }
}
```

---

## Đăng ký Middleware

```php
// bootstrap/app.php (Laravel 11+)
->withMiddleware(function (Middleware $middleware) {
    // Global Middleware
    $middleware->append(TrustProxies::class);

    // Middleware Groups
    $middleware->group('api', [
        ThrottleRequests::class.':api',
        SubstituteBindings::class,
    ]);

    // Route Aliases
    $middleware->alias([
        'auth'     => Authenticate::class,
        'verified' => EnsureUserIsVerified::class,
        'throttle' => ThrottleRequests::class,
    ]);
})
```

---

## Áp dụng Middleware

```php
// Trên Route
Route::middleware(['auth:sanctum', 'verified'])->group(function () {
    Route::apiResource('orders', OrderController::class);
});

// Trong Controller
class OrderController extends Controller
{
    public function __construct()
    {
        $this->middleware('auth:sanctum');
        $this->middleware('throttle:60,1')->only(['store', 'update']);
    }
}
```

---

## Middleware quan trọng trong dự án Enterprise

| Middleware | Mục đích |
|---|---|
| `auth:sanctum` | Xác thực API token |
| `throttle:60,1` | Rate limiting (60 req/phút) |
| `cors` | Cross-Origin Resource Sharing |
| `signed` | Kiểm tra signed URLs |
| `password.confirm` | Yêu cầu xác nhận mật khẩu |
| `can:manage-orders` | Gate-based authorization |

---

## Liên kết

- [Request Lifecycle](./request-lifecycle.md)
- [Authentication & Authorization](../12-auth-session/authentication.md)
- [OWASP Top 10](../05-security/owasp-top10.md)
