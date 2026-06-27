# String — Xử Lý Chuỗi trong PHP

**Priority: 4** | Nhóm: [PHP Syntax](./README.md)

---

## Các hàm String cơ bản

```php
// Độ dài
strlen('Hello World'); // 11
mb_strlen('Xin chào'); // 8 (hỗ trợ UTF-8 — LUÔN dùng mb_ cho tiếng Việt)

// Tìm kiếm
str_contains('Laravel Framework', 'Laravel'); // true (PHP 8+)
str_starts_with('email@domain.com', 'email'); // true (PHP 8+)
str_ends_with('file.pdf', '.pdf');            // true (PHP 8+)
strpos('Hello World', 'World'); // 6 (vị trí đầu tiên)

// Biến đổi
strtolower('HELLO');            // 'hello'
strtoupper('hello');            // 'HELLO'
mb_strtolower('TIẾNG VIỆT');   // 'tiếng việt'
ucfirst('hello world');         // 'Hello world'
ucwords('hello world foo');     // 'Hello World Foo'
trim('  hello  ');              // 'hello'
ltrim(' hello');                // 'hello'
rtrim('hello ');                // 'hello'

// Thao tác
str_replace('foo', 'bar', 'foo baz foo'); // 'bar baz bar'
str_pad('5', 3, '0', STR_PAD_LEFT);      // '005' (zero-padding)
str_repeat('ab', 3);                       // 'ababab'
substr('Hello World', 6, 5);              // 'World'
mb_substr('Xin chào', 4, 4);             // 'chào'

// Tách và ghép
explode(',', 'a,b,c,d');  // ['a', 'b', 'c', 'd']
implode(', ', ['a', 'b', 'c']); // 'a, b, c'
```

---

## Laravel Str Helper

Laravel cung cấp class `Str` với nhiều helper hữu ích hơn:

```php
use Illuminate\Support\Str;

// Slug
Str::slug('Sản phẩm mới 2024'); // 'san-pham-moi-2024'

// Uppercase/Lowercase styles
Str::camel('hello_world');       // 'helloWorld'
Str::snake('HelloWorld');        // 'hello_world'
Str::studly('hello_world');      // 'HelloWorld'
Str::kebab('HelloWorld');        // 'hello-world'

// Cắt chuỗi dài
Str::limit('Đây là một chuỗi dài...', 20, '...'); // 'Đây là một chuỗi...'
Str::words('Một hai ba bốn', 2, ' ...');           // 'Một hai ...'

// Xử lý thông thường
Str::contains('Hello World', 'World');         // true
Str::startsWith('Hello', 'He');                // true
Str::endsWith('file.pdf', '.pdf');             // true
Str::random(32);                               // Chuỗi random 32 ký tự
Str::uuid();                                   // UUID v4
Str::orderedUuid();                            // UUID có thứ tự (tốt cho DB index)

// Pluralize / Singularize (English)
Str::plural('product');   // 'products'
Str::singular('orders');  // 'order'
```

---

## String Interpolation và Heredoc

```php
$name = 'An';

// Double-quote interpolation
echo "Xin chào, $name!";
echo "Tổng: {$order->total}đ";

// Heredoc (giữ format, hỗ trợ interpolation)
$html = <<<HTML
    <div class="card">
        <h2>$name</h2>
    </div>
HTML;

// Nowdoc (không interpolation — giống single quote)
$template = <<<'TEXT'
    Xin chào, $name (không bị thay thế)
TEXT;
```

---

## Regex

```php
// Kiểm tra pattern
preg_match('/^\+84[0-9]{9}$/', '+84901234567'); // 1 (match)

// Lấy tất cả matches
preg_match_all('/\d+/', 'Price: 100000 VND, Qty: 2', $matches);
// $matches[0] = ['100000', '2']

// Thay thế
preg_replace('/\s+/', ' ', 'Hello    World'); // 'Hello World'

// Kiểm tra email hợp lệ
filter_var('user@example.com', FILTER_VALIDATE_EMAIL); // string nếu hợp lệ, false nếu không
```

---

## Liên kết

- [Array](./array.md)
- [DateTime](./datetime.md)
