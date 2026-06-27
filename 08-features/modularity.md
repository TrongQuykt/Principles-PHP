# Modularity — Tính Module Hoá

**Priority: 2** | Nhóm: [Features](./README.md)

---

## Định nghĩa

**Modularity** là khả năng chia hệ thống thành các module độc lập. Mỗi module có thể được thiết kế, thực thi và kiểm tra riêng biệt trước khi tích hợp vào hệ thống lớn.

Nguồn: [Wikipedia — Modularity](https://en.wikipedia.org/wiki/Modularity)

---

## Hai chỉ số quan trọng

Để đánh giá một module tốt, chúng ta dùng hai khái niệm:

### 1. High Cohesion (Độ kết dính cao)
Các phần tử bên trong một module phải có liên quan mật thiết đến nhau và phục vụ một mục đích duy nhất.
> "Thứ gì thay đổi cùng nhau, hãy để chúng cạnh nhau."

### 2. Low Coupling (Độ phụ thuộc thấp)
Sự phụ thuộc giữa các module phải là tối thiểu. Module A không nên biết quá sâu về cách Module B hoạt động.
> "Biết ít về nhau nhất có thể."

---

## Nguyên tắc thiết kế Module

| Nguyên tắc | Mô tả |
|---|---|
| **Encapsulation** | Ẩn chi tiết bên trong, chỉ lộ diện (expose) interface cần thiết. |
| **Interface-based** | Giao tiếp qua interface, không qua implementation cụ thể. |
| **Independence** | Module có thể hoạt động (hoặc được test) mà không cần toàn bộ hệ thống. |
| **Single Responsibility** | Mỗi module chỉ giải quyết một miền vấn đề (domain). |

---

## Ví dụ về Module trong Web

Trong Next.js (theo chuẩn Next-item):

```
src/
├── features/
│   ├── auth/           <-- Module Auth
│   │   ├── components/
│   │   ├── hooks/
│   │   ├── api/
│   │   └── index.ts    <-- Public API của module
│   ├── cart/           <-- Module Cart
│   └── catalog/        <-- Module Catalog
```

File `index.ts` đóng vai trò là **Gatekeeper**. Các module khác chỉ được phép import những gì được export ở file này.

---

## Liên kết

- [Separation of Concerns](../01-foundations/separation-of-concerns.md)
- [Encapsulation (Wikipedia)](https://en.wikipedia.org/wiki/Encapsulation_(computer_programming))
