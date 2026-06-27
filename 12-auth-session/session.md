# Session Security — Bảo Mật Phiên Làm Việc

**Priority: 5** | Nhóm: [Auth & Session](./README.md)

---

## Cấu hình Session chuẩn

```dotenv
# .env
SESSION_DRIVER=redis         # Hoặc database — không dùng cookie cho production
SESSION_LIFETIME=15          # 15 phút không tương tác
SESSION_ENCRYPT=true         # Mã hoá session data
SESSION_SECURE_COOKIE=true   # Chỉ gửi cookie qua HTTPS
SESSION_HTTP_ONLY=true       # Không thể truy cập cookie bằng JavaScript
SESSION_SAME_SITE=strict     # Chống CSRF từ cross-site
```

```php
// config/session.php — Cấu hình bổ sung
return [
    'driver'          => env('SESSION_DRIVER', 'redis'),
    'lifetime'        => (int) env('SESSION_LIFETIME', 15),
    'expire_on_close' => true,           // Hết khi đóng browser
    'encrypt'         => true,
    'gc_probability'  => 2,
    'secure'          => env('SESSION_SECURE_COOKIE', true),
    'http_only'       => true,
    'same_site'       => 'strict',
    'partitioned'     => false,
];
```

---

## Session ID Rotation — Chống Session Fixation

```php
// BẮT BUỘC: Tạo session ID mới sau khi login
public function login(LoginRequest $request): JsonResponse
{
    if (!Auth::attempt($request->only('email', 'password'))) {
        /* ... xử lý fail ... */
    }

    // Xoay Session ID — đây là bước bắt buộc
    $request->session()->regenerate();

    return response()->json(['status' => 'success']);
}

// BẮT BUỘC: Xoá session khi logout
public function logout(Request $request): JsonResponse
{
    Auth::logout();

    $request->session()->invalidate();     // Xoá toàn bộ session data
    $request->session()->regenerateToken(); // Tạo CSRF token mới

    return response()->json(['status' => 'success']);
}
```

---

## Session Timeout — Tự động timeout 15 phút

```php
// app/Http/Middleware/CheckSessionTimeout.php
class CheckSessionTimeout
{
    private int $timeoutMinutes = 15;

    public function handle(Request $request, Closure $next): Response
    {
        if ($request->session()->has('last_activity')) {
            $lastActivity = $request->session()->get('last_activity');

            if (now()->diffInMinutes($lastActivity) > $this->timeoutMinutes) {
                Auth::logout();
                $request->session()->invalidate();
                $request->session()->regenerateToken();

                return response()->json([
                    'status'  => 'error',
                    'message' => 'Phiên làm việc hết hạn. Vui lòng đăng nhập lại.',
                    'code'    => 'SESSION_EXPIRED',
                ], 401);
            }
        }

        $request->session()->put('last_activity', now());

        return $next($request);
    }
}
```

---

## CSRF Protection

```php
// Laravel tự động bảo vệ CSRF cho web routes
// Đảm bảo mọi form đều có @csrf

// API routes (stateless) không cần CSRF — dùng token-based auth
// Nhưng PHẢI trong VerifyCsrfToken::$except nếu là webhook endpoint
class VerifyCsrfToken extends Middleware
{
    protected $except = [
        'webhooks/*',     // Webhook từ VNPay, Momo không có CSRF token
    ];
}
```

---

## Tóm tắt checklist Session Security

| Yêu cầu | Cấu hình |
|---|---|
| Timeout 15 phút | `SESSION_LIFETIME=15` |
| Session ID xoay khi login | `$request->session()->regenerate()` |
| Session ID xoay khi logout | `$request->session()->invalidate()` |
| Cookie httpOnly | `SESSION_HTTP_ONLY=true` |
| Cookie Secure (HTTPS) | `SESSION_SECURE_COOKIE=true` |
| SameSite=Strict | `SESSION_SAME_SITE=strict` |
| Driver server-side | `SESSION_DRIVER=redis` hoặc `database` |
| Mã hoá session data | `SESSION_ENCRYPT=true` |

---

## Liên kết

- [Authentication](./authentication.md)
- [JWT](./jwt.md)
- [OWASP Top 10](../05-security/owasp-top10.md)
