# Date & Time — Xử Lý Ngày Tháng

**Priority: 4** | Nhóm: [PHP Syntax](./README.md)

---

## Carbon — Thư viện DateTime của Laravel

**Carbon** là thư viện DateTime mở rộng của PHP, được tích hợp sẵn trong Laravel.
Luôn dùng Carbon thay vì `date()` hoặc `new DateTime()` khi làm việc với Laravel.

```php
use Carbon\Carbon;

// Tạo instance
$now       = Carbon::now();                        // Thời điểm hiện tại
$today     = Carbon::today();                      // Hôm nay 00:00:00
$dateStr   = Carbon::parse('2024-06-27');          // Từ string
$fromTs    = Carbon::createFromTimestamp(1719475200);
$specific  = Carbon::create(2024, 6, 27, 10, 30, 0); // Y, M, D, H, i, s
```

---

## Format ngày tháng — Chuẩn ISO 8601

```php
// Database luôn lưu UTC — ISO 8601
$date->toIso8601String();  // '2024-06-27T03:30:00+00:00'
$date->toDateTimeString(); // '2024-06-27 03:30:00'
$date->toDateString();     // '2024-06-27'
$date->toTimeString();     // '03:30:00'
$date->format('Y-m-d H:i:s'); // Custom format

// Hiển thị cho người dùng (timezone của user)
$date->setTimezone('Asia/Ho_Chi_Minh')->format('d/m/Y H:i'); // '27/06/2024 10:30'

// Hiển thị thân thiện
$date->diffForHumans();    // '5 minutes ago', '3 days ago'
```

---

## Cộng trừ thời gian

```php
$date = Carbon::now();

// Cộng
$date->addDays(7);
$date->addHours(3)->addMinutes(30);
$date->addMonths(1);
Carbon::now()->addWeek();

// Trừ
$date->subDays(3);
$date->subHours(1);

// Helpers
Carbon::now()->startOfDay();      // 00:00:00
Carbon::now()->endOfDay();        // 23:59:59
Carbon::now()->startOfMonth();
Carbon::now()->endOfMonth();
Carbon::now()->startOfWeek();     // Monday
```

---

## So sánh và kiểm tra

```php
$date1 = Carbon::parse('2024-06-27');
$date2 = Carbon::parse('2024-06-30');

$date1->isBefore($date2);        // true
$date1->isAfter($date2);         // false
$date1->isSameDay($date2);       // false
$date1->isFuture();              // false (nếu đã qua)
$date1->isPast();                // true

$date1->diffInDays($date2);      // 3
$date1->diffInHours($date2);     // 72
```

---

## Timezone chuẩn cho dự án

```dotenv
# .env
APP_TIMEZONE=Asia/Ho_Chi_Minh
```

```php
// config/app.php
'timezone' => env('APP_TIMEZONE', 'Asia/Ho_Chi_Minh'),
```

**Nguyên tắc**:
- Database luôn lưu **UTC** (`timestamps()` trong migration)
- Application layer convert sang **timezone của user** khi hiển thị
- API trả về **ISO 8601** (UTC): `2024-06-27T03:30:00Z`

---

## Date Period Format chuẩn

| Mục đích | Format | Ví dụ |
|---|---|---|
| Database column (datetime) | `YYYY-MM-DD HH:MM:SS` | `2024-06-27 03:30:00` |
| API response (ISO 8601) | `YYYY-MM-DDTHH:MM:SSZ` | `2024-06-27T03:30:00Z` |
| Hiển thị ngày | `DD/MM/YYYY` | `27/06/2024` |
| Hiển thị ngày giờ | `DD/MM/YYYY HH:MM` | `27/06/2024 10:30` |
| Tên file (sortable) | `YYYYMMDD_HHmmss` | `20240627_103000` |
| Migration tên | `YYYY_MM_DD_HHmmss` | `2024_06_27_103000` |

---

## Liên kết

- [Database Design](../11-database-design/README.md)
- [Naming Conventions](../04-code-quality/naming-conventions.md)
