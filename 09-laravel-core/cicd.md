# CI/CD — Automated Deployment Pipeline

**Priority: 4** | Nhóm: [Laravel Core](./README.md)

---

## Định nghĩa

**CI/CD** (Continuous Integration / Continuous Deployment) là quy trình tự động hóa việc kiểm thử và triển khai phần mềm, giúp phát hiện lỗi sớm và giảm rủi ro khi release.

| Khái niệm | Mô tả |
|---|---|
| **CI** (Continuous Integration) | Tự động build và test mỗi khi có commit |
| **CD** (Continuous Delivery) | Tự động đẩy lên staging sau khi CI pass |
| **CD** (Continuous Deployment) | Tự động deploy lên production sau khi test đạt |

---

## GitHub Actions — Pipeline chuẩn

```yaml
# .github/workflows/ci.yml
name: Laravel CI

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest

    services:
      mysql:
        image: mysql:8.0
        env:
          MYSQL_DATABASE: test_db
          MYSQL_ROOT_PASSWORD: password
        ports:
          - 3306:3306
      redis:
        image: redis:alpine
        ports:
          - 6379:6379

    steps:
      - uses: actions/checkout@v4

      - name: Setup PHP 8.3
        uses: shivammathur/setup-php@v2
        with:
          php-version: '8.3'
          extensions: mbstring, pdo_mysql, redis
          coverage: xdebug

      - name: Install Composer dependencies
        run: composer install --no-progress --prefer-dist --optimize-autoloader

      - name: Setup environment
        run: |
          cp .env.testing .env
          php artisan key:generate

      - name: Run database migrations
        run: php artisan migrate --force

      - name: Run PHPUnit tests
        run: php artisan test --coverage --min=80

  lint:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Run PHP CS Fixer
        run: vendor/bin/php-cs-fixer fix --dry-run --diff

  deploy-staging:
    needs: [test, lint]
    if: github.ref == 'refs/heads/develop'
    runs-on: ubuntu-latest
    steps:
      - name: Deploy to staging
        uses: appleboy/ssh-action@v1
        with:
          host: ${{ secrets.STAGING_HOST }}
          username: ${{ secrets.STAGING_USER }}
          key: ${{ secrets.SSH_PRIVATE_KEY }}
          script: |
            cd /var/www/staging
            git pull origin develop
            composer install --no-dev --optimize-autoloader
            php artisan migrate --force
            php artisan config:cache
            php artisan route:cache
            php artisan view:cache
            php artisan queue:restart
            sudo systemctl reload php8.3-fpm
```

---

## Zero-Downtime Deployment

```bash
# Dùng Laravel Envoyer hoặc Deployer.php
# Cấu trúc thư mục zero-downtime:
/var/www/
    current -> releases/20240627_103045/   (symlink)
    releases/
        20240627_103045/   ← Release mới
        20240626_090000/   ← Release cũ (giữ lại để rollback)
    shared/
        .env               ← Shared config
        storage/           ← Shared storage
```

---

## Checklist Deployment Production

- [ ] `php artisan config:cache` — Cache config
- [ ] `php artisan route:cache` — Cache routes
- [ ] `php artisan view:cache` — Cache views
- [ ] `php artisan migrate --force` — Chạy migrations
- [ ] `php artisan queue:restart` — Restart workers
- [ ] `php artisan telescope:prune` — Dọn dẹp telescope (nếu dùng)
- [ ] Reload PHP-FPM
- [ ] Xoá CDN cache (nếu cần)

---

## .env Management

**Tuyệt đối không commit file `.env` vào Git.**

```bash
# .gitignore
.env
.env.*
!.env.example   ← Chỉ commit file example (không chứa credentials)
```

```dotenv
# .env.example — Template không chứa giá trị thật
APP_KEY=
DB_PASSWORD=
VNPAY_KEY=
MAIL_PASSWORD=
```

---

## Liên kết

- [Testing](./testing.md)
- [Security — No Hardcode Credentials](../11-database-design/security-standards.md)
