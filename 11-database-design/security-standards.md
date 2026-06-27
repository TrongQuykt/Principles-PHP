# Security Standards — Bảo Mật Mã Nguồn và Dữ Liệu

**Priority: 5** | Nhóm: [Database Design](./README.md)

---

## 1. Không Hardcode Credentials

Tuyệt đối không đặt password, API key, token, secret vào trong code source.

```php
// SAI — Credentials trong code
class VNPayGateway
{
    private string $apiKey = 'vnp_hashsecret_abc123xyz789';
    private string $merchantId = 'VNPAY_MERCHANT_001';
}

// ĐÚNG — Đọc từ environment variable
class VNPayGateway
{
    private string $apiKey;
    private string $merchantId;

    public function __construct()
    {
        $this->apiKey     = config('services.vnpay.hash_secret');
        $this->merchantId = config('services.vnpay.merchant_id');
    }
}
```

```dotenv
# .env (không commit vào Git)
VNPAY_HASH_SECRET=your_actual_secret_here
VNPAY_MERCHANT_ID=your_merchant_id_here

DB_PASSWORD=your_db_password
MAIL_PASSWORD=your_mail_password
JWT_SECRET=your_jwt_secret
```

```php
// config/services.php — Ánh xạ từ .env vào config
'vnpay' => [
    'hash_secret' => env('VNPAY_HASH_SECRET'),
    'merchant_id' => env('VNPAY_MERCHANT_ID'),
    'endpoint'    => env('VNPAY_ENDPOINT', 'https://sandbox.vnpayment.vn/paymentv2/vpcpay.html'),
],
```

---

## 2. Không Log Thông Tin Nhạy Cảm

```php
// SAI — Log password, token, full card number
Log::info('User login attempt', [
    'email'    => $request->email,
    'password' => $request->password, // NEVER
]);

Log::info('Payment request', [
    'amount'      => $amount,
    'card_number' => $cardNumber,    // NEVER — PCI DSS violation
    'cvv'         => $cvv,           // NEVER
]);

// ĐÚNG — Chỉ log dữ liệu không nhạy cảm
Log::info('User login attempt', [
    'email'      => $request->email,
    'ip'         => $request->ip(),
    'user_agent' => $request->userAgent(),
]);

Log::info('Payment initiated', [
    'order_id'     => $order->id,
    'amount'       => $amount,
    'gateway'      => 'vnpay',
    'card_last4'   => substr($cardNumber, -4), // Chỉ 4 số cuối
]);
```

---

## 3. Không Lưu Thông Tin Nhạy Cảm Trong Database Dạng Plain Text

```php
// Cột nào CẦN mã hoá / hash
'password'       → Hash::make() — bcrypt, không reversible
'remember_token' → Random token — không cần encrypt
'api_key'        → Hash (lưu hash, so sánh hash)
'credit_card'    → Encrypt — nếu bắt buộc lưu, dùng AES-256

// Cột KHÔNG cần mã hoá (không nhạy cảm)
'name', 'email', 'address', 'phone' → Plain text OK
```

---

## 4. Database Credentials trong .env

```dotenv
# .env — Không bao giờ commit file này
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=ecommerce_db
DB_USERNAME=ecommerce_user
DB_PASSWORD=StrongP@ssword!123

# .env.example — Commit file này (template không có giá trị)
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=
DB_USERNAME=
DB_PASSWORD=
```

---

## 5. Nguyên tắc Least Privilege cho Database User

```sql
-- Không dùng root user cho application
-- Tạo user riêng với quyền tối thiểu

CREATE USER 'ecommerce_app'@'localhost' IDENTIFIED BY 'StrongPassword!';

-- Chỉ cấp quyền cần thiết
GRANT SELECT, INSERT, UPDATE, DELETE ON ecommerce_db.* TO 'ecommerce_app'@'localhost';

-- Không cấp DROP, CREATE, ALTER cho app user
-- Migrations chạy bằng user riêng có quyền cao hơn
```

---

## 6. Encryption at Rest cho dữ liệu quan trọng

```php
// Dùng Laravel Encryption (AES-256-CBC)
// Model cast tự động encrypt/decrypt
protected $casts = [
    'tax_id'          => 'encrypted',     // Mã số thuế
    'bank_account'    => 'encrypted',     // Tài khoản ngân hàng
];

// Manual encrypt/decrypt
$encrypted = Crypt::encryptString($sensitiveData);
$decrypted = Crypt::decryptString($encrypted);
```

---

## Liên kết

- [OWASP Top 10](../05-security/owasp-top10.md)
- [Authentication](../12-auth-session/README.md)
- [CI/CD — .env management](../09-laravel-core/cicd.md)
