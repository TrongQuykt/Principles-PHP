# Code Review

**Priority: 4** | Nhóm: [Code Quality](./README.md)

---

## Mục tiêu

Code Review không phải là kiểm tra lỗi — đó là **chia sẻ kiến thức và đảm bảo chất lượng**.

---

## Checklist — Reviewer

### Correctness
- [ ] Logic có đúng với yêu cầu không?
- [ ] Edge cases có được xử lý không? (null, empty, negative)
- [ ] Error handling có đầy đủ không?

### Principles
- [ ] Không vi phạm SOLID, DRY, KISS
- [ ] Không có N+1 query
- [ ] Không có blocking operation trong request cycle

### Security
- [ ] Input có được validate không?
- [ ] Không lộ sensitive data trong response/log
- [ ] Authorization đúng chưa?

### Code Quality
- [ ] Tên biến/hàm/class rõ nghĩa không?
- [ ] Không có magic numbers
- [ ] Comment có cần thiết không? (Tránh comment giải thích code — hãy cải thiện code)

---

## Etiquette — Văn hoá Code Review

| Người review | Người được review |
|---|---|
| Phê bình code, không phê bình người | Không defensive khi nhận feedback |
| Giải thích lý do, không chỉ yêu cầu | Hỏi khi không hiểu feedback |
| Phân biệt "phải sửa" vs "gợi ý" | Trả lời mọi comment trước khi request re-review |
| Acknowledge điểm tốt trong code | Giải thích reasoning khi không đồng ý |

---

## Liên kết

- [Naming Conventions](./naming-conventions.md)
- [Testing Principles](./testing-principles.md)
