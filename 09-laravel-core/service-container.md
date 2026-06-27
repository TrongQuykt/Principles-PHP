# Service Container — IoC Container

**Priority: 1 (áp dụng DIP)** | Nhóm: [Laravel Core](./README.md)

---

## Định nghĩa

**Service Container** (hay IoC Container — Inversion of Control Container) là trái tim của Laravel.
Nó là một công cụ quản lý **class dependencies** và thực hiện **Dependency Injection** một cách tự động.

Nguồn: [Laravel Docs — Service Container](https://laravel.com/docs/container)

---

## Tại sao cần Service Container?

Không dùng Container:
```php
// Phụ thuộc cứng — khó test, khó thay đổi
class OrderService
{
    private MySQLOrderRepository $repo;
    private VNPayGateway $payment;

    public function __construct()
    {
        $this->repo    = new MySQLOrderRepository(new PDO(...));
        $this->payment = new VNPayGateway(config('services.vnpay.key'));
    }
}
```

Dùng Service Container:
```php
// Laravel tự giải quyết dependencies — class chỉ biết interface
class OrderService
{
    public function __construct(
        private OrderRepositoryInterface $repo,
        private PaymentGatewayInterface  $payment,
    ) {}
}
```

---

## Các cách Binding

### Bind cơ bản
```php
// AppServiceProvider::register()

// Mỗi lần resolve = 1 instance mới
$this->app->bind(OrderRepositoryInterface::class, MySQLOrderRepository::class);

// Singleton — chỉ tạo 1 instance duy nhất
$this->app->singleton(CacheManager::class, function ($app) {
    return new CacheManager($app['config']['cache']);
});

// Instance cố định — bind một object đã tạo sẵn
$this->app->instance(PaymentConfig::class, new PaymentConfig([
    'key' => config('services.vnpay.key'),
]));
```

### Contextual Binding — Inject khác nhau tuỳ context
```php
$this->app->when(PhotoController::class)
          ->needs(Filesystem::class)
          ->give(function () {
              return Storage::disk('photos');
          });

$this->app->when(VideoController::class)
          ->needs(Filesystem::class)
          ->give(function () {
              return Storage::disk('videos');
          });
```

---

## Resolve (Lấy instance từ Container)

```php
// Cách 1: Type-hint trong constructor (tự động — khuyến nghị)
class OrderController extends Controller
{
    public function __construct(private OrderService $service) {}
}

// Cách 2: app() helper
$service = app(OrderService::class);
$service = app()->make(OrderService::class);

// Cách 3: Facade resolve
$service = App::make(OrderService::class);
```

---

## Tagging — Nhóm nhiều binding
```php
// Đăng ký tag
$this->app->tag([VNPayGateway::class, MoMoGateway::class], 'payment-gateways');

// Resolve tất cả
$gateways = $this->app->tagged('payment-gateways'); // Collection of instances
```

---

## Liên kết

- [Service Providers](./service-providers.md)
- [SOLID — DIP](../01-foundations/SOLID.md)
- [Design Patterns — Factory Method](../02-design-patterns/creational/factory-method.md)
