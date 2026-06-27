# Features — Thiết Kế Theo Tính Năng

**Priority: 2 (Song song với Design Patterns)**

---

## Tổng quan

Thay vì tổ chức code theo "Loại file" (Controllers, Models, Views), chúng ta ưu tiên tổ chức code theo **"Tính năng"** (Features) hoặc **"Miền nghiệp vụ"** (Domains).

Cách tiếp cận này giúp:
- Dễ dàng tìm kiếm code liên quan đến một tính năng
- Giảm thiểu xung đột khi nhiều người cùng làm các tính năng khác nhau
- Dễ dàng đóng gói và tái sử dụng (hoặc xoá bỏ) một tính năng

---

## Danh sách tài liệu

| Chủ đề | File | Mô tả |
|---|---|---|
| Vertical Slices | [vertical-slices.md](./vertical-slices.md) | Tổ chức code theo lát cắt dọc |
| Modularity | [modularity.md](./modularity.md) | Tính module hoá trong kiến trúc |
| Feature Flags | [feature-flags.md](./feature-flags.md) | Quản lý việc bật/tắt tính năng tại runtime |

---

## Tầm quan trọng của Feature-Based Structure

Trong các hệ thống lớn, việc gom tất cả Controller vào một folder và tất cả Model vào một folder khác sẽ tạo ra "folder địa ngục" (folder hell) khi số lượng file lên tới hàng trăm.

Oganizing theo Feature giúp hệ thống trở nên **cohesive** hơn (các file liên quan nằm cạnh nhau).

---

## Liên kết

- [Separation of Concerns](../01-foundations/separation-of-concerns.md)
- [Clean Architecture](../03-architecture/clean-architecture.md)
- [Naming Conventions](../04-code-quality/naming-conventions.md)
