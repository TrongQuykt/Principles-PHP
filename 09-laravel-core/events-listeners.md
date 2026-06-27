# Events & Listeners — Hệ Thống Sự Kiện

**Priority: 2 (Observer Pattern)** | Nhóm: [Laravel Core](./README.md)

---

## Định nghĩa

Laravel's Event system là triển khai thực tế của **Observer Pattern**.
Cho phép tách biệt hoàn toàn giữa "khi nào sự kiện xảy ra" và "cái gì phản ứng với sự kiện đó".

---

## Tạo Event & Listener

```bash
php artisan make:event OrderPlaced
php artisan make:listener SendConfirmationEmail --event=OrderPlaced
php artisan make:listener UpdateInventory --event=OrderPlaced
```

```php
// app/Events/OrderPlaced.php
class OrderPlaced
{
    use Dispatchable, InteractsWithSockets, SerializesModels;

    public function __construct(
        public readonly Order $order
    ) {}
}

// app/Listeners/SendConfirmationEmail.php
class SendConfirmationEmail implements ShouldQueue  // Async via Queue
{
    public function handle(OrderPlaced $event): void
    {
        Mail::to($event->order->user->email)
            ->send(new OrderConfirmationMail($event->order));
    }

    // Retry config
    public int $tries       = 3;
    public array $backoff   = [30, 60, 120]; // Exponential backoff
}
```

---

## Đăng ký Events (EventServiceProvider)

```php
// app/Providers/EventServiceProvider.php
protected $listen = [
    OrderPlaced::class => [
        SendConfirmationEmail::class,
        UpdateInventory::class,
        NotifyWarehouse::class,
        RecordSalesMetric::class,
    ],

    UserRegistered::class => [
        SendWelcomeEmail::class,
        CreateDefaultProfile::class,
    ],
];
```

---

## Trigger Event

```php
// Cách 1: helper function
event(new OrderPlaced($order));

// Cách 2: static dispatch
OrderPlaced::dispatch($order);

// Cách 3: Model Events tự động trên Eloquent
class Order extends Model
{
    protected static function booted(): void
    {
        static::created(fn ($order) => OrderPlaced::dispatch($order));
        static::updated(fn ($order) => OrderUpdated::dispatch($order));
    }
}
```

---

## Model Observers — Gắn Observer vào Model

```bash
php artisan make:observer OrderObserver --model=Order
```

```php
class OrderObserver
{
    public function creating(Order $order): void
    {
        $order->order_code = 'ORD-' . now()->format('YmdHis') . '-' . Str::random(4);
    }

    public function updated(Order $order): void
    {
        if ($order->isDirty('status') && $order->status === 'shipped') {
            SendShippingNotification::dispatch($order);
        }
    }
}

// Đăng ký trong AppServiceProvider::boot()
Order::observe(OrderObserver::class);
```

---

## Liên kết

- [Design Patterns — Observer](../02-design-patterns/behavioral/observer.md)
- [Queue & Jobs](./queue-jobs.md)
