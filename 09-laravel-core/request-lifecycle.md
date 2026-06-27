# Request Lifecycle — Vòng Đời HTTP Request

**Priority: 3** | Nhóm: [Laravel Core](./README.md)

---

## Tổng quan

Hiểu vòng đời một request giúp biết **chính xác** đoạn code của mình chạy ở đâu và khi nào.

---

## Sơ đồ vòng đời

```
Browser / API Client
        |
        | HTTP Request (GET, POST, ...)
        v
+-------------------------------+
|  public/index.php             |  Entry point duy nhất
|  - Load autoloader            |
|  - Tạo Application instance   |
+-------------------------------+
        |
        v
+-------------------------------+
|  HTTP Kernel (bootstrap)      |
|  - Load Service Providers     |
|  - Cấu hình môi trường        |
+-------------------------------+
        |
        v
+-------------------------------+
|  Global Middleware Pipeline   |  Chạy TRƯỚC khi vào Router
|  - TrustProxies               |
|  - HandleCors                 |
|  - PreventRequestsDuringMaintenance |
|  - TrimStrings, ConvertEmpty  |
+-------------------------------+
        |
        v
+-------------------------------+
|  Router                       |
|  - Tìm route khớp URI         |
|  - Chạy Route Middleware      |
|    (auth, throttle, ...)       |
+-------------------------------+
        |
        v
+-------------------------------+
|  Controller / Closure         |
|  - FormRequest validate       |
|  - Gọi Service                |
|  - Trả về Response            |
+-------------------------------+
        |
        v
+-------------------------------+
|  Response Pipeline            |
|  - Middleware terminate()     |
|  - Gửi Response về client     |
+-------------------------------+
        |
        v
    Browser / API Client
```

---

## Service Providers — Điểm Boot hệ thống

```php
// Thứ tự boot trong config/app.php
'providers' => [
    // Framework providers (load trước)
    Illuminate\Auth\AuthServiceProvider::class,
    Illuminate\Database\DatabaseServiceProvider::class,
    // ...

    // App providers (load sau)
    App\Providers\AppServiceProvider::class,
    App\Providers\RouteServiceProvider::class,
    App\Providers\EventServiceProvider::class,
],
```

Mỗi Provider có 2 method:
- `register()`: Đăng ký binding vào Container (chạy trước)
- `boot()`: Thực thi logic sau khi tất cả providers đã register (chạy sau)

---

## Middleware Lifecycle — Thứ tự thực thi

```php
// Middleware chạy theo thứ tự LIFO (Last In, First Out) khi response
// Request:  A → B → C → Controller
// Response: C → B → A → Client

class LogRequestMiddleware
{
    public function handle(Request $request, Closure $next): Response
    {
        // === Chạy TRƯỚC controller ===
        Log::info('Inbound request', ['uri' => $request->uri()]);

        $response = $next($request); // Đẩy xuống pipeline

        // === Chạy SAU controller ===
        Log::info('Outbound response', ['status' => $response->status()]);

        return $response;
    }

    // Chạy SAU khi response đã được gửi về client
    public function terminate(Request $request, Response $response): void
    {
        // Dọn dẹp, cleanup tasks
    }
}
```

---

## Liên kết

- [Service Providers](./service-providers.md)
- [Middleware](./middleware.md)
- [Layered Architecture](../03-architecture/layered-architecture.md)
