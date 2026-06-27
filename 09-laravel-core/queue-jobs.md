# Queue & Jobs — Xử Lý Bất Đồng Bộ

**Priority: 2 (Command Pattern)** | Nhóm: [Laravel Core](./README.md)

---

## Định nghĩa

**Queue** (hàng đợi) là cơ chế để trì hoãn các tác vụ tốn thời gian ra khỏi chu trình xử lý HTTP request.
Ví dụ: gửi email, xử lý ảnh, gọi API bên thứ 3, tạo báo cáo PDF.

---

## Khi nào dùng Queue

| Nên dùng Queue | Không cần Queue |
|---|---|
| Gửi email / SMS | Truy vấn database đơn giản |
| Gọi API bên thứ 3 | Tính toán tức thời trong < 200ms |
| Xử lý file / ảnh nặng | Render view |
| Tạo PDF, export Excel | Đọc config |
| Sync dữ liệu warehouse | |

---

## Tạo và Dispatch Job

```bash
php artisan make:job SendOrderConfirmationEmail
```

```php
class SendOrderConfirmationEmail implements ShouldQueue
{
    use Dispatchable, InteractsWithQueue, Queueable, SerializesModels;

    // Cấu hình retry
    public int $tries       = 3;        // Thử tối đa 3 lần
    public int $backoff      = 60;       // Chờ 60 giây giữa các lần retry
    public int $timeout      = 120;      // Job bị kill sau 120 giây
    public int $maxExceptions = 2;       // Bỏ sau 2 exception khác nhau

    public function __construct(
        public readonly Order $order     // Eloquent model tự serialize
    ) {}

    public function handle(Mailer $mailer): void
    {
        $mailer->to($this->order->user->email)
               ->send(new OrderConfirmationMail($this->order));
    }

    public function failed(\Throwable $exception): void
    {
        Log::error('Failed to send confirmation email', [
            'order_id' => $this->order->id,
            'error'    => $exception->getMessage(),
        ]);
        // Thông báo admin, ghi ticket, v.v.
    }
}

// Dispatch
SendOrderConfirmationEmail::dispatch($order);                       // Async
SendOrderConfirmationEmail::dispatchSync($order);                  // Sync (dùng trong test)
SendOrderConfirmationEmail::dispatch($order)->delay(now()->addMinutes(5));
SendOrderConfirmationEmail::dispatch($order)->onQueue('emails');    // Queue riêng
```

---

## Cấu hình Queue Driver

```dotenv
# .env
QUEUE_CONNECTION=redis   # Hoặc: database, sqs, sync (local dev)
REDIS_HOST=127.0.0.1
REDIS_PORT=6379
```

```php
// config/queue.php — Cấu hình nhiều queue với priority khác nhau
'connections' => [
    'redis' => [
        'driver'  => 'redis',
        'queue'   => 'default',
        'retry_after' => 90,
    ],
],
```

---

## Chạy Worker

```bash
# Chạy worker (production dùng Supervisor)
php artisan queue:work redis --queue=critical,emails,default --sleep=3 --tries=3

# Giám sát queue (thư viện Laravel Horizon)
php artisan horizon
```

---

## Supervisor config (production)

```ini
[program:laravel-worker]
process_name=%(program_name)s_%(process_num)02d
command=php /var/www/html/artisan queue:work redis --sleep=3 --tries=3 --max-time=3600
autostart=true
autorestart=true
stopasgroup=true
killasgroup=true
numprocs=4
redirect_stderr=true
stdout_logfile=/var/log/worker.log
```

---

## Scheduled Tasks (Cron)

```php
// routes/console.php hoặc app/Console/Kernel.php
Schedule::job(SyncProductInventory::class)->everyFifteenMinutes();
Schedule::command('orders:cancel-expired')->hourly();
Schedule::call(fn () => Cache::flush())->dailyAt('03:00');
Schedule::job(GenerateDailyReport::class)->dailyAt('23:55')->onOneServer();
```

```bash
# Crontab (mỗi phút gọi Laravel Scheduler)
* * * * * cd /var/www/html && php artisan schedule:run >> /dev/null 2>&1
```

---

## Liên kết

- [Design Patterns — Command](../02-design-patterns/behavioral/command.md)
- [Events & Listeners](./events-listeners.md)
- [Caching Strategy](../06-performance/caching-strategies.md)
