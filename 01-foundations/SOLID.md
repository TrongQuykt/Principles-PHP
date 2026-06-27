# SOLID

**Priority: 1** | Nhóm: [Foundations](./README.md)

---

## Định nghĩa

**SOLID** là tập hợp 5 nguyên tắc thiết kế hướng đối tượng (Object-Oriented Design),
được Robert C. Martin (Uncle Bob) hệ thống hoá. Áp dụng SOLID giúp code dễ bảo trì,
mở rộng và kiểm tra hơn.

Nguồn: [Wikipedia — SOLID](https://en.wikipedia.org/wiki/SOLID)

---

## Năm nguyên tắc

### S — Single Responsibility Principle (SRP)

> Một class phải có **duy nhất một lý do để thay đổi**.

Một class chỉ nên đảm nhiệm một chức năng duy nhất. Nếu một class phải thay đổi khi logic
business, logic hiển thị, hoặc logic lưu trữ thay đổi — đó là dấu hiệu của vi phạm SRP.

**Vi phạm:**

```php
class Order
{
    public function calculateTotal(): float { ... }      // Business logic
    public function saveToDatabase(): void { ... }       // Data layer
    public function formatAsInvoicePdf(): string { ... } // Presentation
}
```

**Đúng:**

```php
class Order
{
    public function calculateTotal(): float { ... }
}

class OrderRepository
{
    public function save(Order $order): void { ... }
}

class OrderInvoiceFormatter
{
    public function toPdf(Order $order): string { ... }
}
```

---

### O — Open/Closed Principle (OCP)

> Class phải **mở để mở rộng** (extension), nhưng **đóng để sửa đổi** (modification).

Thêm chức năng mới bằng cách thêm code mới, không phải sửa code cũ.

**Vi phạm:**

```php
class DiscountCalculator
{
    public function calculate(string $type, float $price): float
    {
        if ($type === 'vip') {
            return $price * 0.8;
        } elseif ($type === 'student') {
            return $price * 0.9;
        }
        // Mỗi lần thêm loại mới phải sửa class này
        return $price;
    }
}
```

**Đúng:**

```php
interface DiscountStrategy
{
    public function calculate(float $price): float;
}

class VipDiscount implements DiscountStrategy
{
    public function calculate(float $price): float
    {
        return $price * 0.8;
    }
}

class StudentDiscount implements DiscountStrategy
{
    public function calculate(float $price): float
    {
        return $price * 0.9;
    }
}

class DiscountCalculator
{
    public function calculate(DiscountStrategy $strategy, float $price): float
    {
        return $strategy->calculate($price);
    }
}
```

---

### L — Liskov Substitution Principle (LSP)

> Object của lớp con phải có thể **thay thế** object của lớp cha mà không làm thay đổi
> tính đúng đắn của chương trình.

**Vi phạm:**

```php
class Rectangle
{
    public function setWidth(int $w): void { $this->width = $w; }
    public function setHeight(int $h): void { $this->height = $h; }
    public function area(): int { return $this->width * $this->height; }
}

class Square extends Rectangle
{
    // Vi phạm: Square ghi đè hành vi của Rectangle theo cách không mong đợi
    public function setWidth(int $w): void
    {
        $this->width = $w;
        $this->height = $w; // Tự ý thay đổi height!
    }
}
```

**Đúng:** Dùng interface chung thay vì inheritance không phù hợp:

```php
interface Shape
{
    public function area(): int;
}

class Rectangle implements Shape { ... }
class Square implements Shape { ... }
```

---

### I — Interface Segregation Principle (ISP)

> Client không nên bị ép buộc phụ thuộc vào các interface mà nó không sử dụng.

**Vi phạm:**

```php
interface Worker
{
    public function work(): void;
    public function eat(): void;   // Robot không cần eat
    public function sleep(): void; // Robot không cần sleep
}

class Robot implements Worker
{
    public function work(): void { ... }
    public function eat(): void { /* không làm gì */ }   // Forced!
    public function sleep(): void { /* không làm gì */ } // Forced!
}
```

**Đúng:**

```php
interface Workable
{
    public function work(): void;
}

interface Feedable
{
    public function eat(): void;
}

class Human implements Workable, Feedable { ... }
class Robot implements Workable { ... }
```

---

### D — Dependency Inversion Principle (DIP)

> Module cấp cao không nên phụ thuộc vào module cấp thấp.
> Cả hai nên phụ thuộc vào **abstraction** (interface/abstract class).

**Vi phạm:**

```php
class OrderService
{
    private MySQLOrderRepository $repository; // Phụ thuộc trực tiếp vào implementation

    public function __construct()
    {
        $this->repository = new MySQLOrderRepository(); // Tự tạo dependency
    }
}
```

**Đúng:**

```php
interface OrderRepositoryInterface
{
    public function save(Order $order): void;
}

class OrderService
{
    public function __construct(
        private OrderRepositoryInterface $repository // Phụ thuộc vào abstraction
    ) {}
}

// Có thể inject MySQLOrderRepository hoặc RedisOrderRepository hoặc MockRepository
```

---

## Tổng hợp

| Chữ cái | Nguyên tắc | Từ khoá |
|---|---|---|
| S | Single Responsibility | Một class, một nhiệm vụ |
| O | Open/Closed | Mở rộng không sửa đổi |
| L | Liskov Substitution | Lớp con thay thế được lớp cha |
| I | Interface Segregation | Interface nhỏ, chuyên biệt |
| D | Dependency Inversion | Phụ thuộc vào abstraction |

---

## Liên kết

- [Separation of Concerns](./separation-of-concerns.md)
- [Design Patterns](../02-design-patterns/README.md)
- Wikipedia: [SOLID](https://en.wikipedia.org/wiki/SOLID)
