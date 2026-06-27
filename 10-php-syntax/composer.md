# Composer — Quản Lý Dependencies

**Priority: 4** | Nhóm: [PHP Syntax](./README.md)

---

## Định nghĩa

**Composer** là trình quản lý gói (package manager) cho PHP. Nó quản lý các thư viện bên thứ 3 mà project phụ thuộc vào, tương tự như npm (Node.js) hay pip (Python).

---

## Các lệnh quan trọng

```bash
# Cài đặt toàn bộ dependencies từ composer.json
composer install

# Production — bỏ dev-dependencies, tối ưu autoloader
composer install --no-dev --optimize-autoloader

# Thêm package mới
composer require laravel/sanctum
composer require spatie/laravel-permission

# Thêm dev package (chỉ dùng khi develop)
composer require --dev pestphp/pest
composer require --dev barryvdh/laravel-debugbar

# Cập nhật package
composer update                         # Tất cả (theo constraints)
composer update laravel/framework       # 1 package cụ thể

# Xoá package
composer remove package/name

# Tối ưu autoloader (chạy khi deploy production)
composer dump-autoload --optimize

# Kiểm tra security vulnerabilities
composer audit

# Xem thông tin package
composer show laravel/framework
composer outdated   # Danh sách packages có phiên bản mới
```

---

## composer.json — Cấu trúc chuẩn

```json
{
    "name": "company/project-name",
    "description": "Enterprise Web Application",
    "type": "project",
    "require": {
        "php": "^8.3",
        "laravel/framework": "^11.0",
        "laravel/sanctum": "^4.0",
        "spatie/laravel-permission": "^6.0"
    },
    "require-dev": {
        "pestphp/pest": "^3.0",
        "barryvdh/laravel-debugbar": "^3.0",
        "phpstan/phpstan": "^1.10"
    },
    "autoload": {
        "psr-4": {
            "App\\": "app/",
            "Database\\Factories\\": "database/factories/",
            "Database\\Seeders\\": "database/seeders/"
        }
    },
    "scripts": {
        "test": "php artisan test",
        "analyse": "phpstan analyse --memory-limit=2G",
        "format": "php-cs-fixer fix"
    },
    "config": {
        "preferred-install": "dist",
        "sort-packages": true,
        "optimize-autoloader": true
    }
}
```

---

## Version Constraints

| Ký hiệu | Ý nghĩa | Ví dụ |
|---|---|---|
| `^1.2.0` | >= 1.2.0 và < 2.0.0 (minor và patch) | `^11.0` |
| `~1.2.0` | >= 1.2.0 và < 1.3.0 (patch only) | `~11.5` |
| `1.2.*` | Bất kỳ patch nào của 1.2 | `1.2.*` |
| `>=1.0` | Từ 1.0 trở lên | `>=8.1` |
| `*` | Bất kỳ version nào | Tránh dùng |

---

## composer.lock — Không được ignore

File `composer.lock` ghi lại **chính xác** phiên bản của mỗi dependency.

```bash
# .gitignore — KHÔNG exclude composer.lock trong application projects
# Phải commit composer.lock để đảm bảo tất cả member và CI/CD dùng cùng versions
git add composer.lock
```

---

## Liên kết

- [CI/CD](../09-laravel-core/cicd.md)
- [Artisan Commands](../13-artisan-commands/README.md)
