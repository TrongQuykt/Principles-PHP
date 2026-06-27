# Clean Architecture

**Priority: 3** | Nhóm: [Architecture](./README.md)

---

## Định nghĩa

**Clean Architecture** (Robert C. Martin) tổ chức code theo các vòng tròn đồng tâm,
với **quy tắc phụ thuộc**: dependency chỉ được phép chỉ hướng **vào trong**.

Nguồn: [The Clean Architecture — Uncle Bob](https://blog.cleancoder.com/uncle-bob/2012/08/13/the-clean-architecture.html)

---

## Mô hình vòng tròn

```
+--------------------------------------------------+
|  Frameworks & Drivers (Laravel, DB, UI)          |  Vòng ngoài cùng
|  +--------------------------------------------+  |
|  |  Interface Adapters (Controllers, Repos)   |  |
|  |  +--------------------------------------+  |  |
|  |  |  Application Business Rules          |  |  |
|  |  |  (Use Cases / Services)              |  |  |
|  |  |  +------------------------------+   |  |  |
|  |  |  |  Enterprise Business Rules   |   |  |  |
|  |  |  |  (Entities / Domain Objects) |   |  |  |
|  |  |  +------------------------------+   |  |  |
|  |  +--------------------------------------+  |  |
|  +--------------------------------------------+  |
+--------------------------------------------------+
```

**Quy tắc**: Code ở vòng trong **không được biết** về code ở vòng ngoài.

---

## Quy tắc phụ thuộc

| Vòng | Biết về | Không được biết về |
|---|---|---|
| Entities | Chỉ chính nó | Tất cả vòng ngoài |
| Use Cases | Entities | Controllers, Framework |
| Interface Adapters | Use Cases, Entities | Laravel, DB cụ thể |
| Frameworks | Tất cả | Không áp dụng |

---

## Ứng dụng thực tế trong Laravel

Clean Architecture không yêu cầu áp dụng 100% từ ngày 1. Với dự án nhỏ-vừa, áp dụng theo **nguyên tắc**:

### Entities — Independent business objects

```php
// Domain object thuần — không phụ thuộc Laravel
class Money
{
    public function __construct(
        public readonly int $amount,    // Đơn vị: đồng
        public readonly string $currency = 'VND'
    ) {
        if ($amount < 0) {
            throw new \InvalidArgumentException('Amount cannot be negative');
        }
    }

    public function add(Money $other): Money
    {
        return new Money($this->amount + $other->amount, $this->currency);
    }

    public function format(): string
    {
        return number_format($this->amount) . 'đ';
    }
}
```

### Use Cases — Application-specific business rules

```php
// Service — chứa use case, không biết về HTTP hay DB cụ thể
class PlaceOrderUseCase
{
    public function __construct(
        private OrderRepositoryInterface $orderRepo,    // Interface, không phải MySQL
        private ProductRepositoryInterface $productRepo
    ) {}

    public function execute(PlaceOrderDTO $dto): Order
    {
        $product = $this->productRepo->findOrFail($dto->productId);

        if ($product->stock < $dto->quantity) {
            throw new InsufficientStockException();
        }

        $total = new Money($product->price * $dto->quantity);

        return $this->orderRepo->create([
            'user_id'  => $dto->userId,
            'total'    => $total->amount,
        ]);
    }
}
```

---

## Khi nào áp dụng Clean Architecture đầy đủ

- Hệ thống lớn, cần tồn tại nhiều năm
- Có thể cần swap framework trong tương lai
- Domain logic phức tạp cần independent testing

Với project nhỏ hoặc MVP: áp dụng phần **Service + Interface** là đủ.

---

## Liên kết

- [Layered Architecture](./layered-architecture.md)
- [SOLID](../01-foundations/SOLID.md)
