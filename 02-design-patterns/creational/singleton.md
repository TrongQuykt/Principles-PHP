# Singleton

**Priority: 2** | Nhóm: [Creational Patterns](../README.md)

---

## Định nghĩa

**Singleton** là pattern đảm bảo một class chỉ có **duy nhất một instance** trong toàn bộ
vòng đời của ứng dụng, và cung cấp một điểm truy cập global vào instance đó.

Nguồn: [Wikipedia — Singleton Pattern](https://en.wikipedia.org/wiki/Singleton_pattern)

---

## Cảnh báo — Singleton thường là Anti-Pattern

Singleton bị coi là **anti-pattern** trong nhiều trường hợp vì:

| Vấn đề | Mô tả |
|---|---|
| Global state | Tạo state toàn cục làm code khó predict |
| Khó unit test | Không thể inject mock vào class dùng Singleton |
| Vi phạm SRP | Tự quản lý lifecycle của mình AND làm nhiệm vụ chính |
| Vi phạm DIP | Code coupling trực tiếp vào class cụ thể |

**Trước khi dùng Singleton, hãy xem xét Dependency Injection thay thế.**

---

## Khi nào Singleton hợp lý

Singleton chỉ hợp lý khi:
- Object thực sự vô nghĩa nếu có nhiều instance (VD: connection pool manager)
- Đây là shared resource thực sự (VD: Logger, Config reader)
- Framework/container quản lý lifecycle thay vì tự quản lý

---

## Ví dụ — Singleton thuần (tránh dùng)

```php
// Cách implement Singleton truyền thống — TRÁNH DÙNG trong application code
class DatabaseConnection
{
    private static ?DatabaseConnection $instance = null;
    private \PDO $connection;

    private function __construct()
    {
        $this->connection = new \PDO(config('database.dsn'));
    }

    public static function getInstance(): static
    {
        if (static::$instance === null) {
            static::$instance = new static();
        }
        return static::$instance;
    }

    // Ngăn clone và unserialize
    private function __clone() {}
    public function __wakeup(): never
    {
        throw new \Exception("Cannot unserialize singleton.");
    }
}

// Vấn đề: hard to test, global state, coupling
$db = DatabaseConnection::getInstance();
```

---

## Thay thế đúng — Dependency Injection với binding singleton

Trong Laravel, dùng Service Container để register binding `singleton`:

```php
// AppServiceProvider.php
public function register(): void
{
    // Framework quản lý lifecycle, không phải class tự quản lý
    $this->app->singleton(PaymentGatewayInterface::class, VNPayGateway::class);
    $this->app->singleton(CacheManager::class, function () {
        return new CacheManager(config('cache'));
    });
}

// Sử dụng qua Dependency Injection — dễ test, dễ thay thế
class OrderService
{
    public function __construct(
        private PaymentGatewayInterface $gateway  // Injected, có thể mock
    ) {}
}
```

---

## Tổng kết

| | Singleton thuần | Dependency Injection |
|---|---|---|
| Testability | Khó mock | Dễ inject mock |
| Coupling | Cao (trực tiếp) | Thấp (qua interface) |
| Global state | Có | Không |
| Khuyến nghị | Tránh | Dùng |

---

## Liên kết

- [SOLID — DIP](../../01-foundations/SOLID.md)
- [Factory Method](./factory-method.md)
