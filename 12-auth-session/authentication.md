# Authentication — Xác Thực & Bảo Mật Mật Khẩu

**Priority: 5** | Nhóm: [Auth & Session](./README.md)

---

## Password Hashing — Chuẩn bắt buộc

### Quy tắc

| Yêu cầu | Chuẩn đúng | Cấm tuyệt đối |
|---|---|---|
| Thuật toán | Bcrypt (cost ≥ 12) hoặc Argon2id | MD5, SHA-1, SHA-256 thuần (không có salt) |
| Salt | Tự động trong bcrypt | Tự tạo salt riêng (không cần với bcrypt) |
| Pepper | Thêm vào trước khi hash (optional) | Hardcode pepper trong code |

### Vì sao không dùng SHA-256 thuần?

SHA-256 là hash function tốc độ cao — có thể thực hiện **billions/giây** trên GPU.
Bcrypt được thiết kế để **chậm có chủ đích** (cost factor), khiến brute-force trở nên bất khả thi.

```php
// Laravel sử dụng bcrypt mặc định (cost = 12)
$hashedPassword = Hash::make($request->password);

// Kiểm tra password
if (!Hash::check($request->password, $user->password)) {
    // Sai mật khẩu
}

// Nâng cao — thêm pepper từ env (optional nhưng khuyến nghị)
$peppered = $request->password . env('PASSWORD_PEPPER');
$hashedPassword = Hash::make($peppered);
```

```dotenv
# .env
PASSWORD_PEPPER=a_random_32_char_string_from_keygen
```

---

## Account Lockout — Chống Brute-Force

### Yêu cầu chuẩn
- Khoá tài khoản sau **3-5 lần sai liên tiếp**
- Thời gian khoá: **15-20 phút**
- Ghi log các lần thất bại

```php
// Dùng Laravel Rate Limiter (built-in)
class LoginController extends Controller
{
    public function login(LoginRequest $request): JsonResponse
    {
        $key = 'login.' . Str::lower($request->email) . '.' . $request->ip();

        // Kiểm tra đã bị khoá chưa
        if (RateLimiter::tooManyAttempts($key, maxAttempts: 5)) {
            $seconds = RateLimiter::availableIn($key);
            throw ValidationException::withMessages([
                'email' => ["Tài khoản tạm khoá. Thử lại sau {$seconds} giây."],
            ]);
        }

        // Xác thực
        if (!Auth::attempt($request->only('email', 'password'))) {
            RateLimiter::hit($key, decaySeconds: 900); // 15 phút

            $attempts = RateLimiter::attempts($key);

            Log::warning('Failed login attempt', [
                'email'    => $request->email,
                'ip'       => $request->ip(),
                'attempts' => $attempts,
            ]);

            throw ValidationException::withMessages([
                'email' => ['Email hoặc mật khẩu không đúng.'],
            ]);
        }

        // Thành công — reset counter
        RateLimiter::clear($key);

        // Xoay Session ID (chống Session Fixation)
        $request->session()->regenerate();

        $token = $request->user()->createToken('api-token', expiresAt: now()->addHours(8));

        return response()->json([
            'status' => 'success',
            'token'  => $token->plainTextToken,
        ]);
    }
}
```

---

## Password Validation Rules

```php
// FormRequest cho đặt lại mật khẩu
public function rules(): array
{
    return [
        'password' => [
            'required',
            'confirmed',
            Password::min(8)              // Tối thiểu 8 ký tự
                    ->letters()           // Phải có chữ cái
                    ->mixedCase()         // Upper + lowercase
                    ->numbers()           // Phải có số
                    ->symbols()           // Phải có ký tự đặc biệt
                    ->uncompromised(),    // Kiểm tra trong HIBP leaked DB
        ],
    ];
}
```

---

## POST Method cho Auth

```php
// ĐÚNG — Password qua POST body (HTTPS)
Route::post('/login', [AuthController::class, 'login']);
Route::post('/logout', [AuthController::class, 'logout']);

// SAI — Không bao giờ truyền password/token qua GET/URL
// GET /login?password=abc123  ← Lưu trong log, browser history, referrer
```

---

## Logout — Xoá Token đúng cách

```php
public function logout(Request $request): JsonResponse
{
    // Revoke token hiện tại
    $request->user()->currentAccessToken()->delete();

    // Hoặc revoke tất cả tokens (đăng xuất khỏi mọi thiết bị)
    $request->user()->tokens()->delete();

    return response()->json(['status' => 'success', 'message' => 'Logged out successfully.']);
}
```

---

## Liên kết

- [JWT](./jwt.md)
- [Session Security](./session.md)
- [OWASP Top 10](../05-security/owasp-top10.md)
