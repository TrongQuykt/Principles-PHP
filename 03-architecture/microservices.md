# Microservices

**Priority: 3** | Nhóm: [Architecture](./README.md)

---

## Định nghĩa

**Microservices** là phong cách kiến trúc cấu trúc ứng dụng thành một tập hợp các service nhỏ,
triển khai độc lập, giao tiếp qua network (API hoặc message broker).

Nguồn: [Wikipedia — Microservices](https://en.wikipedia.org/wiki/Microservices)

---

## Monolith vs Microservices

| Tiêu chí | Monolith | Microservices |
|---|---|---|
| Deployment | Một đơn vị duy nhất | Từng service độc lập |
| Team size | Phù hợp team nhỏ | Phù hợp nhiều team |
| Complexity | Thấp hơn | Cao hơn (network, distributed state) |
| Scale | Scale toàn bộ | Scale từng service |
| Phù hợp khi | Startup, MVP, team < 10 | Hệ thống lớn, team nhiều |

---

## Khi nào chuyển sang Microservices

Không nên bắt đầu với Microservices. Chỉ xem xét khi:

1. Monolith đã được build và hoạt động tốt
2. Có bottleneck rõ ràng ở một tính năng cụ thể
3. Có team đủ lớn để maintain nhiều service
4. Có infrastructure hỗ trợ (container orchestration, service discovery)

> "Don't start with microservices. Start with a monolith, identify the seams, extract services."
> — Martin Fowler

---

## Nguyên tắc khi áp dụng Microservices

### 1. Single Responsibility per Service

Mỗi service sở hữu một bounded context:

```
order-service     <- Quản lý đơn hàng
product-service   <- Quản lý sản phẩm
user-service      <- Quản lý người dùng
payment-service   <- Xử lý thanh toán
notification-service <- Gửi thông báo
```

### 2. Database per Service

Mỗi service có database riêng — không share database:

```
order-service     -> orders_db (MySQL)
product-service   -> products_db (MySQL)
user-service      -> users_db (MySQL)
search-service    -> search_index (Elasticsearch)
```

### 3. API Gateway

Một điểm duy nhất để client giao tiếp:

```
Client
  |
  v
API Gateway (xác thực, routing, rate limiting)
  |
  +-- /orders/**    -> order-service:8001
  +-- /products/**  -> product-service:8002
  +-- /users/**     -> user-service:8003
```

---

## Thách thức của Microservices

| Thách thức | Giải pháp phổ biến |
|---|---|
| Distributed transaction | Saga Pattern, Event Sourcing |
| Service discovery | Consul, Kubernetes |
| Network latency & failure | Circuit Breaker, Retry |
| Data consistency | Eventual consistency, Outbox Pattern |
| Debugging | Distributed tracing (Jaeger, Zipkin) |

---

## Liên kết

- [API Design](./api-design.md)
- [Clean Architecture](./clean-architecture.md)
