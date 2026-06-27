# Decorator

**Priority: 2** | Nhóm: [Structural Patterns](../README.md)

---

## Định nghĩa

**Decorator** là pattern cho phép thêm behaviour mới vào một object tại runtime
bằng cách bọc nó trong một decorator object, mà không cần sửa class gốc.

Nguồn: [Wikipedia — Decorator Pattern](https://en.wikipedia.org/wiki/Decorator_pattern)

---

## Khi nào dùng

- Cần thêm chức năng vào object mà không muốn sửa class gốc (tuân thủ OCP)
- Cần kết hợp nhiều behaviour theo nhiều cách khác nhau
- Inheritance sẽ tạo ra quá nhiều subclass

---

## Ví dụ — Logger Decorator

```php
interface CacheInterface
{
    public function get(string $key): mixed;
    public function set(string $key, mixed $value, int $ttl = 3600): void;
}

class RedisCache implements CacheInterface
{
    public function get(string $key): mixed { /* fetch từ Redis */ }
    public function set(string $key, mixed $value, int $ttl = 3600): void { /* lưu vào Redis */ }
}

// Decorator: thêm logging mà không sửa RedisCache
class LoggingCacheDecorator implements CacheInterface
{
    public function __construct(private CacheInterface $cache) {}

    public function get(string $key): mixed
    {
        $value = $this->cache->get($key);
        $hit = $value !== null ? 'HIT' : 'MISS';
        Log::debug("Cache {$hit}: {$key}");
        return $value;
    }

    public function set(string $key, mixed $value, int $ttl = 3600): void
    {
        Log::debug("Cache SET: {$key} (TTL: {$ttl}s)");
        $this->cache->set($key, $value, $ttl);
    }
}

// Decorator: thêm metrics tracking
class MetricsCacheDecorator implements CacheInterface
{
    public function __construct(private CacheInterface $cache) {}

    public function get(string $key): mixed
    {
        $start = microtime(true);
        $value = $this->cache->get($key);
        Metrics::record('cache.get.duration', microtime(true) - $start);
        return $value;
    }

    public function set(string $key, mixed $value, int $ttl = 3600): void
    {
        $this->cache->set($key, $value, $ttl);
    }
}

// Kết hợp nhiều decorator
$cache = new MetricsCacheDecorator(
    new LoggingCacheDecorator(
        new RedisCache()
    )
);

// Tất cả code dùng CacheInterface — không cần biết đang dùng decorator nào
```

---

## Liên kết

- [SOLID — OCP](../../01-foundations/SOLID.md)
- [Adapter](./adapter.md)
