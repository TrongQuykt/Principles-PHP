# Design Patterns — Các Mẫu Thiết Kế

**Priority: 2**

---

## Tổng quan

**Design Patterns** là các giải pháp tái sử dụng đã được kiểm chứng cho các vấn đề thiết kế
phổ biến trong lập trình hướng đối tượng. Chúng không phải là code sẵn sàng dùng,
mà là **template** có thể tùy chỉnh cho từng tình huống cụ thể.

Nguồn: [Wikipedia — Design Patterns (Gang of Four)](https://en.wikipedia.org/wiki/Design_Patterns)

> **Quan trọng**: Design Patterns phải phục vụ cho code, không phải ngược lại.
> Áp dụng pattern khi nó giải quyết vấn đề thực, không phải để "code trông chuyên nghiệp hơn".

---

## Ba nhóm chính

| Nhóm | Mục đích | Patterns trong tài liệu này |
|---|---|---|
| Creational | Tạo đối tượng linh hoạt | Factory Method, Singleton, Builder |
| Structural | Tổ chức class và object | Adapter, Decorator, Facade |
| Behavioral | Giao tiếp giữa object | Observer, Strategy, Command |

---

## Creational Patterns

| Pattern | File | Khi nào dùng |
|---|---|---|
| Factory Method | [factory-method.md](./creational/factory-method.md) | Khi cần tạo object mà không cần biết class cụ thể |
| Singleton | [singleton.md](./creational/singleton.md) | Khi cần đảm bảo chỉ có 1 instance (dùng thận trọng) |
| Builder | [builder.md](./creational/builder.md) | Khi constructor phức tạp với nhiều tham số tuỳ chọn |

---

## Structural Patterns

| Pattern | File | Khi nào dùng |
|---|---|---|
| Adapter | [adapter.md](./structural/adapter.md) | Khi cần tích hợp interface không tương thích |
| Decorator | [decorator.md](./structural/decorator.md) | Khi cần thêm behaviour mà không sửa class gốc |
| Facade | [facade.md](./structural/facade.md) | Khi cần đơn giản hoá interface của một subsystem phức tạp |

---

## Behavioral Patterns

| Pattern | File | Khi nào dùng |
|---|---|---|
| Observer | [observer.md](./behavioral/observer.md) | Khi nhiều object cần phản ứng với một sự kiện |
| Strategy | [strategy.md](./behavioral/strategy.md) | Khi có nhiều thuật toán có thể thay thế nhau |
| Command | [command.md](./behavioral/command.md) | Khi cần đóng gói request thành object (undo, queue) |

---

## Nguyên tắc khi áp dụng Design Patterns

1. **Nhận diện vấn đề trước**, không chọn pattern trước
2. **Xem xét SOLID** — pattern nào vi phạm nguyên tắc nền tảng thì không dùng
3. **Đọc code ra to** — nếu khó giải thích tại sao dùng pattern đó, có thể không cần
4. **Singleton là anti-pattern** trong nhiều trường hợp — xem [singleton.md](./creational/singleton.md)
