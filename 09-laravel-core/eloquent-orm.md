# Eloquent ORM — Object-Relational Mapping

**Priority: 3** | Nhóm: [Laravel Core](./README.md)

---

## Định nghĩa

**Eloquent** là ORM (Object-Relational Mapper) của Laravel, triển khai Active Record Pattern.
Mỗi bảng database có một Model tương ứng để tương tác với dữ liệu.

---

## Cấu trúc Model chuẩn

```php
namespace App\Models;

use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\SoftDeletes;
use Illuminate\Database\Eloquent\Factories\HasFactory;

class Product extends Model
{
    use HasFactory, SoftDeletes;

    // Cho phép mass-assignment (whitelist)
    protected $fillable = ['name', 'slug', 'base_price', 'stock', 'category_id', 'is_active'];

    // Không bao giờ expose ra ngoài (JSON)
    protected $hidden = ['deleted_at'];

    // Tự động ép kiểu
    protected $casts = [
        'base_price'  => 'float',
        'is_active'   => 'boolean',
        'specs'       => 'array',         // JSON column → PHP array
        'released_at' => 'datetime',
    ];

    // --- Relationships ---
    public function category(): BelongsTo
    {
        return $this->belongsTo(Category::class);
    }

    public function variants(): HasMany
    {
        return $this->hasMany(ProductVariant::class);
    }

    public function tags(): BelongsToMany
    {
        return $this->belongsToMany(Tag::class, 'product_tag');
    }

    // --- Scopes ---
    public function scopeActive(Builder $query): Builder
    {
        return $query->where('is_active', true)->where('stock', '>', 0);
    }

    public function scopeExpensive(Builder $query, float $minPrice = 500000): Builder
    {
        return $query->where('base_price', '>=', $minPrice);
    }

    // --- Accessors (Read) ---
    public function getFormattedPriceAttribute(): string
    {
        return number_format($this->base_price) . 'đ';
    }

    // --- Mutators (Write) ---
    public function setNameAttribute(string $value): void
    {
        $this->attributes['name'] = trim($value);
        $this->attributes['slug'] = Str::slug($value);
    }
}
```

---

## Query Builder — Các pattern phổ biến

```php
// Eager Loading (chống N+1)
Product::with(['category', 'variants', 'variants.media'])->active()->get();

// Chunk — xử lý tập dữ liệu lớn mà không tràn RAM
Product::where('is_active', true)->chunk(200, function ($products) {
    foreach ($products as $product) {
        ProcessProduct::dispatch($product);
    }
});

// Aggregate
$stats = Order::selectRaw('DATE(created_at) as date, COUNT(*) as total, SUM(grand_total) as revenue')
    ->whereBetween('created_at', [now()->subDays(30), now()])
    ->groupBy('date')
    ->orderBy('date')
    ->get();

// whereHas — lọc qua relationship
Product::whereHas('reviews', fn ($q) => $q->where('rating', '>=', 4))
       ->with('reviews')
       ->get();
```

---

## Migrations — Schema Management

```bash
php artisan make:migration create_products_table
php artisan make:migration add_discount_to_products_table
```

```php
public function up(): void
{
    Schema::create('products', function (Blueprint $table) {
        $table->id();
        $table->foreignId('category_id')->constrained()->cascadeOnDelete();
        $table->string('name', 255);
        $table->string('slug', 255)->unique();
        $table->text('description')->nullable();
        $table->unsignedBigInteger('base_price');          // Đơn vị: đồng (tránh float)
        $table->unsignedSmallInteger('discount_pct')->default(0);
        $table->unsignedInteger('stock')->default(0);
        $table->boolean('is_active')->default(true)->index();
        $table->json('specs')->nullable();
        $table->timestamps();
        $table->softDeletes();

        // Composite indexes cho query phổ biến
        $table->index(['category_id', 'is_active']);
        $table->index(['is_active', 'stock']);
    });
}

public function down(): void
{
    Schema::dropIfExists('products');
}
```

---

## Seeders & Factories

```bash
php artisan make:factory ProductFactory
php artisan make:seeder ProductSeeder
```

```php
// ProductFactory.php
class ProductFactory extends Factory
{
    public function definition(): array
    {
        return [
            'name'         => $this->faker->words(3, true),
            'base_price'   => $this->faker->numberBetween(50000, 5000000),
            'discount_pct' => $this->faker->randomElement([0, 0, 0, 5, 10, 15, 20]),
            'stock'        => $this->faker->numberBetween(0, 500),
            'is_active'    => $this->faker->boolean(90), // 90% active
        ];
    }

    public function outOfStock(): static
    {
        return $this->state(fn() => ['stock' => 0]);
    }
}

// Dùng trong Test
$product = Product::factory()->outOfStock()->create();
```

---

## Liên kết

- [Database Design](../11-database-design/README.md)
- [Testing](./testing.md)
- [Design Patterns — Repository](../02-design-patterns/README.md)
