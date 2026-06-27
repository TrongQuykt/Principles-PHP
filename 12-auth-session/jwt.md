# JWT — JSON Web Token

**Priority: 5** | Nhóm: [Auth & Session](./README.md)

---

## Định nghĩa

**JWT** (JSON Web Token) là chuẩn mở (RFC 7519) để truyền thông tin xác thực giữa các bên dưới dạng JSON object đã được ký số (signed).

---

## Cấu trúc JWT

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9
.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ
.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c

HEADER.PAYLOAD.SIGNATURE
```

### Header
```json
{
    "alg": "HS256",
    "typ": "JWT"
}
```

### Payload (Claims)
```json
{
    "sub": "123",          // User ID
    "email": "user@x.com",
    "role": "admin",
    "iat": 1719475200,     // Issued At (UTC timestamp)
    "exp": 1719504000,     // Expiration Time (UTC timestamp)
    "jti": "unique-token-id" // JWT ID (để revoke cụ thể)
}
```

---

## JWT trong Laravel — Dùng tymon/jwt-auth hoặc Laravel Sanctum

Laravel **Sanctum** là lựa chọn chính thức, đơn giản hơn JWT thuần:

```bash
composer require laravel/sanctum
php artisan vendor:publish --provider="Laravel\Sanctum\SanctumServiceProvider"
php artisan migrate
```

```php
// Tạo token khi login
$token = $user->createToken(
    name: 'api-token',
    abilities: ['orders:read', 'orders:write'],
    expiresAt: now()->addHours(8)
)->plainTextToken;

// Xác thực token trong request
Route::middleware('auth:sanctum')->group(function () {
    Route::get('/me', fn () => auth()->user());
});

// Kiểm tra abilities
$request->user()->tokenCan('orders:write');
```

---

## JWT Thuần (tymon/jwt-auth) — Dùng khi cần stateless hoàn toàn

```bash
composer require tymon/jwt-auth
php artisan vendor:publish --provider="Tymon\JWTAuth\Providers\LaravelServiceProvider"
php artisan jwt:secret   # Tạo JWT_SECRET trong .env
```

```php
// config/auth.php
'guards' => [
    'api' => [
        'driver'   => 'jwt',
        'provider' => 'users',
    ],
],

// AuthController
public function login(LoginRequest $request): JsonResponse
{
    if (!$token = JWTAuth::attempt($request->only('email', 'password'))) {
        return response()->json(['status' => 'error', 'message' => 'Invalid credentials'], 401);
    }

    return response()->json([
        'status'       => 'success',
        'access_token' => $token,
        'token_type'   => 'bearer',
        'expires_in'   => config('jwt.ttl') * 60, // seconds
    ]);
}

public function refresh(): JsonResponse
{
    return response()->json([
        'access_token' => JWTAuth::refresh(JWTAuth::getToken()),
    ]);
}
```

---

## Bảo mật JWT

| Nguyên tắc | Thực hành |
|---|---|
| Thuật toán | HS256 (HMAC) hoặc RS256 (RSA) — không dùng `none` |
| Secret Key | Tối thiểu 256-bit, lấy từ `.env` (không hardcode) |
| Expiration | Access token: 8-24h, Refresh token: 30 ngày |
| Lưu phía client | `httpOnly cookie` (chống XSS) hoặc `SessionStorage` |
| Revocation | Lưu `jti` vào blacklist khi logout |
| Payload | Không chứa dữ liệu nhạy cảm (JWT có thể decode) |

---

## Refresh Token Pattern

```
Client                          Server
  |                               |
  |-- POST /login ---------------→|
  |←-- access_token (8h) ---------|
  |←-- refresh_token (30d) -------|
  |                               |
  |  (8 giờ sau)                  |
  |-- GET /api/orders ----------→ |
  |←-- 401 Unauthorized ---------|
  |                               |
  |-- POST /auth/refresh --------→|
  |   { refresh_token: ... }      |
  |←-- new access_token ---------|
  |                               |
  |-- GET /api/orders (new token)→|
  |←-- 200 OK --------------------|
```

---

## Liên kết

- [Authentication](./authentication.md)
- [Session Security](./session.md)
- [Security Standards](../11-database-design/security-standards.md)
