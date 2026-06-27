# Testing — Kiểm Thử Tự Động

**Priority: 4** | Nhóm: [Laravel Core](./README.md)

---

## Định nghĩa

Laravel cung cấp hệ sinh thái testing toàn diện dựa trên **PHPUnit** và **Pest**.
Testing là một phần không thể tách rời của quy trình phát triển enterprise.

---

## Cấu trúc thư mục

```
tests/
├── Unit/           ← Test đơn vị (không cần database, không cần HTTP)
│   ├── Services/
│   │   └── OrderServiceTest.php
│   └── Helpers/
│       └── PriceCalculatorTest.php
└── Feature/        ← Test tích hợp (có HTTP, database)
    ├── Api/
    │   └── OrderApiTest.php
    └── Auth/
        └── LoginTest.php
```

---

## Unit Test — Test Service Layer

```php
// tests/Unit/Services/OrderServiceTest.php
class OrderServiceTest extends TestCase
{
    use RefreshDatabase; // Reset DB sau mỗi test

    private OrderService $service;

    protected function setUp(): void
    {
        parent::setUp();
        $this->service = new OrderService(
            new EloquentOrderRepository(),  // Hoặc mock
            new PriceCalculator()
        );
    }

    /** @test */
    public function it_throws_exception_when_stock_is_insufficient(): void
    {
        $product = Product::factory()->create(['stock' => 2]);

        $this->expectException(InsufficientStockException::class);
        $this->expectExceptionMessage('Sản phẩm không đủ tồn kho');

        $this->service->createOrder([
            'product_id' => $product->id,
            'quantity'   => 10,
            'user_id'    => 1,
        ]);
    }

    /** @test */
    public function it_creates_order_with_correct_total(): void
    {
        $product = Product::factory()->create([
            'base_price'   => 200000,
            'discount_pct' => 10,
            'stock'        => 5,
        ]);
        $user = User::factory()->create();

        $order = $this->service->createOrder([
            'product_id' => $product->id,
            'quantity'   => 2,
            'user_id'    => $user->id,
        ]);

        $this->assertDatabaseHas('orders', ['id' => $order->id]);
        $this->assertEquals(360000, $order->grand_total); // 200000 * 0.9 * 2
    }
}
```

---

## Feature Test — Test HTTP Endpoint

```php
// tests/Feature/Api/OrderApiTest.php
class OrderApiTest extends TestCase
{
    use RefreshDatabase;

    private User $user;
    private string $token;

    protected function setUp(): void
    {
        parent::setUp();
        $this->user  = User::factory()->create();
        $this->token = $this->user->createToken('test')->plainTextToken;
    }

    /** @test */
    public function authenticated_user_can_place_order(): void
    {
        $product = Product::factory()->create(['stock' => 5, 'base_price' => 100000]);

        $response = $this->withToken($this->token)
                         ->postJson('/api/v1/orders', [
                             'product_id' => $product->id,
                             'quantity'   => 2,
                         ]);

        $response->assertStatus(201)
                 ->assertJsonStructure([
                     'status', 'data' => ['id', 'grand_total', 'status'],
                 ])
                 ->assertJsonPath('data.grand_total', 200000);

        $this->assertDatabaseHas('orders', ['user_id' => $this->user->id]);
        $this->assertEquals(3, $product->fresh()->stock); // Tồn kho giảm đúng
    }

    /** @test */
    public function unauthenticated_request_returns_401(): void
    {
        $this->postJson('/api/v1/orders', [])->assertStatus(401);
    }

    /** @test */
    public function validation_fails_with_missing_product_id(): void
    {
        $this->withToken($this->token)
             ->postJson('/api/v1/orders', ['quantity' => 1])
             ->assertStatus(422)
             ->assertJsonValidationErrors(['product_id']);
    }
}
```

---

## Test Doubles — Mock & Stub

```php
/** @test */
public function it_dispatches_confirmation_email_after_order(): void
{
    Mail::fake(); // Chặn gửi email thật

    $product = Product::factory()->create(['stock' => 5]);
    $user    = User::factory()->create();

    (new OrderService())->createOrder([
        'product_id' => $product->id,
        'quantity'   => 1,
        'user_id'    => $user->id,
    ]);

    Mail::assertSent(OrderConfirmationMail::class, fn ($mail) =>
        $mail->hasTo($user->email)
    );
}

/** @test */
public function it_queues_warehouse_notification(): void
{
    Queue::fake();

    // ... đặt hàng ...

    Queue::assertPushed(NotifyWarehouse::class);
    Queue::assertPushed(NotifyWarehouse::class, fn ($job) =>
        $job->order->id === $order->id
    );
}
```

---

## Liên kết

- [Testing Principles](../04-code-quality/testing-principles.md)
- [CI/CD](./cicd.md)
