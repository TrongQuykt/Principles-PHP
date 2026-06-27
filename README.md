# Programming Principles

<div align="center">
    <img width="1000" height="480" alt="Gemini_Generated_Image_g75uh1g75uh1g75u" src="https://github.com/user-attachments/assets/9be93dd4-5389-4dde-94ee-44c15ba7d605" />
</div>
Dự án này là bộ Knowledge Base (KB) về các nguyên tắc lập trình, kiến trúc hệ thống và tiêu chuẩn phát triển website đạt chuẩn doanh nghiệp (Enterprise Grade) bằng PHP Laravel.
Được thiết kế để phục vụ cả **lập trình viên con người** và **AI Agent** khi xây dựng website.

---

## Mục tiêu

- Chuẩn hoá tư duy thiết kế và viết code trong tổ chức
- Cung cấp nền tảng kiến thức để đưa ra quyết định kỹ thuật nhất quán
- Làm tài liệu tham chiếu khi AI Agent thực thi nhiệm vụ lập trình

---

## Hệ thống Priority

Mỗi nhóm nguyên tắc được gắn một **Priority Level** (mức độ ưu tiên).

> **Quy tắc vàng**: Nguyên tắc có Priority số nhỏ hơn **luôn vượt trội** so với nguyên tắc có Priority số lớn hơn. Khi hai nguyên tắc xung đột nhau, hãy tuân theo nguyên tắc có mức ưu tiên cao hơn.

Ví dụ: SOLID (Priority 1) > Design Patterns (Priority 2). Nếu một Design Pattern khiến bạn vi phạm SOLID, hãy điều chỉnh hoặc bỏ Pattern đó.

Xem chi tiết tại [PRIORITY.md](./PRIORITY.md).

---

## Cấu trúc thư mục

```
Principles PHP/
├── README.md                    ← Bạn đang ở đây
├── PRIORITY.md                  ← Hệ thống phân cấp Priority
│
├── 00-meta/                     ← Tài liệu tham chiếu chung
│   └── glossary.md
│
├── 01-foundations/              ← Priority 1 | Nguyên tắc nền tảng (bắt buộc)
│   ├── README.md
│   ├── SOLID.md
│   ├── DRY.md
│   ├── KISS.md
│   ├── YAGNI.md
│   └── separation-of-concerns.md
│
├── 02-design-patterns/          ← Priority 2 | Design Patterns
│   ├── README.md
│   ├── creational/
│   │   ├── factory-method.md
│   │   ├── singleton.md
│   │   └── builder.md
│   ├── structural/
│   │   ├── adapter.md
│   │   ├── decorator.md
│   │   └── facade.md
│   └── behavioral/
│       ├── observer.md
│       ├── strategy.md
│       └── command.md
│
├── 03-architecture/             ← Priority 3 | Kiến trúc hệ thống
│   ├── README.md
│   ├── mvc.md
│   ├── layered-architecture.md
│   ├── clean-architecture.md
│   ├── microservices.md
│   └── api-design.md
│
├── 04-code-quality/             ← Priority 4 | Chất lượng code
│   ├── README.md
│   ├── naming-conventions.md
│   ├── code-review.md
│   ├── refactoring.md
│   ├── testing-principles.md
│   └── documentation.md
│
├── 05-security/                 ← Priority 5 | Bảo mật
│   ├── README.md
│   ├── owasp-top10.md
│   ├── authentication-authorization.md
│   └── input-validation.md
│
├── 06-performance/              ← Priority 6 | Hiệu năng
│   ├── README.md
│   ├── caching-strategies.md
│   ├── database-optimization.md
│   └── frontend-optimization.md
│
├── 07-karpathy-principles/      ← Priority 0 | Tư duy nền tảng cho AI Coding
│   ├── README.md
│   ├── think-before-coding.md
│   ├── simplicity-first.md
│   ├── surgical-changes.md
│   └── goal-driven-execution.md
│
└── 08-features/                 ← Priority 2 | Thiết kế tính năng
    ├── README.md
    ├── vertical-slices.md
    ├── modularity.md
    └── feature-flags.md

09-laravel-core/             ← Priority 3 | Kiến trúc cốt lõi Laravel
    ├── README.md
    ├── service-container.md
    ├── request-lifecycle.md
    ├── service-providers.md
    ├── middleware.md
    ├── eloquent-orm.md
    ├── queue-jobs.md
    ├── events-listeners.md
    ├── blade-templates.md
    ├── testing.md
    └── cicd.md

10-php-syntax/               ← Priority 4 | Cú pháp & Kiểu dữ liệu PHP
    ├── README.md
    ├── array.md
    ├── string.md
    ├── datetime.md
    ├── oop.md
    └── composer.md

11-database-design/          ← Priority 1 | Thiết kế Database chuẩn
    ├── README.md
    ├── schema-standards.md
    ├── indexing.md
    ├── security-standards.md
    └── normalization.md

12-auth-session/             ← Priority 5 | Authentication & Session
    ├── README.md
    ├── authentication.md
    ├── jwt.md
    └── session.md

13-artisan-commands/         ← Priority 4 | Tổng hợp lệnh Artisan
    └── README.md
```

---

## Quy ước tài liệu

| Quy tắc | Mô tả |
|---|---|
| Thuật ngữ IT | Giữ nguyên tiếng Anh |
| Diễn giải | Viết bằng tiếng Việt |
| Không dùng icon | Chỉ dùng heading, list, table, code block |
| Code example | Luôn có ví dụ sai và ví dụ đúng |

---

## Thứ tự đọc khuyến nghị

1. [PRIORITY.md](./PRIORITY.md) — Hiểu hệ thống phân cấp
2. [07-karpathy-principles/](./07-karpathy-principles/README.md) — Tư duy nền tảng
3. [01-foundations/](./01-foundations/README.md) — Nguyên tắc cốt lõi
4. [11-database-design/](./11-database-design/README.md) — Thiết kế Database
5. [09-laravel-core/](./09-laravel-core/README.md) — Kiến trúc Laravel
6. [08-features/](./08-features/README.md) — Thiết kế tính năng
7. [02-design-patterns/](./02-design-patterns/README.md) — Design Patterns
8. [03-architecture/](./03-architecture/README.md) — Kiến trúc hệ thống
9. [10-php-syntax/](./10-php-syntax/README.md) — PHP Syntax Reference
10. [04-code-quality/](./04-code-quality/README.md) — Chất lượng code
11. [12-auth-session/](./12-auth-session/README.md) — Authentication & Session
12. [05-security/](./05-security/README.md) — Bảo mật
13. [06-performance/](./06-performance/README.md) — Hiệu năng
14. [13-artisan-commands/](./13-artisan-commands/README.md) — Artisan Commands

---

## Nguồn tham khảo

- Artima — [The Principles of Good Programming](https://www.artima.com/weblogs/viewpost.jsp?thread=331531) (Christopher Diggins)
- [Andrej Karpathy's LLM Coding Pitfalls](https://github.com/multica-ai/andrej-karpathy-skills)
- Wikipedia — [SOLID](https://en.wikipedia.org/wiki/SOLID), [DRY](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself), [KISS](https://en.wikipedia.org/wiki/KISS_principle), [YAGNI](https://en.wikipedia.org/wiki/YAGNI)
- [Gang of Four Design Patterns](https://en.wikipedia.org/wiki/Design_Patterns)
- [The Clean Architecture](https://blog.cleancoder.com/uncle-bob/2012/08/13/the-clean-architecture.html)
