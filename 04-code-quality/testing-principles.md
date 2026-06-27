# Testing Principles

**Priority: 4** | Nhóm: [Code Quality](./README.md)

---

## Mục tiêu của Testing

- Xác nhận code hoạt động đúng như kỳ vọng
- Phát hiện regression (lỗi mới gây ra bởi thay đổi cũ)
- Tạo "safety net" để tự tin refactor
- Documentation sống: test mô tả hành vi mong muốn

---

## Test Triangle

```
         /\
        /  \    <- E2E Tests (ít, chậm, đắt)
       /----\
      /      \  <- Integration Tests (vừa phải)
     /--------\
    /          \ <- Unit Tests (nhiều, nhanh, rẻ)
   /____________\
```

| Loại | Mục đích | Tốc độ | Số lượng |
|---|---|---|---|
| Unit Test | Test từng function/class độc lập | Rất nhanh | Nhiều nhất |
| Integration Test | Test nhiều layer tương tác | Trung bình | Vừa phải |
| E2E Test | Test toàn bộ luồng người dùng | Chậm | Ít nhất |

---

## Nguyên tắc F.I.R.S.T

| Chữ cái | Nguyên tắc | Ý nghĩa |
|---|---|---|
| F | Fast | Test phải chạy nhanh |
| I | Independent | Test không phụ thuộc nhau |
| R | Repeatable | Cùng một điều kiện = cùng kết quả |
| S | Self-validating | Pass/Fail rõ ràng, không cần kiểm tra thủ công |
| T | Timely | Viết test trước hoặc cùng lúc với code |

---

## Cấu trúc test tốt — AAA Pattern

```php
public function test_create_order_reduces_product_stock(): void
{
    // Arrange — Chuẩn bị dữ liệu
    $product = Product::factory()->create(['stock' => 10]);
    $user = User::factory()->create();

    // Act — Thực thi hành động cần test
    $this->actingAs($user)->postJson('/api/v1/orders', [
        'product_id' => $product->id,
        'quantity'   => 3,
    ]);

    // Assert — Kiểm tra kết quả
    $this->assertDatabaseHas('orders', ['user_id' => $user->id]);
    $this->assertEquals(7, $product->fresh()->stock);
}
```

---

## Những gì cần test

| Nên test | Không cần test |
|---|---|
| Business logic (Service layer) | Framework code (Eloquent, Router) |
| Input validation rules | Getter/Setter đơn giản |
| Edge cases (negative stock, empty cart) | Third-party libraries |
| Permission và Authorization | Configuration files |
| Complex calculation | Trivial code (<3 dòng, không có branch) |

---

## Ví dụ Unit Test — Service Layer

```php
class OrderServiceTest extends TestCase
{
    public function test_throws_exception_when_stock_insufficient(): void
    {
        // Arrange
        $product = Product::factory()->create(['stock' => 2]);
        $service = new OrderService(new ProductRepository());

        // Act & Assert
        $this->expectException(InsufficientStockException::class);
        $service->createOrder([
            'product_id' => $product->id,
            'quantity'   => 10, // Nhiều hơn stock
            'user_id'    => 1,
        ]);
    }

    public function test_creates_order_with_correct_total(): void
    {
        $product = Product::factory()->create([
            'price'               => 100000,
            'discount_percentage' => 10,
            'stock'               => 5,
        ]);

        $order = (new OrderService())->createOrder([
            'product_id' => $product->id,
            'quantity'   => 2,
            'user_id'    => 1,
        ]);

        $this->assertEquals(180000, $order->total); // 100000 * 0.9 * 2
    }
}
```

---

## Liên kết

- [Goal-Driven Execution](../07-karpathy-principles/goal-driven-execution.md)
- [Refactoring](./refactoring.md)
