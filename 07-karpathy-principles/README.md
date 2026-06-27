# Karpathy Principles — Tư Duy Nền Tảng cho AI Coding

**Priority: 0 (Cao nhất — vượt trội mọi nguyên tắc kỹ thuật khác)**

---

## Nguồn gốc

Bộ nguyên tắc này được rút ra từ quan sát của **Andrej Karpathy** về các điểm yếu phổ biến
khi LLM (Large Language Models) thực thi tác vụ lập trình.

> "The models make wrong assumptions on your behalf and just run along with them without checking.
> They don't manage their confusion, don't seek clarifications, don't surface inconsistencies,
> don't present tradeoffs, don't push back when they should."
>
> — Andrej Karpathy

---

## Tại sao Priority 0?

Đây không phải nguyên tắc kỹ thuật — đây là nguyên tắc về **tư duy và cách tiếp cận** trước
khi viết bất kỳ dòng code nào. Không có tư duy đúng, mọi nguyên tắc kỹ thuật đều có thể
bị áp dụng sai.

Ứng dụng cho cả **lập trình viên** và **AI Agent**.

---

## Bốn nguyên tắc cốt lõi

| STT | Nguyên tắc | Một câu tóm tắt |
|---|---|---|
| 1 | [Think Before Coding](./think-before-coding.md) | Đừng giả định. Làm rõ trước khi làm. |
| 2 | [Simplicity First](./simplicity-first.md) | Code tối thiểu giải quyết vấn đề. Không gì thêm. |
| 3 | [Surgical Changes](./surgical-changes.md) | Chỉ chạm vào những gì cần chạm. |
| 4 | [Goal-Driven Execution](./goal-driven-execution.md) | Định nghĩa tiêu chí thành công trước khi bắt đầu. |

---

## Các lỗi phổ biến mà nguyên tắc này giải quyết

| Lỗi | Nguyên tắc áp dụng |
|---|---|
| Ngầm chọn một cách hiểu mà không hỏi | Think Before Coding |
| Viết 1000 dòng khi 100 dòng là đủ | Simplicity First |
| "Cải thiện" code liền kề không được yêu cầu | Surgical Changes |
| Không định nghĩa rõ "xong" nghĩa là gì | Goal-Driven Execution |
| Thêm error handling cho tình huống không thể xảy ra | Simplicity First |
| Xoá dead code không liên quan khi sửa bug | Surgical Changes |
