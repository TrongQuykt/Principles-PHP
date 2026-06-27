# Input Validation

**Priority: 5** | Nhóm: [Security](./README.md)

---

## Nguyên tắc

> "Never trust user input."

Mọi dữ liệu đến từ bên ngoài (HTTP request, webhook, import file) đều phải được
**validate và sanitize** trước khi đưa vào hệ thống.

---

## Laravel Form Requests

Mọi POST/PUT/PATCH endpoint đều phải có FormRequest:

```bash
php artisan make:request StoreOrderRequest
```

```php
class StoreOrderRequest extends FormRequest
{
    public function authorize(): bool
    {
        return auth()->check(); // Chỉ cho phép user đã đăng nhập
    }

    public function rules(): array
    {
        return [
            'product_id' => ['required', 'integer', 'exists:products,id'],
            'quantity'   => ['required', 'integer', 'min:1', 'max:100'],
            'coupon_code'=> ['nullable', 'string', 'max:50', 'alpha_num'],
            'note'       => ['nullable', 'string', 'max:500'],
        ];
    }

    public function messages(): array
    {
        return [
            'product_id.exists' => 'Sản phẩm không tồn tại hoặc đã ngừng bán.',
            'quantity.max'      => 'Số lượng tối đa mỗi đơn hàng là 100 sản phẩm.',
        ];
    }
}
```

---

## Quy tắc Validation

| Quy tắc | Lý do |
|---|---|
| Luôn validate `exists:table,column` cho foreign key | Ngăn tham chiếu đến entity không tồn tại |
| Giới hạn `max` cho string input | Ngăn payload quá lớn |
| Dùng `alpha_num` hoặc regex cho code/slug | Ngăn injection |
| Validate file: type, size, extension | Ngăn upload file độc hại |
| Không chỉ validate phía client | Client-side validation chỉ là UX |

---

## Không tin tưởng ID từ Client

```php
// NGUY HIỂM: Dùng user_id từ request
$order = Order::where('user_id', $request->user_id)->get();

// AN TOÀN: Luôn dùng authenticated user's ID
$order = Order::where('user_id', auth()->id())->get();
```

---

## Mass Assignment Protection

```php
// Model bắt buộc dùng $fillable (whitelist), không dùng $guarded = []
class Order extends Model
{
    protected $fillable = [
        'user_id',
        'product_id',
        'quantity',
        'total',
        'status',
    ];

    // Không có 'is_admin', 'role', 'balance' trong fillable
}
```

---

## Liên kết

- [OWASP Top 10](./owasp-top10.md)
- [Authentication & Authorization](./authentication-authorization.md)
