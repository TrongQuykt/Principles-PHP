# Command

**Priority: 2** | Nhóm: [Behavioral Patterns](../README.md)

---

## Định nghĩa

**Command** là pattern đóng gói một request thành một object độc lập,
chứa đầy đủ thông tin về request đó. Cho phép parameterize, queue, log, và undo operations.

Nguồn: [Wikipedia — Command Pattern](https://en.wikipedia.org/wiki/Command_pattern)

---

## Khi nào dùng

- Cần queue các request để thực thi sau
- Cần hỗ trợ undo/redo operations
- Cần log và audit trail cho mọi hành động
- Cần tách biệt người gửi request và người thực thi

---

## Ví dụ — Laravel Jobs (Command Pattern trong thực tế)

```php
// Command object — chứa toàn bộ thông tin để thực thi
class SendOrderConfirmationEmail implements ShouldQueue
{
    use Dispatchable, InteractsWithQueue, Queueable, SerializesModels;

    public function __construct(
        public readonly Order $order
    ) {}

    // Thực thi
    public function handle(Mailer $mailer): void
    {
        $mailer->to($this->order->user->email)
               ->send(new OrderConfirmationMail($this->order));
    }

    // Retry logic
    public int $tries = 3;
    public int $backoff = 60;

    // Xử lý khi fail
    public function failed(\Throwable $exception): void
    {
        Log::error('Failed to send order confirmation', [
            'order_id' => $this->order->id,
            'error'    => $exception->getMessage(),
        ]);
    }
}

// Gửi command (Queue như Invoker)
SendOrderConfirmationEmail::dispatch($order);                   // Async
SendOrderConfirmationEmail::dispatchSync($order);              // Sync
SendOrderConfirmationEmail::dispatch($order)->delay(now()->addMinutes(5)); // Delayed
```

---

## Ví dụ — Command với Undo

```php
interface Command
{
    public function execute(): void;
    public function undo(): void;
}

class TransferMoneyCommand implements Command
{
    private bool $executed = false;

    public function __construct(
        private Account $from,
        private Account $to,
        private int $amount
    ) {}

    public function execute(): void
    {
        $this->from->debit($this->amount);
        $this->to->credit($this->amount);
        $this->executed = true;
    }

    public function undo(): void
    {
        if (!$this->executed) {
            throw new \LogicException('Cannot undo command that was not executed.');
        }
        $this->to->debit($this->amount);
        $this->from->credit($this->amount);
        $this->executed = false;
    }
}
```

---

## Liên kết

- [Observer](./observer.md)
- [Strategy](./strategy.md)
