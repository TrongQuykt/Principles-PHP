# Array — Xử Lý Mảng trong PHP

**Priority: 4** | Nhóm: [PHP Syntax](./README.md)

---

## Khai báo Array

```php
// Indexed array
$fruits = ['apple', 'banana', 'cherry'];

// Associative array (key => value)
$product = [
    'name'  => 'Sản phẩm Laravel',
    'price' => 35000,
    'stock' => 100,
];

// Multidimensional
$orders = [
    ['id' => 1, 'total' => 200000, 'status' => 'pending'],
    ['id' => 2, 'total' => 350000, 'status' => 'shipped'],
];
```

---

## Các hàm quan trọng nhất

### Lọc, Biến đổi, Kiểm tra

```php
$prices = [100000, 250000, 50000, 400000, 75000];

// array_filter — Lọc phần tử (giữ lại nếu callback trả true)
$expensive = array_filter($prices, fn($p) => $p >= 100000);
// [0 => 100000, 1 => 250000, 3 => 400000]

// array_map — Biến đổi từng phần tử
$formatted = array_map(fn($p) => number_format($p) . 'đ', $prices);

// array_reduce — Gộp về 1 giá trị
$total = array_reduce($prices, fn($carry, $item) => $carry + $item, 0);
// 875000

// in_array — Kiểm tra tồn tại
$exists = in_array(250000, $prices); // true

// array_key_exists / isset
$hasName = array_key_exists('name', $product); // true
```

### Sắp xếp

```php
// sort — sắp xếp giá trị (mất key)
sort($prices); // [50000, 75000, 100000, 250000, 400000]

// usort — sắp xếp tùy chỉnh
usort($orders, fn($a, $b) => $a['total'] <=> $b['total']); // ascending total

// ksort — sắp xếp theo key
// arsort — sắp xếp giảm dần, giữ key
```

### Kết hợp, Tách, Tìm kiếm

```php
// array_merge — Ghép mảng
$all = array_merge($arr1, $arr2);

// array_unique — Loại bỏ trùng lặp
$unique = array_unique([1, 2, 2, 3, 3, 4]); // [1, 2, 3, 4]

// array_slice — Cắt mảng
$page1 = array_slice($products, 0, 15); // 15 items đầu

// array_chunk — Chia mảng thành từng chunk
$chunks = array_chunk($products, 10); // Từng mảng 10 phần tử

// array_search — Tìm key theo value
$key = array_search('banana', $fruits); // 1

// array_column — Lấy 1 cột từ mảng đa chiều
$names = array_column($orders, 'status'); // ['pending', 'shipped']
$byId  = array_column($orders, null, 'id'); // index by 'id'
```

---

## Laravel Collection (Eloquent-aware)

Laravel Collection là wrapper của array với API fluent, mạnh hơn array functions:

```php
$products = Product::all(); // Trả về Collection

// Filtering
$inStock = $products->where('stock', '>', 0);
$active  = $products->filter(fn($p) => $p->is_active);

// Transformation
$names     = $products->pluck('name');
$byId      = $products->keyBy('id');
$nameAndId = $products->map(fn($p) => ['id' => $p->id, 'name' => $p->name]);

// Aggregation
$totalValue = $products->sum(fn($p) => $p->price * $p->stock);
$avgPrice   = $products->avg('price');
$maxPrice   = $products->max('price');

// Grouping
$byCategory = $products->groupBy('category_id');

// Chaining
$result = $products
    ->where('is_active', true)
    ->sortByDesc('price')
    ->take(10)
    ->map(fn($p) => ['name' => $p->name, 'price' => $p->price]);
```

---

## Liên kết

- [String](./string.md)
- [Eloquent ORM](../09-laravel-core/eloquent-orm.md)
