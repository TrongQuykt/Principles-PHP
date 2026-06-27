# Blade Templates — Template Engine

**Priority: 4** | Nhóm: [Laravel Core](./README.md)

---

## Cú pháp cơ bản

```blade
{{-- Comment (không render ra HTML) --}}

{{-- Output an toàn (auto HTML-escaped, chống XSS) --}}
{{ $product->name }}

{{-- Output RAW HTML (chỉ dùng cho nội dung đáng tin cậy từ admin) --}}
{!! $product->description_html !!}

{{-- Biểu thức điều kiện --}}
@if ($product->stock > 0)
    <span>Còn hàng</span>
@elseif ($product->stock === 0)
    <span>Hết hàng</span>
@else
    <span>Ngừng kinh doanh</span>
@endif

{{-- Loop --}}
@foreach ($products as $product)
    <div>{{ $loop->iteration }}. {{ $product->name }}</div>
@endforeach

@forelse ($products as $product)
    <div>{{ $product->name }}</div>
@empty
    <p>Không có sản phẩm nào.</p>
@endforelse
```

---

## Layout & Components

```blade
{{-- resources/views/layouts/app.blade.php --}}
<!DOCTYPE html>
<html>
<head>
    <title>@yield('title', 'Trang chủ') - My Shop</title>
    @stack('styles')
</head>
<body>
    @include('partials.nav')

    <main>
        @yield('content')
    </main>

    @stack('scripts')
</body>
</html>

{{-- resources/views/products/index.blade.php --}}
@extends('layouts.app')

@section('title', 'Danh sách sản phẩm')

@section('content')
    <x-product-grid :products="$products" />
@endsection

@push('scripts')
    <script src="{{ asset('js/product-filter.js') }}"></script>
@endpush
```

---

## Anonymous Components

```bash
# resources/views/components/product-card.blade.php
```

```blade
{{-- resources/views/components/product-card.blade.php --}}
@props(['product', 'showActions' => true])

<div class="product-card">
    <h3>{{ $product->name }}</h3>
    <p>{{ $product->formatted_price }}</p>

    @if($showActions)
        <button>Thêm vào giỏ</button>
    @endif
</div>

{{-- Sử dụng --}}
<x-product-card :product="$product" />
<x-product-card :product="$product" :show-actions="false" />
```

---

## Class Components

```bash
php artisan make:component Alert
```

```php
// app/View/Components/Alert.php
class Alert extends Component
{
    public function __construct(
        public string $type    = 'info',
        public string $message = ''
    ) {}

    public function render(): View
    {
        return view('components.alert');
    }
}
```

---

## Directives hay dùng

| Directive | Mục đích |
|---|---|
| `@auth` / `@guest` | Kiểm tra trạng thái đăng nhập |
| `@can('action', $model)` | Kiểm tra authorization |
| `@csrf` | Tạo CSRF token field |
| `@method('PUT')` | Method spoofing cho form |
| `@error('field')` | Hiển thị lỗi validation |
| `@vite(['app.css', 'app.js'])` | Tích hợp Vite assets |

---

## Liên kết

- [MVC Architecture](../03-architecture/mvc.md)
- [Input Validation](../05-security/input-validation.md)
