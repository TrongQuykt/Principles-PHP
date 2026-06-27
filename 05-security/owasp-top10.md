# OWASP Top 10

**Priority: 5** | Nhóm: [Security](./README.md)

---

## Định nghĩa

**OWASP Top 10** là danh sách 10 rủi ro bảo mật phổ biến nhất cho web application,
được cập nhật định kỳ bởi Open Web Application Security Project.

Nguồn: [OWASP Top 10](https://owasp.org/www-project-top-ten/)

---

## OWASP Top 10 (2021) — Tóm tắt và cách phòng ngừa

### A01 — Broken Access Control

**Mô tả**: User có thể truy cập resources hoặc thực hiện actions ngoài quyền của họ.

**Phòng ngừa**:
```php
// Kiểm tra ownership trước khi truy cập
public function show(Order $order): JsonResponse
{
    // Dùng Policy thay vì if thủ công
    $this->authorize('view', $order);
    return response()->json($order);
}

// OrderPolicy.php
public function view(User $user, Order $order): bool
{
    return $user->id === $order->user_id || $user->isAdmin();
}
```

---

### A02 — Cryptographic Failures

**Mô tả**: Dữ liệu nhạy cảm không được mã hoá đúng cách (password, token, PII).

**Phòng ngừa**:
```php
// Luôn hash password với bcrypt (Laravel mặc định)
User::create(['password' => Hash::make($request->password)]);

// Không bao giờ lưu plain text password hoặc sensitive data
// Không log sensitive data
Log::info('User logged in', ['user_id' => $user->id]); // OK
Log::info('Login attempt', ['password' => $request->password]); // NEVER
```

---

### A03 — Injection (SQL, XSS, Command)

**Mô tả**: Attacker inject code vào input để thực thi trên server.

**SQL Injection — Phòng ngừa**:
```php
// NEVER: Raw query với user input
DB::statement("SELECT * FROM users WHERE email = '{$email}'");

// ALWAYS: Parameterized query hoặc Eloquent
User::where('email', $email)->first();
DB::select('SELECT * FROM users WHERE email = ?', [$email]);
```

**XSS — Phòng ngừa**:
```blade
{{-- Blade auto-escapes, dùng {{ }} không phải {!! !!} --}}
{{ $userInput }} {{-- Safe: auto-escaped --}}
{!! $userInput !!} {{-- DANGER: only for trusted HTML --}}
```

---

### A07 — Identification and Authentication Failures

**Mô tả**: Yếu kém trong xác thực (brute force, weak session, hardcoded credentials).

**Phòng ngừa**:
```php
// Rate limiting cho login endpoint
Route::post('/login', [AuthController::class, 'login'])
    ->middleware('throttle:5,1'); // 5 attempts per minute

// Không hardcode credentials
// Dùng .env, không commit .env vào git
$apiKey = config('services.vnpay.api_key'); // Từ .env
```

---

### Bảng tổng hợp nhanh

| # | Tên | Phòng ngừa chính |
|---|---|---|
| A01 | Broken Access Control | Policy/Gate, kiểm tra ownership |
| A02 | Cryptographic Failures | Hash password, encrypt sensitive data |
| A03 | Injection | Parameterized query, HTML escaping |
| A04 | Insecure Design | Threat modeling, security design review |
| A05 | Security Misconfiguration | Không expose debug info, proper headers |
| A06 | Vulnerable Components | Cập nhật dependencies thường xuyên |
| A07 | Auth Failures | Rate limiting, strong session |
| A08 | Software & Data Integrity | Verify dependencies, signed releases |
| A09 | Logging & Monitoring Failures | Log đầy đủ, không log sensitive data |
| A10 | SSRF | Validate URL, whitelist allowed hosts |

---

## Liên kết

- [Authentication & Authorization](./authentication-authorization.md)
- [Input Validation](./input-validation.md)
