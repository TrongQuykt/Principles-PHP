# Vertical Slices — Lát Cắt Dọc

**Priority: 2** | Nhóm: [Features](./README.md)

---

## Định nghĩa

**Vertical Slice Architecture** là cách tổ chức code mà trong đó mỗi folder đại diện cho một tính năng (feature), chứa toàn bộ các thành phần cần thiết để thực thi tính năng đó (từ UI, Controller đến Data Access).

Thay vì chia theo "Ngang" (Layers: Controllers, Services, Models), chúng ta chia theo "Dọc" (Features: Checkout, Login, Search).

---

## So sánh cấu trúc

### Cách truyền thống (Horizontal Layers)
Dễ dẫn đến việc phải mở 5-6 folder khác nhau chỉ để sửa một chức năng.

```
app/
├── Controllers/
│   ├── OrderController.php
├── Models/
│   ├── Order.php
├── Services/
│   ├── OrderService.php
```

### Cách hiện đại (Vertical Slices)
Mọi thứ liên quan nằm cạnh nhau.

```
app/
├── Features/
│   ├── Checkout/
│   │   ├── CheckoutController.php
│   │   ├── CheckoutService.php
│   │   ├── CheckoutRequest.php
│   │   └── Order.php (hoặc tham chiếu)
```

---

## Lợi ích

| Lợi ích | Giải thích |
|---|---|
| **High Cohesion** | Code thay đổi cùng nhau thì nằm cạnh nhau |
| **Low Coupling** | Các tính năng ít phụ thuộc trực tiếp vào code nội bộ của nhau |
| **Surgical Changes** | Dễ dàng thực hiện các thay đổi chính xác mà không ảnh hưởng diện rộng |
| **Scalability** | Team mới có thể join và làm việc trên một folder duy nhất |

---

## Quy tắc áp dụng

1. **Shared code**: Các thành phần dùng chung (như User model, Database connection) vẫn được để ở folder `Shared` hoặc `Common`.
2. **Communication**: Các feature giao tiếp với nhau qua `Interfaces` hoặc `Events`, không gọi trực tiếp vào logic nội bộ của nhau.
3. **Delete-ability**: Nếu một tính năng không còn cần thiết, bạn có thể xoá toàn bộ folder của nó mà không gây lỗi compile ở các phần khác.

---

## Liên kết

- [Separation of Concerns](../01-foundations/separation-of-concerns.md)
- [Modularity](./modularity.md)
