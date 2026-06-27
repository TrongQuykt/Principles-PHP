# Laravel Core — Kiến Trúc Cốt Lõi của Laravel

**Priority: 3 (Song song với Architecture)**

---

## Tổng quan

Hiểu kiến trúc cốt lõi của Laravel là nền tảng để xây dựng ứng dụng enterprise đúng cách.
Laravel không chỉ là framework — nó là một hệ sinh thái được thiết kế theo các nguyên tắc SOLID và Inversion of Control.

---

## Danh sách tài liệu

| Chủ đề | File | Mô tả |
|---|---|---|
| Service Container | [service-container.md](./service-container.md) | IoC Container — trái tim của Laravel |
| Service Providers | [service-providers.md](./service-providers.md) | Điểm đăng ký toàn bộ dependencies |
| Request Lifecycle | [request-lifecycle.md](./request-lifecycle.md) | Vòng đời một HTTP request |
| Middleware | [middleware.md](./middleware.md) | Pipeline xử lý request và response |
| Eloquent ORM | [eloquent-orm.md](./eloquent-orm.md) | Active Record pattern trong Laravel |
| Blade Templates | [blade-templates.md](./blade-templates.md) | Template engine của Laravel |
| Queue & Jobs | [queue-jobs.md](./queue-jobs.md) | Xử lý tác vụ bất đồng bộ |
| Events & Listeners | [events-listeners.md](./events-listeners.md) | Observer Pattern trong Laravel |
| Testing | [testing.md](./testing.md) | Unit và Feature Testing |
| CI/CD | [cicd.md](./cicd.md) | Automated Deployment pipeline |

---

## Vị trí trong Priority System

| Chủ đề | Priority gốc |
|---|---|
| Service Container, DI | Priority 1 (Foundations — DIP) |
| Eloquent, Database | Priority 3 (Architecture) |
| Queue, Events | Priority 2 (Design Patterns — Observer, Command) |
| Testing, CI/CD | Priority 4 (Code Quality) |
| Security middleware | Priority 5 (Security) |

---

## Liên kết với các Principles

- [SOLID — Dependency Inversion](../01-foundations/SOLID.md)
- [Design Patterns — Observer](../02-design-patterns/behavioral/observer.md)
- [Architecture — Layered](../03-architecture/layered-architecture.md)
