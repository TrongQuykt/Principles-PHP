# Hệ Thống Priority — Programming Principles

---

## Bảng phân cấp

| Priority | Folder | Nhóm nguyên tắc | Mức độ bắt buộc |
|---|---|---|---|
| 0 | `07-karpathy-principles/` | Karpathy — Tư duy nền tảng cho AI Coding | Bắt buộc tuyệt đối với AI Agent |
| 1 | `01-foundations/` | Foundations — Nguyên tắc cốt lõi | Bắt buộc tuyệt đối |
| 2 | `02-design-patterns/` | Design Patterns | Bắt buộc khi áp dụng pattern |
| 3 | `03-architecture/` | Architecture | Bắt buộc ở cấp hệ thống |
| 4 | `04-code-quality/` | Code Quality | Bắt buộc trong mọi pull request |
| 5 | `05-security/` | Security | Bắt buộc cho mọi endpoint public |
| 6 | `06-performance/` | Performance | Áp dụng khi có vấn đề đo lường được |

> **Lưu ý**: Priority 0 (Karpathy) được xếp trên Priority 1 vì đây là tư duy meta — tư duy về **cách suy nghĩ** trước khi áp dụng bất kỳ nguyên tắc kỹ thuật nào khác.

---

## Quy tắc áp dụng khi xung đột

### Nguyên tắc chính

Khi hai nguyên tắc mâu thuẫn nhau trong một tình huống cụ thể:

1. Xác định Priority Level của từng nguyên tắc
2. Nguyên tắc có **Priority số nhỏ hơn** được áp dụng trước
3. Ghi lại lý do quyết định trong comment hoặc pull request description

### Ví dụ xung đột thực tế

**Tình huống 1**: Design Pattern vs SOLID

> Bạn muốn dùng Singleton Pattern nhưng nó vi phạm
> Dependency Inversion Principle (trong SOLID).

**Quyết định**: SOLID (Priority 1) > Design Patterns (Priority 2).  
Hãy dùng Dependency Injection thay vì Singleton thuần.

---

**Tình huống 2**: Performance vs Code Quality

> Tối ưu query bằng cách viết raw SQL phức tạp thay vì
> dùng ORM dễ đọc, làm giảm khả năng bảo trì.

**Quyết định**: Code Quality (Priority 4) > Performance (Priority 6).  
Chỉ tối ưu khi có dữ liệu đo lường cho thấy đây thực sự là bottleneck.

---

**Tình huống 3**: DRY vs Simplicity (KISS)

> Tạo abstraction để tái sử dụng nhưng làm code phức tạp hơn
> so với việc lặp lại một đoạn code đơn giản 2 lần.

**Quyết định**: Cả hai đều thuộc Priority 1 (Foundations).  
Áp dụng phán đoán: nếu abstraction làm tăng đáng kể độ phức tạp
mà chỉ giải quyết một use case nhỏ, giữ nguyên code đơn giản.
Tham khảo thêm: [YAGNI](./01-foundations/YAGNI.md).

---

## Anti-patterns trong việc áp dụng Priority

| Anti-pattern | Vấn đề |
|---|---|
| Dùng Performance làm lý do bỏ qua Security | Security (5) cao hơn Performance (6) |
| Dùng Design Pattern khi nó vi phạm SOLID | Foundations (1) cao hơn Patterns (2) |
| Tối ưu sớm trước khi code chạy đúng | Vi phạm Simplicity First và YAGNI |
| Refactor toàn bộ codebase khi chỉ sửa một bug | Vi phạm Surgical Changes (Priority 0) |

---

## Ghi chú cho AI Agent

Khi thực thi tác vụ lập trình, AI Agent **bắt buộc** áp dụng theo thứ tự:

1. Áp dụng tư duy **Priority 0 (Karpathy)**: Suy nghĩ trước, đặt câu hỏi khi mơ hồ
2. Đảm bảo không vi phạm **Priority 1 (Foundations)**: SOLID, DRY, KISS, YAGNI
3. Lựa chọn Pattern và Architecture phù hợp (Priority 2, 3)
4. Đảm bảo Code Quality, Security, Performance theo thứ tự (Priority 4 → 6)
