# Goal-Driven Execution

**Priority: 0** | Nhóm: [Karpathy Principles](./README.md)

---

## Định nghĩa

Trước khi bắt đầu thực thi, hãy **định nghĩa rõ tiêu chí thành công**.
Chuyển đổi một yêu cầu mơ hồ thành một mục tiêu có thể verify được.

> "Strong success criteria let you loop independently.
> Weak criteria ('make it work') require constant clarification."
>
> — Karpathy Principles

---

## Vấn đề cần giải quyết

Các yêu cầu mơ hồ dẫn đến:
- Không biết khi nào thì "xong"
- Liên tục hỏi lại sau mỗi bước nhỏ
- Deliver kết quả không đúng kỳ vọng
- Không thể tự kiểm tra trước khi giao

---

## Cấu trúc Goal-Driven Execution

Với mỗi task có nhiều bước, hãy lập một execution plan có **verify checkpoint**:

```
Execution Plan:

Bước 1: [Mô tả hành động]
   Verify: [Điều kiện có thể kiểm tra để xác nhận bước 1 thành công]

Bước 2: [Mô tả hành động]
   Verify: [Điều kiện có thể kiểm tra để xác nhận bước 2 thành công]

Bước 3: [Mô tả hành động]
   Verify: [Điều kiện có thể kiểm tra để xác nhận bước 3 thành công]

Done khi: [Điều kiện tổng thể xác nhận toàn bộ task hoàn thành]
```

---

## Tiêu chí thành công tốt vs xấu

| Tiêu chí xấu | Tiêu chí tốt |
|---|---|
| "Làm cho nó hoạt động" | "API trả về 200 với đúng JSON schema" |
| "Sửa bug đăng nhập" | "User nhập đúng email/password → redirect về dashboard, sai → hiện lỗi cụ thể" |
| "Thêm tính năng search" | "Gõ tên sản phẩm → kết quả xuất hiện trong < 500ms, không có kết quả → hiện empty state" |
| "Cải thiện performance" | "Thời gian load trang chủ < 2s trên connection 4G" |

---

## Ví dụ áp dụng

### Yêu cầu: "Thêm chức năng đặt hàng"

**Kém — Không có goal rõ ràng:**

```
Bước 1: Tạo OrderController
Bước 2: Viết OrderService
Bước 3: Tạo migration
Bước 4: Làm UI
```

**Tốt — Có verify checkpoint:**

```
Execution Plan: Chức năng đặt hàng

Bước 1: Tạo migration bảng orders và order_items
   Verify: php artisan migrate chạy không có lỗi, 2 bảng tồn tại trong DB

Bước 2: Tạo OrderService với method createOrder()
   Verify: Unit test createOrder() với valid payload → record được tạo đúng,
           invalid payload → Exception với message rõ ràng

Bước 3: Tạo POST /api/v1/orders endpoint
   Verify: Postman request với valid body → 201 response + order data,
           missing field → 422 với validation errors,
           unauthenticated → 401

Bước 4: Tạo UI form đặt hàng
   Verify: User điền form → submit → thấy màn hình confirmation,
           lỗi validation → hiện message cụ thể dưới từng field

Done khi: User có thể hoàn thành luồng đặt hàng từ đầu đến cuối
          mà không gặp lỗi, và dữ liệu được lưu đúng vào DB.
```

---

## Áp dụng cho AI Agent

Khi AI Agent nhận một task phức tạp, bắt buộc:

1. **Phát biểu Goal**: "Mục tiêu của task này là..."
2. **Liệt kê bước với verify**: Mỗi bước có tiêu chí verify cụ thể
3. **Tự kiểm tra trước khi báo cáo**: Chạy lại verify của từng bước
4. **Báo cáo dựa trên evidence**: "Bước X thành công vì [kết quả cụ thể]"

---

## Mối quan hệ với Think Before Coding

| Think Before Coding | Goal-Driven Execution |
|---|---|
| Làm rõ requirement *trước* khi bắt đầu | Định nghĩa done-criteria *trước* khi thực thi |
| Xử lý ambiguity ở tầng hiểu vấn đề | Xử lý ambiguity ở tầng thực thi |
| "Tôi hiểu đúng chưa?" | "Xong nghĩa là gì?" |

Cả hai cùng nhau tạo nên một vòng lặp hoàn chỉnh: **Hiểu đúng → Làm đúng → Verify đúng**.

---

## Checklist — Goal-Driven Execution

- [ ] Tôi đã viết ra tiêu chí thành công trước khi bắt đầu code
- [ ] Mỗi bước lớn có một verify checkpoint cụ thể
- [ ] Tôi có thể kiểm tra verify checkpoint mà không cần hỏi người khác
- [ ] Tôi đã tự kiểm tra tất cả bước trước khi báo cáo hoàn thành
- [ ] Done criteria được viết bằng điều kiện có thể đo lường

---

## Liên kết

- [Think Before Coding](./think-before-coding.md)
- [Surgical Changes](./surgical-changes.md)
- [Testing Principles](../04-code-quality/testing-principles.md)
