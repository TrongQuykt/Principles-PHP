# Service Providers — Điểm Đăng Ký Hệ Thống

**Priority: 3** | Nhóm: [Laravel Core](./README.md)

---

## Định nghĩa

**Service Providers** là trung tâm khởi động (bootstrapping) toàn bộ ứng dụng Laravel.
Tất cả bindings vào Container, events, middleware, route — đều được đăng ký qua Service Providers.

---

## Cấu trúc cơ bản

```php
namespace App\Providers;

use Illuminate\Support\ServiceProvider;

class AppServiceProvider extends ServiceProvider
{
    /**
     * Register: Chỉ dùng để đăng ký binding vào Container.
     * KHÔNG gọi đến các service khác tại đây — chúng có thể chưa sẵn sàng.
     */
    public function register(): void
    {
        $this->app->singleton(OrderRepositoryInterface::class, MySQLOrderRepository::class);
        $this->app->bind(PaymentGatewayInterface::class, VNPayGateway::class);
    }

    /**
     * Boot: Toàn bộ providers đã register xong — có thể gọi service khác.
     */
    public function boot(): void
    {
        // Đăng ký Observers
        Order::observe(OrderObserver::class);

        // Đăng ký Policies
        Gate::policy(Order::class, OrderPolicy::class);

        // Đăng ký Macros
        Response::macro('api', function ($data, $message = 'OK', $status = 200) {
            return Response::json(['status' => 'success', 'message' => $message, 'data' => $data], $status);
        });
    }
}
```

---

## Các loại Service Provider thường dùng

| Provider | Mục đích |
|---|---|
| `AppServiceProvider` | Binding chung, config, macros |
| `RouteServiceProvider` | Khai báo routes, route model binding |
| `EventServiceProvider` | Đăng ký Events và Listeners |
| `AuthServiceProvider` | Khai báo Policies, Gates |
| `BroadcastServiceProvider` | Cấu hình Broadcasting channels |

---

## Deferred Providers — Load chậm khi cần
```php
class HeavyServiceProvider extends ServiceProvider
{
    // Provider này chỉ load khi HeavyService được yêu cầu
    protected $defer = true;

    public function provides(): array
    {
        return [HeavyService::class];
    }

    public function register(): void
    {
        $this->app->singleton(HeavyService::class, function () {
            return new HeavyService(/* expensive init */);
        });
    }
}
```

---

## Liên kết

- [Service Container](./service-container.md)
- [Request Lifecycle](./request-lifecycle.md)
