# Artisan Commands — Tổng Hợp Lệnh Laravel

**Priority: 4 (Code Quality — Developer Tooling)**

---

## Tổng quan

**Artisan** là Command-Line Interface (CLI) tích hợp sẵn trong Laravel.
Cung cấp hàng trăm lệnh hữu ích cho quá trình phát triển và vận hành.

```bash
# Xem tất cả commands
php artisan list

# Xem chi tiết một command
php artisan help make:controller
```

---

## Nhóm 1 — Application

```bash
# Tạo APP_KEY
php artisan key:generate

# Bảo trì (Maintenance mode)
php artisan down --secret="bypass-token-123"  # Chỉ ai biết token mới vào được
php artisan up

# Xem thông tin môi trường
php artisan about
php artisan env
```

---

## Nhóm 2 — Make (Tạo file)

```bash
# Controllers
php artisan make:controller ProductController
php artisan make:controller Api/ProductController --api    # Chỉ CRUD methods
php artisan make:controller Api/ProductController --resource --model=Product

# Models
php artisan make:model Product
php artisan make:model Product -m          # Kèm migration
php artisan make:model Product -mcf        # Kèm migration, controller, factory
php artisan make:model Product -a          # All: migration, factory, seeder, controller, policy

# Migrations
php artisan make:migration create_products_table
php artisan make:migration add_discount_to_products_table --table=products

# Seeders & Factories
php artisan make:seeder ProductSeeder
php artisan make:factory ProductFactory --model=Product

# Requests (FormRequest)
php artisan make:request StoreProductRequest
php artisan make:request UpdateProductRequest

# Services / Jobs / Events / Listeners
php artisan make:service OrderService               # (Nếu dùng package hỗ trợ)
php artisan make:job SendOrderConfirmationEmail
php artisan make:event OrderPlaced
php artisan make:listener SendConfirmationEmail --event=OrderPlaced

# Middleware / Policies / Observers
php artisan make:middleware CheckSessionTimeout
php artisan make:policy OrderPolicy --model=Order
php artisan make:observer OrderObserver --model=Order

# Others
php artisan make:resource ProductResource          # API Resource
php artisan make:resource ProductCollection        # API Collection
php artisan make:rule ValidVietnamesePhone         # Custom validation rule
php artisan make:command GenerateDailyReport       # Custom Artisan command
php artisan make:provider PaymentServiceProvider
php artisan make:mail OrderConfirmationMail --markdown=emails.orders.confirmation
php artisan make:notification OrderShipped
php artisan make:exception InsufficientStockException
```

---

## Nhóm 3 — Database

```bash
# Migrate
php artisan migrate                           # Chạy migrations chưa chạy
php artisan migrate --step                    # Mỗi migration trong transaction riêng
php artisan migrate:rollback                  # Rollback migration cuối
php artisan migrate:rollback --step=3         # Rollback 3 migrations gần nhất
php artisan migrate:reset                     # Rollback TẤT CẢ
php artisan migrate:fresh                     # Drop tất cả bảng + migrate lại
php artisan migrate:fresh --seed             # Fresh + seed
php artisan migrate:status                   # Xem trạng thái migrations

# Seed
php artisan db:seed                          # Chạy tất cả seeders
php artisan db:seed --class=ProductSeeder   # Chạy seeder cụ thể

# Wipe
php artisan db:wipe                          # Xoá tất cả tables (production — rất nguy hiểm)
```

---

## Nhóm 4 — Cache

```bash
# Application cache
php artisan cache:clear                      # Xoá toàn bộ cache
php artisan cache:forget some-key           # Xoá cache key cụ thể

# Config cache (production)
php artisan config:cache                     # Compile config sang file cache
php artisan config:clear                     # Xoá config cache

# Route cache (production)
php artisan route:cache                      # Compile routes
php artisan route:clear                      # Xoá route cache
php artisan route:list                       # Xem tất cả routes

# View cache
php artisan view:cache                       # Compile Blade views
php artisan view:clear                       # Xoá view cache

# Optimize (chạy tất cả cache cho production)
php artisan optimize                         # config + route + view cache
php artisan optimize:clear                   # Xoá tất cả cache
```

---

## Nhóm 5 — Queue

```bash
# Chạy worker
php artisan queue:work                                         # Worker mặc định
php artisan queue:work redis --queue=critical,default         # Queue ưu tiên
php artisan queue:work --sleep=3 --tries=3 --max-time=3600   # Với cấu hình

# Kiểm soát
php artisan queue:restart                    # Restart workers sau deploy
php artisan queue:monitor redis:default,redis:emails # Monitor queue size
php artisan queue:failed                     # Xem failed jobs
php artisan queue:retry all                  # Retry tất cả failed jobs
php artisan queue:retry 5                    # Retry job ID=5
php artisan queue:forget 5                   # Xoá failed job ID=5
php artisan queue:flush                      # Xoá tất cả failed jobs
php artisan queue:prune-failed --hours=48   # Xoá failed jobs cũ hơn 48h
```

---

## Nhóm 6 — Schedule

```bash
php artisan schedule:run          # Chạy scheduled tasks (gọi bởi cron mỗi phút)
php artisan schedule:list         # Xem các scheduled tasks
php artisan schedule:test         # Test chạy thử tất cả tasks
php artisan schedule:work         # Daemon mode (development)
```

---

## Nhóm 7 — Auth & Sanctum

```bash
php artisan sanctum:prune-expired --hours=24  # Xoá expired tokens
php artisan auth:clear-resets                  # Xoá password reset tokens hết hạn
```

---

## Nhóm 8 — Tinker (REPL)

```bash
php artisan tinker    # Mở interactive PHP shell với Laravel context

# Trong tinker:
>>> User::count()
>>> Order::with('user')->latest()->first()
>>> Hash::make('my-password')
>>> Str::uuid()
>>> dispatch(new SendOrderConfirmationEmail(Order::find(1)))
```

---

## Nhóm 9 — Bảo trì và Production

```bash
# Storage
php artisan storage:link          # Tạo symlink public/storage → storage/app/public

# Event
php artisan event:list            # Xem tất cả events và listeners

# Package
php artisan vendor:publish --provider="Package\ServiceProvider"

# Telescope (nếu dùng)
php artisan telescope:prune --hours=48
php artisan telescope:install

# Horizon (Queue Dashboard)
php artisan horizon
php artisan horizon:terminate
php artisan horizon:status
```

---

## Quick Reference Card

| Mục đích | Lệnh nhanh |
|---|---|
| Tạo model + migration + controller | `php artisan make:model Product -mc` |
| Fresh database với dữ liệu mẫu | `php artisan migrate:fresh --seed` |
| Xem logs realtime | `php artisan pail` (Laravel 11+) |
| Tối ưu cho production | `php artisan optimize` |
| Xem tất cả routes | `php artisan route:list --json` |
| Restart queue workers | `php artisan queue:restart` |

---

## Liên kết

- [CI/CD](../09-laravel-core/cicd.md)
- [Queue & Jobs](../09-laravel-core/queue-jobs.md)
- [Composer](../10-php-syntax/composer.md)
