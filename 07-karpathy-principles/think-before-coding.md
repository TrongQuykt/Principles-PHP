# Think Before Coding

**Priority: 0** | Nhóm: [Karpathy Principles](./README.md)

---

## Định nghĩa

Trước khi viết bất kỳ dòng code nào, hãy đảm bảo bạn hiểu **chính xác** vấn đề cần giải quyết.
Đừng để sự mơ hồ bị che khuất bởi sự tự tin giả tạo.

Nguyên tắc này yêu cầu: **đặt câu hỏi rõ ràng thay vì đưa ra giả định ngầm**.

---

## Vấn đề cần giải quyết

LLM và lập trình viên đều có xu hướng:
- Chọn một cách hiểu requirement mà không nói ra
- Bắt tay vào code khi chưa làm rõ ambiguity
- Không thể hiện các tradeoff trước khi đưa ra quyết định
- Không phản biện khi có giải pháp đơn giản hơn

---

## Các hành vi bắt buộc

### 1. Phát biểu assumption rõ ràng

Khi có sự mơ hồ trong requirement, **đừng ngầm chọn một hướng**. Hãy nêu rõ:

```
Tôi hiểu yêu cầu này theo 2 cách:
A) Lọc sản phẩm theo category ở phía client (không cần API mới)
B) Lọc ở phía server với endpoint riêng (cần thêm route)

Tôi sẽ thực hiện theo cách A — đúng không?
```

### 2. Dừng lại khi confused

Nếu requirement mâu thuẫn với code hiện tại hoặc với nhau, hãy **đặt tên rõ điểm mờ**:

```
// Tôi nhận thấy xung đột:
// - Requirement nói "user không cần login"
// - Nhưng endpoint /api/orders đang require auth middleware
// Cần làm rõ trước khi tiếp tục.
```

Đừng tự ý giải quyết xung đột bằng cách chọn một phía.

### 3. Surface tradeoffs

Trước khi implement, nếu có nhiều hướng tiếp cận với tradeoff khác nhau, hãy trình bày:

| Phương án | Ưu điểm | Nhược điểm |
|---|---|---|
| Dùng Redis Cache | Tốc độ đọc rất nhanh | Thêm dependency, cần invalidation |
| Cache ở database layer | Không thêm dependency | Chậm hơn Redis |

### 4. Push back khi cần

Nếu requirement dẫn đến giải pháp quá phức tạp, hãy đề xuất hướng đơn giản hơn:

```
Yêu cầu X có thể giải quyết theo cách đơn giản hơn là Y,
không cần đến Z. Bạn có muốn xem xét không?
```

---

## Ví dụ áp dụng

### Sai — Giả định ngầm

Request: "Thêm filter sản phẩm theo màu sắc"

```php
// Developer tự chọn: filter bằng query string ?color=red
// Không hỏi xem filter này có cần persist trên URL hay không
// Không hỏi xem có cần multi-select hay không
// Viết code ngay → sửa 3 lần sau đó
```

### Đúng — Làm rõ trước

```
Trước khi implement, tôi cần xác nhận:
1. Filter có hỗ trợ multi-select không? (ví dụ: chọn cả đỏ và xanh)
2. Trạng thái filter có cần lưu trên URL (shareable link) không?
3. Filter được áp dụng ngay (real-time) hay cần nhấn nút "Áp dụng"?
```

---

## Checklist — Think Before Coding

- [ ] Tôi đã đọc lại requirement ít nhất 2 lần
- [ ] Tôi đã xác định rõ đâu là input, đâu là expected output
- [ ] Tôi đã liệt kê tất cả assumption và xác nhận chúng
- [ ] Tôi đã xem xét ít nhất 2 cách tiếp cận và hiểu tradeoff
- [ ] Tôi đã kiểm tra code liên quan không có contradiction

---

## Liên kết

- [Simplicity First](./simplicity-first.md)
- [Goal-Driven Execution](./goal-driven-execution.md)
- [KISS](../01-foundations/KISS.md)
