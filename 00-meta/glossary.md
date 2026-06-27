# Glossary — Bảng Thuật Ngữ IT

Danh sách các thuật ngữ kỹ thuật được sử dụng trong tài liệu này.
Thuật ngữ IT **giữ nguyên tiếng Anh**, phần mô tả bằng tiếng Việt.

---

## A

| Thuật ngữ | Định nghĩa |
|---|---|
| Abstraction | Che giấu chi tiết triển khai, chỉ hiển thị những gì cần thiết. |
| API (Application Programming Interface) | Giao diện lập trình ứng dụng — tập hợp các endpoint/hàm cho phép các hệ thống giao tiếp với nhau. |
| Artisan | Command-Line Interface (CLI) mạnh mẽ của Laravel. |
| Authentication | Xác thực danh tính người dùng. |
| Authorization | Kiểm tra quyền hạn của người dùng đã xác thực. |

## B

| Thuật ngữ | Định nghĩa |
|---|---|
| Backend | Phần máy chủ của ứng dụng — xử lý logic, database, business rules. |
| Blade | Template engine mạnh mẽ và an toàn của Laravel. |
| Bottleneck | Điểm nghẽn cổ chai trong hệ thống làm giảm hiệu suất tổng thể. |
| Bug | Lỗi trong code gây ra hành vi không mong muốn. |

## C

| Thuật ngữ | Định nghĩa |
|---|---|
| Cache / Caching | Lưu trữ tạm thời dữ liệu để truy cập nhanh hơn ở các lần sau. |
| Clean Architecture | Kiến trúc sạch — tổ chức code theo vòng tròn dependency hướng vào trong. |
| Cohesion | Mức độ các phần trong một module liên quan chặt chẽ với nhau (càng cao càng tốt). |
| Controller | Lớp điều phối trong MVC — nhận request, gọi service, trả response. |
| Coupling | Mức độ phụ thuộc lẫn nhau giữa các module (càng thấp càng tốt). |
| CRUD (Create, Read, Update, Delete) | Bốn thao tác cơ bản với dữ liệu. |

## D

| Thuật ngữ | Định nghĩa |
|---|---|
| Dependency | Sự phụ thuộc giữa các component. |
| Dependency Injection | Kỹ thuật truyền dependency từ bên ngoài vào thay vì tạo bên trong. |
| Design Pattern | Giải pháp tái sử dụng đã được kiểm chứng cho các vấn đề thiết kế phổ biến. |
| DRY (Don't Repeat Yourself) | Không lặp lại code/logic — mỗi kiến thức chỉ có một đại diện duy nhất. |

## E

| Thuật ngữ | Định nghĩa |
|---|---|
| Eager Loading | Tải dữ liệu quan hệ cùng lúc với query chính để tránh N+1. |
| Encapsulation | Đóng gói — che giấu state bên trong, chỉ expose giao diện cần thiết. |
| Endpoint | Một URL cụ thể trong API nhận request từ client. |

## F

| Thuật ngữ | Định nghĩa |
|---|---|
| Factory | Pattern tạo đối tượng mà không cần chỉ định class cụ thể. |
| Frontend | Phần giao diện người dùng của ứng dụng (trình duyệt). |

## I

| Thuật ngữ | Định nghĩa |
|---|---|
| Interface | Hợp đồng định nghĩa các method mà một class phải implement. |
| Immutability | Tính bất biến — object không thể thay đổi sau khi được tạo. |
| Indexing | Kỹ thuật đánh mục lục cho database để tăng tốc độ truy vấn. |

## K

| Thuật ngữ | Định nghĩa |
|---|---|
| KISS (Keep It Simple, Stupid) | Ưu tiên giải pháp đơn giản nhất có thể hoạt động. |

## L

| Thuật ngữ | Định nghĩa |
|---|---|
| Law of Demeter | Một object chỉ nên giao tiếp với "láng giềng trực tiếp" của nó. |
| Lazy Loading | Chỉ tải dữ liệu khi thực sự cần. |

## M

| Thuật ngữ | Định nghĩa |
|---|---|
| Microservice | Kiến trúc chia ứng dụng thành các service nhỏ, độc lập triển khai. |
| Migration | Tập tin mô tả thay đổi database schema, có thể rollback. |
| Model | Lớp đại diện cho dữ liệu và quan hệ trong MVC. |
| MVC (Model-View-Controller) | Pattern tổ chức code theo 3 lớp: dữ liệu, giao diện, điều phối. |

## N

| Thuật ngữ | Định nghĩa |
|---|---|
| N+1 Query | Vấn đề hiệu năng: 1 query lấy danh sách + N query con lấy từng item. |

## O

| Thuật ngữ | Định nghĩa |
|---|---|
| ORM (Object-Relational Mapping) | Ánh xạ giữa object trong code và bảng trong database. |
| OWASP | Tổ chức phi lợi nhuận tập trung vào bảo mật ứng dụng web. |
| JWT (JSON Web Token) | Chuẩn mở để truyền tin an toàn giữa các bên dưới dạng JSON object. |

## P

| Thuật ngữ | Định nghĩa |
|---|---|
| Payload | Dữ liệu được gửi trong body của HTTP request/response. |
| Polymorphism | Đa hình — cùng interface, nhiều cách triển khai khác nhau. |

## R

| Thuật ngữ | Định nghĩa |
|---|---|
| Refactoring | Tái cấu trúc code mà không thay đổi hành vi bên ngoài. |
| Repository Pattern | Lớp trừu tượng giữa business logic và data access. |
| REST (Representational State Transfer) | Phong cách thiết kế API dựa trên HTTP methods và resources. |

## S

| Thuật ngữ | Định nghĩa |
|---|---|
| Separation of Concerns | Tách biệt các mối quan tâm — mỗi module chỉ chịu trách nhiệm một việc. |
| Service | Lớp chứa business logic trong kiến trúc phân tầng. |
| Service Container | Công cụ mạnh mẽ của Laravel để quản lý class dependencies. |
| Service Provider | Trung tâm khởi tạo (bootstrapping) của ứng dụng Laravel. |
| Singleton | Pattern đảm bảo một class chỉ có duy nhất một instance. |
| SOLID | 5 nguyên tắc thiết kế hướng đối tượng: SRP, OCP, LSP, ISP, DIP. |
| Surgical Changes | Nguyên tắc thay đổi code chính xác, tối thiểu để đạt mục tiêu. |

## T

| Thuật ngữ | Định nghĩa |
|---|---|
| Technical Debt | Nợ kỹ thuật — chi phí phát sinh khi chọn giải pháp nhanh thay vì giải pháp tốt. |
| Transaction | Nhóm các thao tác database được thực thi toàn vẹn — all or nothing. |

## V

| Thuật ngữ | Định nghĩa |
|---|---|
| Validation | Kiểm tra tính hợp lệ của dữ liệu đầu vào. |
| View | Lớp hiển thị giao diện người dùng trong MVC. |

## W

| Thuật ngữ | Định nghĩa |
|---|---|
| Webhook | Cơ chế notify từ server đến server khi có sự kiện xảy ra. |

## Y

| Thuật ngữ | Định nghĩa |
|---|---|
| YAGNI (You Aren't Gonna Need It) | Đừng implement chức năng khi chưa thực sự cần đến. |
