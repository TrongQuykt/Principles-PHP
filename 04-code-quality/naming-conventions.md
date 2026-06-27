# Naming Conventions

**Priority: 4** | Nhóm: [Code Quality](./README.md)

---

## Nguyên tắc chung

> "There are only two hard things in Computer Science: cache invalidation and naming things."
> — Phil Karlton

Tên tốt là tên mà người đọc **hiểu ngay mục đích** mà không cần comment giải thích.

---

## PHP / Laravel

| Đối tượng | Convention | Ví dụ |
|---|---|---|
| Class | PascalCase | `OrderService`, `ProductVariant` |
| Interface | PascalCase + Interface suffix | `PaymentGatewayInterface` |
| Method | camelCase (động từ + danh từ) | `createOrder()`, `getUserById()` |
| Variable | camelCase, danh từ | `$orderTotal`, `$userId` |
| Constant | UPPER_SNAKE_CASE | `MAX_RETRY_ATTEMPTS` |
| Database table | snake_case, số nhiều | `order_items`, `product_variants` |
| Database column | snake_case | `base_price`, `created_at` |
| Foreign key | `singularTable_id` | `user_id`, `order_id` |
| Route | kebab-case | `/product-variants/{id}` |

---

## Đặt tên biến và hàm rõ nghĩa

### Biến

```php
// Sai — không rõ nghĩa
$d = 7;
$arr = User::all();
$tmp = calculateSomething();

// Đúng
$expirationDays = 7;
$activeUsers = User::where('is_active', true)->get();
$discountedPrice = calculateDiscount($product->price, $discountRate);
```

### Hàm

```php
// Sai — quá chung chung
public function process(array $data): void { ... }
public function handle(int $id): mixed { ... }
public function get(): array { ... }

// Đúng — rõ ràng về hành động và đối tượng
public function processRefund(array $refundData): RefundResult { ... }
public function findOrderById(int $orderId): Order { ... }
public function getActiveProductsByCategory(int $categoryId): Collection { ... }
```

---

## Boolean variables và functions

```php
// Tên boolean nên bắt đầu bằng is, has, can, should
$isActive = true;
$hasDiscount = $product->discount_percentage > 0;
$canCheckout = $cart->items->isNotEmpty();

// Function trả về boolean
public function isEligibleForFreeShipping(Order $order): bool { ... }
public function hasAvailableStock(int $productId, int $quantity): bool { ... }
```

---

## Tránh các tên gây nhầm lẫn

| Tên xấu | Vấn đề | Tên tốt |
|---|---|---|
| `$data` | Không biết là dữ liệu gì | `$orderData`, `$userProfile` |
| `$temp` | Không nói lên mục đích | `$discountedUnitPrice` |
| `$flag` | Không rõ flag nghĩa là gì | `$isOutOfStock` |
| `$manager` | Quá chung chung | `$orderManager`, `$cacheManager` |
| `$info` | Không cụ thể | `$userContactInfo`, `$productSpecs` |

---

## JavaScript / TypeScript

| Đối tượng | Convention | Ví dụ |
|---|---|---|
| Variable / Function | camelCase | `orderTotal`, `getUserById` |
| Class / Interface | PascalCase | `OrderService`, `ProductVariant` |
| Constant | UPPER_SNAKE_CASE | `MAX_RETRY_ATTEMPTS` |
| React Component | PascalCase | `ProductCard`, `CheckoutForm` |
| CSS class | kebab-case | `product-card`, `nav-menu` |
| File name | kebab-case | `product-card.tsx`, `use-cart.ts` |

---

## Liên kết

- [Code Review](./code-review.md)
- [Documentation](./documentation.md)
