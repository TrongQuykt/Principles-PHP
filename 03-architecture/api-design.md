# API Design

**Priority: 3** | Nhóm: [Architecture](./README.md)

---

## Định nghĩa

Các nguyên tắc thiết kế **RESTful API** đảm bảo API nhất quán, dễ sử dụng, và có khả năng mở rộng.

---

## RESTful Conventions

### HTTP Methods

| Method | Hành động | Ví dụ |
|---|---|---|
| GET | Đọc dữ liệu | `GET /api/v1/products` |
| POST | Tạo mới | `POST /api/v1/products` |
| PUT | Cập nhật toàn bộ | `PUT /api/v1/products/{id}` |
| PATCH | Cập nhật một phần | `PATCH /api/v1/products/{id}` |
| DELETE | Xoá | `DELETE /api/v1/products/{id}` |

### URL Naming Rules

| Quy tắc | Đúng | Sai |
|---|---|---|
| Dùng danh từ số nhiều | `/products` | `/getProducts`, `/product` |
| Lowercase, dấu gạch ngang | `/order-items` | `/orderItems`, `/OrderItems` |
| Đặt version | `/api/v1/products` | `/api/products` |
| Resource lồng nhau | `/orders/{id}/items` | `/getOrderItems?orderId=1` |
| Không dùng verb trong URL | `/products/{id}/publish` (ngoại lệ: action) | `/publishProduct/{id}` |

---

## Response Format Chuẩn

Mọi response đều theo cấu trúc nhất quán:

```json
{
    "status": "success",
    "message": "Đặt hàng thành công",
    "data": {
        "id": 123,
        "total": 450000
    }
}
```

```json
{
    "status": "error",
    "message": "Validation failed",
    "errors": {
        "product_id": ["Sản phẩm không tồn tại"],
        "quantity": ["Số lượng phải lớn hơn 0"]
    }
}
```

---

## HTTP Status Codes

| Code | Ý nghĩa | Khi nào dùng |
|---|---|---|
| 200 | OK | GET thành công, PUT/PATCH thành công |
| 201 | Created | POST tạo resource mới thành công |
| 204 | No Content | DELETE thành công |
| 400 | Bad Request | Request không hợp lệ về cú pháp |
| 401 | Unauthorized | Chưa xác thực |
| 403 | Forbidden | Đã xác thực nhưng không có quyền |
| 404 | Not Found | Resource không tồn tại |
| 422 | Unprocessable Entity | Validation fails |
| 429 | Too Many Requests | Rate limit exceeded |
| 500 | Internal Server Error | Lỗi server không mong đợi |

---

## Pagination

```json
{
    "status": "success",
    "data": [...],
    "meta": {
        "current_page": 1,
        "per_page": 15,
        "total": 150,
        "last_page": 10
    },
    "links": {
        "first": "/api/v1/products?page=1",
        "last": "/api/v1/products?page=10",
        "prev": null,
        "next": "/api/v1/products?page=2"
    }
}
```

---

## Versioning

Luôn đặt **version** trong URL để hỗ trợ backward compatibility:

```
/api/v1/products    <- Phiên bản hiện tại
/api/v2/products    <- Phiên bản mới (khi có breaking change)
```

Giữ `v1` hoạt động song song với `v2` trong thời gian chuyển đổi.

---

## Liên kết

- [Authentication & Authorization](../05-security/authentication-authorization.md)
- [Input Validation](../05-security/input-validation.md)
